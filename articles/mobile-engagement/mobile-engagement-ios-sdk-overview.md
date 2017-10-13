---
title: "Azure Mobile Engagement iOS SDK 概觀 | Microsoft Docs"
description: "Azure Mobile Engagement iOS SDK 的最新更新與程序"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 6acd343782a3ee07750e27ec3022ff81cedfadee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>Azure Mobile Engagement iOS SDK
從這裡開始取得有關如何在 iOS 應用程式中整合 Azure Mobile Engagement 的所有詳細資料。 如果您想要先試用一下，請務必先完成我們的 [15 分鐘教學課程](mobile-engagement-ios-get-started.md)。

按一下以查看 [SDK 內容](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>整合程序
1. 從這裡開始： [如何在 iOS 應用程式中整合 Mobile Engagement](mobile-engagement-ios-integrate-engagement.md)
2. 通知： [如何在 iOS 應用程式中整合 Reach (通知)](mobile-engagement-ios-integrate-engagement-reach.md)
3. 標記計劃實作： [如何在 iOS 應用程式中使用進階的 Mobile Engagement 標記 API](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>版本資訊
### <a name="410-07172017"></a>4.1.0 (07/17/2017)
* 修正在背景清除徽章。
* 修正在 XCode 9 上關於 API 未在主要佇列呼叫的警告。
* 修正觸達輪詢中的記憶體流失。
* 停止支援 iOS 6.X。 從此版本開始，您的應用程式部署目標必須至少為 iOS 7。

如需較早版本，請參閱 [完整版本資訊](mobile-engagement-ios-release-notes.md)

## <a name="upgrade-procedures"></a>升級程序
如果您已經整合舊版 Engagement 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

如果您錯過數個版本的 SDK，可能必須遵循幾個程序步驟，請參閱完整的 [升級程序](mobile-engagement-ios-upgrade-procedure.md)。

針對每個新版 SDK，您必須先取代 (在 xcode 中移除並重新匯入) EngagementSDK 與 EngagementReach 資料夾。

### <a name="from-300-to-400"></a>從 3.0.0 到 4.0.0
### <a name="xcode-8"></a>XCode 8
從 SDK 4.0.0 版開始就必須要有 XCode 8。

> [!NOTE]
> 如果您實際上是仰賴 XCode 7，則可以使用 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)。 這個舊版本的觸達模組在 iOS 10 裝置上執行時有已知錯誤︰系統通知不會採取動作。 若要修正此錯誤，您必須在應用程式委派中實作已被取代的 API `application:didReceiveRemoteNotification:` ，方式如下︰
>
>

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **我們不建議此因應措施** ，因為此 iOS API 已被取代，此行為在任何即將推出的 (甚至次要的) iOS 版本升級中會有所變更。 您應盡快改用 XCode 8。
>
>

#### <a name="usernotifications-framework"></a>UserNotifications 架構
您需要在建置階段新增 `UserNotifications` 架構。

在專案總管中，開啟專案窗格並選取正確的目標。 然後，開啟 [建置階段] 索引標籤，並在 [連結二進位檔與程式庫] 功能表中新增架構 `UserNotifications.framework``Optional`

#### <a name="application-push-capability"></a>應用程式推播功能
XCode 8 可能會重設您的應用程式推播功能，請在您選取的目標的 `capability` 索引標籤中再檢查一次。

#### <a name="add-the-new-ios-10-notification-registration-code"></a>新增 iOS 10 通知註冊程式碼
要將應用程式註冊通知的較舊程式碼片段仍會運作，但在 iOS 10 上執行時會使用已被取代的 API。

匯入 `User Notification` 架構：

        #import <UserNotifications/UserNotifications.h>

在您的應用程式中委派 `application:didFinishLaunchingWithOptions` 方法取代︰

        if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
            [application registerForRemoteNotifications];
        }
        else {

            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

依︰

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

#### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>解決 UNUserNotificationCenter 委派衝突

如果您的應用程式或其中一個協力廠商程式庫都未實作 `UNUserNotificationCenterDelegate`，則可以略過這個部分。

SDK 會用 `UNUserNotificationCenter` 委派來監視在 iOS 10 或更新版本上執行的裝置中的 Engagement 通知生命週期。 SDK 會實作自己的 `UNUserNotificationCenterDelegate` 通訊協定，但每個應用程式只能有一個 `UNUserNotificationCenter` 委派。 新增至 `UNUserNotificationCenter` 物件的任何其他委派會與 Engagement one 發生衝突。 如果 SDK 偵測到您的委派或任何其他協力廠商的委派，則不會使用它自己的實作來讓您有機會解決衝突。 您必須將 Engagement 邏輯新增至您自己的委派，以便解決衝突。

有兩種方式可以達到這個目的。

提案 1，直接將委派呼叫轉送給 SDK：

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

或提案 2，繼承自 `AEUserNotificationHandler` 類別

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> 您可以藉由將通知的 `userInfo` 字典傳遞給代理程式的 `isEngagementPushPayload:` 類別方法，來決定通知是否來自 Engagement。

請確定 `UNUserNotificationCenter` 物件的委派已設為您在 `application:willFinishLaunchingWithOptions:` 內的委派或應用程式委派的 `application:didFinishLaunchingWithOptions:` 方法。
例如，如果您實作了上述提案 1：

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code

        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }
