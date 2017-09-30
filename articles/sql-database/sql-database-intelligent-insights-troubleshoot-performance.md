---
title: "使用 Intelligent Insights 針對 Azure SQL Database 效能問題進行疑難排解 | Microsoft Docs"
description: "Intelligent Insights 可協助您針對 Azure SQL Database 效能問題進行疑難排解。"
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: edd8c526a681c9cd5226ede6110f21c3362aaef6
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>使用 Intelligent Insights 針對 Azure SQL Database 效能問題進行疑難排解

此頁面提供透過 [Intelligent Insights](sql-database-intelligent-insights.md) 資料庫效能診斷記錄偵測到的 Azure SQL Database 效能問題相關資訊。 此診斷記錄可以傳送給 [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)、[Azure 事件中樞](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)、[Azure 儲存體](sql-database-metrics-diag-logging.md#stream-into-azure-storage)或協力廠商解決方案，以提供自訂的 DevOps 警示和報告功能。

> [!NOTE]
> 如需透過 Intelligent Insights 進行快速 Azure SQL Database 效能疑難排解的指南，請參閱本文件中的[建議的疑難排解流程](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow)流程圖。
>

## <a name="detectable-database-performance-patterns"></a>可偵測的資料庫效能模式

Intelligent Insights 會自動根據查詢執行等候時間、錯誤或逾時來偵測 Azure SQL Database 的效能問題，然後將偵測到的效能模式輸出到診斷記錄中。 下表摘要說明可偵測的效能模式：

| 可偵測的效能模式 | 輸出的詳細資料 |
| :------------------- | ------------------- |
| [達到資源限制](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | 可用資源 (DTU)、資料庫背景工作執行緒或受監視訂用帳戶上可用之資料庫登入工作階段的使用量已達到限制，因而導致 Azure SQL Database 效能問題。 |
| [工作負載增加](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | 在資料庫上偵測到工作負載增加或工作負載持續累積，因而導致 Azure SQL Database 效能問題。 |
| [記憶體壓力](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | 要求授與記憶體的背景工作角色就統計而言明顯花費相當長的時間等候記憶體配置，或是要求授與記憶體的背景工作角色有累積增加的情況，因而影響 Azure SQL Database 效能。 |
| [鎖定](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | 偵測到過多的資料庫鎖定，因而影響 Azure SQL Database 效能。 |
| [MAXDOP 增加](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | 平行處理原則的最大程度 (MAXDOP) 選項 已變更，因而影響查詢執行效率。  |
| [頁面閂鎖爭用](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | 偵測到頁面閂鎖爭用，因而影響 Azure SQL Database 效能。 多個執行緒同時嘗試存取相同的記憶體內資料緩衝頁面，因而導致等候時間增加而影響 Azure SQL Database 效能。 |
| [遺漏索引](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | 偵測到遺漏索引問題，因而影響 Azure SQL Database 效能。 |
| [新查詢](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | 偵測到新查詢，因而影響整體 Azure SQL Database 效能。  |
| [不尋常的等候統計資料](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | 偵測到不尋常的資料庫等候時間，因而影響 Azure SQL Database 效能。 |
| [TempDB 爭用](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | 多個執行緒嘗試存取相同的 TempDB 資源，因而造成瓶頸而影響 Azure SQL Database 效能。 |
| [彈性集區 DTU 不足](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | 彈性集區中的可用 eDTU 不足，因而影響 Azure SQL Database 效能。 |
| [計畫迴歸](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | 偵測到新計畫或是現有計畫的工作負載發生變更，因而影響 Azure SQL Database 效能。 |
| [資料庫範圍組態值變更](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | 資料庫上的組態發生變更，因而影響 Azure SQL Database 效能。 |
| [用戶端執行速度太慢](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | 偵測到應用程式用戶端執行速度太慢，取用 Azure SQL Database 的輸出時速度不夠快，因而影響 Azure SQL Database 效能。 |
| [定價層降級](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | 定價層降級動作減少了可用的資源，因而影響 Azure SQL Database 效能。 |

> [!TIP]
> 若要將 Azure SQL Database 的效能持續最佳化，您可以考慮啟用 [Azure SQL Database 自動調整](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning) - 這是 Azure SQL 內建智慧功能的一項獨特功能，可持續監視您的 Azure SQL Database，並自動調整索引及套用查詢執行計畫更正。
>

下節將以更詳細的方式說明先前列出的可偵測效能模式。

## <a name="reaching-resource-limits"></a>達到資源限制

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式結合了與達到可用資源限制、背景工作角色限制及工作階段限制有關的效能問題。 一旦偵測到此效能問題，診斷記錄的描述欄位就會指出效能問題是與資源、背景工作角色還是工作階段限制相關。

Azure SQL Database 上的資源通常稱為 [DTU 資源](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-what-is-a-dtu)，是由 CPU 與 I/O (資料和交易記錄 I/O) 資源的混合量值所組成。 當偵測到導致查詢效能降低的原因是達到所測量的任何資源限制時，就會認定是達到資源限制模式。

工作階段限制資源代表 Azure SQL Database 的可用並行登入次數。 當連線到 Azure SQL Database 的應用程式已達到該資料庫的可用並行登入次數時，就會認定是這個效能模式。 當應用程式嘗試使用的工作階段數超出資料庫上可用的工作階段數時，就會影響查詢效能。

達到背景工作角色限制是達到資源限制的一個特定案例，因為 DTU 使用量中並未計入可用的背景工作角色數。 達到資料庫上的背景工作角色限制會造成資源特定的等候時間增加，因而降低查詢效能。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出影響效能和資源耗用量百分比之查詢的查詢雜湊。 您可以使用此資訊作為將資料庫工作負載最佳化的起點。 尤其，您可以考慮將導致效能降低的查詢最佳化、新增索引，或是藉由更平均分配工作負載的方式將應用程式最佳化。 如果您無法降低工作負載或進行最佳化，或許可以考慮提高 Azure SQL Database 訂用帳戶的定價層，以增加可用的資源數量。

如果您已達到可用工作階段限制，可以考慮藉由降低登入資料庫次數的方式將應用程式最佳化。 如果您無法降低應用程式登入資料庫的次數，則可以考慮提高資料庫的定價層，或是也許將資料庫分割並移轉成多個資料庫，以便讓工作負載分配更平衡。

如需更多有關解決工作階段限制的建議，請參閱[如何處理 Azure SQL Database 登入次數上限的限制](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/) (英文)。 若要了解您訂用帳戶層的可用資源限制，請參閱 [Azure SQL Database 資源限制](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-resource-limits)。

## <a name="workload-increase"></a>工作負載增加

### <a name="what-is-happening"></a>發生的情況

這個效能模式所識別的是工作負載增加或其更嚴重形式 (即工作負載堆積) 所造成的問題。

這個偵測是透過組合數個計量來進行的。 所測量的基本計量會偵測與過去的工作負載基準相比，工作負載是否增加。 另一個偵測形式根據的是測量作用中背景工作執行緒是否大幅增加，亦即是否大到足以影響查詢效能。

在其更嚴重的形式中，工作負載會因 Azure SQL Database 無法處理工作負載而持續堆積。 結果就是工作負載大小持續成長，也就是發生工作負載堆積情況。 因為這個情況，工作負載等候執行的時間將變長，而這也代表了其中一個最嚴重的資料庫效能問題。 透過監視中止的背景工作執行緒數目即可偵測到這個問題。 

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出執行時間增加的查詢數目，以及對工作負載增加影響最大之查詢的查詢雜湊。 您可以使用此資訊作為將工作負載最佳化的起點，特別是針對已識別為工作負載增加之最大因素的查詢。

您也可以考慮將工作負載更平均地分配給資料庫。 此外，也可以考慮將影響效能的查詢最佳化並新增索引。 另一個可考慮的方法則可能是將工作負載分散到多個資料庫。 如果無法這麼做，您可以考慮提高 Azure SQL Database 訂用帳戶的定價層，以增加可用的資源數量。

## <a name="memory-pressure"></a>記憶體壓力

### <a name="what-is-happening"></a>發生的情況

這個效能模式表示與過去 7 天的效能基準相比，目前的資料庫效能降低，原因是記憶體壓力或其更嚴重的形式，亦即有記憶體堆積的情況。

記憶體壓力代表一種效能情況，也就是 Azure SQL Database 中有相當大量要求授與記憶體的背景工作執行緒。 這會導致高記憶體使用量情況，其中 Azure SQL Database 將無法有效率地配置記憶體給要求記憶體的所有背景工作角色。 此問題的其中一個最常見原因一方面是與 Azure SQL Database 可用的記憶體數量有關，另一方面則是與造成背景工作執行緒和記憶體壓力增加的工作負載增加有關。

記憶體壓力的更嚴重形式是記憶體堆積情況，代表的是要求授與記憶體的背景工作執行緒數目大於釋出記憶體的查詢數目。 這個要求授與記憶體的背景工作執行緒數目可能也會持續增加 (堆積)，因為引擎無法有效率地配置記憶體來滿足需求。 記憶體堆積情況代表其中一個最嚴重的資料庫效能問題。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出記憶體物件存放區詳細資料，其中含有標示為高記憶體使用量最大原因的 Clerk (即背景工作執行緒)，也會輸出相關的時間戳記。 此資訊可用來作為疑難排解的基礎。 

您可以考慮將與記憶體使用量最高之 Clerk 相關的查詢最佳化或移除。 您也可以確保不要查詢您不打算使用的資料。 理想的做法是在您的查詢中一律使用 WHERE 子句。 此外，建議您建立非叢集索引來搜尋資料，而不要掃描資料。

您也可以考慮將工作負載降低、最佳化或分散到多個資料庫。 另一個可考慮的方法則可能是將工作負載分散到多個資料庫。 如果無法這麼做，您可以考慮提高 Azure SQL Database 訂用帳戶的定價層，以增加資料庫可用的記憶體資源數量。

如需其他疑難排解建議，請參閱[記憶體授與深思：有許多名稱的神秘 SQL Server 記憶體取用者](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/) (英文)。

## <a name="locking"></a>鎖定

### <a name="what-is-happening"></a>發生的情況

這個效能模式表示與過去 7 天的效能基準相比，目前的資料庫效能降低，其中偵測到過多的資料庫鎖定。 

在現代化的 RDBMS 中，對於實作多執行緒的系統 (其中會透過儘可能執行多個同時背景工作角色和平行資料庫交易來發揮最大效能) 來說，鎖定是不可或缺的。 簡單地說，此內容中的鎖定係指內建的存取機制，其中只有單一交易能夠以獨佔方式存取所需的資料列、頁面、資料表及檔案，而不會與另一個交易競爭資源。 當鎖定資源來使用的交易已經使用完資源時，就會釋出這些資源上的鎖定，以允許其他交易存取所需的資源。 如需有關鎖定的詳細資訊，請參閱[資料庫引擎中的鎖定](https://msdn.microsoft.com/library/ms190615.aspx)。

當 SQL 引擎所執行的交易存取鎖定的資源來使用時，如果等候時間變長，這個等候時間將會導致工作負載執行效能變差。 

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出鎖定詳細資料，此資料可用來作為疑難排解的基礎。 您可以分析系統回報的鎖定查詢 (即導致鎖定效能降低的查詢)，然後將它們移除。 在某些情況下，您可能可以順利將鎖定查詢最佳化。
此問題的最簡單且最安全緩和方式就是讓交易簡短，以及減少最耗用資源之查詢的鎖定使用量。 您可以考慮將大批作業拆解成較小的作業。 理想的做法是儘可能提升查詢效率，以減少查詢鎖定使用量。 請考慮減少大型掃描，因為它們會增加死結的機率而對整體資料庫效能帶來負面影響。 針對已識別為會造成鎖定的查詢，您可以考慮建立新的索引，或是在現有的索引新增資料行來避免資料表掃描。 如需更多建議，請參閱[如何解決 SQL Server 中鎖定擴大所造成的鎖定問題](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in) (英文)。

## <a name="increased-maxdop"></a>MAXDOP 增加

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示一種情況，亦即所選查詢執行計畫的平行處理程度超出其應有的程度。 Azure SQL Database 查詢最佳化工具會藉由做出以平行方式執行查詢的決策來儘可能加快速度，以提升工作負載效能。 不過，在某些情況下，相較於使用較少平行背景工作角色 (或在某些情況下甚至是使用單一背景工作處理序) 來執行相同的查詢，處理查詢的平行背景工作角色會花費更多的時間等候彼此進行同步並合併結果。

專家系統會分析與基準期間相較之下的目前資料庫效能，然後判斷先前執行的查詢是否因查詢執行計畫的平行處理程度超出其應有的程度，而導致執行速度變得比以前慢。

Azure SQL Database 上的 MAXDOP 伺服器組態選項可用來控制能夠使用多少 CPU 核心來平行執行相同的查詢。 

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出執行持續時間因平行處理程度大於應有程度而增加之查詢的相關查詢雜湊。 此記錄也會輸出 CXP 等候時間。 此時間代表單一組合管理/協調者執行緒 (執行緒 0) 在合併結果並繼續進行之前，等候所有其他執行緒完成工作的時間。 此外，診斷記錄也會輸出效能不佳之查詢整體等候執行的等候時間。 此資訊是進行問題疑難排解的基礎。

您可以先研究將複雜查詢最佳化或簡化的可能性。 理想的做法是將大批作業拆解成較小的作業。 此外，請確定您已建立支援查詢的索引。 您也可以考慮針對已標示為效能不佳的特定查詢，手動強制執行 MAXDOP (平行處理原則的最大程度)。 透過使用 T-SQL 即可設定此作業，請參閱[設定平行處理原則的最大程度伺服器組態選項](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)。

將 MAXDOP 伺服器組態選項設定為零 (0) 作為預設值時，表示 Azure SQL Database 可以使用所有可用的邏輯 CPU 核心來平行處理執行單一查詢的執行緒。 將 MAXDOP 設定為一 (1) 時，則表示只能使用一個核心來進行單一查詢執行，實際上就是關閉平行處理原則。 您可以視每一案例情況、資料庫可用的核心及診斷記錄資訊而定，將 MAXDOP 選項調整成可解決您案例中問題的平行查詢執行核心數。

## <a name="pagelatch-contention"></a>頁面閂鎖爭用

### <a name="what-is-happening"></a>發生的情況

這個效能模式表示與過去 7 天的工作負載基準相比，目前的資料庫工作負載效能降低，原因是發生頁面閂鎖爭用。

閂鎖是 Azure SQL Database 所使用的輕量型同步處理機制，用來啟用多執行緒功能，以及確保記憶體內結構 (包括索引、資料頁面及其他內部結構) 的一致性。

Azure SQL Database 上有多種可用的閂鎖。 為了簡單起見，會使用緩衝區閂鎖來保護緩衝集區中的記憶體內頁面，以及使用 I/O 閂鎖來保護尚未載入到緩衝集區中的頁面。 每次將資料寫入到緩衝集區中的頁面或從該頁面讀取資料時，背景工作執行緒都必須先取得該頁面的緩衝區閂鎖。 每次背景工作執行緒嘗試存取記憶體內緩衝集區中尚未提供的頁面時，都會發出 I/O 要求以從儲存體載入所需的資訊，這代表的是一種更嚴重的效能降低情況。

當多個執行緒同時嘗試在相同的記憶體內結構上取得閂鎖時，就會發生頁面閂鎖爭用，而導致等候執行查詢的時間增加。 當必須從儲存體存取資料時，如果發生頁面閂鎖 I/O 爭用，這個等候時間甚至會更長而會大幅影響工作負載效能。 對於等候彼此並競爭多個 CPU 系統上資源的執行緒來說，頁面閂鎖爭用是最常見的情況。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出頁面閂鎖爭用詳細資料，此資料可用來作為進行問題疑難排解的基礎。

由於頁面閂鎖是 Azure SQL Database 的內部控制機制，因此它會自動判斷何時該使用頁面閂鎖。 因為閂鎖具有決定性行為，所以應用程式決策 (包括結構描述設計) 會影響頁面閂鎖行為。
其中一個處理閂鎖爭用的方法是使用非循序索引鍵來取代循序索引鍵，以將插入平均分散到整個索引範圍。 做法通常是在索引中建立一個按比例分配工作負載的前置資料行。 另一個可考慮的方法是資料表分割。 使用資料分割資料表上的計算資料行來建立雜湊分割配置，是一個緩和過多閂鎖爭用的常見方法。 當發生頁面閂鎖 I/O 爭用時，建議導入索引來協助緩和這個效能問題。 如需詳細資訊，請參閱[診斷和解決 SQL Server 上的閂鎖爭用](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (英文) (PDF 下載)。

## <a name="missing-index"></a>遺漏索引

### <a name="what-is-happening"></a>發生的情況

這個效能模式表示與過去 7 天的基準相比，目前的資料庫工作負載效能降低，原因是遺漏索引。

索引可用來提升查詢的效能。 它可藉由減少所需瀏覽或掃描的資料集頁數，加快存取資料表資料的速度。

透過這項偵測可識別出造成效能降低的特定查詢，針對這些查詢，建立索引將有助於提升效能。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出已識別為影響工作負載效能之查詢的查詢雜湊。 您可以考慮針對這些查詢建立索引。 您也可以考慮將這些查詢最佳化，或如果不需要這些查詢，則將其移除。 理想的效能做法是避免查詢您不使用的資料。

> [!TIP]
> 您知道 Azure 內建的智慧功能可以自動為資料庫管理效能最佳的索引嗎？
>
> 若要將 Azure SQL Database 的效能持續最佳化，建議您啟用 [Azure SQL Database 自動調整](sql-database-automatic-tuning.md) - 這是 Azure SQL 內建智慧功能的一項獨特功能，可持續監視您的 Azure SQL Database，並自動為資料庫調整及建立索引。
>

## <a name="new-query"></a>新查詢

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示偵測到新查詢，而與過去 7 天效能基準相比，此查詢的效能不佳且影響工作負載效能。

撰寫效能良好的查詢有時會是相當具有挑戰性的工作。 如需有關撰寫查詢的詳細資訊，請參閱[撰寫 SQL 查詢](https://msdn.microsoft.com/library/bb264565.aspx) (英文)，若要將現有的查詢效能最佳化，請參閱[查詢微調](https://msdn.microsoft.com/library/ms176005.aspx)。

### <a name="troubleshooting"></a>疑難排解

診斷記錄最多會輸出兩個 (2) 耗用最多 CPU 資源之查詢的資訊，包括其查詢雜湊。 由於偵測到的查詢正影響工作負載效能，因此您可以考慮將查詢最佳化。 理想的做法是不要擷取您不使用的資料。 搭配 WHERE 子句來使用查詢也是建議的做法。 此外，也建議您將複雜的查詢簡化及拆解成較小的查詢。 將大批的查詢拆解成較小批的查詢也是可考慮的良好做法。 通常，緩和此效能問題的理想做法是為新查詢導入索引。

您可以考慮使用 [Azure SQL Database 查詢效能深入解析](sql-database-query-performance.md)。

## <a name="unusual-wait-statistic"></a>不尋常的等候統計資料

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示與過去 7 天的工作負載基準相比，工作負載效能降低，其中發現有效能不佳的查詢。

在此情況下，系統無法將效能不佳的查詢歸類到任何其他可偵測的標準效能類別底下，但已偵測到造成衰退的等候統計資料，因此將這些查詢視為具有「不尋常的等候統計資料」的查詢，其中也會顯示造成衰退的不尋常等候統計資料。 

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出有關不尋常等候時間詳細資料的資訊、受影響之查詢的查詢雜湊，以及等候時間。

在此情況下，由於系統無法順利找出效能不佳查詢的根本原因，因此效能不佳查詢的相關診斷資訊可作為進行手動疑難排解的理想起點。 您可以考慮將這些查詢的效能最佳化。 通常，理想的做法是不要擷取您不使用的資料，以及將複雜的查詢簡化及拆解成較小的查詢。 如需有關將查詢效能最佳化的詳細資訊，請參閱[查詢微調](https://msdn.microsoft.com/library/ms176005.aspx)。

## <a name="tempdb-contention"></a>TempDB 爭用

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示一種資料庫效能情況，亦即嘗試存取 TempDB 資源的執行緒發生瓶頸 (此情況與 I/O 無關)。 此效能問題的典型案例就是有數百個並行查詢，這些查詢都建立、使用然後卸除小型 TempDB 資料表 (也就是 TempDB 資料表)。 系統已偵測到使用相同 TempDB 資料表的並行查詢數目就統計而言明顯增加，以致與過去 7 天效能基準相比，影響資料庫效能。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出 TempDB 爭用詳細資料，此資料可用來作為進行效能問題疑難排解的起點。 您可以採取一些措施來緩解這種爭用情況，並提升整體工作負載的輸送量。 您可以停止使用暫存資料表。 您也可以考慮使用經記憶體最佳化的資料表，請參閱[經記憶體最佳化的資料表簡介](https://docs.microsoft.com/en-us/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)。 

## <a name="elastic-pool-dtu-shortage"></a>彈性集區 DTU 不足

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示與過去 7 天的基準相比，目前的資料庫工作負載效能降低，原因是您訂用帳戶彈性集區中的可用 DTU 不足。 

Azure SQL Database 上的資源通常稱為 [DTU 資源](sql-database-what-is-a-dtu.md)，是由 CPU 與 I/O (資料和交易記錄 I/O) 資源的混合量值所組成。 [Azure 彈性集區資源](sql-database-elastic-pool.md)可用來作為多個資料庫間共用的可用 eDTU 資源集區，以供進行調整之用。 當您彈性集區中的可用 eDTU 資源並未大到足以支援集區中的所有資料庫時，系統就會偵測到「彈性集區 DTU 不足」效能問題。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出彈性集區的相關資訊、列出 DTU 取用量最高的資料庫，以及提供取用量最高之資料庫所使用的集區 DTU 百分比。

由於這個效能情況與使用彈性集區中相同 eDTU 集區的多個資料庫相關，因此疑難排解步驟的焦點必須放在 DTU 取用量最高的資料庫上。 您可以考慮降低取用量最高之資料庫上的工作負載，包括將這些資料庫上取用量最高的查詢最佳化。 您也可以確保不要查詢您不使用的資料。 另一個方法是將使用 DTU 取用量最高之資料庫的應用程式最佳化，然後將工作負載重新分散到多個資料庫。

如果您無法將 DTU 取用量最高之資料庫上的目前工作負載降低和最佳化，則可以考慮提升彈性集區定價層。 這個提升將可增加彈性集區中的可用的 DTU。

## <a name="plan-regression"></a>計畫迴歸

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式代表一種情況，亦即 Azure SQL Database 開始使用次佳的查詢執行計畫。 次佳的計畫通常會造成查詢的執行時間增加，導致目前及其他查詢的等候時間變長。

Azure SQL Database 會判斷出查詢執行成本最低的查詢執行計畫。 不過，隨著查詢類型和工作負載發生變更，有時現有的計畫會變得不再有效率，或 Azure SQL Database 可能並未做出理想的評估。 為了更正這種狀況，您可以手動強制執行查詢執行計畫。

這個可偵測的效能模式結合了三種不同的計畫迴歸案例：新計畫迴歸、舊計畫迴歸，以及現有計畫變更工作負載。 診斷記錄的 &#8220;*details*&#8221; 屬性中會提供所發生特定計畫迴歸的類型。

新計畫迴歸情況所指的狀態是 Azure SQL Database 開始執行效率比舊計畫差的新查詢執行計畫。 舊計畫迴歸情況所指的狀態是 Azure SQL Database 從使用較有效率的新計畫切換成使用效率比新計畫差的舊計畫。 現有計畫變更工作負載迴歸所指的狀態是新、舊兩個計畫不斷被更改，而較朝向效能不佳的計畫方向發展。

如需有關計畫迴歸的詳細資訊，請參閱[什麼是 SQL Server 中的計畫迴歸](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/) (英文)。 

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出可用來作為此情況之效能疑難排解基礎的查詢雜湊、良好計畫識別碼、不良計畫識別碼及查詢識別碼。

您可以分析對特定的查詢 (可以藉由系統提供的查詢雜湊來識別) 而言哪個計畫的效能較佳。 判斷出哪個計畫較適用於您的查詢之後，您即可手動強制執行該計畫。 如需詳細資訊，請參閱 [SQL Server 如何防止計畫迴歸](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/) (英文)。

> [!TIP]
> 您知道 Azure 內建的智慧功能可以自動為資料庫管理效能最佳的查詢執行計畫嗎？
>
> 若要將 Azure SQL Database 的效能持續最佳化，建議您啟用 [Azure SQL Database 自動調整](sql-database-automatic-tuning.md) - 這是 Azure SQL 內建智慧功能的一項獨特功能，可持續監視您的 Azure SQL Database，並自動為資料庫調整及建立效能最佳的查詢執行計畫。
>

## <a name="database-scoped-configuration-value-change"></a>資料庫範圍組態值變更

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示一種情況，亦即資料庫範圍組態已發生變更，而此情況導致偵測到與過去 7 天的資料庫工作負載行為相比，效能呈現衰退。 這代表最近對資料庫範圍組態所做的變更似乎對資料庫效能沒有幫助。

您可以為每一個個別的資料庫設定資料庫範圍組態變更。 使用這個組態時會依個案使用，以將您資料庫的個別效能最佳化。 以下是可針對每一個個別資料庫設定的選項：MAXDOP、LEGACY_CARDINALITY_ESTIMATION、PARAMETER_SNIFFING、QUERY_OPTIMIZER_HOTFIXES 及 CLEAR PROCEDURE_CACHE。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出最近所進行且與過去 7 天的工作負載行為相比，導致效能降低的資料庫範圍組態變更。 您可以考慮將組態變更還原成先前的值，或也許逐一調整值，直到達到想要的效能等級為止。 您也可以考慮從具有滿意效能的類似資料庫複製資料庫範圍組態值。 如果無法針對效能進行疑難排解，您可以考慮還原成預設的 Azure SQL Database 預設值，然後嘗試從此基準開始進行微調。

如有關將資料庫範圍組態最佳化的進一步詳細資料，以及有關變更組態的 T-SQL 語法，請參閱 [Alter database scoped configuration (Transact-SQL)](https://msdn.microsoft.com/en-us/library/mt629158.aspx)。

## <a name="slow-client"></a>用戶端執行速度太慢

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示一種情況，亦即使用 Azure SQL Database 的用戶端在取用資料庫輸出時，速度比資料庫傳送結果的速度慢。 由於 Azure SQL Database 並不會將所執行查詢的結果暫存在緩衝區中，因此它會減緩速度並等候用戶端取用所傳輸的查詢輸出，然後才繼續進行。 這種情況也可能與網路速度太慢有關，也就是無法將 Azure SQL Database 的輸出以夠快的速度傳輸給取用的用戶端。

只有當偵測到與過去 7 天的資料庫工作負載行為相比，效能降低時，才會產生此情況。 這可確保只有在統計上與先前的效能行為相比，效能明顯降低時，才會偵測到此效能問題。

### <a name="troubleshooting"></a>疑難排解

這個可偵測的效能模式表示一種用戶端情況，亦即在用戶端應用程式或用戶端網路需要進行疑難排解。 診斷記錄會輸出查詢雜湊，以及過去 2 小時內似乎等候用戶端取用它們等得最久的等候時間。 這些資訊可用來作為您進行疑難排解的基礎。

您可以考慮將應用程式的效能最佳化以取用這些查詢。 您也可以考慮可能的網路延遲問題。 由於效能降低問題所根據的是過去 7 天效能基準的變化，因此您可以調查最近是否有發生可能造成此效能衰退事件的應用程式變更或網路情況變更。 

## <a name="pricing-tier-downgrade"></a>定價層降級

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示一種情況，亦即 Azure SQL Database 的定價層降級。 由於資料庫可用的資源 (DTU) 減少，因此系統偵測到與過去 7 天的基準相比，目前的資料庫效能降低。

此外，也可能是發生 Azure SQL Database 訂用帳戶的定價層降級，然後在短期間內又升級到較高層級的情況。 這表示是暫時性的效能降低偵測，此偵測在診斷記錄的詳細資料區段中會輸出成定價層降級和升級。

### <a name="troubleshooting"></a>疑難排解

如果您已降低定價層，因而減少 Azure SQL Database 可用的 DTU，但對效能感到滿意，目前便無須採取任何動作。 如果在降低定價層之後，您對 Azure SQL Database 的效能感到不滿意，則可以考慮降低資料庫工作負載，或考慮將定價層提升到較高的層級。

## <a name="recommended-troubleshooting-flow"></a>建議的疑難排解流程

請依照下方流程圖所提供的建議方法，使用 Intelligent Insights 來針對效能問題進行疑難排解。

透過 Azure 入口網站瀏覽至「Azure SQL 分析」來存取 Intelligent Insights。 嘗試找出傳入的效能警示並按一下該警示。 了解偵測頁面上發生什麼事。 觀察所提供的問題「根本原因分析」、查詢文字、查詢時間趨勢，以及事件演進情況。 使用 Intelligent Insights 針對解決效能問題所提出的建議來嘗試緩和該問題。 

[![疑難排解流程圖](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> 按一下流程圖即可下載此流程圖的 PDF 版本。

Intelligent Insights 通常需要 1 小時的時間來執行效能問題的根本原因分析。 如果您在 Intelligent Insights 中找不到您的問題 (在大多數情況下，這些會是 1 小時內的問題)，並且此問題對您而言相當重要，請使用「查詢資料存放區」(QDS) 來手動識別效能問題的根本原因。 如需詳細資訊，請參閱[使用查詢存放區來監視效能](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)。

## <a name="next-steps"></a>後續步驟
- 了解 [Intelligent Insights](sql-database-intelligent-insights.md) 概念
- 使用 [Intelligent Insights Azure SQL Database 效能診斷記錄](sql-database-intelligent-insights-use-diagnostics-log.md)
- [使用 Azure SQL 分析來監視 Azure SQL Database](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)
- 了解如何[收集並取用來自 Azure 資源的記錄資料](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

