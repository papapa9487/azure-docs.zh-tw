---
title: "Azure Mobile Engagement Web SDK 整合 | Microsoft Docs"
description: "Azure Mobile Engagement Web SDK 的最新更新與程序"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.openlocfilehash: 7d8eaa180e277741a583522ee62d68f5247b92bb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>在 Web 應用程式中整合 Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

本文中的程序說明在 Web 應用程式中，啟用 Azure Mobile Engagement 中分析與監視功能的最簡單方式。

遵循下列步驟來啟用進行下列動作所需的記錄檔報告：計算關於使用者、工作階段、活動、當機和技術的所有統計資料。 對於相依於應用程式的統計資料 (例如事件、錯誤及工作)，您必須使用 Azure Mobile Engagement API 手動啟用記錄檔報告。 如需詳細資訊，請了解 [如何在 Web 應用程式中使用進階的 Mobile Engagement 標記 API](mobile-engagement-web-use-engagement-api.md)。

## <a name="introduction"></a>簡介
[下載 Azure Mobile Engagement Web SDK](http://aka.ms/P7b453)。
Mobile Engagement Web SDK 是以單一 JavaScript 檔案 (azure-engagement.js) 隨附，您必須將它包含在網站或 Web 應用程式的每個頁面中。

> [!IMPORTANT]
> 執行此指令碼之前，必須先執行您撰寫來為應用程式設定 Mobile Engagement 的指令碼或程式碼片段。
> 
> 

## <a name="browser-compatibility"></a>瀏覽器相容性
Mobile Engagement Web SDK 使用原生 JSON 編碼和解碼，以及跨網域 AJAX 要求 (仰賴 W3C CORS 規格)。 它與下列瀏覽器相容：

* Microsoft Edge 12+
* Internet Explorer 10+
* Firefox 3.5+
* Chrome 4+
* Safari 6+
* Opera 12+

## <a name="configure-mobile-engagement"></a>設定 Mobile Engagement
撰寫能建立全域 `azureEngagement` JavaScript 物件的指令碼，如下列範例所示。 因為您的網站可能包含多個頁面，此範例假設這個指令碼包含於每個頁面中。 在此範例中，JavaScript 物件的名稱為 `azure-engagement-conf.js`。

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

適用於您應用程式的 `connectionString` 值會顯示於 Azure 入口網站中。

> [!NOTE]
> `appVersionName` 和 `appVersionCode` 是選擇性的。 不過，我們建議您設定它們，讓分析可以處理版本資訊。
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>在頁面中包含 Mobile Engagement 指令碼
使用下列其中一種方式，將 Mobile Engagement 指令碼新增至您的頁面：

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

或是這個：

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias
載入 Mobile Engagement Web SDK 指令碼之後，它會建立 **engagement** 別名來存取 SDK API。 您無法使用此別名來定義 SDK 組態。 此別名將用來做為此文件中的參考。

請注意，如果預設別名與另一個來自您頁面的全域變數發生衝突，您可以在載入 Mobile Engagement Web SDK 之前，於組態中將它重新定義，如下所示：

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>基本報告
Mobile Engagement 中的基本報告涵蓋了工作階段層級的統計資料，例如使用者、工作階段、活動和當機的相關統計資料。

### <a name="session-tracking"></a>工作階段追蹤
Mobile Engagement 工作階段被分成一系列活動，每一個都可透過名稱來識別。

在傳統網站中，我們建議您在每個網站頁面上宣告不同的活動。 對於目前頁面永遠不會變更的網站或 Web 應用程式，您可能想要追蹤較小範圍中的活動，例如，在該頁面內。

無論如何，若要開始或變更目前的使用者活動，請呼叫 `engagement.agent.startActivity` 函式。 例如：

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

Mobile Engagement 伺服器會在應用程式頁面關閉後，於三分鐘內自動結束開啟的工作階段。

或者，您可以呼叫 `engagement.agent.endActivity`手動結束工作階段。 這會將目前的使用者活動設定為 'Idle'。  除非有新的 `engagement.agent.startActivity` 呼叫繼續執行工作階段，否則該工作階段將會在 10 秒後結束。

您可以在全域 engagement 物件中設定 10 秒延遲，如下所示：

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> 您無法在 `onunload` 回呼中使用 `engagement.agent.endActivity`，因為您不能在這個階段進行 AJAX 呼叫。
> 
> 

## <a name="advanced-reporting"></a>進階報告
此外，如果您想要報告應用程式特定的事件、錯誤及工作，就必須使用 Mobile Engagement API。 您可以透過 `engagement.agent` 物件存取 Mobile Engagement API。

您可以在 Mobile Engagement API 中存取 Mobile Engagement 中所有的進階功能。 [如何在 Web 應用程式中使用進階的 Mobile Engagement 標記 API](mobile-engagement-web-use-engagement-api.md)文章中會詳述此 API。

## <a name="customize-the-urls-used-for-ajax-calls"></a>自訂用於 AJAX 呼叫的 URL
您可以自訂 Mobile Engagement Web SDK 使用的 URL。 例如，若要重新定義記錄檔 URL (用於記錄的 SDK 端點)，您可以透過下列方式複寫組態：

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

如果您的 URL 函式傳回開頭為 `/`、`//`、`http://` 或 `https://` 的字串，便代表沒有使用預設配置。 根據預設，這些 URL 會使用 `https://` 配置。 如果您想要自訂預設配置，請透過下列方式複寫組態：

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };
