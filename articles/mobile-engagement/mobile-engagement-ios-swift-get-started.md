---
title: "開始使用適用於 iOS (Swift) 的 Azure Mobile Engagement | Microsoft Docs"
description: "了解如何使用 iOS 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 196c282d-6f2f-4cbc-aeee-6517c5ad866d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: swift
ms.topic: hero-article
ms.date: 09/20/2016
ms.author: piyushjo
ms.openlocfilehash: 1011b9823333e79a52cd2d187df4f8d063b1f799
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>開始使用適用於 iOS 應用程式 (Swift) 的 Azure Mobile Engagement
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure Mobile Engagement 了解您的應用程式使用情形，並將推送通知傳送至 iOS 應用程式的區隔使用者。
在本教學課程中，您將使用 Apple 推播通知服務 (APNS)，建立可收集基本資料，並接收推播通知的空白 iOS 應用程式。

本教學課程需要下列各項：

* Xcode 8，可以從您的 MAC App Store 安裝
* [Mobile Engagement iOS SDK]
* 推播通知憑證 (.p12)，您可以在 Apple Dev Center 取得

> [!NOTE]
> 本教學課程使用 Swift 3.0 版。 
> 
> 

完成本教學課程是所有其他 iOS 應用程式 Mobile Engagement 教學課程的先決條件。

> [!NOTE]
> 若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started)。
> 
> 

## <a id="setup-azme"></a>為您的 iOS 應用程式設定 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端
本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。 完整的整合文件位於 [Mobile Engagement iOS SDK 整合](mobile-engagement-ios-sdk-overview.md)

我們將會使用 XCode 建立基本應用程式來示範整合：

### <a name="create-a-new-ios-project"></a>建立新的 iOS 專案
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-mobile-engagement-backend"></a>將您的應用程式連線至 Mobile Engagement 後端
1. 下載 [Mobile Engagement iOS SDK]
2. 將 .tar.gz 檔案解壓縮到電腦中的資料夾
3. 以滑鼠右鍵按一下專案，然後選取 [Add files to ...]
   
    ![][1]
4. 瀏覽至您解壓縮 SDK 的資料夾，選取 `EngagementSDK` 資料夾，然後按 [OK]。
   
    ![][2]
5. 開啟 `Build Phases` 索引標籤，並在 `Link Binary With Libraries` 功能表中新增框架，如下所示：
   
    ![][3]
6. 選擇 [File] > [New] > [File] > [iOS] > [Source] > [Header File] 來建立「橋接」標頭，以使用 SDK 的 Objective C API。
   
    ![][4]
7. 編輯橋接標頭檔案來將 Mobile Engagement Objective-C 程式碼公開至 Swift 程式碼，並新增以下匯入：
   
        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"
8. 在 [Build Settings]，請確定在 [Swift Compiler - Code Generation] 下的 [Objective-C Bridging Header] 組件設定有指向此標頭的路徑。 路徑的範例： **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (依路徑而定)**
   
   ![][6]
9. 回到 Azure 入口網站中您應用程式的 [連線資訊]  頁面，並複製 [連線字串]。
   
   ![][5]
10. 現在 `didFinishLaunchingWithOptions` 代理人中貼上連接字串。
    
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
              [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
              [...]
        }

## <a id="monitor"></a>啟用即時監視
若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。

1. 開啟 **ViewController.swift** 檔案，將 **ViewController** 的基底類別取代為 **EngagementViewController**：
   
    `class ViewController : EngagementViewController {`

## <a id="monitor"></a>將 App 與即時監視連接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>啟用推播通知與應用程式內傳訊
Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達 (REACH)。 此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。
以下各節將設定您的應用程式來接收它們。

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>啟用應用程式接收無聲推播通知
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>將觸達程式庫加入至專案
1. 以滑鼠右鍵按一下您的專案
2. 選取 `Add file to ...`
3. 瀏覽至您解壓縮 SDK 所在的資料夾
4. 選取 `EngagementReach` 資料夾
5. 按一下 [新增]
6. 編輯橋接標頭檔案來將 Mobile Engagement Objective-C 觸達標頭公開，並新增以下匯入：
   
        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>修改您的應用程式代理人
1. 在 `didFinishLaunchingWithOptions` 內 - 建立觸達模組並將它傳遞到您現有的 Engagement 初始化行：
   
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>讓您的應用程式能接收 APNS 推播通知
1. 將下行新增至 `didFinishLaunchingWithOptions` 方法：
   
        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }
2. 新增 `didRegisterForRemoteNotificationsWithDeviceToken` 方法，如下所示：
   
        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }
3. 新增 `didReceiveRemoteNotification:fetchCompletionHandler:` 方法，如下所示：
   
        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
