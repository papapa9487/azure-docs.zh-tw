---
title: "開始使用 Azure AD Windows 市集 | Microsoft Docs"
description: "建置與 Azure AD 整合來進行登入的 Windows 市集應用程式，並使用 OAuth 呼叫受 Azure AD 保護的 API。"
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 09/16/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6b5189dc06d7f8b0ed4426944948b904feba847e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-with-windows-store-apps"></a>將 Azure AD 與 Windows 市集應用程式整合
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> Visual Studio 2017 不支援 Windows Store 8.1 和舊版專案。  如需詳細資訊，請參閱 [Visual Studio 2017 平台目標及相容性](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs)。

如果您要開發 Windows 市集應用程式，Azure Active Directory (Azure AD) 可讓您簡單又直接地以 Active Directory 帳戶驗證您的使用者。 透過與 Azure AD 整合，應用程式便可安全地使用任何受 Azure AD 保護的 Web API，例如 Office 365 API 或 Azure API。

針對需要存取受保護資源的 Windows 市集傳統型應用程式，Azure AD 提供了「Active Directory 驗證程式庫」(ADAL)。 ADAL 的唯一目的是要讓應用程式能夠容易取得存取權杖。 為了示範有多麼容易，本文將說明如何建置能夠執行下列操作的 DirectorySearcher Windows 市集應用程式︰

* 使用 [OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)來取得呼叫 Azure AD Graph API 的存取權杖。
* 搜尋目錄以尋找具有指定使用者主體名稱 (UPN) 的使用者。
* 將使用者登出。

## <a name="before-you-get-started"></a>開始之前
* 下載[基本架構專案](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip)或下載[完整的範例](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip)。 每一個下載項目都是 Visual Studio 2015 解決方案。
* 您還需要一個可供建立使用者及註冊應用程式的 Azure AD 租用戶。 如果您還沒有租用戶， [了解如何取得租用戶](active-directory-howto-tenant.md)。

當您準備就緒時，請依照接下來三個小節的程序操作。

## <a name="step-1-register-the-directorysearcher-app"></a>步驟 1：註冊 DirectorySearcher 應用程式
若要讓應用程式能夠取得權杖，您必須先在 Azure AD 租用戶中註冊該應用程式，然後授與它存取 Azure AD Graph API 的權限。 方式如下：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頂端列中，按一下您的帳戶。 然後，在 [目錄] 清單下，選取您要註冊應用程式的 Active Directory 租用戶。
3. 按一下左側窗格中的 [更多服務]，然後選取 [Azure Active Directory]。
4. 按一下 [應用程式註冊]，然後選取 [新增]。
5. 依照提示操作來建立「原生用戶端應用程式」。
  * [名稱] 可向使用者描述該應用程式。
  * [重新導向 URI] 是配置與字串的組合，Azure AD 會用它來傳回權杖回應。 暫時先輸入一個預留位置值 (例如 **http://DirectorySearcher**)。 您稍後將會取代此值。
6. 完成註冊之後，Azure AD 會為應用程式指派一個唯一的應用程式識別碼。 複製 [應用程式] 索引標籤上的值，因為稍後將會需要它。
7. 在 [設定] 頁面上，選取 [必要權限]，然後選取 [新增]。
8. 針對 **Azure Active Directory** 應用程式，選取 [Microsoft Graph] 作為 API。
9. 在 [委派的權限] 下，新增 [以登入使用者的身分存取目錄] 權限。 這麼做可讓應用程式查詢使用者的 Graph API。

## <a name="step-2-install-and-configure-adal"></a>步驟 2：安裝及設定 ADAL
既然您在 Azure AD 中已有應用程式，您現在便可安裝 ADAL，並撰寫身分識別相關程式碼。 若要讓 ADAL 與 Azure AD 進行通訊，請提供它一些應用程式註冊相關資訊。

1. 藉由使用「套件管理器主控台」，將 ADAL 新增到 DirectorySearcher 專案中。

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

2. 在 DirectorySearcher 專案中，開啟 MainPage.xaml.cs。
3. 使用您在 Azure 入口網站中輸入的值來取代 [組態值] 區域中的值。 每當您的程式碼使用 ADAL 時，都會參考這些值。
  * *tenant* 是您 Azure AD 租用戶的網域 (例如 contoso.onmicrosoft.com)。
  * *clientId* 是您從入口網站複製的應用程式用戶端識別碼。
4. 您現在必須找出 Windows 市集應用程式的回呼 URI。 在 `MainPage` 方法的這一行上設定中斷點：
    ```
    redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
    ```
5. 建置方案，確認已還原所有封裝參考。 如果遺失任何封裝，請開啟「NuGet 封裝管理員」，然後將它們還原。
6. 執行應用程式，然後在到達中斷點時複製 `redirectUri` 的值。 該值應該看起來如下：

    ```
    ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
    ```

7. 回到 Azure 入口網站中應用程式的 [設定] 索引標籤上，新增具有上述值的 **RedirectUri**。  

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>步驟 3：使用 ADAL 從 Azure AD 取得權杖
ADAL 背後的基本原則就是每當應用程式需要存取權杖時，只要呼叫 `authContext.AcquireToken(…)`，ADAL 就會執行其餘動作。  

1. 將應用程式的 `AuthenticationContext` (亦即 ADAL 的主要類別) 初始化。 此動作會將 ADAL 與 Azure AD 通訊所需的座標傳遞給 ADAL，並告訴它如何快取權杖。

    ```C#
    public MainPage()
    {
        ...

        authContext = new AuthenticationContext(authority);
    }
    ```

2. 找出 `Search(...)` 方法，這是在使用者按一下應用程式 UI 上的 [搜尋] 按鈕時所叫用的方法。 這個方法會對 Azure AD Graph API 發出 get 要求，以查詢是否有 UPN 開頭為指定搜尋詞彙的使用者。 若要查詢 Graph API，請將存取權杖包含在該要求的 **Authorization** 標頭中。 這就是 ADAL 的切入點。

    ```C#
    private async void Search(object sender, RoutedEventArgs e)
    {
        ...
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "authentication_canceled")
            {
                ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
            }
            return;
        }
        ...
    }
    ```
    當應用程式透過呼叫 `AcquireTokenAsync(...)` 來要求權杖時，ADAL 會嘗試在不向使用者要求認證的情況下傳回權杖。 如果 ADAL 判斷使用者必須登入才能取得權杖，就會顯示登入對話方塊、收集使用者的認證，然後在驗證成功後傳回權杖。 如果 ADAL 因任何原因而無法傳回權杖，*AuthenticationResult* 狀態就會是錯誤。
3. 現在可以開始使用您剛才取得的存取權杖。 此外，請在 `Search(...)` 方法中，將該權杖附加至 Graph API get 要求的 **Authorization** 標頭：

    ```C#
    // Add the access token to the Authorization header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

    ```
4. 您可以使用 `AuthenticationResult` 物件以在應用程式中顯示使用者的相關資訊，例如使用者的識別碼：

    ```C#
    // Update the page UI to represent the signed-in user
    ActiveUser.Text = result.UserInfo.DisplayableId;
    ```
5. 您也可以使用 ADAL 將使用者登出應用程式。 當使用者按一下 [登出] 按鈕時，請確保下次呼叫 `AcquireTokenAsync(...)` 時會顯示登入檢視。 使用 ADAL 時，此動作就像清除權杖快取一樣簡單：

    ```C#
    private void SignOut()
    {
        // Clear session state from the token cache.
        authContext.TokenCache.Clear();

        ...
    }
    ```

## <a name="whats-next"></a>後續步驟
您現在已擁有一個能夠運作的 Windows 市集應用程式，它可以驗證使用者、使用 OAuth 2.0 來安全地呼叫 Web API，以及取得使用者的相關基本資訊。

如果您尚未在租用戶中填入使用者，現在便可以這麼做。
1. 執行 DirectorySearcher 應用程式，然後使用其中一個使用者來登入。
2. 根據 UPN 搜尋其他使用者。
3. 關閉應用程式，然後重新執行它。 請注意，使用者工作階段會維持不變。
4. 透過按一下滑鼠右鍵以顯示底部列來登出，然後以其他使用者身分重新登入。

ADAL 可讓您輕鬆地將所有這些常見的身分識別功能納入應用程式中。 它會為您處理所有麻煩的工作，包括快取管理、OAuth 通訊協定支援、向使用者顯示登入 UI，以及重新整理過期權杖。 您只需要知道單一 API 呼叫 `authContext.AcquireToken*(…)`。

如需參考資料，請下載[完整的範例](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip) (不含您的組態值)。

您現在可以繼續其他身分識別案例。 例如，嘗試[使用 Azure AD 來保護 .NET Web API](active-directory-devquickstarts-webapi-dotnet.md)。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
