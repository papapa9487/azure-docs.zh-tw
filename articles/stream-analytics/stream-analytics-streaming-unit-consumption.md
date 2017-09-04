---
title: "Azure 串流分析︰最佳化您的作業以有效率地使用串流單位 |Microsoft Docs"
description: "查詢 Azure 串流分析中關於規模與效能的最佳做法。"
keywords: "串流單位、查詢效能"
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
ms.openlocfilehash: 43dcbd474bc66b194959616611aa109f1cdec230
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---

# <a name="optimize-your-job-to-use-streaming-units-efficiently"></a>最佳化您的作業以有效率地使用串流單位

Azure 串流分析會將執行一個作業的效能「權重」彙總為串流單位 (SU)。 SU 代表用在執行作業的計算資源。 SU 會根據 CPU、記憶體，以及讀寫率的混合量值，提供一個方式來描述相關的事件處理容量。 此功能可讓您專注在查詢邏輯上，您不必知道儲存層的效能考量、不用手動配置您的作業，以及估計需要多少 CPU 核心計數來及時執行您的作業。

## <a name="how-many-sus-are-required-for-a-job"></a>一個作業需要多少 SU？

選擇特定作業所需的 SU 數量取決於輸入的磁碟分割組態以及作業內定義的查詢。 [規模] 刀鋒視窗可讓您設定正確的 SU 數目。 最好作法是配置比所需數目還多的 SU。 串流處理引擎會不惜分派額外的記憶體，針對延遲和輸送量進行最佳化。

一般情況下，最佳的作法是對不是使用 *PARTITION BY* 的查詢使用 6 個 SU 開始。 然後使用反覆嘗試的方法來決定最佳配置，這方法就是您可以在傳送代表的資料總數後修改 SU 數目，並且檢查 SU 的 %Utilization 計量。

在 Azure 串流分析開始任何處理作業前，Azure 串流分析會先將事件保留於「重新排序緩衝區」視窗中。 事件會依時間順序排列於重新排序視窗中，並會依事件的暫時排序執行後續作業。 依時間時重新排列事件可確保運算子會在設定的時間範圍內顯示在所有事件中。 這也可讓運算子執行必要處理程序和產生輸出。 這項機制的副作用是，處理作業會因重新排列視窗的持續時間而導致延遲。 該作業的記憶體使用量 (將影響 SU 耗用量) 即是此重新排列視窗的函數大小，以及其中所包含的事件數目。

> [!NOTE]
> 如果在作業升級期間變更讀取器數量，暫時性警告會寫入稽核記錄中。 串流分析作業會從這些暫時性問題中自動復原。

## <a name="common-high-memory-causes-for-high-su-usage-for-running-jobs"></a>常見的高效能記憶體會需要高 SU 使用率來執行作業

### <a name="high-cardinality-for-group-by"></a>分組依據的高基數

傳入事件的基數指定了工作的記憶體使用量。

例如，在 `SELECT count(*) from input group by clustered, tumblingwindow (minutes, 5)`，與**叢集**相關的數字是查詢基數。

若要減少高基數造成的問題，須使用 **PARTITION BY** 來增加分割區以擴展查詢。

```
Select count(*) from input
Partition By clusterid
GROUP BY clustered tumblingwindow (minutes, 5)
```

*叢集*數目在這裡是 GROUP BY 的基數。

查詢分割後，便會分散到多個節點。 如此一來，進入到每個節點的事件數目便會降低，因此也降低了重新排列緩衝區的大小。 您也應該依據 partitionid 分割事件中樞的分割區。

### <a name="high-unmatched-event-count-for-join"></a>JOIN 的高量不相符事件計數

在 JOIN 中有高量不相符的事件數目，會影響查詢的記憶體使用率。 例如，有個查詢是要找出產生點擊數的廣告曝光數：

```
SELECT id from clicks INNER JOIN,
impressions on impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10
```

在此案例中，很可能會顯示許多廣告，但只產生少數點擊數。 這類的結果就需要可將所有事件保留在同個時間範圍的作業。 範圍大小和事件出現率與記憶體耗用量成正比。 

若要減少這種情況下，須使用「PARTITION BY」來增加分割區以擴展查詢。 

查詢分割後，便會分散到多個處理節點。 如此一來，進入到每個節點的事件數目便會降低，因此也降低了重新排列緩衝區的大小。

### <a name="large-number-of-out-of-order-events"></a>大量的順序錯亂事件 

在大型時間範圍內的大量順序錯亂事件會導致「重新排序」規模變得更大。 若要減少這種情況下，須使用「PARTITION BY」來增加分割區以調整查詢規模。 查詢分割後，便會分散到多個節點。 如此一來，進入到每個節點的事件數目便會降低，因此也降低了重新排列緩衝區的大小。 


## <a name="get-help"></a>取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

