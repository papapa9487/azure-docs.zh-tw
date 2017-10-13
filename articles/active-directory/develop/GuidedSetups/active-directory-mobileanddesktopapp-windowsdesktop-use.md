---
title: "Azure AD v2 Windows Desktop 快速入門 - 使用 | Microsoft Docs"
description: "Windows Desktop .NET (XAML) 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 826ba0a00b26993d4f37f0a8ce587d7bb77e7eb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 Microsoft Authentication Library (MSAL) 取得 Microsoft 圖形 API 的權杖

本節說明如何使用 MSAL 取得 Microsoft 圖形 API 的權杖。

1.  在 `MainWindow.xaml.cs` 中，將 MSAL 程式庫參考新增到類別：

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
將 <code>MainWindow</code> 類別程式碼取代為：
</li>
</ol>

```csharp
public partial class MainWindow : Window
{
    //Set the API Endpoint to Graph 'me' endpoint
    string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

    //Set the scope for API call to user.read
    string[] _scopes = new string[] { "user.read" };


    public MainWindow()
    {
        InitializeComponent();
    }

    /// <summary>
    /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
    /// </summary>
    private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
    {
        AuthenticationResult authResult = null;

        try
        {
            authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
        }
        catch (MsalUiRequiredException ex)
        {
            // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
            System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
            }
            catch (MsalException msalex)
            {
                ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
            }
        }
        catch (Exception ex)
        {
            ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
            return;
        }

        if (authResult != null)
        {
            ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>詳細資訊
#### <a name="getting-a-user-token-interactive"></a>以互動方式取得使用者權杖
呼叫 `AcquireTokenAsync` 方法時會顯示一個視窗，提示使用者登入。 當使用者第一次需要存取受保護的資源，或取得權杖的無訊息作業失敗 (例如使用者的密碼過期) 時，應用程式通常會要求使用者以互動方式登入。

#### <a name="getting-a-user-token-silently"></a>以無訊息方式取得使用者權杖
`AcquireTokenSilentAsync` 會處理權杖取得和更新作業，不需要與使用者進行任何互動。 `AcquireTokenAsync` 在第一次執行之後，`AcquireTokenSilentAsync` 就會成為用來取得權杖的常用方法，以在後續呼叫中使用那些權杖存取受保護的資源，並且會以無訊息方式進行呼叫來要求或更新權杖。
最後，`AcquireTokenSilentAsync` 將會失敗，例如，使用者已經登出，或已經在其他裝置上變更其密碼。 當 MSAL 偵測到可透過要求執行互動式動作來解決問題時，就會發出一個 `MsalUiRequiredException`。 您的應用程式可以透過兩種方式處理此例外狀況：

1.  立即針對 `AcquireTokenAsync` 進行呼叫，這會促使系統提示使用者登入。 此模式通常用於應用程式中沒有離線內容可供使用者使用的線上應用程式。 此引導式設定所產生的範例使用此模式：您可以在第一次執行範例時看到它執行：因為沒有使用者曾經使用過這個應用程式，`PublicClientApp.Users.FirstOrDefault()` 將包含 Null 值，而且會擲回一個 `MsalUiRequiredException` 例外狀況。 然後範例中的程式碼會透過呼叫 `AcquireTokenAsync` 來處理例外狀況，進而提示使用者登入。

2.  應用程式也可以提供視覺指示，讓使用者知道需要透過互動方式登入，使用者就能選取正確的登入時機，或應用程式可以在之後重試 `AcquireTokenSilentAsync`。 此方式通常用於使用者可以使用應用程式的其他功能，不需要因此中斷作業的情況，例如，應用程式中有離線內容可供使用者使用。 在此案例中，使用者可以決定他們要登入以存取受保護資源，或重新整理過時資訊的時機，或者您的應用程式可以決定在網路暫時中斷之後恢復連線時重試 `AcquireTokenSilentAsync`。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>使用您剛剛取得的權杖呼叫 Microsoft 圖形 API

1. 將下面的新方法新增到您的 `MainWindow.xaml.cs`。 此方法是用來使用授權標頭針對圖形 API 提出 `GET` 要求：

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```
<!--start-collapse-->
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫的詳細資訊

在這個範例應用程式中，`GetHttpContentWithToken` 方法是用來針對受保護資源提出 HTTP `GET` 要求，那些受保護資源需要權杖才能存取，然後再將內容傳回給使用者。 此方法會在「HTTP 授權標頭」中加入取得的權杖。 對於此範例，資源為 Microsoft 圖形 API *me* 端點，它會顯示使用者的設定檔資訊。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>新增方法來將使用者登出

1. 將下列方法新增到您的 `MainWindow.xaml.cs` 來將使用者登出：

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>關於登出的詳細資訊

`SignOutButton_Click` 會將使用者從 MSAL 使用者快取中移除，這能夠有效告知 MSAL 忘記目前的使用者，只有將此作業設為互動式作業，未來取得權杖的要求才能成功。
雖然此範例中的應用程式支援單一使用者，MSAL 也支援可同時登入多個帳戶的案例，例如，使用者擁有多個帳戶的電子郵件應用程式。
<!--end-collapse-->

## <a name="display-basic-token-information"></a>顯示基本權杖資訊

1. 將下列方法新增到您的 `MainWindow.xaml.cs`，以顯示和權杖有關的基本資訊：

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>詳細資訊

透過 *OpenID Connect* 取得的權杖也包含一小部分與使用者有關的資訊。 `DisplayBasicTokenInfo` 會顯示權杖中包含的基本資訊：例如，使用者的顯示名稱和識別碼，以及權杖到期日期和代表存取權杖本身的字串。 系統會顯示此資訊供您查看。 您可以按下 [呼叫 Microsoft 圖形 API] 按鈕多次，以了解相同的權杖如何重複用於多個後續要求。 您也可以在 MSAL 決定應該要更新權杖時，看到延長的到期日期。
<!--end-collapse-->

