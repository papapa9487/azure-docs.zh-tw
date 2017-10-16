---
title: "開始使用 Windows 通用 App Azure Mobile Engagement"
description: "了解如何使用適用於 Windows 通用 App 的 Azure Mobile Engagement 執行分析和傳送推播通知。"
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: 
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 40db7e4dd151ec391c754dc6d4145aeeb8058eca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>開始使用適用於 Windows 通用 App 的 Azure Mobile Engagement
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure Mobile Engagement 了解您的應用程式使用狀況，以及將推播通知傳送給 Windows 通用應用程式的分段使用者。
本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。 您建立一個空白的 Windows 通用應用程式，以使用 Windows 通知服務 (WNS) 來收集基本的應用程式使用資料及接收推播通知。

> [!NOTE]
> Azure Mobile Engagement 服務將於 2018 年 3 月淘汰，目前僅供現有客戶使用。 如需詳細資訊，請參閱 [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/)。

## <a name="prerequisites"></a>必要條件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>設定 Windows 通用應用程式的 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端
本教學課程將說明「基本整合」，這是收集資料及傳送推播通知所需的最低設定。 您可以在 [Mobile Engagement Windows 通用 SDK 整合](mobile-engagement-windows-store-sdk-overview.md)中找到完整的整合文件。

您使用 Visual Studio 建立基本應用程式來示範整合。

### <a name="create-a-windows-universal-app-project"></a>建立一個 Windows 通用應用程式專案
即使下列步驟與舊版 Visual Studio 中的步驟類似，但這些步驟假設使用的是 Visual Studio 2015。

1. 啟動 Visual Studio，並在 [首頁] 畫面上選取 [新增專案]。
2. 在快顯視窗中，選取 [Windows] -> [通用] -> [空白應用程式 (通用 Windows)]。 輸入應用程式的 名稱 和 方案名稱，然後按一下確定。

    ![][1]

現在已建立 Windows 通用應用程式專案，接著您要將 Azure Mobile Engagement SDK 整合至其中。

### <a name="connect-your-app-to-mobile-engagement-backend"></a>將您的應用程式連線至 Mobile Engagement 後端
1. 在您的專案中安裝 [MicrosoftAzure.MobileEngagement] Nuget 封裝。 如果您是以 Windows 和 Windows Phone 兩個平台為目標，您就必須對這些專案執行此操作。 對於 Windows 8.x 和 Windows Phone 8.1，相同的 Nuget 封裝會在每個專案中放置正確的平台專屬二進位檔。
2. 開啟 **Package.appxmanifest** ，並確定已在該處新增下列功能：

        Internet (Client)

    ![][2]
3. 現在複製您稍早為 Mobile Engagement App 複製的連接字串，並將該字串貼在 `Resources\EngagementConfiguration.xml` 檔案中的 `<connectionString>` 和 `</connectionString>` 標記之間：

    ![][3]

    > [!TIP]
    > 如果您的應用程式以 Windows 和 Windows Phone 兩個平台為目標，那麼您仍應該建立兩個 Mobile Engagment 應用程式，讓每個支援的平台各使用一個。 建立兩個應用程式可以確保您建立正確的對象區隔，以及正確地針對各平台傳送目標式通知。

    > [!IMPORTANT]
    > NuGet 目前不會自動複製 Windows 10 UWP 應用程式中的 SDK 資源。 在安裝 Nuget 套件時，您必須遵循顯示的步驟 (readme.txt) 手動進行。  

1. 在 `App.xaml.cs` 檔案中：

    a. 新增 `using` 陳述式：

            using Microsoft.Azure.Engagement;

    b. 新增初始化 Engagement 的方法：

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. 在 **OnLaunched** 方法中初始化 SDK：

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. 在 **OnActivated** 方法中插入下列內容，並新增該方法 (如果它尚未存在)：

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a id="monitor"></a>啟用即時監視
若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。

1. 在 **MainPage.xaml.cs`using` 中，新增下列**  陳述式：

    using Microsoft.Azure.Engagement.Overlay;
2. 將 **MainPage** 的基底類別從 **Page** 變更為 **EngagementPageOverlay**：

        class MainPage : EngagementPageOverlay
3. 在 `MainPage.xaml` 檔案中：

    a. 新增命名空間宣告：

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. 使用 **engagement:EngagementPageOverlay** 來取代 XML 標記名稱中的 **Page**

> [!IMPORTANT]
> 如果您的頁面會覆寫 `OnNavigatedTo` 方法，請務必呼叫 `base.OnNavigatedTo(e)`。 否則不會報告活動 (`EngagementPage` 會在其 `OnNavigatedTo` 方法內呼叫 `StartActivity`)。 這在預設範本含有 `OnNavigatedTo` 方法的 Windows Phone 專案中特別重要。
>
> 對於 **Windows 10 通用應用程式**，請使用[使用 Windows 通用 App Engagement SDK 的進階報告](mobile-engagement-windows-store-advanced-reporting.md)之「建議使用的方法：多載您的 Page 類別」一節的建議方法，而非先前所述方法。

## <a id="monitor"></a>將 App 與即時監視連接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>啟用推播通知與 App 內傳訊
Mobile Engagement 可讓您透過推播通知和應用程式內傳訊，於活動進行時與使用者互動和觸達。 此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。
以下各節將設定您的用程式來接收它們。

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>啟用您的應用程式接收 WNS 推播通知
1. 在 `Package.appxmanifest` 檔案中，於 [應用程式] 索引標籤的 [通知] 下方，將 [支援快顯通知:] 設為 [是]

    ![][5]

### <a name="initialize-the-reach-sdk"></a>初始化 REACH SDK
在 `App.xaml.cs` 中，於代理程式初始化之後，立即在 **InitEngagement** 函式中呼叫 **EngagementReach.Instance.Init(e);**：

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

您現在可以開始傳送快顯通知。 接下來我們要驗證您已正確完成這項基本整合。

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>授與 Mobile Engagement 存取權以傳送通知
1. 在網頁瀏覽器中開啟 [Windows 市集開發人員中心] ，視需要登入和建立帳戶。
2. 按一下右上角的 [儀表板]，然後按一下左面板功能表中的 [建立新的應用程式]。

    ![][9]
3. 建立您的應用程式並保留其名稱。

    ![][10]
4. 建立應用程式之後，從左側功能表瀏覽至 [服務] -> [推播通知]。

    ![][11]
5. 在 [推播通知] 區段中按一下 [Live 服務網站]  連結。

    ![][12]
6. 您會瀏覽至推送認證區段。 請確定您位於 [應用程式設定] 區段中，然後複製您的 [套件 SID] 和 [用戶端密碼]

    ![][13]
7. 瀏覽到 Mobile Engagement 入口網站的 [設定]，然後按一下左側的 [原生推送] 區段。 然後，按一下 [編輯] 按鈕來輸入您的 [套件安全性識別碼 (SID)] 和 [秘密金鑰]，如下所示：

    ![][6]
8. 最後確定您的 Visual Studio 應用程式與在應用程式市集中建立的此應用程式相關聯。 按一下 Visual Studio 中的 [建立應用程式與市集關聯]  。

    ![][7]

## <a id="send"></a>傳送通知至應用程式
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

如果應用程式正在執行，您會看到應用程式內通知。 如果應用程式已關閉，則會看到快顯通知。
如果您看見的是應用程式內通知而不是快顯通知，而且您正在 Visual Studio 中的偵錯模式下執行應用程式，則可嘗試執行工具列中的 [週期事件] -> [暫止]，以確保應用程式會暫止。 如果您在 Visual Studio 中偵錯應用程式時按了 [首頁] 按鈕，則應用程式不一定會暫止，而且您會看見應用程式內通知，它不會顯示為快顯通知。  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Windows 市集開發人員中心]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png
