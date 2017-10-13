---
title: "Azure Mobile Engagement 的 Android SDK 整合"
description: "描述如何整合 Azure Mobile Engagement SDK 至 Android 應用程式"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: 35935e911f1f17989beb71978396c6d1b7d601d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Azure Mobile Engagement 的 Android SDK 整合
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

此文件說明適用於 Azure Mobile Engagement Android SDK 的所有整合及組態選項。

## <a name="prerequisites"></a>必要條件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>進階功能
### <a name="reporting-features"></a>報告功能
您可以新增這些功能：

1. [進階報告選項](mobile-engagement-android-advanced-reporting.md)
2. [位置報告選項](mobile-engagement-android-location-reporting.md)
3. [進階組態選項](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>通知:
[如何將 Reach (通知) 整合在 Android 應用程式中](mobile-engagement-android-integrate-engagement-reach.md)

1. Google 雲端通訊 (GCM)： [如何整合 GCM 與 Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. Amazon 裝置傳訊 (ADM)： [如何整合 ADM 與 Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>標記計畫實作：
[如何在 Android 應用程式中使用進階 Mobile Engagement 標記 API](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>版本資訊

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* 修正在呼叫 `EngagementAgentUtils.isInDedicatedEngagementProcess` (也由 `EngagementApplication` 類別使用) 時可能罕見發生的損毀。

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Android 8 支援 (舊版 SDK 不適用於 Android 8)。
* 不再依賴支援程式庫。
* 移除 `EngagementFragmentActivity` 類別。
* 由於 Android 8 上的[背景執行限制](https://developer.android.com/preview/features/background.html)，在背景中的記錄檔可能會延遲，直到使用者與裝置互動之時，這會影響推送活動**已傳遞**和**系統通知顯示**統計資料延遲，如果裝置已進入休眠狀態的話 (通知仍會顯示、且仍會即時響鈴和震動)。
* 由於[背景位置限制](https://developer.android.com/preview/features/background-location-limits.html)，背景的即時位置將不會在 Android 8 上經常更新。

如需所有版本，請參閱 [完整版本資訊](mobile-engagement-android-release-notes.md)。

## <a name="upgrade-procedures"></a>升級程序
如果您已經將較舊版的 SDK 整合到應用程式中，請參閱 [升級程序](mobile-engagement-android-upgrade-procedure.md)。

