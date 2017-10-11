---
title: "Azure Mobile Engagement Web SDK 升級程序 | Microsoft Docs"
description: "Azure Mobile Engagement Web SDK 的最新更新與程序"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: afa8037dcb7a53042fa606e2c4014b442d4be326
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Azure Mobile Engagement Web SDK 升級程序
如果您已經將舊版 Azure Mobile Engagement Web SDK 整合到您的 Web 應用程式，則當您升級 SDK 時需要考慮下列幾點。

如果您略過多個版本的 Mobile Engagement Web SDK，您可能需要在升級程序期間完成數個程序。 例如，如果您從 1.4.0 移轉到 1.6.0，請先遵循從從 1.4.0 升級到 1.5.0 的程序。 然後，遵循從 1.5.0 升級到 1.6.0 的程序。

不論您要從哪一個版本升級，都要使用最新版的 azure-engagement.js 檔案來取代舊版檔案。

## <a name="upgrade-from-121-to-200"></a>從 1.2.1 升級到 2.0.0
本節說明如何將 Mobile Engagement Web SDK 整合從 Capptain SAS 提供的 Capptain 服務移轉到 Azure Mobile Engagement 應用程式。 如果您是從較早版本移轉，請參閱 Capptain 網站，先移轉到 1.2.1 後再套用以下程序。

此版本的 Mobile Engagement Web SDK 不支援 Samsung Smart TV、Opera TV、webOS 或 Reach 功能。

> [!IMPORTANT]
> Capptain 和 Azure Mobile Engagement 不是同一個服務。 下列程序只會強調說明如何移轉用戶端應用程式。 移轉應用程式中的 Mobile Engagement Web SDK，不會將您的資料從 Capptain 伺服器移轉到 Mobile Engagement 伺服器。
> 
> 

### <a name="javascript-files"></a>JavaScript 檔案
使用 azure-engagement.js 檔案來取代 capptain-sdk.js 檔案，然後據以更新您的指令碼匯入。

### <a name="remove-capptain-reach"></a>移除 Capptain Reach
此版本的 Mobile Engagement Web SDK 不支援 Reach 功能。 如果您將 Capptain Reach 整合到您的應用程式，就需要加以移除。

從您的頁面移除 Reach CSS 匯入，並移除相關的 .css 檔案 (預設為 capptain-reach.css)。

刪除下列 Reach 資源：關閉影像 (預設為 capptain-close.png) 和品牌圖示 (預設為 capptain-notification-icon)。

移除用於應用程式內通知的 Reach UI。 預設配置看起來像這樣：

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

移除用於文字和 Web 宣告和輪詢的 Reach UI。 預設配置看起來像這樣：

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

從您的組態中移除 `reach` 物件 (如果存在)。 它看起來像這樣：

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

移除所有其他的 Reach 自訂，例如類別。

### <a name="remove-deprecated-apis"></a>移除已被取代的 API
在 Mobile Engagement Web SDK 中，某些來自 Capptain 的 API 已被取代。

移除針對下列 API 的所有呼叫：`agent.connect`、`agent.disconnect`、`agent.pause` 及 `agent.sendMessageToDevice`。

從您的 Capptain 組態移除下列回呼的所有執行個體：`onConnected`、`onDisconnected`、`onDeviceMessageReceived` 及 `onPushMessageReceived`。

### <a name="configuration"></a>組態
Mobile Engagement 使用連接字串來設定 SDK 識別碼，例如應用程式識別碼。

使用您的連接字串來取代應用程式識別碼。 請注意，適用於 SDK 組態的全域物件會從 `capptain` 變更為 `azureEngagement`。

移轉前：

    window.capptain = {
      appId: ...,
      [...]
    };

移轉後：

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

您應用程式的連接字串會顯示於 Azure 入口網站中。

### <a name="javascript-apis"></a>JavaScript API
全域 JavaScript 物件 `window.capptain` 已重新命名為 `window.azureEngagement`，但您可以針對 API 呼叫使用 `window.engagement` 別名。 您無法使用此別名來定義 SDK 組態。

例如，`capptain.deviceId` 會變成 `engagement.deviceId`，`capptain.agent.startActivity` 會變成 `engagement.agent.startActivity`，依此類推。

