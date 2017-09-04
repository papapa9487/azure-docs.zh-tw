---
title: "Azure 串流分析的事件順序和延遲處理 | Microsoft Docs"
description: "深入了解串流分析如何處理資料串流中順序錯亂或延遲的事件。"
keywords: "順序錯亂、延遲、事件"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 5089dda48ea829902663ef9d09fe83177df6f220
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="azure-stream-analytics-event-order-handling"></a>Azure 串流分析事件的順序處理

在事件的暫存資料串流中，每個事件都會進行記錄，其中會包含收到事件的時間。 有時候，某些情況可能會造成串流收到某些事件的順序與事件送出的順序不同。 簡易的 TCP 重新傳送動作或甚至是傳送裝置與接收事件中樞間的時鐘誤差，都有可能造成此狀況發生。 接收的事件串流中也已新增「標點符號」事件，以便在事件未抵達的情況下讓時間繼續前進。 例如「3 分鐘後沒出現登入作業請通知我」的情況就需要標點符號事件。

未依照順序的輸入串流為以下兩者其中之一：
* 已分類 (並因此**延遲**)。
* 已由系統調整 (根據使用者指定的原則)。


## <a name="lateness-tolerance"></a>延遲容許
串流分析可容許這類情況。 串流分析可處理「順序錯亂」和「延遲」事件。 以下是串流分析處理這些事件的方式︰

* 抵達順序錯亂但在所設定容許範圍內的事件會透過**時間戳記重新排序**。
* 抵達時間比容許範圍還晚的事件會遭到**捨棄或調整**。
    * **調整**︰調整為看起來是在最低可接受時間抵達。
    * **捨棄**︰放棄。

![串流分析事件處理](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

## <a name="reduce-the-number-of-out-of-order-events"></a>減少順序錯亂的事件數目

由於串流分析在處理傳入事件時會套用暫存轉換 (例如視窗型彙總或暫存聯結)，因此串流分析會以時間戳記的順序將傳入事件排序。

如果**沒有**使用 timestamp by 關鍵字，就會依預設使用 Azure 事件中樞的事件加入佇列時間。 事件中樞可保證事件中樞每個分割區中時間戳記的單調性。 也保證所有分割區的事件將會依時間戳記順序進行合併。 這兩個事件中樞保證可確保沒有順序錯亂的事件發生。

有時候，使用傳送者的時間戳記對您來說相當重要。 在此情況下將會使用 timestamp by 來選擇事件承載的時間戳記。 以下案列會介紹一個或多個事件順序錯誤的情況：

* 事件產生器有時鐘誤差。 此狀況常見於不同電腦的產生器，因為電腦的時鐘不同。
* 從事件來源到目的事件中樞之間的網路發生延遲。
* 事件中樞分割區之間存在時鐘誤差。 串流分析第一次從所有事件中樞分割區排序事件時是以事件加入佇列的時間排序。 因此，資料串流被視為順序錯誤的事件。

在 [組態] 索引標籤中，您會看到下列預設值︰

![串流分析順序錯亂處理](media/stream-analytics-event-handling/stream-analytics-out-of-order-handling.png)

如果您將順序錯亂容許範圍設定為 0 秒，則系統會認定您要求所有事件永遠按照順序。 假設是這三種順序錯亂事件來源，則這是不可能實現的事。 

若要允許串流分析修正事件順序錯亂的情況，您可以指定非零值作為順序錯亂的容許範圍。 串流分析會讓事件緩衝，直到範圍上限，然後使用您選取的時間戳記重新排序事件。 接著套用暫存轉換。 您可以從 3 秒鐘的範圍開始，然後調整值以減少遭到調整時間的事件數目。 

緩衝事件的副作用是輸出會有**相同時間的延遲**。 您可以調整值以減少順序錯亂的事件數量，然後讓作業保持低延遲。

## <a name="get-help"></a>取得說明
如需其他協助，請參閱我們的 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [串流分析介紹](stream-analytics-introduction.md)
* [開始使用串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整串流分析作業](stream-analytics-scale-jobs.md)
* [串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
