---
title: "開始使用適用於 iOS (Objective C) 的 Azure Mobile Engagement | Microsoft Docs"
description: "了解如何使用 iOS app 的 Azure Mobile Engagement 與分析和推播通知。"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 117b5742-522b-41de-98c5-f432da2d98f8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 1b87a2ebb35b31ee3d3139ecead6267e62eb1033
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>開始使用適用於 iOS 應用程式 (Objective C) 的 Azure Mobile Engagement
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure Mobile Engagement 了解您的應用程式使用情形，並將推送通知傳送至 iOS 應用程式的區隔使用者。
在本教學課程中，您將使用 Apple 推播通知服務 (APNS)，建立可收集基本資料，並接收推播通知的空白 iOS 應用程式。

本教學課程需要下列各項：

* Xcode 8，可以從您的 MAC App Store 安裝
* [Mobile Engagement iOS SDK]

完成本教學課程是所有其他 iOS 應用程式 Mobile Engagement 教學課程的先決條件。

> [!NOTE]
> 若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started)。
>
>

## <a id="setup-azme"></a>為您的 iOS 應用程式設定 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端
本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。 完整的整合文件位於 [Mobile Engagement iOS SDK 整合](mobile-engagement-ios-sdk-overview.md)

我們將會使用 XCode 建立基本 app 來示範整合。

### <a name="create-a-new-ios-project"></a>建立新的 iOS 專案
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>將您的應用程式連線至 Mobile Engagement 後端
1. 下載 [Mobile Engagement iOS SDK]。
2. 將 .tar.gz 檔案解壓所到您電腦中的某個資料夾。
3. 以滑鼠右鍵按一下專案，然後選取 [新增檔案至] 。

    ![][1]
4. 瀏覽至您解壓縮 SDK 的資料夾，選取 `EngagementSDK` 資料夾，按一下左下角的 [選項]，並確定已核取 [視需要複製項目] 核取方塊和您目標的核取方塊，然後按 [確定]。

    ![][2]
5. 開啟 [建置階段] 索引標籤，在 [連結二進位檔與程式庫] 功能表中新增架構，如下所示：

    ![][3]
6. 回到 Azure 入口網站中您 app 的 [連線資訊]  頁面，複製連接字串。

    ![][4]
7. 在 **AppDelegate.m** 檔案中新增以下程式碼行。

        #import "EngagementAgent.h"
8. 現在於 `didFinishLaunchingWithOptions` 代理人中貼上連接字串。

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
              [...]   
              [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
              [...]
        }
9. `setTestLogEnabled` 是選擇性的陳述式，可啟用 SDK 記錄檔，供您找出問題。

## <a id="monitor"></a>啟用即時監視
若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。

1. 開啟 **ViewController.h** 檔案，並匯入 **EngagementViewController.h**：

    `#import "EngagementViewController.h"`
2. 現在以 `EngagementViewController` 取代 **ViewController** 介面的超級類別：

    `@interface ViewController : EngagementViewController`

## <a id="monitor"></a>將 App 與即時監視連接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>啟用推播通知與 App 內傳訊
Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達 (REACH)。 此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。
以下各節將設定您的用程式來接收它們。

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>啟用應用程式接收無聲推播通知
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>將觸達程式庫加入至專案
1. 以滑鼠右鍵按一下您的專案。
2. 選取 [新增檔案至] 。
3. 瀏覽至您解壓縮 SDK 所在的資料夾。
4. 選取 `EngagementReach` 資料夾。
5. 按一下 [新增] 。

### <a name="modify-your-application-delegate"></a>修改您的應用程式代理人
1. 回到 **AppDeletegate.m** 檔案，匯入 Engagement Reach 模組。

        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>
2. 在 `application:didFinishLaunchingWithOptions` 方法內，建立觸達模組，並將它傳遞到您現有的 Engagement 初始化行：

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>讓您的應用程式能接收 APNS 推播通知
1. 將下行新增至 `application:didFinishLaunchingWithOptions` 方法：

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }
2. 新增 `application:didRegisterForRemoteNotificationsWithDeviceToken` 方法，如下所示：

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
             [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }
3. 新增 `didFailToRegisterForRemoteNotificationsWithError` 方法，如下所示：

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           NSLog(@"Failed to get token, error: %@", error);
        }
4. 新增 `didReceiveRemoteNotification:fetchCompletionHandler` 方法，如下所示：

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
