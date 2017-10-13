---
title: "Azure 通知中心"
description: "了解如何使用 Azure 通知中樞新增推播通知功能。"
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: 
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 1/17/2017
ms.author: yuaxu
ms.openlocfilehash: a1be0b13cd1feb582a23965df142e44d90ac6851
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs"></a>Azure 通知中心
## <a name="overview"></a>概觀
Azure 通知中樞提供方便使用、多平台、可相應放大的推播引擎。 利用單一的跨平台 API 呼叫，您就可以輕鬆地將鎖定目標且個人化的推播通知，從任何雲端或內部部署後端傳送到任何行動平台。

通知中樞很適合企業和消費者案例。 以下是一些客戶使用通知中樞之用途的範例︰

* 傳送即時新聞通知給數百萬人，且延遲時間很低。
* 將位置型折價券傳送給感興趣的使用者區段。
* 將事件相關通知傳送給媒體/運動/財金/遊戲應用程式的使用者或群組。
* 將促銷內容推播到應用程式來吸引客戶並進行銷售。
* 對使用者發送企業事件通知，例如新訊息和工作項目。
* 傳送 Multi-Factor Authentication 的程式碼。

## <a name="what-are-push-notifications"></a>什麼是推播通知？
推播通知是一種由應用程式對使用者發出的通訊形式，通常會以快顯視窗或對話方塊來對行動應用程式的使用者發出某些他們想要之資訊的通知。 使用者一般可以選擇要檢視或關閉訊息，選擇前者便會開啟傳送通知的行動應用程式。

推播通知是很重要的功能，對於消費者應用程式來說，可提高應用程式的參與和使用率，對於企業應用程式來說，則可傳送最新的企業資訊。 此功能是最佳的應用程式對使用者通訊，因為它不會過度耗用行動裝置的電量、可讓通知傳送者保有彈性，並且可在對應的應用程式未執行時使用。

如需某些熱門平台的推播通知詳細資訊︰
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>推播通知的運作方式
推播通知可透過名為「平台通知系統 (PNS)」的平台特定基礎結構來傳遞。 其可提供簡易型推播功能，以所提供的控制代碼傳遞訊息至裝置，並且沒有通用介面。 若要跨 iOS、Android 和 Windows 版本的應用程式傳送通知給所有客戶，開發人員必須在批次處理傳送時使用 APNS (Apple Push Notification Service)、FCM (Firebase Cloud Messaging) 和 WNS (Windows 通知服務)。

概括而言，以下是推播功能的運作方式︰

1. 用戶端應用程式決定它要接收推播，因此連絡對應的 PNS 以擷取其唯一且暫時的推播控制代碼。 控制代碼類型取決於系統 (例如 WNS 有 URI，APNS 則有權杖)。
2. 用戶端應用程式會將此控制代碼儲存在應用程式後端或提供者。
3. 若要傳送推播通知，應用程式後端必須使用控制代碼連絡 PNS，以將特定的用戶端應用程式定為目標。
4. PNS 會將通知轉送至控制代碼所指定的裝置。

![][0]

## <a name="the-challenges-of-push-notifications"></a>推播通知的挑戰
儘管 PNS 非常強大，但即便 App 開發人員想要實作一般的推播通知工作 (例如廣播或傳送推播通知給分段式使用者)，都有許多工作有待處理。

推播是行動雲端服務中其中一項最常被要求的功能，因為它的運作需要與應用程式的主要商業邏輯沒有關聯的複雜基礎結構。 基礎結構方面的某些挑戰包括︰

* **平台相依性**： 

  * 後端必須有複雜且難以維護之取決於平台的邏輯，才能將通知傳送給各種平台上的裝置，因為 PNS 並未統一。
* **調整**：

  * 根據 PNS 準則，在每次啟動應用程式時，都必須重新整理裝置權杖。 這表示後端會處理大量的流量以及資料庫的存取權，卻只是為了讓權杖保持最新狀態。 當裝置數目增加至數億和數十億時，建立及維護此基礎結構的成本將會很可觀。
  * 大部分的 PNS 並不支援廣播至多個裝置。 這表示單單是對百萬個裝置的廣播就會產生百萬個 PNS 呼叫。 想要擴增這種數量的流量卻又要將延遲降到最低並非易事。
* **路由**：
  
  * 雖然 PNS 提供了將訊息傳送至裝置的方法，但大部分應用程式通知都是以使用者或相關群組為目標。 這表示後端必須維護登錄，以將裝置關聯到相關群組、使用者、屬性等。這項額外工作將導致應用程式的上市時程延宕和維護成本提高。

## <a name="why-use-notification-hubs"></a>為何要使用通知中心？
通知中樞會消除所有與自行啟用推播相關聯的複雜工作。 其多平台、可相應放大的推播通知基礎結構可減少推播相關程式碼，並簡化您的後端。 使用通知中樞時，裝置只需負責向中樞註冊其 PNS 控制代碼，而由後端負責將訊息傳送給使用者或相關群組，如下圖所示︰

![][1]

通知中樞是立即可用的推播引擎，其具有下列優勢︰

* **跨平台**

  * 支援所有主要的推播平台，包括 iOS、Android、Windows、Kindle 和百度。
  * 具有通用介面，可以平台特定或平台無關的格式推播至所有平台，但不必進行平台特定工作。
  * 集中在一個位置管理裝置控制代碼。
* **跨後端**
  
  * 雲端或內部部署
  * .NET、Node.js、Java 等
* **豐富的傳遞模式集**：

  * *廣播到一個或多個平台*︰只要單一 API 呼叫，您就可以立即跨平台廣播到數百萬個裝置。
  * *推播到裝置*︰您可以將通知目標鎖定為個別裝置。
  * *推播到使用者*︰標記和範本功能可協助您觸達某位使用者的所有跨平台裝置。
  * *以動態標記推播至區段*︰標記功能可協助您將裝置分區段，並根據您的需求推播至這些區段，不論您是要傳送給一個區段或一個運算式的區段 (例如 active AND lives in Seattle NOT new user)。 不必侷限於發行/訂閱模式，您可以隨時隨地更新裝置的標記。
  * *當地語系化的推播*︰範本功能可協助您實現當地語系化，又不影響後端程式碼。
  * *無訊息推播*︰您可以將無訊息通知傳送至裝置，並觸發它們以完成特定提取或動作，以啟用推播到提取模式。
  * *排定推播*︰您可以排程在任何時間傳送通知。
  * *直接推播*︰您可以略過向我們的服務註冊裝置的動作，並直接批次推播至裝置控制代碼清單。
  * *個人化推播*︰裝置推播變數可協助您以自訂的索引鍵/值組傳送裝置特定的個人化推播通知。
* **豐富的遙測**
  
  * 您可以在 Azure 入口網站或以程式設計方式取得一般的推播、裝置、錯誤和作業遙測。
  * 每一訊息遙測會追蹤每個推播從初始要求呼叫到我們的服務成功批次處理推播的過程。
  * 平台通知系統意見反應會傳送平台通知系統的所有意見反應，以協助偵錯。
* **延展性** 
  
  * 將快速訊息傳送至數百萬個裝置，而不需要經過重新架構或裝置分區化程序。
* **安全性**

  * 共用存取密碼 (SAS) 或同盟驗證。

## <a name="integration-with-app-service-mobile-apps"></a>與 App Service Mobile Apps 整合
為了在 Azure 服務間促進完美且統一的體驗， [App Service Mobile Apps] 具備使用通知中樞提供推播通知的內建支援。 [App Service Mobile Apps] 具有高擴充性且可供全球使用，是專為企業開發人員與系統整合人員設計的行動應用程式開發平台，能提供一組豐富的功能給行動應用程式開發人員。

Mobile Apps 開發人員可以使用下列流程來利用通知中樞：

1. 擷取裝置 PNS 控制代碼
2. 透過便利的 Mobile Apps Client SDK 註冊 API，使用通知中樞註冊裝置
   * 請注意，Mobile Apps 會基於安全性考量，去除註冊上的所有標籤。 直接從後端使用通知中樞，將標籤關聯至裝置。
3. 從 App 後端使用通知中樞傳送通知

以下是透過此註冊為開發人員帶來的一些便利性：

* **Mobile Apps 用戶端 SDK**︰這些多平台 SDK 提供簡單的 API 來進行註冊，然後會自動與連結到行動 App 的通知中樞聯繫。 開發人員不需要透過通知中樞認證進行挖掘，以及使用其他服務。

  * *推播給使用者*：SDK 會使用 Mobile Apps 驗證的使用者識別碼自動標記指定的裝置，以啟用推播至使用者案例。
  * *推播至裝置*：SDK 會自動使用 Mobile Apps 安裝識別碼做為 GUID 來向通知中樞註冊，省去開發人員維護多個服務 GUID 的麻煩。
* **安裝模式**：Mobile Apps 會使用通知中樞的最新推送模型，來呈現 JSON 安裝中所有與裝置相關聯的推送屬性，其會與推播通知密切合作且易於使用。
* **彈性**：即使已就地整合，開發人員一律還是可以選擇直接使用通知中樞。
* **[Azure 入口網站]中的整合式體驗**：Mobile Apps 中會以視覺化方式呈現以功能形式出現的推送，而開發人員可以透過 Mobile Apps 輕鬆使用相關聯的通知中樞。

## <a name="next-steps"></a>後續步驟
您可以在以下主題找到「通知中樞」的詳細資訊：

* **[客戶如何使用通知中樞]**
* **[通知中樞教學課程和指南]**
* **通知中樞快速入門教學課程**：[iOS]、[Android]、[Windows Universal]、[Windows Phone]、[Kindle]、[Xamarin.iOS]、[Xamarin.Android]

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[客戶如何使用通知中樞]: http://azure.microsoft.com/services/notification-hubs
[通知中樞教學課程和指南]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure 入口網站]: https://portal.azure.com
[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
