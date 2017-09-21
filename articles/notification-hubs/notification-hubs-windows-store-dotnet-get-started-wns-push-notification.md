---
title: "開始使用適用於通用 Windows 平台應用程式的 Azure 通知中樞 | Microsoft Docs"
description: "在本教學課程中，您將了解如何使用 Azure 通知中樞，將通知推播至 Windows 通用平台應用程式。"
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: erikre
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: e18a810bcdbd97c79418f53c647df8723ecb6076
ms.contentlocale: zh-tw
ms.lasthandoff: 09/08/2017

---
# <a name="get-started-with-notification-hubs-for-universal-windows-platform-apps"></a>開始使用適用於通用 Windows 平台應用程式的通知中樞

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概觀
本文說明如何使用 Azure 通知中樞將推播通知傳送至 Windows 通用平台 (UWP) 應用程式。

在本文中，您將使用 Windows 推播通知服務 (WNS)，建立可接收推播通知的空白 Windows 市集應用程式。 完成時，您即可使用通知中樞，將推播通知廣播到執行您的應用程式的所有裝置。

## <a name="before-you-begin"></a>開始之前
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

您可以在 [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal) 上找到本教學課程的完整程式碼。

## <a name="prerequisites"></a>必要條件
本教學課程需要下列各項：

* [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) 或更新版本
* [已安裝 UWP 應用程式開發工具](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
* 作用中的 Azure 帳戶   
    如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F)。
* 有效的 Windows 市集帳戶

完成本教學課程是參加 UWP 應用程式所有其他通知中樞教學課程的先決條件。

## <a name="register-your-app-for-the-windows-store"></a>向 Windows 市集註冊應用程式
若要傳送推播通知給 Windows UWP 應用程式，將您的應用程式與 Windows 市集產生關聯。 接著設定您的通知中樞，以便與 WNS 進行整合。

1. 如果您尚未註冊您的應用程式，請瀏覽至 [Windows 開發人員中心](https://dev.windows.com/overview)，並使用 Microsoft 帳戶登入，然後選取 [建立新的應用程式]。

2. 輸入您的應用程式名稱，然後選取 [保留應用程式名稱]。 這麼做會為您的應用程式建立新的 Windows 市集註冊。

3. 在 Visual Studio 中，使用 UWP [空白應用程式] 範本來建立新的 Visual C# 市集應用程式專案，然後選取 [確定]。

4. 接受目標和最小平台版本的預設值。

5. 在 [方案總管] 中，以滑鼠右鍵按一下 Windows 市集應用程式專案，然後依序選取 [市集] 和 [將應用程式與市集建立關聯]。  
    隨即顯示 [將您的應用程式與 Windows 市集建立關聯]  精靈。

6. 在此精靈中，使用您的 Microsoft 帳戶登入。

7. 選取您在步驟 2 中註冊的應用程式，選取 [下一步]，然後選取 [關聯]。 這麼做會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。

8. 回到新應用程式的 [Windows 開發人員中心](http://dev.windows.com/overview)頁面，依序選取 [服務] 和 [推播通知]，然後選取 [WNS/MPNS]。

9. 選取 [新增通知]。

10. 選取 [空白 (快顯)] 範本，然後選取 [確定]。

11. 輸入通知 [名稱] 和視覺化 [內容] 訊息，然後選取 [儲存為草稿]。

12. 移至[應用程式註冊入口網站](http://apps.dev.microsoft.com)並登入。

13. 選取您的應用程式名稱。 在 [Windows 市集] 平台設定中，記下 [應用程式祕密] 和 [套件安全性識別碼 (SID)]。

    >[!WARNING]
    >應用程式密碼與封裝 SID 是重要的安全性認證。 請勿與任何人共用這些值，或與您的應用程式一起散發密碼。

## <a name="configure-your-notification-hub"></a>設定您的通知中樞
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="5">
<li><p>選取 [通知服務]<b></b>[Windows (WNS)]> <b></b>，然後在 [安全性金鑰]<b></b> 方塊中輸入應用程式祕密密碼。 在 [套件 SID]<b></b> 方塊中，輸入您在上一節中從 WNS 取得的值，然後選取 [儲存]<b></b>。</p>
</li>
</ol>

![套件 SID 和安全性金鑰方塊](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

您的通知中樞現在已設定成使用 WNS。 您已擁有可用來註冊應用程式和傳送通知的連接字串。

## <a name="connect-your-app-to-the-notification-hub"></a>將您的應用程式連接到通知中樞
1. 在 Visual Studio 中，以滑鼠右鍵按一下方案，然後選取 [管理 NuGet 套件] 。  
    [管理 NuGet 套件] 視窗隨即開啟。

2. 在搜尋方塊中，輸入 **WindowsAzure.Messaging.Managed**，選取 [安裝] 並接受使用條款。
   
    ![[管理 NuGet 套件] 視窗][20]
   
    此動作會使用 [WindowsAzure.Messaging.Managed NuGet 套件](http://nuget.org/packages/WindowsAzure.Messaging)來下載、安裝並新增適用於 Windows 的 Azure 傳訊程式庫參考。

3. 開啟 App.xaml.cs 專案檔案，並新增下列 `using` 陳述式： 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. 而且在 App.xaml.cs 中，將下列 **InitNotificationsAsync** 方法定義新增至 [應用程式] 類別：
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    此程式碼會從 WNS 中擷取應用程式的通道 URI，然後向您的通知中樞註冊該通道 URI。
   
    >[!NOTE]
    >* 使用 Azure 入口網站中顯示的通知中樞名稱，取代 **hub name** 預留位置。 
    >* 此外，使用您在上一節中從通知中樞的 [存取原則] 頁面取得的 **DefaultListenSharedAccessSignature** 連接字串，取代連接字串預留位置。
   > 
   > 
5. 在 App.xaml.cs 的 **OnLaunched** 事件處理常式頂端，將下列呼叫新增至新 **InitNotificationsAsync** 方法：
   
        InitNotificationsAsync();
   
    此動作會保證每次啟動應用程式時，通道 URI 便會在通知中樞中註冊。

6. 若要執行應用程式，請選取 **F5** 鍵。 包含註冊金鑰的對話方塊隨即顯示。

您的應用程式現在已能夠接收快顯通知。

## <a name="send-notifications"></a>傳送通知
在 [Azure 入口網站](https://portal.azure.com/)中傳送通知，即可在應用程式中快速測試通知的接收。 使用通知中樞上的 [測試傳送] 按鈕，如以下影像所示：

![[傳送測試] 窗格](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

使用相容的程式庫，推播通知通常會以後端服務傳送，例如行動服務或 ASP.NET。 如果程式庫不適用於您的後端，也可以直接使用 REST API 來傳送通知訊息。 

本教學課程只會在主控台應用程式 (而非後端服務) 中使用通知中樞適用的 .NET SDK 傳送通知，示範如何測試您的用戶端應用程式。 我們建議以[使用通知中樞將通知推播給使用者]教學課程作為下一個步驟，以便從 ASP.NET 後端傳送通知。 不過，您可以使用下列方法來傳送通知：

* **REST 介面**：您可以使用 [REST 介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)，在任何後端平台上支援通知。

* **Microsoft Azure 通知中樞 .NET SDK**︰在適用於 Visual Studio 的 NuGet 套件管理員中，執行 [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。

* **Node.js**：請參閱[如何從 Node.js 使用通知中樞](notification-hubs-nodejs-push-notification-tutorial.md)。
* **Azure Mobile Apps**：如需如何從已與通知中樞整合的 Azure 行動裝置應用程式傳送通知的範例，請參閱[新增 Mobile Apps 的推播通知](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md)。

* **Java 或 PHP**：如需如何使用 REST API 傳送通知的範例，請參閱：
    * [Java](notification-hubs-java-push-notification-tutorial.md)
    * [PHP](notification-hubs-php-push-notification-tutorial.md)

## <a name="optional-send-notifications-from-a-console-app"></a>(選擇性) 從主控台應用程式傳送通知
若要使用 .NET 主控台應用程式來傳送通知，請執行下列作業： 

1. 以滑鼠右鍵按一下方案，選取 [新增] > [新增專案]，在 [Visual C#] 底下選取 [Windows] 和 [主控台應用程式]，然後選取 [確定]。
   
    新的 Visual C# 主控台應用程式會新增到方案。 您也可以在個別方案中新增此專案。

2. 在 Visual Studio 中，依序選取 [工具]、[NuGet 套件管理員] 和 [套件管理員主控台]。
   
    套件管理員主控台隨即在 Visual Studio 中開啟。

3. 在 [套件管理員主控台] 視窗中，將 [預設專案]  設為新的主控台應用程式專案，然後在主控台視窗中執行下列命令：
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    此動作會使用 [Microsoft.Azure.Notification Hubs NuGet 套件](http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)來新增對 Azure 通知中樞 SDK 的參考。
   
    ![「預設專案」名稱](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. 開啟 Program.cs 檔案，然後新增下列 `using` 陳述式：
   
        using Microsoft.Azure.NotificationHubs;

5. 在 **Program** 類別中，新增下列方法：
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }
   
    >[!NOTE]
    >* 使用 Azure 入口網站中顯示的通知中樞名稱，取代 **hub name** 預留位置。 
    >* 使用您在「設定通知中樞」一節中從通知中樞的 [存取原則] 頁面取得的 **DefaultFullSharedAccessSignature** 連接字串，取代連接字串預留位置。
    >* 使用具有「完整」存取權，而非「接聽」存取權的連接字串。 接聽存取權的字串沒有傳送通知的權限。
   > 
   > 
6. 在 [主要] 方法中新增下列程式碼行：
   
         SendNotificationAsync();
         Console.ReadLine();

7. 在 Visual Studio 中，以滑鼠右鍵按一下主控台應用程式專案，然後選取 [設定為啟始專案]，將它設為啟始專案。 然後選取 **F5** 鍵執行應用程式。
   
    您將會在所有註冊裝置上收到快顯通知。 選取或點選快顯橫幅即會載入應用程式。

您可以在 MSDN 上的[快顯目錄]、[圖格目錄]和[徽章概觀]主題中找到所有支援的承載。

## <a name="next-steps"></a>後續步驟
在此簡單範例中，您可使用入口網站或主控台應用程式，將廣播通知傳送到您的所有 Windows 裝置。 在下一個步驟中，我們建議[使用通知中樞將通知推播給使用者]教學課程。 它會示範如何使用標記以特定使用者為目標，從 ASP.NET 後端傳送通知。

如果您想要按興趣群組分隔使用者，請參閱 [使用通知中心傳送即時新聞]。 

如需通知中樞的一般資訊，請參閱[通知中樞指引](notification-hubs-push-notification-overview.md)。

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[使用通知中樞將通知推播給使用者]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[使用通知中心傳送即時新聞]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[快顯目錄]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[圖格目錄]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[徽章概觀]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 

