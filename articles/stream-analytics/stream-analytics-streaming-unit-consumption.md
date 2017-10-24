---
title: "Azure 串流分析：了解及調整串流單位 | Microsoft Docs"
description: "了解哪些因素會影響 Azure 串流分析的效能。"
keywords: "串流單位、查詢效能"
services: stream-analytics
documentationcenter: 
author: JSeb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 14f73322701dffea283432bf2a25b5e6cd3e9de4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="understand-and-adjust-streaming-units"></a>了解及調整串流單位

Azure 串流分析會將執行一個作業的效能「權重」彙總為串流單位 (SU)。 SU 代表用在執行作業的計算資源。 SU 會根據 CPU、記憶體，以及讀寫率的混合量值，提供一個方式來描述相關的事件處理容量。 此功能可讓您專注在查詢邏輯上，您不必知道儲存層的效能考量、不用手動配置您的作業，以及估計需要多少 CPU 核心計數來及時執行您的作業。

為了達到低延遲的串流處理，Azure 串流分析作業會在記憶體中執行所有處理。 當記憶體用完時，串流工作將會失敗。 因此，對於生產作業來說，請務必監視串流作業的資源使用狀況，並配置足夠的資源讓作業保持全天候運作。

計量是從 0% 到 100% 的百分比數值。 對於使用量最少的串流作業來說，SU % 使用量度量通常介於 10% 到 20% 間。 建議您最好將計量保持低於 80%，以因應偶發的尖峰使用量。  您可以設定度量警示 (請參閱[這裡以設定度量警示](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/insights-alerts-portal))。



## <a name="configure-stream-analytics-streaming-units-sus"></a>設定串流分析串流單位 (SU)
1. 登入 [Azure 入口網站](http://portal.azure.com/)
2. 在資源清單中，尋找並開啟您想要調整的串流分析作業。 
3. 在作業頁面中，按一下 [設定] 底下的 [調整]。 

    ![Azure 入口網站串流分析作業組態][img.stream.analytics.preview.portal.settings.scale]
    
4. 使用滑桿來設定作業的 SU。 請注意，您只能調整特定的 SU 設定。 


## <a name="monitor-job-performance"></a>監視工作效能
您可以使用 Azure 入口網站來追蹤作業的輸送量：

![Azure 串流分析監視工作][img.stream.analytics.monitor.job]

計算工作負載的預期輸送量。 如果輸送量少於預期，請調整輸入分割區、調整查詢，並將 SU 新增至作業。


## <a name="how-many-sus-are-required-for-a-job"></a>一個作業需要多少 SU？

選擇特定作業所需的 SU 數量取決於輸入的磁碟分割組態以及作業內定義的查詢。 [調整] 頁面可讓您設定正確的 SU 數目。 最好作法是配置比所需數目還多的 SU。 串流處理引擎會不惜分派額外的記憶體，針對延遲和輸送量進行最佳化。

一般情況下，最佳的作法是對不是使用 **PARTITION BY** 的查詢使用 6 個 SU 開始。 然後使用反覆嘗試的方法來決定最佳配置，這方法就是您可以在傳送代表的資料總數後修改 SU 數目，並且檢查 SU% 使用率計量。

如需選擇正確 SU 數目的詳細資訊，請參閱頁面：[調整 Azure 串流分析工作以增加輸送量](stream-analytics-scale-jobs.md)

> [!Note]
> 選擇特定工作所需的 SU 數量，取決於輸入的分割組態和針對作業所定義的查詢。 您為作業選取的 SU 以您的配額為上限。 根據預設，對於特定區域中的所有分析作業，每個 Azure 訂用帳戶的配額上限為 200 個 SU。 若要為您的訂用帳戶增加超出此配額的 SU，請連絡 [Microsoft 支援服務](http://support.microsoft.com)。 每個作業有效的 SU 值為 1、3、6 和更大 (以 6 為增量單位)。
> 請注意，我們不建議分配 1 SU 給生產作業。 我們通常只會建議將 1 SU 作業用於原型設計和測試作業。



## <a name="factors-increasing-su-utilization"></a>增加 SU% 使用率的因素 
### <a name="stateful-query-logic"></a>具狀態查詢邏輯 
Azure 串流分析作業的其中一個獨特功能是執行具狀態的處理工作，如視窗型彙總、時態性聯結及時態性分析函式。 這些運算子每個都會保留某些狀態。 
#### <a name="windowed-aggregates"></a>視窗型彙總
視窗型彙總的狀態大小，與運算子群組中的群組數目 (基數) 成正比。 例如，在以下查詢中，與 clusterid 相關的數字是查詢基數。 

    SELECT count(*)
    FROM input 
    GROUP BY  clusterid, tumblingwindow (minutes, 5)


為了改善前一個查詢中由高基數所造成的問題，您可以將事件傳送到依據 clusterid 分割的事件中樞，透過使用 **PARTITION BY** 允許系統個別處理每個輸入分割區來向外延展查詢，如下所示︰


    SELECT count(*) 
    FROM PARTITION BY PartitionId
    GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)

一旦查詢已分割時，便會分散到多個節點。 如此一來，進入到每個節點的 clusterid 數目便會降低，因此也降低了運算子群組的基數。 

事件中樞分割區應依據群組索引鍵來分割，以避免需要進行減量步驟。 如需其他詳細資料，請參閱[這裡](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-overview)。 
#### <a name="temporal-join"></a>時態性聯結
時態性聯結的狀態大小，與聯結時態性扭擺空間內的事件數目成正比，也就是事件輸入速率乘以扭擺空間大小。 

在聯結中不相符的事件數目，會影響查詢的記憶體使用率。 下列查詢用來尋找能產生點擊率的廣告曝光項目︰

    SELECT id
    FROM clicks 
    INNER JOIN, impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.

在此範例中，可能有大量廣告顯示，但僅有少數人會點選，而且仍然需要在時間範圍中保留所有事件。 視窗大小和事件出現率與記憶體耗用程度成正比。 

若要修復這種情況，請將事件傳送到依據聯結索引鍵 (即本例中的 ID) 分割的事件中樞，透過使用 **PARTITION BY** 允許系統個別處理每個輸入分割區來向外延展查詢，如下所示︰

    SELECT id
    FROM clicks PARTITION BY PartitionId
    INNER JOIN impressions PARTITION BY PartitionId 
    ON impression.PartitionId = clocks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
</code>

一旦查詢已分割時，便會分散到多個節點。 如此一來，進入到每個節點的事件數目便會降低，因此也降低了聯結時間範圍內保留的狀態大小。 
#### <a name="temporal-analytic-function"></a>時態性分析函式
時態性分析函式的狀態大小，與事件速率乘以持續時間成正比。 修復方法與時態性聯結相似。 您可以使用 **PARTITION BY** 向外延展查詢。 

### <a name="out-of-order-buffer"></a>次序錯誤緩衝區 
使用者可以在 [事件順序] 組態窗格中設定次序錯誤緩衝區大小。 緩衝區可用來保留時間範圍持續時間的輸入，並將它們重新排序。 緩衝區的大小，與事件輸入速率乘以次序錯誤時間範圍大小成正比。 預設的時間範圍大小為 0。 

若要修復這種情況，請使用 **PARTITION BY** 向外延展查詢。 一旦查詢已分割時，便會分散到多個節點。 如此一來，進入到每個節點的事件數目便會降低，因此也降低了每個重新排列緩衝區中的事件數目。 

### <a name="input-partition-count"></a>輸入分割區計數 
每個輸入分割區的作業輸入都有一個緩衝區。 輸入分割區的數目越大，作業耗用的資源也就越多。 對於每個 SU，Azure 串流分析可以處理約 1 MB/s 的輸入，因此您可能需要讓 ASA SU 數目與事件中樞分割區的數目相符。 一般而言，1SU 作業足以供應 2 個分割區的事件中樞 (也就是最小的事件中樞)。 如果事件中樞有更多分割區，ASA 作業便會耗用更多資源，但不一定會使用事件中樞提供的額外輸送量。 對於 6SU 作業來說，您的事件中樞可能需要 4 或 8 個分割區。 在 1SU 作業中使用 16 個或更多分割區的事件中樞，通常會導致過度使用資源的結果，因此應予以避免。 

### <a name="reference-data"></a>參考資料 
ASA 中的參考資料會載入記憶體中，以供快速查閱。 針對目前的實作，每個與參考資料聯結的聯結作業都會在記憶體中保留一份參考資料，即使您多次聯結同樣的參考資料也是如此。 對於使用 **PARTITION BY** 的查詢，每個分割區都有一份參考資料，因此分割區是完全分離的。 在乘數效應之下，如果您多次聯結參考資料與多個分割區，記憶體使用量將會急速攀升。  

#### <a name="use-of-udf-functions"></a>使用 UDF 函式
當您新增 UDF 函式時，Azure 串流分析會將 JavaScript 執行階段載入記憶體。 這會影響 SU%。


## <a name="get-help"></a>取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [在 Azure 串流分析中建立可平行查詢](stream-analytics-parallelization.md)
* [調整 Azure 串流分析工作以增加輸送量](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
