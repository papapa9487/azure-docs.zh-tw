---
title: "調整串流分析工作以增加輸送量 | Microsoft Docs"
description: "了解如何透過設定輸入資料分割、微調查詢定義，及設定工作串流處理單元來調整串流分析工作。"
keywords: "資料串流處理, 串流資料處理, 微調分析"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: f1e5e11e82d344508aa4375c42d509f96aaa1d00
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>調整 Azure 串流分析工作，以提高串流資料處理的輸送量
本文示範如何調整串流分析查詢，以增加串流分析作業的輸送量。 您將透過設定輸入分割區、調整分析查詢定義，以及計算和設定作業「串流單位」(SU)，以了解如何調整串流分析作業。 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>串流分析工作由哪些部分所組成？
串流分析工作的定義包含輸入、查詢及輸出。 輸入是指作業讀取資料流的來源。 查詢是用來轉換資料輸入資料流，而輸出是作業傳送作業結果的目的地。  

一個工作至少需要一個輸入來源來進行資料串流。 資料流輸入來源可以儲存在 Azure 事件中樞或 Azure Blob 儲存體中。 如需詳細資訊，請參閱 [Azure 串流分析簡介](stream-analytics-introduction.md)和[開始使用 Azure 串流分析](stream-analytics-real-time-fraud-detection.md)。

## <a name="partitions-in-event-hubs-and-azure-storage"></a>事件中樞和 Azure 儲存體中的分割區
調整串流分析作業需要利用輸入或輸出中的分割區。 資料分割可讓您根據分割索引鍵，將資料分成子集。 取用資料的流程 (例如串流分析作業) 可以平行取用和寫入不同的分割區，這樣可以增加輸送量。 使用串流分析時，您可以利用事件中樞和 Blob 儲存體中的分割區。 

如需分割區的詳細資訊，請參閱下列文章：

* [事件中樞功能概觀](../event-hubs/event-hubs-features.md#partitions)
* [資料分割](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="streaming-units-sus"></a>串流單位 (SU)
串流單位 (SU) 代表執行 Azure 串流分析作業所需的資源和運算能力。 SU 會根據 CPU、記憶體，以及讀寫率的混合量值，提供一個方式來描述相關的事件處理容量。 每個 SU 相當於大約 1 MB/秒的輸送量。 

選擇特定工作所需的 SU 數量，取決於輸入的分割組態和針對作業所定義的查詢。 您為作業選取的 SU 以您的配額為上限。 根據預設，對於特定區域中的所有分析作業，每個 Azure 訂用帳戶的配額上限為 50 個 SU。 若要為您的訂用帳戶增加超出此配額的 SU，請連絡 [Microsoft 支援服務](http://support.microsoft.com)。 每個作業有效的 SU 值為 1、3、6 和更大 (以 6 為增量單位)。

## <a name="embarrassingly-parallel-jobs"></a>窘迫平行作業
「窘迫平行」作業是我們在 Azure 串流分析中調整性最高的情節。 它將對於查詢執行個體之輸入的某個資料分割，連接到輸出的某個資料分割。 此平行處理原則具有下列需求︰

1. 如果查詢邏輯相依於同一個查詢執行個體所處理的相同索引鍵，則您必須確保事件會傳送至輸入的相同分割區。 對於事件中樞，這表示事件資料必須設定 **PartitionKey** 值。 或者，您可以使用分割的傳送者。 對於 Blob 儲存體，這表示事件會傳送至相同的磁碟分割資料夾。 如果查詢邏輯並不需要同一個查詢執行個體所處理的相同索引鍵，您可以忽略這項需求。 簡單的選取-投影-篩選查詢，即為此邏輯的一個例子。  

2. 當資料放在輸入端時，您必須確保查詢已分割。 所有步驟中都必須使用 **Partition By**。 您可以使用多個步驟，但全部都必須依相同的索引鍵來分割。 目前，資料分割索引鍵必須設定為 **PartitionId**，作業才能完全平行。  

3. 目前，只有事件中樞和 blob 儲存體支援已分割的輸出。 於事件中樞輸出，您必須將分割區索引鍵設定為 **PartitionId**。 對於 blob 儲存體輸出，您不需要採取任何動作。  

4. 輸入分割區的數目必須等於輸出分割區的數目。 Blob 儲存體輸出目前不支援分割區。 但是沒關係，因為它會繼承上游查詢的資料分割配置。 以下是允許完全平行作業的分割區值範例：  

   * 8 個事件中樞輸入分割區和 8 個事件中樞輸出分割區
   * 8 個事件中樞輸入分割區和 blob 儲存體輸出  
   * 8 個 blob 儲存體輸入分割區和 blob 儲存體輸出  
   * 8 個 blob 儲存體輸入分割區和 8 個事件中樞輸出分割區  

以下幾節討論一些窘迫平行的範例情節。

### <a name="simple-query"></a>簡單查詢

* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 8 個分割區的事件中樞

查詢：

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

此查詢是簡單的篩選。 因此，我們不需要擔心將傳送到事件中樞的輸入分割。 請注意，此查詢包含 **Partition By PartitionId**，因此滿足稍早的需求 #2。 對於輸出，我們必須將作業中的事件中樞輸出設定為讓分割區索引鍵設為 **PartitionId**。 最後一項檢查是確保輸入分割區數目等於輸出分割區數目。

### <a name="query-with-a-grouping-key"></a>利用群組索引鍵的查詢

* 輸入：具有 8 個分割區的事件中樞
* 輸出：Blob 儲存體

查詢：

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

此查詢含有群組索引鍵。 因此，相同的索引鍵必須由相同的查詢執行個體來處理，這表示事件必須以分割形式傳送到事件中樞。 但是，應該使用哪個索引鍵？ **PartitionId** 是作業邏輯概念。 我們真正關心的索引鍵是 **TollBoothId**，因此，事件資料的 **PartitionKey** 值應該是 **TollBoothId**。 在作法上，我們在查詢中將 **Partition By** 設定為 **PartitionId**。 因為輸出是 blob 儲存體，所以我們不需要擔心設定分割區索引鍵值，以滿足需求 #4。

### <a name="multi-step-query-with-a-grouping-key"></a>利用群組索引鍵的多重步驟查詢
* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 8 個分割區的事件中樞執行個體

查詢：

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

這個查詢有群組索引鍵，因此，相同的索引鍵必須由相同的查詢執行個體來處理。 我們可以採用上一個範例中相同的策略。 在此案例中，查詢有多個步驟。 每個步驟都有 **Partition By PartitionId** 嗎？ 是，所以查詢滿足需求 #3。 對於輸出，我們需要將分割區索引鍵設定為 **PartitionId**，如先前所述。 我們也可以看到它的分割區數目與輸入的分割區數目相同。

## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>「非」窘迫平行的範例情節

在上一節，我們示範一些窘迫平行情節。 在本節中，我們要討論的情節不符合成為窘迫平行的所有需求。 

### <a name="mismatched-partition-count"></a>不相符的分割區計數
* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 32 個分割區的事件中樞

在此案例中，查詢並不重要。 如果輸入分割區計數不符合輸出分割區計數，則拓撲不是窘迫平行。

### <a name="not-using-event-hubs-or-blob-storage-as-output"></a>不使用事件中樞或 blob 儲存體作為輸出
* 輸入：具有 8 個分割區的事件中樞
* 輸出：PowerBI

PowerBI 輸出目前不支援資料分割。 因此，此情節不是窘迫平行。

### <a name="multi-step-query-with-different-partition-by-values"></a>具有不同 Partition By 值的多重步驟查詢
* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 8 個分割區的事件中樞

查詢：

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

如您所見，第二個步驟把[TollBoothId]  當做資料分割索引鍵來使用。 此步驟和第一個步驟不同，因此需要變換一下。 

上述範例示範一些符合 (或不符合) 窘迫平行拓撲的串流分析作業。 如果符合，則可能有最大調整幅度。 對於不符合其中一個設定檔的作業，則提供未來更新時的調整指引。 現在，請在下列各節中使用一般指引。

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>計算工作的最大串流處理單元
資料流分析工作可使用的串流處理單元總數，取決於為工作定義之查詢中的步驟數目，和每個步驟的資料分割數目。

### <a name="steps-in-a-query"></a>查詢中的步驟
一個查詢可以有一或多個步驟。 每個步驟都是使用 **WITH** 關鍵字定義的子查詢。 在 **WITH** 關鍵字外的查詢 (只有一個查詢) 也視為一個步驟，例如下列查詢中的 **SELECT** 陳述式：

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

此查詢有兩個步驟。

> [!NOTE]
> 本文稍後會詳細討論此查詢。
>  

### <a name="partition-a-step"></a>分割步驟
要分割步驟必須符合下列條件：

* 必須分割輸入來源。 
* 查詢的 **SELECT** 陳述式必須讀取某個已分割的輸入來源。
* 步驟內的查詢必須有 **Partition By** 關鍵字。

分割查詢時，將會在個別的分割區群組中處理和彙總輸入事件，然後為每個群組產生輸出事件。 如果需要合併的彙總，您必須建立第二個非分割步驟來彙總。

### <a name="calculate-the-max-streaming-units-for-a-job"></a>計算工作的最大串流處理單元
所有非分割步驟合起來，可將串流分析作業調整為最多 6 個串流單元 (SU)。 若要新增 SU，必須分割步驟。 每個分割區可以有 6 個 SU。

<table border="1">
<tr><th>查詢</th><th>作業的最多 SU</th></td>

<tr><td>
<ul>
<li>查詢包含一個步驟。</li>
<li>步驟未分割。</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>輸入資料流分割時會除以 3。</li>
<li>查詢包含一個步驟。</li>
<li>步驟進行分割。</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>查詢包含兩個步驟。</li>
<li>兩個步驟都不會分割。</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>輸入資料流分割時會除以 3。</li>
<li>查詢包含兩個步驟。 輸入步驟會分割，而第二個步驟則否。</li>
<li><strong>SELECT</strong> 陳述式會讀取分割的輸入。</li>
</ul>
</td>
<td>24 (18 個用於分割的步驟 + 6 個用於非分割的步驟)</td></tr>
</table>

### <a name="examples-of-scaling"></a>調整的範例

下列查詢會計算三分鐘內通過收費站 (有三個收費亭) 的車流量。 此查詢可以調整為最多 6 個 SU。

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

若要讓查詢使用更多 SU，輸入資料流和查詢都必須分割。 因為資料流分割區設為 3，以下修改的查詢可以調整為最多 18 個 SU：

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

分割查詢時將會處理輸入事件並將其彙總在個別的資料分割群組中。 也會為每個群組產生輸出事件。 當 **GROUP BY** 欄位不是輸入資料流中的分割區索引鍵時，資料分割可能會導致某些非預期的結果。 例如，上一個查詢中的 **TollBoothId** 欄位不是 **Input1** 的分割區索引鍵。 結果是收費亭 #1 的資料可能分散在多個分割區。

串流分析會個別處理每個 **Input1** 分割區。 因此，在相同的輪轉視窗中，相同收費亭的汽車計數會建立多筆記錄。 如果輸入分割區索引鍵無法變更，可藉由新增非分割步驟來解決此問題，如下列範例所示：

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

此查詢可以調整為 24 個 SU。

> [!NOTE]
> 如果您要聯結兩個資料流，請確定以您用來建立聯結的資料行的分割區索引鍵來分割資料流。 也請確定兩個資料流中的分割區數目相同。
> 
> 

## <a name="configure-stream-analytics-streaming-units"></a>設定串流分析串流單位

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在資源清單中，尋找並開啟您想要調整的串流分析作業。
3. 在作業刀鋒視窗中，按一下 [設定] 底下的 [調整]。

    ![Azure 入口網站串流分析作業組態][img.stream.analytics.preview.portal.settings.scale]

4. 使用滑桿來設定作業的 SU。 請注意，您只能調整特定的 SU 設定。


## <a name="monitor-job-performance"></a>監視工作效能
您可以使用 Azure 入口網站來追蹤作業的輸送量：

![Azure 串流分析監視工作][img.stream.analytics.monitor.job]

計算工作負載的預期輸送量。 如果輸送量少於預期，請調整輸入分割區、調整查詢，並將 SU 新增至作業。


## <a name="visualize-stream-analytics-throughput-at-scale-the-raspberry-pi-scenario"></a>視覺化大規模串流分析輸送量：Raspberry Pi 情節
為了協助您了解串流分析作業如何調整，我們已根據來自 Raspberry Pi 裝置的輸入完成一項實驗。 此實驗讓我們看到對多個串流單位和分割區的輸送量造成的影響。

在此情節中，裝置會將感應器資料 (用戶端) 傳送到事件中樞。 串流分析會處理資料，並將警示或統計資料當作輸出傳送到另一個事件中樞。 

用戶端會以 JSON 格式傳送感應器資料。 資料輸出也是 JSON 格式。 資料如下所示：

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

下列查詢用於關閉燈光開關時傳送警示：

    SELECT AVG(lght),
     "LightOff" as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>測量輸送量

在此案例中，輸送量是串流分析在固定時間內處理的輸入資料量。 (我們測量 10 分鐘。)為了以最佳方式處理輸入資料的輸送量，資料流輸入和查詢都必須分割。 我們在查詢中包含 **COUNT()** 來測量已處理的輸入事件數目。 為了確保作業不會單純地等待輸入事件到來，輸入事件中樞的每個分割區都已預先載入大約 300 MB 的輸入資料。

下表顯示我們在增加串流單位數目時所看到的結果，以及事件中樞裡對應的分割區計數。  

<table border="1">
<tr><th>輸入資料分割</th><th>輸出資料分割</th><th>串流處理單位</th><th>持續的輸送量
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/秒</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/秒</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/秒</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/秒</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/秒</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/秒</td>
</tr>
</table>

下圖顯示 SU 和輸送量之間的關聯性視覺效果。

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


