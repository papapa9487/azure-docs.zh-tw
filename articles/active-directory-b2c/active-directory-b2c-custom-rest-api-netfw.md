---
title: "Azure Active Directory B2C：將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為使用者輸入的驗證"
description: "如何將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為使用者輸入的驗證的範例"
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
ms.date: 09/30/2017
ms.author: yoelh
ms.openlocfilehash: da7b8326a89fc6eb0bc3e7365c522d4a3adc809b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Azure Active Directory B2C：將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為使用者輸入的驗證
構成 Azure Active Directory B2C (Azure AD B2C) 基礎的識別體驗架構 (IEF)，可讓您與使用者旅程圖中的 RESTful API 整合。 在本逐步解說中，我們會說明 B2C 與.NET framework RESTful 服務 (Web API) 之間的互動方式。

## <a name="introduction"></a>簡介
Azure AD B2C 可讓您藉由呼叫 RESTful 服務，將您的商務邏輯新增至使用者旅程圖。 身分識別體驗架構會在**輸入宣告**集合中將資料傳送至 RESTful 服務，並在**輸出宣告**集合中從 RESTful 接收資料。 使用 RESTful 服務整合，您可以：

* 驗證使用者輸入資料，防止將格式不正確的資料持續保存至 Azure AD。 如果來自使用者的值無效，您的 RESTful 服務會傳回錯誤訊息，指示使用者提供輸入。 例如，您可能會驗證使用者提供的電子郵件地址存在於客戶資料庫中。
* 覆寫輸入宣告。 比方說，如果使用者以大寫或小寫字母輸入名字，您可以設定名稱的格式，並且僅將第一個字母大寫
* 進一步與公司的特定商務應用程式整合，讓使用者資料更豐富。 RESTful 服務可能會接收使用者電子郵件地址、查詢客戶資料庫，並將使用者的忠誠度號碼傳回 B2C。 傳回宣告可能會儲存在使用者 AAD 帳戶中，於下一個**協調流程步驟**中評估，或包含在存取權杖中。
* 執行自訂商務邏輯：傳送推播通知、更新公司的資料庫、執行使用者移轉程序、權限管理、稽核等等。

與 RESTful 服務的整合可以設計為宣告交換或驗證技術設定檔：

* **驗證技術設定檔**：對 RESTful 服務的呼叫發生在給定 TechnicalProfile 的 ValidationTechnicalProfile 內部。 在使用者旅程圖前進之前，驗證技術設定檔會驗證使用者提供的資料。 您可以使用驗證技術設定檔：
  * 傳送輸入宣告
  * 驗證輸入宣告並擲回自訂錯誤訊息
  * 送回輸出宣告

* **宣告交換** - 類似上一個，但發生協調流程步驟內。 這個定義僅限於：
   * 傳送輸入宣告
   * 送回輸出宣告

## <a name="restful-walkthrough"></a>RESTful 逐步解說
在本逐步解說中，您將開發會驗證使用者輸入並提供使用者忠誠度號碼的 .NET framework Web API。 例如，您的應用程式可以根據忠誠度號碼來授與「白金權益」的存取權。

概觀：
*   開發 RESTful 服務 (.NET framework Web API)
*   在使用者旅程圖中使用 RESTful 服務
*   傳送輸入宣告，並在您的程式碼中讀取
*   驗證使用者的名字
*   傳回背景忠誠度號碼 
*   將忠誠度號碼新增至 JSON Web 權杖 (JWT)

## <a name="prerequisites"></a>必要條件
完成[開始使用自訂原則](active-directory-b2c-get-started-custom.md)一文中的步驟。

## <a name="step-1-create-an-aspnet-web-api"></a>步驟 1：建立 ASP.NET Web API
1.  在 Visual Studio 中，選取 [檔案] > [新增] > [專案] 以建立專案。
2.  在 [新增專案] 對話方塊中，選取 [Visual C#] > [Web] > [ASP.NET Web 應用程式 (.NET Framework)]。
3.  為應用程式命名，例如 Contoso.AADB2C.API，然後選取 [確定]。

    ![建立新的 Visual Studio 專案](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4.  選取 [Web API] 或 [Azure API 應用程式] 範本
5.  請確認驗證已設為**沒有驗證**

    ![選取 Web API 範本](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

6.  按一下 [確定] 以建立專案

## <a name="step-2-prepare-the-rest-api-endpoint"></a>步驟 2：準備 REST API 端點

### <a name="step-21-add-data-models"></a>步驟 2.1 新增資料模型
模型會呈現 RESTful 服務中的輸入宣告以及輸出宣告資料。 您的程式碼會利用將來自 JSON 字串的輸入宣告模型還原序列化為 C# 物件 (您的模型)，藉此讀取輸入資料。 ASP.NET Web API 會將輸出宣告模型自動還原序列化回 JSON，然後將序列化資料寫入 HTTP 回應訊息的本文。 讓我們從建立表示輸入宣告的模型開始。

1.  如果沒有顯示 [方案總管]，請按一下 [檢視] 功能表，然後選取 [方案總管]。 在 [方案總管] 中，於 Models 資料夾上按一下滑鼠右鍵。 從操作功能表中，選取 [新增]，然後選取 [類別]。

    ![新增模型](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

2.  將類別命名為 `InputClaimsModel`，並將下列屬性新增至 `InputClaimsModel` 類別

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

3.  建立新模型 `OutputClaimsModel`，並將下列屬性新增至 `OutputClaimsModel` 類別

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

4.  再建立一個模型 `B2CResponseContent`。 此模型用來擲回輸入驗證的錯誤訊息。 將下列屬性新增至 `B2CResponseContent` 類別，請提供遺漏的參考並儲存檔案。

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>步驟 2.2：新增控制器
在 Web API 中，_控制器_是處理 HTTP 要求的物件。 我們會新增可傳回輸出宣告的控制器，或如果名字無效，則會擲回「衝突」HTTP 錯誤訊息。

1.  在 [方案總管] 中，於 Controllers 資料夾上按一下滑鼠右鍵。 選取 [新增]，然後選取 [控制器]。

    ![新增控制器](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2.  在 [新增 Scaffold] 對話方塊中，選取 [Web API 控制器 - 空白]。 按一下 [新增] 。

    !![選取 Web API 2 控制器 - 空的](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3.  在 [新增控制器] 對話方塊中，將控制器命名為 `IdentityController`，然後按一下 [新增]。

    ![輸入控制器名稱](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Scaffolding 會在 [控制器] 資料夾中建立名為 IdentityController.cs 的檔案。

4.  如果此檔案尚未開啟，請按兩下檔案將它開啟。 將這個檔案中的程式碼以下列程式碼行取代：

    ```C#
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If not data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create output claims object and set the loyalty number with random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-to-azure"></a>步驟 3：發佈至 Azure
1.  在 [方案總管] 中，以滑鼠右鍵按一下 **Contoso.AADB2C.API** 專案，然後選取 [發佈]。

    ![發佈至 Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2.  確定已選取 [Microsoft Azure App Service]，然後選取 [發佈]。

    ![建立新的 Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    這會開啟 [建立 App Service] 對話方塊，協助您建立在 Azure 中執行 ASP.NET Web 應用程式所需的 Azure 資源。

> [!NOTE]
>如需如何發佈的詳細資訊，請參閱[在 Azure 中建立 ASP.NET Web 應用程式](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)

3.  在 [Web 應用程式名稱] 中，輸入唯一的應用程式名稱 (有效字元為 `a-z`、`0-9` 和 `-`)。 Web 應用程式的 URL 是 `http://<app_name>.azurewebsites.NET`，其中 `<app_name>` 是您的 Web 應用程式名稱。 您可以接受自動產生的名稱 (這是唯一的)。

    選取 [建立] 開始建立 Azure 資源。

    ![提供 App Service 屬性](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4.  精靈完成後，它會將 ASP.NET Web 應用程式發佈至 Azure，然後在預設瀏覽器中啟動該應用程式，複製該 URL。

## <a name="step-4-add-the-new-claim-loyaltynumber-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>步驟 4：將新的宣告 `loyaltyNumber` 新增至 TrustFrameworkExtensions.xml 檔案的結構描述
宣告 `loyaltyNumber` 尚未在結構描述的其他地方加以定義。 因此，請在元素 `<BuildingBlocks>` 內部新增定義。 您可以在 TrustFrameworkExtensions.xml 檔案開頭處找到此元素。

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-adding-claims-provider"></a>步驟 5：新增宣告提供者 
每個宣告提供者必須有一或多個技術設定檔，以決定與該宣告提供者進行通訊所需的端點以及通訊協定。 基於各種原因，宣告提供者可以有多個技術設定檔。 例如，可能定義了多個技術設定檔，因為考慮到宣告提供者支援多個通訊協定，各種端點具有不同的功能，或是在不同的保證層級釋出不同的宣告。 它可能會在一個使用者旅程圖中接受釋出機密宣告，但在另一個則不接受。 

下列 XML 程式碼片段包含的 `ClaimsProvider` 節點具有兩個 `TechnicalProfile`：
* `<TechnicalProfile Id="REST-API-SignUp">` 會定義您的 RESTful 服務。 `Proprietary` 會描述為通訊協定，做為 RESTful 提供者。 `InputClaims` 元素會定義將從 B2C 傳送至 REST 服務的宣告。 在此範例中，`givenName` 宣告的內容會傳送至 REST 服務做為 `firstName`，宣告 `surename` 的內容會傳送至 REST 服務做為 `lastName`，而 `email` 會依原狀傳送。 `OutputClaims` 元素定義的宣告會從 RESTful 服務擷取回 B2C。

* `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` 會將驗證技術的設定檔新增至現有的技術設定檔 (於基底原則中定義)。 在註冊旅程期間，驗證技術設定檔會叫用上述技術設定檔。 如果 RESTful 服務傳回 HTTP 錯誤 409 (衝突)，即會向使用者呈現錯誤訊息。 

找出 `<ClaimsProviders>` 節點並 `<ClaimsProviders>` 節點下新增下列 XML 程式碼片段：

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://yourdomain.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>步驟 6：將宣告 `loyaltyNumber` 新增至您的信賴憑證者原則檔案，以便將宣告傳送至您的應用程式
編輯您的 SignUpOrSignIn.xml 信賴憑證者 (RP) 檔案，並修改 `<TechnicalProfile Id="PolicyProfile">` 元素以新增下列內容：`<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`。

新增宣告之後，`RelyingParty` 看起來像這樣：

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>步驟 7：將原則上傳至您的租用戶
1.  在 [Azure 入口網站](https://portal.azure.com)中，切換至[您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後開啟 [Azure AD B2C]
2.  選取 [識別體驗架構]
3.  開啟 [所有原則] 
4.  選取 [上傳原則]
5.  勾選 [覆寫已存在的原則] 方塊。
6.  **上傳** TrustFrameworkExtensions.xml，並確定它通過驗證
7.  重複最後一個步驟，並上傳 SignUpOrSignIn.xml

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>步驟 8：使用 [立即執行] 測試自訂原則
1.  開啟 [Azure AD B2C 設定]，然後移至 [識別體驗架構]。

> [!NOTE]
>
>[立即執行] 需要在租用戶上至少預先註冊一個應用程式。 若要了解如何註冊應用程式，請參閱 Azure AD B2C [開始使用](active-directory-b2c-get-started.md)一文或[應用程式註冊](active-directory-b2c-app-registration.md)一文。


2.  開啟 **B2C_1A_signup_signin**，此為您上傳的信賴憑證者 (RP) 自訂原則。 選取 [立即執行]。

    ![](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3.  嘗試在 [名字] 欄位中輸入"Test"，B2C 會在頁面頂端顯示錯誤訊息

    ![測試您的原則](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  嘗試在 [名字] 欄位輸入名字 ("test" 除外)。 B2C 會註冊使用者，然後將 loyaltyNumber 傳送至您的應用程式。 記下此 JWT 中的數字。

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>[選用] 下載完整的原則檔案和程式碼
* 建議您在使用自訂原則逐步解說完成使用者入門後，使用自己的自訂原則檔案來建置您的情節，而非使用這些範例檔案。  [參考的範例原則檔案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)
* 您可以在這裡下載完整程式碼：[供參考的範例 Visual Studio 方案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/)
    
## <a name="next-steps"></a>後續步驟
1.  [使用基本驗證 (使用者名稱和密碼) 保護您的 RESTful API](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
2.  [使用用戶端憑證保護您的 RESTful API](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)