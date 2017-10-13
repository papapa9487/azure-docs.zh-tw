---
title: "適用於 Windows 通用 App Engagement SDK 的進階組態"
description: "適用於 Azure Mobile Engagement 與 Windows 通用 App 的進階組態選項"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6d85dd5d-ac07-43ba-bbe4-e91c3a17690b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: cb9454212c94cf65093219c3d24c71277ede7877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>適用於 Windows 通用 App Engagement SDK 的進階組態
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
> 
> 

此程序描述如何設定 Azure Mobile Engagement Android 應用程式的各種組態選項。

## <a name="prerequisites"></a>必要條件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="advanced-configuration"></a>進階組態
### <a name="disable-automatic-crash-reporting"></a>停用自動當機報告
您可以停用 Engagement 的自動當機報告功能。 然後，在發生未處理的例外狀況時，Engagement 不做任何動作。

> [!WARNING]
> 如果您停用此功能，則當應用程式中發生未處理的當機時，Engagement 不會傳送該當機，「且」不會關閉工作階段和工作。
> 
> 

若要停用自動當機報告，請依照您宣告的方式自訂您的組態：

#### <a name="from-engagementconfigurationxml-file"></a>從 `EngagementConfiguration.xml` 檔案
在 `<reportCrash>` 和 `</reportCrash>` 標記之間，將報告當機設為 `false`。

#### <a name="from-engagementconfiguration-object-at-run-time"></a>於執行階段時從 `EngagementConfiguration` 物件
使用 EngagementConfiguration 物件將報告當機設為 false。

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>停用即時報告
根據預設，Engagement 會即時報告記錄檔。 如果應用程式報告記錄檔的頻率很高，建議您緩衝記錄檔，以固定時段一次報告全部的記錄檔。 這稱為「高載模式」。

若要這樣做，請呼叫方法：

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

該引數是以 「毫秒」為單位的值。 如果您想要重新啟動及時記錄，可以呼叫該方法，而不需要使用任何參數 (或使用 0 為值)。

高載模式可以稍微延長電池使用時間但對 Engagement 監視器會有影響：所有工作階段和工作持續時間會調整為高載閾值 (因此，可能將看不到時間比高載閾值短的工作階段和作業)。 我們建議使用低於 30000 (30 秒) 的高載閾值。 儲存的記錄檔僅限 300 個項目。 如果傳送太長，您可能會遺失某些記錄檔。

> [!WARNING]
> 高載閾值無法設定為小於一秒的時間間隔。 如果您這樣做，SDK 會顯示含錯誤訊息的追蹤，並且自動重設為預設值 (0 秒)。 這樣會觸發 SDK 以即時的方式報告記錄檔。
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
