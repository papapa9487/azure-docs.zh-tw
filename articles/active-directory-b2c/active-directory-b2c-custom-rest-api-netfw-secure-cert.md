---
title: "Azure Active Directory B2C：使用用戶端憑證保護您的 RESTful 服務"
description: "如何使用用戶端憑證在您的 Azure AD B2C 中保護您的自訂 REST API 宣告交換的範例"
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
ms.openlocfilehash: a5cfe0e3f40b929e969e0a118939caa1ccb33cc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-client-certificates"></a>Azure Active Directory B2C：使用用戶端憑證保護您的 RESTful 服務
[建立 RESTful 服務](active-directory-b2c-custom-rest-api-netfw.md)以與 Azure AD B2C 互動之後，我們將示範如何使用用戶端憑證來限制對您的 Azure Web 應用程式 (RESTful API) 的存取。 此機制稱為 TLS 相互驗證或**用戶端憑證驗證**。  只有具有適當憑證的服務 (例如 Azure AD B2C) 將能夠存取您的服務。

> [!NOTE]
>
>您也可以[利用 HTTP 基本驗證](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)來保護 RESTful 服務。 不過，您應該知道相較於用戶端憑證，HTTP 基本驗證被認為較不安全。 我們建議是使用用戶端憑證驗證來保護 RESTful 服務的安全，如本文章中所述。

這篇文章詳細說明如何：
1. 將您的 Web 應用程式設定為使用用戶端憑證驗證
1. 將憑證上傳至 Azure AD B2C 原則金鑰
1. 將您的自訂原則設定為使用用戶端憑證

## <a name="prerequisites"></a>必要條件
* 完成[整合 REST API 宣告交換](active-directory-b2c-custom-rest-api-netfw.md)文章的前述步驟
* 您應該具備有效的憑證 (具有私密金鑰的 .pfx 檔案)

## <a name="step-1-configure-web-app-for-client-certificate-authentication"></a>步驟 1：設定 Web 應用程式進行用戶端憑證驗證
若要設定 **Azure App Service** 以要求用戶端憑證，Web 應用程式 `clientCertEnabled` 站台設定必須是 true。 此設定目前無法透過 Azure 入口網站中的管理經驗使用，而且需要使用 REST API 才能完成此變更。

> [!NOTE]
>
>請確定您的 Azure App Service 方案至少為標準版本。 如需詳細資訊，請參閱 [Azure App Service 方案深入概觀](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)


使用 [Azure 資源總管 (預覽)](https://resources.azure.com) 將 clientCertEnabled 屬性翻轉為 true。 下列螢幕擷取畫面會示範如何透過 Azure 資源總管設定 clientCertEnabled 值 ![透過 Azure 資源總管設定 clientCertEnabled](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

> [!NOTE]
>
>如需如何設定 clientCertEnabled 屬性的詳細資訊，請參閱：[如何設定 Web 應用程式的 TLS 相互驗證](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)


> [!TIP]
>
>您也可以使用 [ARMClient](https://github.com/projectkudu/ARMClient) 工具，輕鬆地製作 REST API 呼叫。

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>步驟 2：將憑證上傳至 Azure AD B2C 原則金鑰
將 `clientCertEnabled` 設定為 `true` 之後，與您的 RESTful API 的通訊現在需要用戶端憑證。 若要這樣做，您需要在您的 Azure AD B2C 租用戶中上傳和儲存用戶端憑證。   
1.  移至您的 Azure AD B2C 租用戶，並選取 [B2C 設定] > [身分識別體驗架構]
2.  選取**原則金鑰**以檢視您的租用戶中可用的金鑰。
3.  按一下 [+新增]。
4.  針對 [選項] 使用 [上傳]。
5.  針對 [名稱] 使用 `B2cRestClientCertificate`。  
    會自動新增前置詞 `B2C_1A_`。
6.  在 [檔案上傳]中，選取包含私密金鑰的憑證 .pfx 檔案。 並提供憑證的**密碼**。

    ![上傳原則金鑰](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7.  按一下 [建立] 
8.  若要確認您已建立金鑰 `B2C_1A_B2cRestClientCertificate`，請選取 [原則金鑰] 以檢視您的租用戶中可用的金鑰。

## <a name="step-3-change-the-technicalprofile-to-support-client-certificate-authentication-in-your-extension-policy"></a>步驟 3：在擴充原則中變更 `TechnicalProfile` 以支援用戶端憑證驗證
1.  從您的工作目錄中開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。
2.  找出 `Id="REST-API-SignUp"` 隨附的 `<TechnicalProfile>` 節點
3.  找出 `<Metadata>` 元素
4.  將 `AuthenticationType` 變更為 `ClientCertificate`

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5.  在 `<Metadata>` 元素關閉之後緊接著新增下列 XML 程式碼片段：  

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

新增 XML 程式碼片段之後，您的 `TechnicalProfile` 看起來應該如下：

![設定 ClientCertificate 驗證 XML 元素](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>步驟 4：將原則上傳至您的租用戶

1.  在 [Azure 入口網站](https://portal.azure.com)中，切換至[您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後按一下 [Azure AD B2C]。
2.  選取 [識別體驗架構]。
3.  按一下 [所有原則]。
4.  選取 [上傳原則]
5.  勾選 [覆寫已存在的原則] 方塊。
6.  **上傳** TrustFrameworkExtensions.xml，並確定它通過驗證

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>步驟 5：使用 [立即執行] 測試自訂原則
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

   > [!NOTE]
   >如果您收到上述錯誤訊息，這表示在呈現用戶端憑證時，Azure AD B2C 已成功呼叫 RESTful 服務。 下一個步驟是驗證憑證。

## <a name="step-6-adding-certificate-validation"></a>步驟 6：新增憑證驗證
Azure Web 應用程式平台不會對 Azure AD B2C 傳送至 RESTful 服務的用戶端憑證進行任何驗證 (除了檢查憑證是否存在)。 驗證憑證是 Web 應用程式的責任。 以下是基於驗證而驗證憑證內容的範例 ASP.NET 程式碼。

> [!NOTE]
>如需如何針對用戶端憑證驗證設定 Azure Service 應用程式的詳細資訊，請參閱：[如何設定 Web 應用程式的 TLS 相互驗證](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)

### <a name="61-add-application-settings-to-projects-webconfig-file"></a>6.1 將應用程式設定新增至專案的 web.config 檔案
開啟您稍早建立的 Visual Studio 專案，並將下列應用程式設定新增至 web.config 檔案的 `appSettings` 元素下

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

取代憑證的值：
* 主體名稱
* 簽發者名稱
* 憑證指紋

### <a name="62-add-isvalidclientcertificate-function"></a>6.2 新增 IsValidClientCertificate 函式
開啟 Controllers\IdentityController.cs 並將下列函式新增至 `Identity` 控制器類別。 

```C#
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check time validity of certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check subject name of certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check issuer name of certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check thumprint of certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

在上述**範例**中，只有在符合所有條件時，我們才會將憑證視為有效：
1. 憑證未過期，並且在伺服器上目前為作用中。
2. 具有通用名稱憑證的 `Subject` 名稱等於 `ClientCertificate:Subject` 應用程式設定值
3. 具有通用名稱憑證的 `Issuer` 名稱等於 `ClientCertificate:Issuer` 應用程式設定值
4. `thumbprint` 的憑證等於 `ClientCertificate:Thumbprint` 應用程式設定值

> [!IMPORTANT]
>
>根據您的服務的敏感度，您可能需要新增更多驗證。 例如：測試憑證是否鏈結至信任的根授權單位、簽發者組織名稱驗證，依此類推。

### <a name="63-add-isvalidclientcertificate-function"></a>6.3 新增 IsValidClientCertificate 函式
開啟 Controllers\IdentityController.cs，在 `SignUp()`函式的開頭新增下列幾行程式碼。 

```C#
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

新增程式碼片段之後，您的 `Identity` 控制器看起來應該如下：

![新增憑證驗證程式碼](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-to-azure-and-test"></a>步驟 7：發佈到 Azure 並測試
1. 在 [方案總管] 中，以滑鼠右鍵按一下 **Contoso.AADB2C.API** 專案，然後選取 [發佈]。
2. 重複步驟 6，並使用憑證驗證測試您的自訂原則 (再次)。 新增驗證之後，嘗試執行原則並確定一切可運作
3. 現在，在 web.config 檔案中，將 `ClientCertificate:Subject` 的值變更為**無效**值。 再次執行此原則，您應該會看到錯誤訊息。
4. 將值變更回**有效**值，並確定該原則可以呼叫您的 REST API
5. [使用 Application Insights 收集記錄](active-directory-b2c-troubleshoot-custom.md)以進行疑難排解

## <a name="optional-download-the-complete-policy-files-and-code"></a>[選用] 下載完整的原則檔案和程式碼
* 建議您在使用自訂原則逐步解說完成使用者入門後，使用自己的自訂原則檔案來建置您的情節，而非使用這些範例檔案。  [參考的範例原則檔案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)
* 您可以在這裡下載完整程式碼：[供參考的範例 Visual Studio 方案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)