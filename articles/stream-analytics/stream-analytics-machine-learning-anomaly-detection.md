---
title: "Azure 使用量異常偵測指南 (預覽) | Microsoft Docs"
description: "使用串流分析和機器學習方法來偵測異常。"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 7ab489f6ae7da2640ba199b20e7727da60497918
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---

# <a name="using-the-anomalydetection-operator"></a>使用 ANOMALYDETECTION 運算子

> [!IMPORTANT]
> 這項功能為預覽狀態。 我們不建議用於生產環境。

**ANOMALYDETECTION** 運算子可用來偵測事件資料流中的異常。
例如，長時間可用記憶體緩慢減少可能表示記憶體流失，或範圍內穩定的 Web 服務要求數量可能有急劇的增加或減少。

它會檢查特定時間內以下類型的異常：

- 雙向層級變更
- 緩慢的正值趨勢
- 緩慢的負值趨勢

使用 **LIMIT DURATION** 子句來限制目前需要查看的事件要回溯記錄的時間間隔。 **ANOMALYDETECTION** 可使用 **WHEN** 子句選擇性地僅限於符合特定屬性或條件的事件。

也可以根據 **PARTITION BY** 子句中指定的索引鍵，選擇性地單獨處理事件群組。 每個分割區中會單獨發生訓練和預測。

## <a name="syntax"></a>語法

`ANOMALYDETECTION(\<scalar_expression\>) OVER ([PARTITION BY \<partition key\>] LIMIT DURATION(\<unit\>, \<length\>) [WHEN boolean_expression])` 


## <a name="example-usage"></a>使用方式範例

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id \> 100) FROM input`|


## <a name="arguments"></a>引數

- **scalar_expression**

  執行異常偵測的純量運算式。 其是浮點數的運算式或傳回單一 (純量) 值的 bigint 類型。 不允許萬用字元運算式 **\***。 **scalar_expression** 不能包含其他分析函數或外部函數。

- **OVER ( [ partition_by_clause ] limit_duration_clause [when_clause])**

- **partition_by_clause** 

  `PARTITION BY \<partition key\>` 子句會將學習和訓練劃分為單獨的分割區。 換句話說，`\<partition key\>` 的每個值會使用單獨的模型，只有具備該值的事件才會用於該模型中的學習與訓練。 例如，

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

  將僅對應其他相同感應器的讀數來訓練和評分某一讀數。

- **limit_duration 子句** DURATION(\<unit\>, \<length\>)

  指定 **ANOMALYDETECTION** 計算中會考量多少目前事件的記錄。 如需支援單位及其縮寫的詳細說明，請參閱 DATEDIFF。

- **when_clause** 

  指定 **ANOMALYDETECTION** 計算中會考量之事件的布林條件。

## <a name="return-types"></a>傳回型別

函數會傳回包含所有三個分數做為輸出的記錄。 與不同類型的異常偵測器相關聯的屬性稱為：

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

若要從記錄中擷取個別值，請使用 **GetRecordPropertyValue** 函數。 例如：

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) \> 3.25` 


當其中一個異常分數超過閾值時，會檢測到特定類型的異常。 閾值可以是任何浮點數 \>= 0。 閾值是敏感度與信賴之間的取捨。 例如，較低的閾值會讓偵測對於變化更加敏感並產生更多警示，而較高的閾值讓偵測較不敏感而更有信心，但會遮罩某些異常。 要使用的確切閾值視情況而定。 沒有上限，但建議的範圍是 3.25-5。

## <a name="algorithm"></a>演算法

**ANOMALYDETECTION** 使用滑動視窗語意，這表示每個輸入函數的事件會執行計算，並產生該事件的分數。 計算是根據 Exchangeability Martingales，藉由檢查事件值分佈是否已更改來運作。 如果是的話，即是已偵測到潛在的異常。 傳回的分數表示該異常的信賴等級。 做為內部最佳化，**ANOMALYDETECTION** 會根據 *d* 至 *2d* 的事件計算異常分數，其中 *d* 是指定的偵測視窗大小。

**ANOMALYDETECTION** 預期輸入時間序列是統一的。 事件資料流可藉由輪轉彙總或跳動視窗來進行統一。 在事件之間的間隙總是小於彙總視窗的情況下，輪轉視窗足以讓時間序列一致。 當間隙可以更大時，可以透過使用跳動視窗重複最後一個值來填滿間隙。 接下來的範例可處理這兩種案例。 目前無法跳過 `FillInMissingValuesStep` 步驟。 沒有這個步驟將導致編譯錯誤。

## <a name="performance-guidance"></a>效能指南

- 將 6 SU 用於作業中。 
- 傳送事件至少間隔 1 秒。
- 使用 **ANOMALYDETECTION** 函數的非資料分割查詢可能在平均計算延遲大約 25 毫秒的情況下產生結果。
- 隨著計算次數的增加，資料分割查詢所經歷的延遲與分割數略有不同。 不過，延遲與所有分割中可比較事件總數的非資料分割情況大致相同。
- 讀取非即時資料時，會快速內嵌大量資料。 目前處理此資料明顯比較慢。 這種情況下的延遲是隨著視窗中資料點數目 (而非視窗大小或事件間隔本身) 呈線性增加。 若要降低非即時情況的延遲，請考慮使用較小的視窗大小。 或者，請考慮從目前時間開始您的作業。 非資料分割查詢中延遲的一些範例： 
    - 偵測視窗中的 60 個資料點可能會導致 3MBps 輸送量出現 10 秒的延遲時間。 
    - 在 600 個資料點時，0.4MBps 的輸送量可能會達到大約 80 秒的延遲時間。

## <a name="example"></a>範例

如果偵測到異常，下列查詢可用於輸出警示。
當輸入資料流不統一時，彙總步驟可協助將其轉換成統一的時間序列。 此範例會使用 **AVG**，但特定類型的彙總視使用者情況而定。 此外，當時間序列具有大於彙總視窗的間隙時，時間序列中不會有任何事件觸發異常偵測 (根據滑動視窗語意)。 因此，當下一個事件來臨，將打破一致性的假設。 在這種情況下，我們需要填滿時間序列間隙的方法。 其中一個方法是在每個跳動視窗中採用最後一個事件，如下所示。

如先前所述，目前請勿跳過 `FillInMissingValuesStep` 步驟。 省略該步驟將導致編譯錯誤。

    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) \>= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) \>=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) \>=
       3.25

## <a name="references"></a>參考

* [異常偵測 – 使用 Machine Learning 以偵測時間序列資料中的異常](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Machine Learning 異常偵測 API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [時間序列異常偵測](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="get-support"></a>取得支援
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


