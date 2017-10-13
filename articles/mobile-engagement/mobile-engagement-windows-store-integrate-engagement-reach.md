---
title: "Windows 通用 app Reach SDK 整合"
description: "如何將 Azure Mobile Engagement Reach 與 Windows 通用 app 整合"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9311e998e67d8d0d56da68fc9460df32ce7ce5a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Windows 通用 app Reach SDK 整合
依照本指南進行之前，您必須先遵循 [Windows 通用 Engagement SDK 整合](mobile-engagement-windows-store-integrate-engagement.md) 文件中所述的整合程序。

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>將 Engagement Reach SDK 內嵌至您的 Windows 通用專案
您不需要新增任何項目。 `EngagementReach` 的參考和資源已在您的專案中。

> [!TIP]
> 您可以自定專案的 `Resources` 資料夾中的影像，尤其是品牌圖示 (預設為 Engagement 的圖示)。 在跨平台 app 上，您也可以移動共用專案上的 `Resources` 資料夾，以便在應用程式間共用其內容；但因為 `Resources\EngagementConfiguration.xml` 檔案和平台相依，所以您必須將它保留在預設位置。
> 
> 

## <a name="enable-the-windows-notification-service"></a>啟用 Windows 通知服務
### <a name="windows-8x-and-windows-phone-81-only"></a>僅 Windows 8.x 和 Windows Phone 8.1
若要在`Application UI` 上的 `Package.appxmanifest` 檔案中使用 **Windows 通知服務** (簡稱 WNS)，請在左邊 bot 方塊中的 `All Image Assets` 上按一下。 請在 `Notifications` 中方塊的右邊，將 `toast capable` 從 `(not set)` 變更為 `(Yes)`。

### <a name="all-platforms"></a>所有平台
您必須將您的應用程式與您的 Microsoft 帳戶以及 Engagement 平台同步。 因此您需要建立一個帳戶或登入 [Windows 開發人員中心](https://dev.windows.com)。 然後，建立新的應用程式，並且尋找 SID 和秘密金鑰。 在 Engagement 前端中，繼續應用程式的 `native push` 設定，並貼上您的認證。 接下來，在您的專案上按一下滑鼠右鍵，依序選取 `store` 和 `Associate App with the Store...`。 您只需要在同步處理之前選取您建立的應用程式。

## <a name="initialize-the-engagement-reach-sdk"></a>初始化 Engagement Reach SDK
修改 `App.xaml.cs`：

* 在 `InitEngagement` 方法中，將 `EngagementReach.Instance.Init` 插入 `EngagementAgent.Instance.Init` 後方：
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  `EngagementReach.Instance.Init` 會在專用的執行緒中執行。 您不必自行進行此作業。

> [!NOTE]
> 如果您在應用程式的其他地方使用推播通知，則您必須與 Engagement Reach [共用推播通道](#push-channel-sharing) 。
> 
> 

## <a name="integration"></a>整合
Engagement 提供兩種方式在應用程式中加入 Reach 應用程式內橫幅，以及宣告和輪詢的插入式檢視：重疊整合和 Web 檢視手動整合。 您不應該在相同頁面上結合這兩種方法。

兩種整合之間的選擇可以如此歸納︰

* 如果您的頁面已繼承自代理程式 `EngagementPage`，您可以選擇重疊整合，只需在頁面中將 `EngagementPage` 取代為 `EngagementPageOverlay`，將 `xmlns:engagement="using:Microsoft.Azure.Engagement"` 取代為 `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"`。
* 如果您想要精確地將 Reach UI 放在頁面中，或是不想在頁面加入另一個繼承層級，可以選擇 Web 檢視手動整合。 

### <a name="overlay-integration"></a>重疊整合
Engagement 重疊會以動態方式加入用以在頁面中顯示觸達活動的 UI 元素。 如果重疊不符合您的版面配置，您應該改為考慮 Web 檢視手動整合。

在您的 .xaml 檔案中，將 `EngagementPage` 參考變更為 `EngagementPageOverlay`

* 新增至命名空間宣告：
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* 以 `engagement:EngagementPageOverlay` 取代 `engagement:EngagementPage`：

**有 EngagementPage：**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**有 EngagementPageOverlay：**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

然後在您的 .cs 檔案中以 `EngagementPageOverlay` 而不是 `EngagementPage` 標記頁面，並匯入 `Microsoft.Azure.Engagement.Overlay`。

            using Microsoft.Azure.Engagement.Overlay;

* 以 `EngagementPageOverlay` 取代 `EngagementPage`：

**有 EngagementPage：**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**有 EngagementPageOverlay：**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Engagement 重疊會在版面配置組成的頁面頂端加入 `Grid` 元素和兩個 `WebView` 元素，一個用於橫幅，另一個則用於插入式檢視。

您可以直接在 `EngagementPageOverlay.cs` 檔案中自訂重疊元素。

### <a name="web-views-manual-integration"></a>Web 檢視手動整合
Reach 會搜尋頁面中負責顯示橫幅和插入式檢視的兩個 `WebView` 元素。 您唯一要做的是將這兩個 `WebView` 元素加入頁面中的某處，範例如下︰

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


在此範例中， `WebView` 元素會伸展以符合其容器，容器會在螢幕旋轉或視窗大小變更時自動重新調整大小。

> [!WARNING]
> 請務必為 `WebView` 元素的 `engagement_notification_content` 和 `engagement_announcement_content` 保留相同的命名。 Reach 依其名稱來識別。 
> 
> 

## <a name="handle-datapush-optional"></a>處理資料推送 (選擇性)
如果您希望您的應用程式接收 Reach 資料推送，您必須實作 EngagementReach 類別的兩個事件：

在 App.xaml.cs 的 App() 建構函式中加入︰

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };

            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

您可以看到每個方法的回呼會傳回布林值。 Engagement 會在發送資料推送之後傳送回饋到它的後端。 如果回呼傳回 false，會傳送 `exit` 回饋。 否則將會是 `action`。 如果沒有設定事件的回呼，就會傳送 `drop` 回饋到 Engagement。

> [!WARNING]
> Engagement 無法接收單一資料推送的多個回饋。 如果計畫在單一事件上設定多個處理常式，請留意回饋將與最後一個傳送的對應。 在此情況下，我們建議一律傳回相同的值，避免在前端有令人困惑的回饋。
> 
> 

## <a name="customize-ui-optional"></a>自訂 UI (選擇性)
### <a name="first-step"></a>第一步
我們讓您可以自訂 Reach UI。

若要這樣做，您必須建立 `EngagementReachHandler` 類別的子類別。

**範例程式碼：**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

然後，使用 `App()` 方法中 `App.xaml.cs` 類別的自訂物件，設定 `EngagementReach.Instance.Handler` 欄位的內容。

**範例程式碼：**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> 根據預設，Engagement 會使用自己的 `EngagementReachHandler` 實作。
> 您不需要自己建立，但如果有需要，您不需要覆寫每個方法。 預設行為是選取 Engagement 基底物件。
> 
> 

### <a name="web-view"></a>Web 檢視
根據預設，Reach 會使用 DLL 的內嵌資源來顯示通知和頁面。

若要提供完全自訂的可能性，我們只會使用網頁檢視。 如果您想要自訂版面配置，請直接覆寫資源檔案 `EngagementAnnouncement.html` 和 `EngagementNotification.html`。 Engagement 需要 `<body></body>` 內的所有程式碼正確執行。 但您可以在 `engagement_webview_area`之外新增標記。

不過，您可以決定使用自己的資源。

您可以在您的子類別中覆寫 `EngagementReachHandler` 方法，以告訴 Engagement 要使用您的版面配置，但是請小心內嵌的 Engagement 機制：

**範例程式碼：**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


根據預設，AnnouncementHTML 是 `ms-appx-web:///Resources/EngagementAnnouncement.html`。 它代表設計推播訊息之內容的 html 檔案 (文字宣告、Web 宣告和投票宣告)。 AnnouncementName 是 `engagement_announcement_content`。 它是您 xaml 頁面中 Web 檢視設計的名稱。

NotfificationHTML 是 `ms-appx-web:///Resources/EngagementNotification.html`。 它代表設計推播訊息通知的 html 檔案。 NotfificationName 是 `engagement_notification_content`。 它是您 xaml 頁面中 Web 檢視設計的名稱。

### <a name="customization"></a>自訂
如果您保留 Engagement 物件，便可以自訂通知和宣告的 Web 檢視。 請注意，Web 檢視會說明三次 (第一次在 xaml 中、第二次在 "setwebview()" 內的 .cs 檔案中，而第三次則在 html 檔案中)。

* 在 xaml 中是描述目前的圖形版面配置 Web 檢視元件。
* 您可以在 .cs 檔案中定義設定兩種 Web 檢視 (通知、宣告) 的維度之 "setwebview()"。 這在應用程式調整大小時非常有效。
* 我們在 Engagement html 檔案中描述 Web 檢視的內容、設計，以及各元素之間的位置。

### <a name="launch-message"></a>啟動訊息
當使用者按一下系統通知 (快顯通知) 時，Engagement 會啟動該應用程式、載入推播訊息的內容，並顯示對應之活動的頁面。

啟動應用程式與頁面顯示之間會有延遲 (取決於您的網路速度)。

若要向使用者指示正在載入項目，您應該提供視覺化的資訊，例如進度列或進度列指示器。 Engagement 無法自行處理這些，但有提供您幾個處理常式。

若要實作回呼，在 App.xaml.cs 中的 "Public app {}" 新增：

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

您可以在 `App.xaml.cs` 檔案的 "Public App(){}" 方法中設定回呼，最好設定在 `EngagementReach.Instance.Init()` 呼叫之前。

> [!TIP]
> 每個處理常式都是由 UI 執行緒呼叫。 在使用 MessageBox 或 UI 相關的項目時您不必擔心。
> 
> 

## <a id="push-channel-sharing"></a> 推播通道共用
如果您在應用程式中將推播通知用於其他目的，則您必須使用 Engagement SDK 的推播通道共用功能。 這是為了避免遺失推播。

* 您可以對 Engagement Reach 初始化提供自己的推播通道。 SDK 將會使用它而不是要求新的。

使用您在 `App.xaml.cs` 檔案之 `InitEngagement` 方法中的推播通道更新 Engagement Reach 初始化：

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* 或者，如果您只是想在 Reach 初始化之後使用推播通道，那麼您可以在 Engagement Reach 上設定回呼，以在 SDK 建立推播通到之後立即取得它。

在 Reach 初始化「之後」  的任何地方設定回呼：

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>自訂配置秘訣
我們提供使用自訂配置。 您可以從 Engagement 前端傳送您 Engagement 應用程式使用的不同類型之 URI。 如果裝置上沒有安裝預設的應用程式，預設配置 (例如，由 Windows 管理 `http, ftp, ...` ) 便會出現視窗提示。 您也可以在您的應用程式中使用自訂配置。

若要在您的應用程式中設定自訂配置，最簡單的方式就是開啟 `Package.appxmanifest`，然後進入 `Declarations` 面板。 在 [可用宣告] 捲動方塊中選取 `Protocol` 並將它新增。 以您想要的新通訊協定名稱來編輯 `Name` 欄位。

現在若要使用此通訊協定，請使用 `OnActivated` 方法編輯您的 `App.xaml.cs`，並請不要忘記也在此初始化 Engagement：

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

