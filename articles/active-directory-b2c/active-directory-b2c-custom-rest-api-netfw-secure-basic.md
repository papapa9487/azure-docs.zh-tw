---
title: "Azure Active Directory B2C：使用 HTTP 基本驗證保護您的 RESTful 服務"
description: "如何使用 HTTP 基本驗證在您的 Azure AD B2C 中保護您的自訂 REST API 宣告交換的範例"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 226ecbe7e4d9d95fd4ba3255c95ec6baa1a86576
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-http-basic-authentication"></a>Azure Active Directory B2C：使用 HTTP 基本驗證保護您的 RESTful 服務
在[相關文章](active-directory-b2c-custom-rest-api-netfw.md)中，我們在未經驗證的情況下，即建立了整合至 Azure AD B2C 使用者旅程圖的 RESTful 服務 (Web API)。 此文章示範如何將 HTTP 基本驗證新增至 RESTful 服務，使得只有經過驗證的使用者 (包括 B2C) 可存取您的 API。 利用 HTTP 基本驗證，您可以在自訂原則中設定使用者認證 (應用程式識別碼和應用程式密碼) 以使用該認證。 

> [!NOTE]
>
>下列文章中會更詳細說明[在 ASP.NET Web API 中進行基本驗證](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/basic-authentication)。

## <a name="prerequisites"></a>必要條件
完成[將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中](active-directory-b2c-custom-rest-api-netfw.md)文章的前述步驟

## <a name="step-1-add-authentication-support"></a>步驟 1：新增驗證支援

### <a name="step-11-add-application-settings-to-projects-webconfig-file"></a>步驟 1.1 將應用程式設定新增至專案的 web.config 檔案
1. 開啟您稍早建立的 Visual Studio 專案 
2. 將下列應用程式設定新增至 web.config 檔案的 `appSettings` 元素下：

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. 建立密碼並設定 `WebApp:ClientSecret` 值

    若要產生 coplex 密碼，您可以執行下列 PowerShell。 但您可以使用任意值。

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>步驟 1.2 安裝 OWIN 程式庫
首先，請使用 Visual Studio Package Manager Console，將 OWIN 中介軟體 NuGet 封裝新增至專案：

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-authentication-middleware-class"></a>步驟 1.3 新增驗證中介軟體類別
在 `App_Start` 資料夾新增 `ClientAuthMiddleware.cs` 類別。 以滑鼠右鍵按一下 `App_Start` 資料夾，選取 [新增]，然後選取 [類別]

![在 App_Start 資料夾下新增 ClientAuthMiddleware.cs 類別](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

輸入 `ClientAuthMiddleware.cs` 做為類別名稱

![建立新的 C# 類別](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

開啟檔案 `App_Start\ClientAuthMiddleware.cs` 並以下列程式碼取代檔案內容：


```C#

using Microsoft.Owin;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using System.Security.Principal;
using System.Text;
using System.Threading.Tasks;
using System.Web;

namespace Contoso.AADB2C.API
{
    /// <summary>
    /// Class to create a custom owin middleware to check for client authentication
    /// </summary>
    public class ClientAuthMiddleware
    {
        private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
        private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];

        /// <summary>
        /// Gets or sets the next owin middleware
        /// </summary>
        private Func<IDictionary<string, object>, Task> Next { get; set; }

        /// <summary>
        /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
        /// </summary>
        /// <param name="next"></param>
        public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
        {
            this.Next = next;
        }

        /// <summary>
        /// Invoke client authentication middleware during each request.
        /// </summary>
        /// <param name="environment">Owin environment</param>
        /// <returns></returns>
        public Task Invoke(IDictionary<string, object> environment)
        {
            // Get wrapper class for the environment
            var context = new OwinContext(environment);

            // Check whether the authorization header is available. This contains the credentials.
            var authzValue = context.Request.Headers.Get("Authorization");
            if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
            {
                // Process next middleware
                return Next(environment);
            }

            // Get credentials
            var creds = authzValue.Substring("Basic ".Length).Trim();
            string clientId;
            string clientSecret;

            if (RetrieveCreds(creds, out clientId, out clientSecret))
            {
                // Set transaction authenticated as client
                context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
            }

            return Next(environment);
        }

        /// <summary>
        /// Retrieve credentials from header
        /// </summary>
        /// <param name="credentials">Authorization header</param>
        /// <param name="clientId">Client identifier</param>
        /// <param name="clientSecret">Client secret</param>
        /// <returns>True if valid credentials were presented</returns>
        private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
        {
            string pair;
            clientId = clientSecret = string.Empty;

            try
            {
                pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
            }
            catch (FormatException)
            {
                return false;
            }
            catch (ArgumentException)
            {
                return false;
            }

            var ix = pair.IndexOf(':');
            if (ix == -1)
            {
                return false;
            }

            clientId = pair.Substring(0, ix);
            clientSecret = pair.Substring(ix + 1);

            // Return whether credentials are valid
            return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
        }
    }
}
```

### <a name="step-14-add-an-owin-startup-class"></a>步驟 1.4：新增 OWIN 啟動類別
將 OWIN 啟動類別新增至名為 `Startup.cs` 的 API。 以滑鼠右鍵按一下專案，依序選取 [新增] 和 [新增項目]，然後搜尋 OWIN。

![新增 OWIN 啟動類別](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

開啟檔案 `Startup.cs` 並以下列程式碼取代檔案內容：

```C#
using Microsoft.Owin;
using Owin;

[assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
namespace Contoso.AADB2C.API
{
    public class Startup
    {
        public void Configuration(IAppBuilder app)
        {
                app.Use<ClientAuthMiddleware>();
        }
    }
}
```

### <a name="step-15-protect-identity-api-class"></a>步驟 1.5 保護身分識別 API 類別
開啟 Controllers\IdentityController.cs，並將 `[Authorize]`標記新增至控制器類別。 `[Authorize]` 標記會限制符合授權需求之使用者對控制器的存取。

![將 [Authorize] 標記新增至控制器](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>步驟 2：發佈至 Azure
若要發佈您的專案，請在 [方案總管] 中，以滑鼠右鍵按一下 **Contoso.AADB2C.API** 專案，然後選取 [發佈]。

## <a name="step-3-add-the-restful-services-app-id--app-secret-to-azure-ad-b2c"></a>步驟 3：將 RESTful 服務應用程式識別碼及應用程式密碼新增至 Azure AD B2C
利用用戶端識別碼 (使用者名稱) 和用戶端密碼保護 RESTful 服務之後，您需要將認證儲存在您的 Azure AD B2C 租用戶中。 在叫用 RESTful 服務時，您的自訂原則可提供認證。  

### <a name="step-31-add-restful-services-client-id"></a>步驟 3.1 新增 RESTful 服務用戶端識別碼
1.  移至您的 Azure AD B2C 租用戶，並選取 [B2C 設定] > [身分識別體驗架構]
2.  選取**原則金鑰**以檢視您的租用戶中可用的金鑰。
3.  按一下 [+新增]。
4.  針對 [選項] 使用 [手動]。
5.  針對 [名稱] 使用 `B2cRestClientId`。  
    可能會自動加入前置詞 `B2C_1A_`。
6.  在 [密碼] 方塊中，輸入您先前定義的應用程式識別碼
7.  針對 [金鑰使用方法] 使用 [密碼]。
8.  按一下 [建立] 
9.  確認您已建立金鑰 `B2C_1A_B2cRestClientId`。

### <a name="step-32-add-restful-services-client-secret"></a>步驟 3.2 新增 RESTful 服務用戶端密碼
1.  移至您的 Azure AD B2C 租用戶，並選取 [B2C 設定] > [身分識別體驗架構]
2.  選取**原則金鑰**以檢視您的租用戶中可用的金鑰。
3.  按一下 [+新增]。
4.  針對 [選項] 使用 [手動]。
5.  針對 [名稱] 使用 `B2cRestClientSecret`。  
    可能會自動加入前置詞 `B2C_1A_`。
6.  在 [密碼] 方塊中，輸入您先前定義的應用程式密碼
7.  針對 [金鑰使用方法] 使用 [密碼]。
8.  按一下 [建立] 
9.  確認您已建立金鑰 `B2C_1A_B2cRestClientSecret`。

## <a name="step-4-change-the-technicalprofile-to-support-basic-authentication-in-your-extension-policy"></a>步驟 4：在擴充原則中變更 `TechnicalProfile` 以支援基本驗證
1.  從您的工作目錄中開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。
2.  找出 `Id="REST-API-SignUp"` 隨附的 `<TechnicalProfile>` 節點
3.  找出 `<Metadata>` 元素
4.  將 `AuthenticationType` 變更為 `Basic`
```xml
<Item Key="AuthenticationType">Basic</Item>
```
5.  在 `<Metadata>` 元素關閉之後緊接著新增下列 XML 程式碼片段：  

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
新增 XML 程式碼片段之後，您的 `TechnicalProfile` 看起來應該如下：

![新增基本驗證 XML 元素](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>步驟 5：將原則上傳至您的租用戶

1.  在 [Azure 入口網站](https://portal.azure.com)中，切換至[您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後按一下 [Azure AD B2C]。
2.  選取 [識別體驗架構]。
3.  按一下 [所有原則]。
4.  選取 [上傳原則]
5.  勾選 [覆寫已存在的原則] 方塊。
6.  **上傳** TrustFrameworkExtensions.xml，並確定它通過驗證

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>步驟 6：使用 [立即執行] 測試自訂原則
1.  開啟 [Azure AD B2C 設定]，然後移至 [識別體驗架構]。

    >[!NOTE]
    >
    >    [立即執行] 需要在租用戶上至少預先註冊一個應用程式。 
    >    若要了解如何註冊應用程式，請參閱 Azure AD B2C [開始使用](active-directory-b2c-get-started.md)一文或[應用程式註冊](active-directory-b2c-app-registration.md)一文。

2.  開啟 **B2C_1A_signup_signin**，此為您上傳的信賴憑證者 (RP) 自訂原則。 選取 [立即執行]。
3.  嘗試在 [名字] 欄位中輸入"Test"，B2C 會在頁面頂端顯示錯誤訊息

    ![測試您的身分識別 API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  嘗試在 [名字] 欄位輸入名字 ("test" 除外)。 B2C 會註冊使用者，然後將 loyaltyNumber 傳送至您的應用程式。 記下此範例中此 JWT 中的數字。

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="next-steps"></a>後續步驟
* [使用用戶端憑證來保護您的 RESTful API](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

## <a name="optional-download-the-complete-policy-files-and-code"></a>[選用] 下載完整的原則檔案和程式碼
* 建議您在使用自訂原則逐步解說完成使用者入門後，使用自己的自訂原則檔案來建置您的情節，而非使用這些範例檔案。  [參考的範例原則檔案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)
* 您可以在這裡下載完整程式碼：[供參考的範例 Visual Studio 方案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)