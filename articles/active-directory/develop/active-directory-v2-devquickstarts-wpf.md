---
title: "Azure Active Directory v2.0 .NET 原生應用程式 | Microsoft Docs"
description: "如何建置可使用個人 Microsoft 帳戶及工作或學校帳戶登入使用者的 .NET 原生應用程式。"
services: active-directory
documentationcenter: 
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7389f55ee6fef9548abb0ca4ac1bbd0399868d47
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>將登入新增至 Windows 桌面應用程式
v2.0 端點可讓您快速地將驗證加入您的桌面應用程式，同時支援個人 Microsoft 帳戶以及工作或學校帳戶。  它也可讓您的應用程式安全地與後端 Web API，以及 [Microsoft Graph](https://graph.microsoft.io) 和幾個 [Office 365 統一 API](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) 進行通訊。

> [!NOTE]
> v2.0 端點並非支援每個 Azure Active Directory (AD) 案例和功能。  如果要判斷是否應該使用 v2.0 端點，請閱讀 [v2.0 限制](active-directory-v2-limitations.md)。
> 
> 

對於 [在裝置上執行的 .NET 原生應用程式](active-directory-v2-flows.md#mobile-and-native-apps)，Azure AD 提供 Microsoft Identity Authentication Library，或稱 MSAL。  MSAL 存在的唯一目的是為了讓您的應用程式輕鬆取得權杖以呼叫 Web 服務。  為了示範這有多麼簡單，我們將在此建置一個執行下列動作的 .NET WPF 待辦事項清單應用程式：

* 使用 [OAuth 2.0 驗證通訊協定](active-directory-v2-protocols.md)登入使用者並取得存取權杖。
* 安全地呼叫受 OAuth 2.0 保護的後端待辦事項清單 Web 服務。
* 將使用者登出。

## <a name="download-sample-code"></a>下載範例程式碼
本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet)。  若要遵循執行，您可以 [用 .zip 格式下載應用程式的基本架構](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) ，或複製基本架構：

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

本教學課程最後也會提供完整的應用程式。

## <a name="register-an-app"></a>註冊應用程式
在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) 建立新的應用程式，或遵循下列[詳細步驟](active-directory-v2-app-registration.md)。  請確定：

* 將指派給您應用程式的 **應用程式識別碼** 複製起來，您很快會需要用到這些識別碼。
* 為您的應用程式新增 **行動** 平台。

## <a name="install--configure-msal"></a>安裝和設定 MSAL
您現在有了已向 Microsoft 註冊的應用程式，可以安裝 MSAL 並撰寫與您身分識別相關的程式碼。  為了讓 MSAL 能與 v2.0 端點通訊，您需要提供一些應用程式註冊的相關資訊。

* 首先，使用 Package Manager Console 將 MSAL 新增到 TodoListClient 專案中。

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* 在 TodoListClient 專案中，開啟 `app.config`。  取代 `<appSettings>` 區段中的元素值，以反映您在應用程式註冊入口網站中輸入的值。  每當使用 MSAL 時，您的程式碼便會參考這些值。
  
  * `ida:ClientId` 是您從入口網站複製的應用程式的 **應用程式 ID** 。
* 在 TodoList-Service 專案中，開啟專案根目錄中的 `web.config` 。  
  
  * 以來自入口網站的相同**應用程式識別碼**取代 `ida:Audience` 值。

## <a name="use-msal-to-get-tokens"></a>使用 MSAL 取得權杖
MSAL 的基本原則是，每當應用程式需要存取權杖時，您只需呼叫 `app.AcquireToken(...)`，MSAL 就會執行其餘工作。  

* 在 `TodoListClient` 專案中，開啟 `MainWindow.xaml.cs` 並找出 `OnInitialized(...)` 方法。  第一步是初始化應用程式的 `PublicClientApplication` - 代表原生應用程式的 MSAL 主要類別。  您在這裡將 ADAL 與 Azure AD 通訊所需的座標傳給 MSAL，並告訴它如何快取權杖。

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* 當應用程式啟動時，我們希望檢查並查看使用者是否已登入應用程式。  不過，我們不想這個時候叫用登入 UI，而是讓使用者按一下 [登入] 才執行此作業。  另在 `OnInitialized(...)` 方法中：

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* 如果使用者未登入而按下 [登入] 按鈕，我們希望叫用登入 UI 並讓使用者輸入其認證。  實作登入按鈕處理常式：

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

* 如果使用者成功登入，MSAL 會為您接收和快取權杖，您可以放心大膽地繼續呼叫 `GetTodoList()` 方法。  要取得使用者工作的剩餘步驟，是實作 `GetTodoList()` 方法。

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>執行
恭喜！ 您現在擁有一個運作正常的 .NET WPF 應用程式，可使用 OAuth 2.0 驗證使用者並安全地呼叫 Web API。  執行您的兩個專案，並以個人的 Microsoft 或工作/學校的帳戶登入。  將工作新增到使用者的待辦事項清單。  登出並以其他使用者再次登入，查看其他使用者的待辦事項清單。  關閉並重新執行應用程式。  注意使用者的工作階段是否維持不變，這是因為應用程式會快取本機檔案中的權杖。

MSAL 可使用個人和工作帳戶，輕鬆地將通用的身分識別功能納入您的應用程式。  它會為您處理一切麻煩的事，包括快取管理、OAuth 通訊協定支援、向使用者顯示登入 UI、重新整理過期權杖等等。  您唯一需要知道的就是單一 API 呼叫， `app.AcquireTokenAsync(...)`。

如需參考， [此處以 .zip 格式提供](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)完整範例 (不含您的組態值)，您也可以從 GitHub 予以複製：

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>後續步驟
您現在可以進入更進階的主題。  您可以嘗試：

* [透過 v2.0 端點保護 TodoListService Web API](active-directory-v2-devquickstarts-dotnet-api.md)

如需其他資源，請參閱：  

* [v2.0 開發人員指南 >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow "msal" 標籤 >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新
我們鼓勵您造訪 [此頁面](https://technet.microsoft.com/security/dd252948) 並訂閱資訊安全摘要報告警示，以在安全性事件發生時收到通知。

