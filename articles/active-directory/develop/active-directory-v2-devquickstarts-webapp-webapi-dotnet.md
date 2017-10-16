---
title: "Azure AD v2.0 .NET Web 應用程式呼叫 API 入門 | Microsoft Docs"
description: "如何建置使用個人 Microsoft 帳戶以及工作或學校帳戶登入，以呼叫 Web 服務的 .NET MVC Web 應用程式。"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 56be906e-71de-469d-9a5c-9fc08aae4223
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: dc3162ae8e6ce622139125c2e78fa45d2e90d534
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="calling-a-web-api-from-a-net-web-app"></a>從 .NET Web 應用程式呼叫 Web API
透過 v2.0 端點，您可以快速地將驗證加入 Web 應用程式和 Web API，同時支援個人 Microsoft 帳戶以及工作或學校帳戶。  我們將在此處建置 MVC Web 應用程式，借 Microsoft OWIN 中介軟體之力，使用 OpenID Connect 登入使用者。  Web 應用程式將針對 OAuth 2.0 保護的 Web API 取得 OAuth 2.0 存取權杖，以允許建立、讀取及刪除特定使用者的「待辦事項清單」。

本教學課程主要著重於使用 ADAL 來取得和使用 Web 應用程式中的存取權杖，完整說明載於[這裡](active-directory-v2-flows.md#web-apps)。  建議您先了解如何[將登入新增 Web 應用程式](active-directory-v2-devquickstarts-dotnet-web.md)，或[如何正確保護 Web API](active-directory-v2-devquickstarts-dotnet-api.md)。

> [!NOTE]
> v2.0 端點並非支援每個 Azure Active Directory 案例和功能。  若要判斷是否應該使用 v2.0 端點，請閱讀相關的 [v2.0 限制](active-directory-v2-limitations.md)。
> 
> 

## <a name="download-sample-code"></a>下載範例程式碼
本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet)。  若要遵循執行，您可以 [用 .zip 格式下載應用程式的基本架構](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) ，或複製基本架構：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

或者，您可以 [將已完成的應用程式下載為 .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) ，或複製已完成的應用程式：

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>註冊應用程式
在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) 建立新的應用程式，或遵循下列[詳細步驟](active-directory-v2-app-registration.md)。  請確定：

* 將指派給您應用程式的 **應用程式識別碼** 複製起來，您很快會需要用到這些識別碼。
* 建立**密碼**類型的**應用程式祕密**，並複製其值以備後用
* 為您的應用程式新增 **Web** 平台。
* 輸入正確的 **重新導向 URI**。 重新導向 URI 會向 Azure AD 指出驗證回應應導向的位置，本教學課程的預設為 `https://localhost:44326/`。

## <a name="install-owin"></a>安裝 OWIN
請使用封裝管理員主控台，將 OWIN 中介軟體 NuGet 封裝加入 `TodoList-WebApp` 專案。  OWIN 中介軟體將用來發出登入和登出要求、管理使用者的工作階段，以及取得使用者相關資訊等其他作業。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>登入使用者
現在設定 OWIN 中介軟體來使用 [OpenID Connect 驗證通訊協定](active-directory-v2-protocols.md)。  

* 開啟 `TodoList-WebApp` 專案根目錄中的 `web.config` 檔案，並在 `<appSettings>` 區段中輸入應用程式的組態值。
  * `ida:ClientId` 是在註冊入口網站中指派給應用程式的 **應用程式識別碼** 。
  * `ida:ClientSecret` 是您在註冊入口網站中建立的 **應用程式密碼** 。
  * `ida:RedirectUri` 是您在入口網站中輸入的 **重新導向 URI** 。
* 開啟 `TodoList-Service` 專案根目錄中的 `web.config` 檔案，並以與上述相同的**應用程式 ID** 取代 `ida:Audience`。
* 開啟檔案 `App_Start\Startup.Auth.cs`，並加入上述程式庫的 `using` 陳述式。
* 在相同檔案中實作 `ConfigureAuth(...)` 方法。  您在 `OpenIDConnectAuthenticationOptions` 中所提供的參數將會做為您的應用程式與 Azure AD 進行通訊的座標使用。

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>使用 MSAL 取得存取權杖
在 `AuthorizationCodeReceived` 通知中，我們想要使用與 [OpenID Connect 串聯的 OAuth 2.0](active-directory-v2-protocols.md)，以兌換待辦事項清單服務之存取權杖的 authorization_code。  MSAL 可為您簡化這個程序：

* 首先，安裝 MSAL 預覽版本：

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```

* 並針對 MSAL，將另一個 `using` 陳述式新增到 `App_Start\Startup.Auth.cs` 檔案。
* 現在，新增一個名為 `OnAuthorizationCodeReceived` 的事件處理常式方法。  此處理常式會使用 MSAL 取得待辦事項清單 API 的存取權杖，並將 MSAL 權杖中的權杖儲存起來以供日後使用：

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

* 在 Web Apps 中，MSAL 具有可延伸的權杖快取，可以用來儲存權杖。  這個範例會實作使用 http 工作階段存放區來快取權杖的 `NaiveSessionCache` 。

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>呼叫 Web API
現在可以實際使用您在步驟 3 中取得的 access_token。  開啟 Web 應用程式的 `Controllers\TodoListController.cs` 檔案，此檔案可向待辦事項清單 API 提出所有 CRUD 請求。

* 這裡可再次使用 MSAL，從 MSAL 快取擷取 access_tokens。  首先，將 MSAL 的 `using` 陳述式新增至這個檔案。
  
    `using Microsoft.Identity.Client;`
* 在 `Index` 動作中，使用 MSAL 的 `AcquireTokenSilentAsync` 方法取得可用來讀取待辦事項清單服務資料的 access_token：

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

* 此範例接下來會將產生的權杖加入 HTTP GET 要求當做 `Authorization` 標頭，讓待辦事項清單服務用來驗證要求。
* 如果待辦事項清單服務傳回 `401 Unauthorized` 回應，表示 MSAL 的 access_tokens 已因為某些原因而無效。  在此情況下，您應該卸除所有來自 MSAL 快取的 access_tokens，並向使用者顯示訊息告知可能需要再次登入，而這會重新啟動權杖的取得流程。

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

* 同樣地，如果 MSAL 因為任何原因而無法傳回 access_token，您也應指示使用者再次登入。  這就像擷取任何 `MSALException` 一樣簡單：

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

* `Create` 和 `Delete` 動作中執行完全一致的 `AcquireTokenSilentAsync` 呼叫。  在 Web 應用程式中，只要應用程式有需要，就可以使用這個 MSAL 方法取得 access_tokens。  MSAL 會為您取得、快取和重新整理權杖。

最後，建置並執行您的應用程式！  使用 Microsoft 帳戶或 Azure AD 帳戶登入，並注意上方導覽列中的使用者身分識別的反映狀態。  在使用者的 [待辦事項清單] 中加入和刪除某些項目，以查看執行中 OAuth 2.0 保護的 API 呼叫。  您的 Web 應用程式和 Web API 現在都使用業界標準的通訊協定保護，可以使用個人與工作/學校帳戶來驗證使用者。

[這裡提供](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip)完整的範例供您參考 (不含您的設定值)。  

## <a name="next-steps"></a>後續步驟
如需其他資源，請參閱：

* [v2.0 開發人員指南 >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow "azure-active-directory" 標籤 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新
我們鼓勵您造訪 [此頁面](https://technet.microsoft.com/security/dd252948) 並訂閱資訊安全摘要報告警示，以在安全性事件發生時收到通知。

