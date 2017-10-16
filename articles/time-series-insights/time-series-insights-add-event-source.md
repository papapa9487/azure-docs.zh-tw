---
title: "將事件來源新增至 Azure Time Series Insights 環境 | Microsoft Docs"
description: "在本教學課程中，您會將事件來源連線至 Time Series Insights 環境"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
ms.openlocfilehash: c247a7abd3d1682f8ccd1940e506ef54f9409263
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-event-source-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>使用 Ibiza 入口網站建立 Time Series Insights 環境的事件來源

Time Series Insights 事件來源衍生自事件代理程式，例如 Azure 事件中樞。 Time Series Insights 會直接連線到事件來源，並內嵌資料流，而不需要使用者撰寫一行程式碼。 目前，Time Series Insights 支援 Azure 事件中樞與 Azure IoT 中樞。 未來將會新增更多事件來源。

## <a name="steps-to-add-an-event-source-to-your-environment"></a>將事件來源新增至您的環境的步驟

1.  登入 [Ibiza 入口網站](https://portal.azure.com)。
2.  按一下 Ibiza 入口網站左側功能表中的 [所有資源]。
3.  選取 Time Series Insights 環境。

  ![建立 Time Series Insights 事件來源](media/add-event-source/getstarted-create-event-source-1.png)

4.  選取 [事件來源]，按一下 [+ 新增]。

  ![建立 Time Series Insights 事件來源 - 詳細資料](media/add-event-source/getstarted-create-event-source-2.png)

5.  指定事件來源的名稱。 此名稱與來自此事件來源的所有事件相關聯，並可在查詢時使用。
6.  從目前訂用帳戶的事件中樞資源清單選取事件中樞。 否則請選擇匯入選項「手動提供事件中樞設定」來指定另一個訂用帳戶的事件中樞。 事件必須以 JSON 格式發佈。
7.  選取具有事件中樞讀取權限的原則。
8.  指定事件中樞取用者群組。

  > [!IMPORTANT]
  > 確定任何其他服務 (例如串流分析作業或其他 Time Series Insights 環境) 均未使用此取用者群組。 如果有其他服務使用此取用者群組，讀取作業會對此環境和其他服務造成負面影響。 如果您使用 “$Default” 作為取用者群組，有可能會導致其他讀取者重複使用。

9.  按一下 [建立]。

建立事件來源之後，Time Series Insights 會自動開始將資料串流處理至您的環境。

## <a name="next-steps"></a>後續步驟

* [將事件傳送](time-series-insights-send-events.md)到事件來源
* 在 [Time Series Insights 入口網站](https://insights.timeseries.azure.com)中檢視您的環境
