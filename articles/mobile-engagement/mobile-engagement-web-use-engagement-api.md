---
title: Azure Mobile Engagement Web SDK API | Microsoft Docs
description: "Azure Mobile Engagement Web SDK 的最新更新與程序"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8a87d5ac-d8b7-4a0d-bdee-414dbcc561b2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 54c22ce6a03e382b1bbde102bccc97deec249b30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>在 Web 應用程式中使用 Azure Mobile Engagement API
此文件是文件的補充，說明如何 [在您的 Web 應用程式中整合 Mobile Engagement](mobile-engagement-web-integrate-engagement.md)。 它會提供關於如何使用 Azure Mobile Engagement API 來回報您應用程式的統計資料之詳細資訊。

Mobile Engagement API 是由 `engagement.agent` 物件提供。 預設 Azure Mobile Engagement Web SDK 別名是 `engagement`。 您可以從 SDK 組態來重新定義此別名。

## <a name="mobile-engagement-concepts"></a>Mobile Engagement 概念
以下部分簡要說明適用於 Web 平台的 [Mobile Engagement 概念](mobile-engagement-concepts.md) 。

### <a name="session-and-activity"></a>`Session`和`Activity`
如果使用者在兩個活動之間維持閒置超過幾秒鐘，其活動序列會分割成兩個相異的工作階段。 這幾秒被稱為工作階段逾時。

如果您的 Web 應用程式不自行宣告使用者活動的結束 (透過呼叫 `engagement.agent.endActivity` 函式)，Mobile Engagement 伺服器將會在應用程式頁面關閉後的 3 分鐘之內自動讓使用者工作階段到期。 這被稱為伺服器工作階段逾時。

### `Crash`
根據預設，不會建立無法攔截的 JavaScript 例外狀況的自動報告。 不過，您可以透過使用 `sendCrash` 函式來手動報告當機 (請參閱報告當機的章節)。

## <a name="reporting-activities"></a>報告活動
使用者啟動新的活動以及使用者結束目前活動時報告使用者活動。

### <a name="user-starts-a-new-activity"></a>使用者啟動新的活動
    engagement.agent.startActivity("MyUserActivity");

每當使用者活動變更，您就需要呼叫 `startActivity()` 。 第一次呼叫此函數會啟動新的使用者工作階段。

### <a name="user-ends-the-current-activity"></a>使用者結束目前的活動
    engagement.agent.endActivity();

使用者完成最後一個活動時，您至少需要呼叫 `endActivity()` 一次。 這會通知 Mobile Engagement Web SDK，說明使用者目前處於閒置狀態，且工作階段逾時到期之後就必須關閉使用者工作階段。 如果您在工作階段逾時到期之前呼叫 `startActivity()` ，工作階段只會繼續。

因為當導覽視窗關閉時沒有可靠呼叫，在 Web 環境內攔截使用者活動的結束通常很困難或無法達成。 這就是為什麼 Mobile Engagement 伺服器會在應用程式頁面關閉後，於 3 分鐘之內自動讓使用者工作階段到期。

## <a name="reporting-events"></a>報告事件
報告事件涵蓋了工作階段事件和獨立事件。

### <a name="session-events"></a>工作階段事件
工作階段事件通常用來報告在使用者工作階段期間由使用者所執行的動作。

**不含額外資料的範例：**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**含額外資料的範例：**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>獨立事件
與工作階段事件不同，獨立的事件可能發生在工作階段的內容之外。

針對那種情況，請使用 ``engagement.agent.sendEvent``，而不是 ``engagement.agent.sendSessionEvent``。

## <a name="reporting-errors"></a>報告錯誤
報告錯誤涵蓋了工作階段錯誤和獨立錯誤。

### <a name="session-errors"></a>工作階段錯誤
工作階段錯誤通常用來報告在使用者工作階段期間影響使用者的錯誤。

**不含額外資料的範例：**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**含額外資料的範例：**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>獨立錯誤
不同於工作階段錯誤，獨立錯誤可以出現在工作階段的內容之外。

針對那種情況，請使用 `engagement.agent.sendError`，而不是 `engagement.agent.sendSessionError`。

## <a name="reporting-jobs"></a>報告工作
報告作業涵蓋報告在作業期間發生的錯誤和事件，以及報告當機。

**範例：**

如果您想要監視 AJAX 要求，可以使用下列項目：

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>報告於作業期間發生的錯誤
錯誤可能與正在執行的作業關聯，而不是與目前的使用者作業階段關聯。

**範例：**

如果您想要在 AJAX 要求失敗時報告錯誤：

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>在作業期間報告事件
透過 `engagement.agent.sendJobEvent` 函式，事件可以與執行中的作業相關，而不是與目前的使用者工作階段相關。

此函式的運作方式與 `engagement.agent.sendJobError`完全相同。

### <a name="reporting-crashes"></a>報告當機
使用 `sendCrash` 函式來手動報告當機。

`crashid` 引數是識別當機類型的字串。
`crash` 引數通常是字串格式的當機的堆疊追蹤。

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>額外的參數
您可以將任意資料附加到事件、錯誤、活動或作業。

此資料可以是任何 JSON 物件 (但是不是陣列或基本類型)。

**範例：**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>限制
套用至額外參數的限制是在索引鍵、值類型和大小的規則運算式的領域。

#### <a name="keys"></a>之間的信任
物件中的每個索引鍵都必須符合下列規則運算式：

    ^[a-zA-Z][a-zA-Z_0-9]*

這表示索引鍵必須至少以一個字母開頭，後面連接字母、數字或底線 (\_)。

#### <a name="values"></a>值
值被限制為字串、數字及布林類型。

#### <a name="size"></a>大小
額外項目限制為一次呼叫 1024 個字元 (在 Mobile Engagement Web SDK 以 JSON 編碼之後)。

## <a name="reporting-application-information"></a>報告應用程式資訊
您可以使用 `sendAppInfo()` 函式手動報告追蹤資訊 (或是任何其他應用程式特定資訊)。

請注意，這項資訊可以累加方式傳送。 只會針對特定裝置保留特定索引鍵的最新值。

和事件額外資料一樣，您可以使用任何 JSON 物件來摘錄應用程式資訊。 請注意，陣列或子物件會視為一般字串 (使用 JSON 序列化)。

**範例：**

以下是傳送使用者性別和出生日期的程式碼範例：

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>限制
套用至應用程式資訊的限制是在索引鍵和大小的規則運算式的領域。

#### <a name="keys"></a>之間的信任
物件中的每個索引鍵都必須符合下列規則運算式：

    ^[a-zA-Z][a-zA-Z_0-9]*

這表示索引鍵必須至少以一個字母開頭，後面連接字母、數字或底線 (\_)。

#### <a name="size"></a>大小
應用程式資訊限制為一次呼叫 1024 個字元 (在 Mobile Engagement Web SDK 以 JSON 編碼之後)。

在上述範例中，傳送到伺服器的 JSON 會是 44 個字元：

    {"birthdate":"1983-12-07","gender":"female"}
