---
title: "Azure AD .NET Web 應用程式入門 | Microsoft Docs"
description: "建置與 Azure AD 整合來進行登入的 .NET MVC Web 應用程式。"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3c1e558c9d41e385f80939203a3457b74e30973b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>使用 Azure AD 來進行 ASP.NET Web 應用程式登入和登出
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) 透過以幾行程式碼提供單一登入和登出功能，讓您能夠輕鬆將 Web 應用程式身分識別管理外包。 您可以藉由使用 Microsoft 的 Open Web Interface for .NET (OWIN) 中介軟體實作，將使用者登入和登出 ASP.NET Web 應用程式。 社群導向 OWIN 中介軟體包含在 .NET Framework 4.5 中。 本文說明如何使用 OWIN 來執行下列操作：

* 藉由使用 Azure AD 作為身分識別提供者，將使用者登入 Web 應用程式。
* 顯示部分使用者資訊。
* 將使用者登出應用程式。

## <a name="before-you-get-started"></a>開始之前
* 下載[應用程式基本架構](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)或下載[完整的範例](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)。
* 您還需要一個可供註冊應用程式的 Azure AD 租用戶。 如果您還沒有 Azure AD 租用戶，請[了解如何取得租用戶](active-directory-howto-tenant.md)。

當您準備就緒時，請依照接下來四個小節的程序操作。

## <a name="step-1-register-the-new-app-with-azure-ad"></a>步驟 1︰向 Azure AD 註冊新的應用程式
若要設定應用程式以驗證使用者，請先藉由執行下列操作，在您的租用戶中註冊該應用程式︰

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頂端列中，按一下您的帳戶名稱。 在 [目錄] 清單下，選取您要註冊應用程式的 Active Directory 租用戶。
3. 按一下左側窗格中的 [更多服務]，然後選取 [Azure Active Directory]。
4. 按一下 [應用程式註冊]，然後選取 [新增]。
5. 依照提示來建立新的「Web 應用程式和/或 Web API」。
  * [名稱] 可向使用者描述該應用程式。
  * [登入 URL] 是應用程式的基底 URL。 基本架構的預設 URL 是 https://localhost:44320/。
6. 完成註冊之後，Azure AD 會為應用程式指派一個唯一的應用程式識別碼。 請從應用程式頁面複製該值，以在接下來的小節中使用。
7. 從應用程式的 [設定]  ->  [屬性] 頁面，更新應用程式識別碼 URI。 [應用程式識別碼 URI] 是應用程式的唯一識別碼。 命名慣例是 `https://<tenant-domain>/<app-name>` (例如 `https://contoso.onmicrosoft.com/my-first-aad-app`)。

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>步驟 2：設定應用程式以使用 OWIN 驗證管線
在此步驟中，您將設定 OWIN 中介軟體以使用 OpenID Connect 驗證通訊協定。 您將使用 OWIN 來發出登入和登出要求、管理使用者工作階段、取得使用者資訊等等。

1. 若要開始進行，請使用「套件管理器主控台」將 OWIN 中介軟體 NuGet 套件新增到專案中。

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. 若要將 OWIN 啟動類別新增到名為 `Startup.cs` 的專案中，請在專案上按一下滑鼠右鍵，依序選取 [新增]、[新增項目]，然後搜尋 **OWIN**。 OWIN 中介軟體會在應用程式啟動時叫用 **Configuration(...)** 方法。
3. 將類別宣告變更為 `public partial class Startup`。 我們已在另一個檔案中為您實作此類別的一部分。 請在 **Configuration(...)** 方法中，呼叫 **ConfgureAuth(...)** 來為應用程式設定驗證。  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. 開啟 App_Start\Startup.Auth.cs 檔案，然後實作 **ConfigureAuth(...)** 方法。 您在 *OpenIDConnectAuthenticationOptions* 中提供的參數會作為供應用程式與 Azure AD 進行通訊的座標。 您還必須設定 Cookie 驗證，因為 OpenID Connect 中介軟體會在背景中使用 Cookie。

     ```C#
     public void ConfigureAuth(IAppBuilder app)
     {
         app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

         app.UseCookieAuthentication(new CookieAuthenticationOptions());

         app.UseOpenIdConnectAuthentication(
             new OpenIdConnectAuthenticationOptions
             {
                 ClientId = clientId,
                 Authority = authority,
                 PostLogoutRedirectUri = postLogoutRedirectUri,
                 Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = context =>
                        {
                            context.HandleResponse();
                            context.Response.Redirect("/Error?message=" + context.Exception.Message);
                            return Task.FromResult(0);
                        }
                    }
             });
     }
     ```

5. 開啟專案根目錄中的 web.config 檔案，然後在 `<appSettings>` 區段中輸入組態值。
  * `ida:ClientId`：您在「步驟 1：向 Azure AD 註冊新的應用程式」中從 Azure 入口網站複製的 GUID。
  * `ida:Tenant`：您 Azure AD 租用戶的名稱 (例如 contoso.onmicrosoft.com)。
  * `ida:PostLogoutRedirectUri`：告訴 Azure AD 在順利完成登出要求後應將使用者重新導向到何處的指標。

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>步驟 3：使用 OWIN 向 Azure AD 發出登入和登出要求
應用程式現已設定妥當，可使用 OpenID Connect 驗證通訊協定來與 Azure AD 進行通訊。 OWIN 已處理有關製作驗證訊息、驗證來自 Azure AD 的權杖及維護使用者工作階段的一切細節。 剩餘的工作就是提供使用者一個登入和登出的方式。

1. 您可以在控制器中使用授權標籤，以要求使用者在存取特定頁面時先登入。 若要這麼做，請開啟 Controllers\HomeController.cs，然後將 `[Authorize]` 標籤新增至 About 動作。

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. 您也可以使用 OWIN 從程式碼中直接發出驗證要求。 若要這麼做，請開啟 Controllers\AccountController.cs。 然後在 SignIn() 和 SignOut() 動作中，發出 OpenID Connect 挑戰和登出要求。

     ```C#
     public void SignIn()
     {
         // Send an OpenID Connect sign-in request.
         if (!Request.IsAuthenticated)
         {
             HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
         }
     }
     public void SignOut()
     {
         // Send an OpenID Connect sign-out request.
         HttpContext.GetOwinContext().Authentication.SignOut(
              OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
     }
     ```

3. 開啟 Views\Shared\_LoginPartial.cshtml 以向使用者顯示應用程式登入和登出連結，並在檢視中列印出使用者的名稱。

    ```HTML
    @if (Request.IsAuthenticated)
    {
     <text>
         <ul class="nav navbar-nav navbar-right">
             <li class="navbar-text">
                 Hello, @User.Identity.Name!
             </li>
             <li>
                 @Html.ActionLink("Sign out", "SignOut", "Account")
             </li>
         </ul>
     </text>
    }
    else
    {
     <ul class="nav navbar-nav navbar-right">
         <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
     </ul>
    }
    ```

## <a name="step-4-display-user-information"></a>步驟 4：顯示使用者資訊
當 Azure AD 向 OpenID Connect 驗證使用者時，會將包含「宣告」或使用者相關判斷提示的 id_token 傳回給應用程式。 您可以藉由執行下列操作，使用這些宣告將應用程式個人化：

1. 開啟 Controllers\HomeController.cs 檔案。 您可以透過 `ClaimsPrincipal.Current` 安全性主體物件來存取控制器中的使用者宣告。

 ```C#
 public ActionResult About()
 {
     ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
     ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
     ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
     ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
     ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

     return View();
 }
 ```

2. 建置並執行應用程式。 如果您尚未在網域為 onmicrosoft.com 的租用戶中建立新使用者，現在便可以這麼做。 方式如下：

  a. 以該使用者身分登入，並注意頂端列上所反映的使用者身分識別。

  b. 登出，然後在您的租用戶中以另一個使用者身分登入。

  c. 如果想再多進行一些操作，您還可以註冊並執行此應用程式的另一個執行個體 (使用其自己的 clientId)，然後監看單一登入的運作情況。

## <a name="next-steps"></a>後續步驟
如需參考資料，請參閱[完整的範例](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip) (不含您的組態值)。

您現在可以繼續前進到更進階的主題。 例如，嘗試[使用 Azure AD 來保護 Web API](active-directory-devquickstarts-webapi-dotnet.md)。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
