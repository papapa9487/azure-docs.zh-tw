---
title: "針對 Azure 串流分析與事件中樞接收器進行偵錯 | Microsoft Docs"
description: "查詢最佳做法以配置串流分析作業中的事件中樞取用者群組。"
keywords: "事件中樞限制、取用者群組"
services: stream-analytics
documentationcenter: 
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
ms.openlocfilehash: ede3137de92e251f4ad020bc1ce3f041918242b2
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---

# <a name="debug-azure-stream-analytics-with-event-hub-receivers"></a>針對 Azure 串流分析與事件中樞接收器進行偵錯

您可以使用 Azure 串流分析中的 Azure 事件中樞，來內嵌或輸出作業的資料。 使用事件中樞的最佳做法是使用多個取用者群組，以確保作業延展性。 其中一個原因是在特定輸入的串流分析作業中，其讀取器數量會影響單一取用者群組中的讀取器數量。 接收器精確數量會以擴展拓撲邏輯的內部實作詳細資料為基礎。 接收器數量不會向外公開。 讀取器數量可在作業開始時或作業升級期間變更。

> [!NOTE]
> 如果在作業升級期間變更讀取器數量，暫時性警告會寫入稽核記錄中。 串流分析作業會從這些暫時性問題中自動復原。

## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>讀取器數量 (每個分割區) 超過事件中樞上限 (5 個)

讀取器數量 (每個分割區) 超過事件中樞上限 (5 個) 的情況如下：

* 多個 SELECT 陳述式︰ 如果您使用多個 SELECT 陳述式參照**相同**的事件中樞輸入，則每個 SELECT 陳述式皆會造成新的接收器建立。
* UNION︰當您使用 UNION 時，就有可能會有多個輸入參照**相同**的事件中樞和取用者群組。
* SELF JOIN︰當您使用 SELF JOIN 作業時，就可能發生參照**相同**事件中樞多次的情形。

## <a name="solution"></a>方案

以下最佳做法可減少讀取器數量 (每個分割區) 超過事件中樞上限 (5 個) 的情況。

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>使用 WITH 子句將查詢分割成多個步驟

WITH 子句會指定名為結果集的暫存，並且可由查詢中的 FROM 子句加以參照。 您可以定義單一 SELECT 陳述式執行範圍的 WITH 子句。

例如，不要使用此查詢︰

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

使用此查詢︰

```
WITH input (
   SELECT * FROM inputEventHub
) as data

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>請確定這些輸入會繫結至不同的取用者群組

如果查詢中有三個以上的輸入與同一個事件中樞取用者群組連線，請建立個別的取用者群組。 這需要建立其他串流分析輸入。


## <a name="get-help"></a>取得說明
如需其他協助，請參閱我們的 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [串流分析介紹](stream-analytics-introduction.md)
* [開始使用串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整串流分析作業](stream-analytics-scale-jobs.md)
* [串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

