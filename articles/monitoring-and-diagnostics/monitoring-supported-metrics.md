---
title: "Azure 監視器計量 - 每個資源類型所支援的計量 | Microsoft Docs"
description: "AAzure 監視器每一個資源類型的可用計量清單。"
author: anirudhcavale
manager: ashwink
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: ancav
ms.openlocfilehash: 05830547a5b8a24a59571edf6dd44d101b660189
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="supported-metrics-with-azure-monitor"></a>支援 Azure 監視器的計量
Azure 監視器提供數種與計量進行互動的方式，包括在入口網站中製作計量圖表、透過 REST API 存取計量，或使用 PowerShell 或 CLI 查詢計量。 以下是目前可供 Azure 監視器計量管線使用的所有計量完整清單。

> [!NOTE]
> 其他計量可在入口網站中或使用舊版 API 提供。 此清單只包含使用彙總 Azure 監視器計量管線時可供使用的計量。 若要查詢並存取具有維度的計量，請使用 [2017-05-01-preview api-version](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions)
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|平均值|QPU。 範圍 0-100 (S1)、0-200 (S2) 和 0-400 (S4)|無維度|
|memory_metric|記憶體|位元組|平均值|記憶體。 範圍 0-25 GB (S1)、0-50 GB (S2) 和 0-100 GB (S4)|無維度|
|TotalConnectionRequests|連線要求的總計|Count|平均值|連線要求的總計。 這些是達到的。|無維度|
|SuccessfullConnectionsPerSec|每秒連線成功的次數|每秒計數|平均值|成功完成連線的速率。|無維度|
|TotalConnectionFailures|連線失敗的總計|Count|平均值|連線嘗試失敗的總計。|無維度|
|CurrentUserSessions|目前的使用者工作階段|Count|平均值|目前建立的使用者工作階段數目。|無維度|
|QueryPoolBusyThreads|查詢集區忙碌執行緒|Count|平均值|查詢執行緒集區中的忙碌執行緒數目。|無維度|
|CommandPoolJobQueueLength|命令集區作業佇列長度|Count|平均值|命令執行緒集區佇列中的作業數目。|無維度|
|ProcessingPoolJobQueueLength|處理集區作業佇列長度|Count|平均值|處理執行緒集區佇列中的非 I/O 作業數目。|無維度|
|CurrentConnections|連接：目前的連接|Count|平均值|目前已建立的用戶端連接數目。|無維度|
|CleanerCurrentPrice|記憶體︰清除工具目前價格|Count|平均值|記憶體目前的價格 ($/位元組/時間)，並正規化為 1000。|無維度|
|CleanerMemoryShrinkable|記憶體︰可壓縮的清除器記憶體|位元組|平均值|背景清除器將清除的記憶體數量，以 KB 為單位。|無維度|
|CleanerMemoryNonshrinkable|記憶體︰不可壓縮的清除器記憶體|位元組|平均值|背景清除器將不會清除的記憶體數量，以 KB 為單位。|無維度|
|MemoryUsage|記憶體：記憶體使用量|位元組|平均值|伺服器處理序用於計算清除器記憶體價格的記憶體使用量。 等於計數器 Process\PrivateBytes 加上記憶體對應的資料大小，忽略 xVelocity 記憶體內部分析引擎 (VertiPaq) 在超出 xVelocity 引擎記憶體限制外對應或配置的任何記憶體。|無維度|
|MemoryLimitHard|記憶體︰固定記憶體限制|位元組|平均值|組態檔中的固定記憶體限制。|無維度|
|MemoryLimitHigh|記憶體︰記憶體上限|位元組|平均值|來自組態檔的記憶體上限。|無維度|
|MemoryLimitLow|記憶體︰記憶體下限|位元組|平均值|來自組態檔的記憶體下限。|無維度|
|MemoryLimitVertiPaq|記憶體︰記憶體限制 VertiPaq|位元組|平均值|來自組態檔的記憶體內部限制。|無維度|
|Quota|記憶體︰配額|位元組|平均值|目前的記憶體配額，以位元組為單位。 記憶體配額也就是指授與使用的記憶體，或是保留的記憶體。|無維度|
|QuotaBlocked|記憶體︰封鎖的配額|Count|平均值|在釋放其他記憶體配額之前，目前已封鎖的配額要求數目。|無維度|
|VertiPaqNonpaged|記憶體︰未分頁的 VertiPaq|位元組|平均值|工作集中已封鎖來供記憶體內部引擎使用的記憶體位元組。|無維度|
|VertiPaqPaged|記憶體︰分頁的 VertiPaq|位元組|平均值|可供記憶體內部資料使用的分頁記憶體位元組。|無維度|
|RowsReadPerSec|處理︰每秒讀取的資料列|每秒計數|平均值|從所有關聯式資料庫讀取資料列的速率。|無維度|
|RowsConvertedPerSec|處理︰每秒轉換的資料列|每秒計數|平均值|處理期間資料列轉換的速率。|無維度|
|RowsWrittenPerSec|處理︰每秒寫入的資料列|每秒計數|平均值|處理期間資料列寫入的速率。|無維度|
|CommandPoolBusyThreads|執行緒︰命令集區的忙碌執行緒數|Count|平均值|命令執行緒集區中的忙碌執行緒數。|無維度|
|CommandPoolIdleThreads|執行緒︰命令集區的閒置執行緒數|Count|平均值|命令執行緒集區中的閒置執行緒數。|無維度|
|LongParsingBusyThreads|執行緒︰完整剖析的忙碌執行緒數|Count|平均值|完整剖析執行緒集區中的忙碌執行緒數目。|無維度|
|LongParsingIdleThreads|執行緒︰完整剖析的閒置執行緒數|Count|平均值|完整剖析執行緒集區中的閒置執行緒數目。|無維度|
|LongParsingJobQueueLength|執行緒︰完整剖析的作業佇列長度|Count|平均值|完整剖析執行緒集區佇列中的作業數目。|無維度|
|ProcessingPoolBusyIOJobThreads|執行緒︰處理集區的忙碌 I/O 作業執行緒數|Count|平均值|處理執行緒集區中執行 I/O 作業的執行緒數目。|無維度|
|ProcessingPoolBusyNonIOThreads|執行緒︰處理集區的忙碌非 I/O 執行緒數|Count|平均值|處理執行緒集區中執行非 I/O 作業的執行緒數目。|無維度|
|ProcessingPoolIOJobQueueLength|執行緒：處理集區 I/O 作業佇列長度|Count|平均值|處理執行緒集區佇列中的 I/O 作業數目。|無維度|
|ProcessingPoolIdleIOJobThreads|執行緒︰處理集區的閒置 I/O 作業執行緒數|Count|平均值|處理執行緒集區中 I/O 作業的閒置執行緒數目。|無維度|
|ProcessingPoolIdleNonIOThreads|執行緒︰處理集區的閒置非 I/O 執行緒數|Count|平均值|處理執行緒集區中專供非 I/O 作業使用的閒置執行緒數目。|無維度|
|QueryPoolIdleThreads|執行緒︰查詢集區的閒置執行緒數|Count|平均值|處理執行緒集區中 I/O 作業的閒置執行緒數目。|無維度|
|QueryPoolJobQueueLength|執行緒︰查詢集區的作業佇列長度|Count|平均值|查詢執行緒集區佇列中的作業數目。|無維度|
|ShortParsingBusyThreads|執行緒︰簡短剖析的忙碌執行緒數|Count|平均值|簡短剖析執行緒集區中的忙碌執行緒數目。|無維度|
|ShortParsingIdleThreads|執行緒︰簡短剖析的閒置執行緒數|Count|平均值|簡短剖析執行緒集區中的閒置執行緒數目。|無維度|
|ShortParsingJobQueueLength|執行緒︰簡短剖析的作業佇列長度|Count|平均值|簡短剖析執行緒集區佇列中的作業數目。|無維度|
|memory_thrashing_metric|記憶體猛移|百分比|平均值|記憶體猛移的平均值。|無維度|
|mashup_engine_qpu_metric|M 引擎 QPU|Count|平均值|混搭引擎處理序的 QPU 使用量|無維度|
|mashup_engine_memory_metric|M 引擎記憶體|位元組|平均值|混搭引擎處理序的記憶體使用量|無維度|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|TotalRequests|閘道要求總數|Count|總計|閘道器要求數目|位置，主機名稱|
|SuccessfulRequests|成功的閘道要求|Count|總計|成功閘道器要求數目|位置，主機名稱|
|UnauthorizedRequests|未經授權閘道器要求|Count|總計|未經授權閘道器要求數目|位置，主機名稱|
|FailedRequests|失敗的閘道要求|Count|總計|閘道要求中的失敗數目|位置，主機名稱|
|OtherRequests|其他閘道器要求|Count|總計|其他閘道器要求數目|位置，主機名稱|
|Duration|閘道要求的整體持續期間|毫秒|平均值|閘道要求的整體持續期間，以毫秒為單位|位置，主機名稱|
|容量|容量 (預覽)|百分比|最大值|ApiManagement 服務的使用率衡量標準|位置|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|TotalJob|工作總數|Count|總計|工作總數|無維度|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|CoreCount|專用核心計數|Count|總計|Batch 帳戶中的專用核心總數|無維度|
|TotalNodeCount|專用節點計數|Count|總計|Batch 帳戶中的專用節點總數|無維度|
|LowPriorityCoreCount|LowPriority 核心計數|Count|總計|Batch 帳戶中的低優先順序核心總數|無維度|
|TotalLowPriorityNodeCount|低優先順序節點計數|Count|總計|Batch 帳戶中的低優先順序節點總數|無維度|
|CreatingNodeCount|建立節點計數|Count|總計|正在建立的節點數目|無維度|
|StartingNodeCount|啟動節點計數|Count|總計|啟動的節點數目|無維度|
|WaitingForStartTaskNodeCount|等候啟動工作節點計數|Count|總計|等待啟動工作完成的節點數目|無維度|
|StartTaskFailedNodeCount|啟動工作失敗的節點計數|Count|總計|啟動工作失敗的節點數目|無維度|
|IdleNodeCount|閒置的節點計數|Count|總計|閒置節點的數目|無維度|
|OfflineNodeCount|離線節點計數|Count|總計|離線節點的數目|無維度|
|RebootingNodeCount|重新啟動節點計數|Count|總計|重新啟動節點的數目|無維度|
|RebootingNodeCount|重新安裝映像節點計數|Count|總計|重新安裝映像的節點數目|無維度|
|RunningNodeCount|執行節點計數|Count|總計|執行節點的數目|無維度|
|LeavingPoolNodeCount|離開集區節點計數|Count|總計|離開集區的節點數目|無維度|
|UnusableNodeCount|無法使用的節點計數|Count|總計|無法使用的節點數目|無維度|
|PreemptedNodeCount|先占節點計數|Count|總計|先占節點的數量|無維度|
|TaskStartEvent|工作啟動的事件|Count|總計|已啟動的工作總次數|無維度|
|TaskCompleteEvent|工作完成事件|Count|總計|已完成的工作總次數|無維度|
|TaskFailEvent|工作失敗事件|Count|總計|失敗狀態中已完成的工作總數|無維度|
|PoolCreateEvent|集區建立事件|Count|總計|已建立集區的總數|無維度|
|PoolResizeStartEvent|調整集區大小啟動事件|Count|總計|已啟動的調整集區大小總次數|無維度|
|PoolResizeCompleteEvent|調整集區大小完成事件|Count|總計|已完成的調整集區大小總次數|無維度|
|PoolDeleteStartEvent|集區刪除啟動事件|Count|總計|已啟動的集區刪除總次數|無維度|
|PoolDeleteCompleteEvent|集區刪除完成事件|Count|總計|已完成的集區刪除總次數|無維度|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|connectedclients|連線的用戶端|Count|最大值||無維度|
|totalcommandsprocessed|總作業數|Count|總計||無維度|
|cachehits|快取點擊|Count|總計||無維度|
|cachemisses|快取遺漏|Count|總計||無維度|
|getcommands|取得|Count|總計||無維度|
|setcommands|設定|Count|總計||無維度|
|evictedkeys|收回的金鑰|Count|總計||無維度|
|totalkeys|索引鍵總計|Count|最大值||無維度|
|expiredkeys|到期的金鑰|Count|總計||無維度|
|usedmemory|已使用的記憶體|位元組|最大值||無維度|
|usedmemoryRss|已用的記憶體 RSS|位元組|最大值||無維度|
|serverLoad|伺服器負載|百分比|最大值||無維度|
|cacheWrite|快取寫入|每秒位元組|最大值||無維度|
|cacheRead|快取讀取|每秒位元組|最大值||無維度|
|percentProcessorTime|CPU|百分比|最大值||無維度|
|connectedclients0|連線的用戶端 (分區 0)|Count|最大值||無維度|
|totalcommandsprocessed0|總作業數 (分區 0)|Count|總計||無維度|
|cachehits0|快取點擊 (分區 0)|Count|總計||無維度|
|cachemisses0|快取遺漏 (分區 0)|Count|總計||無維度|
|getcommands0|取得 (分區 0)|Count|總計||無維度|
|setcommands0|設定 (分區 0)|Count|總計||無維度|
|evictedkeys0|收回的金鑰 (分區 0)|Count|總計||無維度|
|totalkeys0|金鑰總數 (分區 0)|Count|最大值||無維度|
|expiredkeys0|到期的金鑰 (分區 0)|Count|總計||無維度|
|usedmemory0|已用的記憶體 (分區 0)|位元組|最大值||無維度|
|usedmemoryRss0|已用的記憶體 RSS (分區 0)|位元組|最大值||無維度|
|serverLoad0|伺服器負載 (分區 0)|百分比|最大值||無維度|
|cacheWrite0|快取寫入 (分區 0)|每秒位元組|最大值||無維度|
|cacheRead0|快取讀取 (分區 0)|每秒位元組|最大值||無維度|
|percentProcessorTime0|CPU (分區 0)|百分比|最大值||無維度|
|connectedclients1|連線的用戶端 (分區 1)|Count|最大值||無維度|
|totalcommandsprocessed1|總作業數 (分區 1)|Count|總計||無維度|
|cachehits1|快取點擊 (分區 1)|Count|總計||無維度|
|cachemisses1|快取遺漏 (分區 1)|Count|總計||無維度|
|getcommands1|取得 (分區 1)|Count|總計||無維度|
|setcommands1|設定 (分區 1)|Count|總計||無維度|
|evictedkeys1|收回的金鑰 (分區 1)|Count|總計||無維度|
|totalkeys1|金鑰總數 (分區 1)|Count|最大值||無維度|
|expiredkeys1|到期的金鑰 (分區 1)|Count|總計||無維度|
|usedmemory1|已用的記憶體 (分區 1)|位元組|最大值||無維度|
|usedmemoryRss1|已用的記憶體 RSS (分區 1)|位元組|最大值||無維度|
|serverLoad1|伺服器負載 (分區 1)|百分比|最大值||無維度|
|cacheWrite1|快取寫入 (分區 1)|每秒位元組|最大值||無維度|
|cacheRead1|快取讀取 (分區 1)|每秒位元組|最大值||無維度|
|percentProcessorTime1|CPU (分區 1)|百分比|最大值||無維度|
|connectedclients2|連線的用戶端 (分區 2)|Count|最大值||無維度|
|totalcommandsprocessed2|總作業數 (分區 2)|Count|總計||無維度|
|cachehits2|快取點擊 (分區 2)|Count|總計||無維度|
|cachemisses2|快取遺漏 (分區 2)|Count|總計||無維度|
|getcommands2|取得 (分區 2)|Count|總計||無維度|
|setcommands2|設定 (分區 2)|Count|總計||無維度|
|evictedkeys2|收回的金鑰 (分區 2)|Count|總計||無維度|
|totalkeys2|金鑰總數 (分區 2)|Count|最大值||無維度|
|expiredkeys2|到期的金鑰 (分區 2)|Count|總計||無維度|
|usedmemory2|已用的記憶體 (分區 2)|位元組|最大值||無維度|
|usedmemoryRss2|已用的記憶體 RSS (分區 2)|位元組|最大值||無維度|
|serverLoad2|伺服器負載 (分區 2)|百分比|最大值||無維度|
|cacheWrite2|快取寫入 (分區 2)|每秒位元組|最大值||無維度|
|cacheRead2|快取讀取 (分區 2)|每秒位元組|最大值||無維度|
|percentProcessorTime2|CPU (分區 2)|百分比|最大值||無維度|
|connectedclients3|連線的用戶端 (分區 3)|Count|最大值||無維度|
|totalcommandsprocessed3|總作業數 (分區 3)|Count|總計||無維度|
|cachehits3|快取點擊 (分區 3)|Count|總計||無維度|
|cachemisses3|快取遺漏 (分區 3)|Count|總計||無維度|
|getcommands3|取得 (分區 3)|Count|總計||無維度|
|setcommands3|設定 (分區 3)|Count|總計||無維度|
|evictedkeys3|收回的金鑰 (分區 3)|Count|總計||無維度|
|totalkeys3|金鑰總數 (分區 3)|Count|最大值||無維度|
|expiredkeys3|到期的金鑰 (分區 3)|Count|總計||無維度|
|usedmemory3|已用的記憶體 (分區 3)|位元組|最大值||無維度|
|usedmemoryRss3|已用的記憶體 RSS (分區 3)|位元組|最大值||無維度|
|serverLoad3|伺服器負載 (分區 3)|百分比|最大值||無維度|
|cacheWrite3|快取寫入 (分區 3)|每秒位元組|最大值||無維度|
|cacheRead3|快取讀取 (分區 3)|每秒位元組|最大值||無維度|
|percentProcessorTime3|CPU (分區 3)|百分比|最大值||無維度|
|connectedclients4|連線的用戶端 (分區 4)|Count|最大值||無維度|
|totalcommandsprocessed4|總作業數 (分區 4)|Count|總計||無維度|
|cachehits4|快取點擊 (分區 4)|Count|總計||無維度|
|cachemisses4|快取遺漏 (分區 4)|Count|總計||無維度|
|getcommands4|取得 (分區 4)|Count|總計||無維度|
|setcommands4|設定 (分區 4)|Count|總計||無維度|
|evictedkeys4|收回的金鑰 (分區 4)|Count|總計||無維度|
|totalkeys4|金鑰總數 (分區 4)|Count|最大值||無維度|
|expiredkeys4|到期的金鑰 (分區 4)|Count|總計||無維度|
|usedmemory4|已用的記憶體 (分區 4)|位元組|最大值||無維度|
|usedmemoryRss4|已用的記憶體 RSS (分區 4)|位元組|最大值||無維度|
|serverLoad4|伺服器負載 (分區 4)|百分比|最大值||無維度|
|cacheWrite4|快取寫入 (分區 4)|每秒位元組|最大值||無維度|
|cacheRead4|快取讀取 (分區 4)|每秒位元組|最大值||無維度|
|percentProcessorTime4|CPU (分區 4)|百分比|最大值||無維度|
|connectedclients5|連線的用戶端 (分區 5)|Count|最大值||無維度|
|totalcommandsprocessed5|總作業數 (分區 5)|Count|總計||無維度|
|cachehits5|快取點擊 (分區 5)|Count|總計||無維度|
|cachemisses5|快取遺漏 (分區 5)|Count|總計||無維度|
|getcommands5|取得 (分區 5)|Count|總計||無維度|
|setcommands5|設定 (分區 5)|Count|總計||無維度|
|evictedkeys5|收回的金鑰 (分區 5)|Count|總計||無維度|
|totalkeys5|金鑰總數 (分區 5)|Count|最大值||無維度|
|expiredkeys5|到期的金鑰 (分區 5)|Count|總計||無維度|
|usedmemory5|已用的記憶體 (分區 5)|位元組|最大值||無維度|
|usedmemoryRss5|已用的記憶體 RSS (分區 5)|位元組|最大值||無維度|
|serverLoad5|伺服器負載 (分區 5)|百分比|最大值||無維度|
|cacheWrite5|快取寫入 (分區 5)|每秒位元組|最大值||無維度|
|cacheRead5|快取讀取 (分區 5)|每秒位元組|最大值||無維度|
|percentProcessorTime5|CPU (分區 5)|百分比|最大值||無維度|
|connectedclients6|連線的用戶端 (分區 6)|Count|最大值||無維度|
|totalcommandsprocessed6|總作業數 (分區 6)|Count|總計||無維度|
|cachehits6|快取點擊 (分區 6)|Count|總計||無維度|
|cachemisses6|快取遺漏 (分區 6)|Count|總計||無維度|
|getcommands6|取得 (分區 6)|Count|總計||無維度|
|setcommands6|設定 (分區 6)|Count|總計||無維度|
|evictedkeys6|收回的金鑰 (分區 6)|Count|總計||無維度|
|totalkeys6|金鑰總數 (分區 6)|Count|最大值||無維度|
|expiredkeys6|到期的金鑰 (分區 6)|Count|總計||無維度|
|usedmemory6|已用的記憶體 (分區 6)|位元組|最大值||無維度|
|usedmemoryRss6|已用的記憶體 RSS (分區 6)|位元組|最大值||無維度|
|serverLoad6|伺服器負載 (分區 6)|百分比|最大值||無維度|
|cacheWrite6|快取寫入 (分區 6)|每秒位元組|最大值||無維度|
|cacheRead6|快取讀取 (分區 6)|每秒位元組|最大值||無維度|
|percentProcessorTime6|CPU (分區 6)|百分比|最大值||無維度|
|connectedclients7|連線的用戶端 (分區 7)|Count|最大值||無維度|
|totalcommandsprocessed7|總作業數 (分區 7)|Count|總計||無維度|
|cachehits7|快取點擊 (分區 7)|Count|總計||無維度|
|cachemisses7|快取遺漏 (分區 7)|Count|總計||無維度|
|getcommands7|取得 (分區 7)|Count|總計||無維度|
|setcommands7|設定 (分區 7)|Count|總計||無維度|
|evictedkeys7|收回的金鑰 (分區 7)|Count|總計||無維度|
|totalkeys7|金鑰總數 (分區 7)|Count|最大值||無維度|
|expiredkeys7|到期的金鑰 (分區 7)|Count|總計||無維度|
|usedmemory7|已用的記憶體 (分區 7)|位元組|最大值||無維度|
|usedmemoryRss7|已用的記憶體 RSS (分區 7)|位元組|最大值||無維度|
|serverLoad7|伺服器負載 (分區 7)|百分比|最大值||無維度|
|cacheWrite7|快取寫入 (分區 7)|每秒位元組|最大值||無維度|
|cacheRead7|快取讀取 (分區 7)|每秒位元組|最大值||無維度|
|percentProcessorTime7|CPU (分區 7)|百分比|最大值||無維度|
|connectedclients8|連線的用戶端 (分區 8)|Count|最大值||無維度|
|totalcommandsprocessed8|總作業數 (分區 8)|Count|總計||無維度|
|cachehits8|快取點擊 (分區 8)|Count|總計||無維度|
|cachemisses8|快取遺漏 (分區 8)|Count|總計||無維度|
|getcommands8|取得 (分區 8)|Count|總計||無維度|
|setcommands8|設定 (分區 8)|Count|總計||無維度|
|evictedkeys8|收回的金鑰 (分區 8)|Count|總計||無維度|
|totalkeys8|金鑰總數 (分區 8)|Count|最大值||無維度|
|expiredkeys8|到期的金鑰 (分區 8)|Count|總計||無維度|
|usedmemory8|已用的記憶體 (分區 8)|位元組|最大值||無維度|
|usedmemoryRss8|已用的記憶體 RSS (分區 8)|位元組|最大值||無維度|
|serverLoad8|伺服器負載 (分區 8)|百分比|最大值||無維度|
|cacheWrite8|快取寫入 (分區 8)|每秒位元組|最大值||無維度|
|cacheRead8|快取讀取 (分區 8)|每秒位元組|最大值||無維度|
|percentProcessorTime8|CPU (分區 8)|百分比|最大值||無維度|
|connectedclients9|連線的用戶端 (分區 9)|Count|最大值||無維度|
|totalcommandsprocessed9|總作業數 (分區 9)|Count|總計||無維度|
|cachehits9|快取點擊 (分區 9)|Count|總計||無維度|
|cachemisses9|快取遺漏 (分區 9)|Count|總計||無維度|
|getcommands9|取得 (分區 9)|Count|總計||無維度|
|setcommands9|設定 (分區 9)|Count|總計||無維度|
|evictedkeys9|收回的金鑰 (分區 9)|Count|總計||無維度|
|totalkeys9|金鑰總數 (分區 9)|Count|最大值||無維度|
|expiredkeys9|到期的金鑰 (分區 9)|Count|總計||無維度|
|usedmemory9|已用的記憶體 (分區 9)|位元組|最大值||無維度|
|usedmemoryRss9|已用的記憶體 RSS (分區 9)|位元組|最大值||無維度|
|serverLoad9|伺服器負載 (分區 9)|百分比|最大值||無維度|
|cacheWrite9|快取寫入 (分區 9)|每秒位元組|最大值||無維度|
|cacheRead9|快取讀取 (分區 9)|每秒位元組|最大值||無維度|
|percentProcessorTime9|CPU (分區 9)|百分比|最大值||無維度|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|平均值|虛擬機器目前使用的配置計算單位百分比。|無維度|
|Network In|Network In|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目。|無維度|
|Network Out|Network Out|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目。|無維度|
|Disk Read Bytes/Sec|磁碟讀取|每秒位元組|平均值|監視期間從磁碟讀取的平均位元組。|無維度|
|Disk Write Bytes/Sec|磁碟寫入|每秒位元組|平均值|監視期間寫入磁碟的平均位元組。|無維度|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|平均值|磁碟讀取 IOPS。|無維度|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|平均值|磁碟寫入 IOPS。|無維度|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|TotalCalls|呼叫總數|Count|總計|呼叫總數。|無維度|
|SuccessfulCalls|成功的呼叫|Count|總計|成功的呼叫數。|無維度|
|TotalErrors|錯誤總數|Count|總計|具有錯誤回應 (HTTP 回應碼 4xx 或 5xx) 的呼叫總數。|無維度|
|BlockedCalls|已封鎖的呼叫|Count|總計|超過速率或配額限制的呼叫數目。|無維度|
|ServerErrors|伺服器錯誤|Count|總計|具有服務內部錯誤 (HTTP 回應碼 5xx) 的呼叫數目。|無維度|
|ClientErrors|用戶端錯誤|Count|總計|具有用戶端錯誤 (HTTP 回應碼 4xx) 的呼叫數目。|無維度|
|DataIn|資料輸入|位元組|總計|內送資料大小，以位元組為單位。|無維度|
|DataOut|資料輸出|位元組|總計|輸出資料大小，以位元組為單位。|無維度|
|Latency|Latency|毫秒|平均值|延遲，以毫秒為單位。|無維度|
|CharactersTranslated|轉譯字元數|Count|總計|傳入文字要求中的字元總數。|無維度|
|SpeechSessionDuration|語音工作階段持續時間|秒|總計|語音工作階段的總持續時間 (秒)。|無維度|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|平均值|目前虛擬機器正在使用中的已配置計算單位百分比|無維度|
|Network In|Network In|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|無維度|
|Network Out|Network Out|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|無維度|
|Disk Read Bytes|Disk Read Bytes|位元組|總計|在監視期間從磁碟讀取的位元組總計|無維度|
|Disk Write Bytes|Disk Write Bytes|位元組|總計|在監視期間寫入磁碟的位元組總計|無維度|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|平均值|磁碟讀取 IOPS|無維度|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|平均值|磁碟寫入 IOPS|無維度|
|剩餘 CPU 信用額度|剩餘 CPU 信用額度|Count|平均值|可用於高載的信用額度總數|無維度|
|CPU Credits Consumed|CPU Credits Consumed|Count|平均值|由虛擬機器取用的信用額度總數|無維度|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|平均值|目前虛擬機器正在使用中的已配置計算單位百分比|無維度|
|Network In|Network In|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|無維度|
|Network Out|Network Out|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|無維度|
|Disk Read Bytes|Disk Read Bytes|位元組|總計|在監視期間從磁碟讀取的位元組總計|無維度|
|Disk Write Bytes|Disk Write Bytes|位元組|總計|在監視期間寫入磁碟的位元組總計|無維度|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|平均值|磁碟讀取 IOPS|無維度|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|平均值|磁碟寫入 IOPS|無維度|
|剩餘 CPU 信用額度|剩餘 CPU 信用額度|Count|平均值|可用於高載的信用額度總數|無維度|
|CPU Credits Consumed|CPU Credits Consumed|Count|平均值|由虛擬機器取用的信用額度總數|無維度|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|平均值|目前虛擬機器正在使用中的已配置計算單位百分比|無維度|
|Network In|Network In|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|無維度|
|Network Out|Network Out|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|無維度|
|Disk Read Bytes|Disk Read Bytes|位元組|總計|在監視期間從磁碟讀取的位元組總計|無維度|
|Disk Write Bytes|Disk Write Bytes|位元組|總計|在監視期間寫入磁碟的位元組總計|無維度|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|平均值|磁碟讀取 IOPS|無維度|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|平均值|磁碟寫入 IOPS|無維度|
|剩餘 CPU 信用額度|剩餘 CPU 信用額度|Count|平均值|可用於高載的信用額度總數|無維度|
|CPU Credits Consumed|CPU Credits Consumed|Count|平均值|由虛擬機器取用的信用額度總數|無維度|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API 呼叫|Count|總計||無維度|
|DCIMappingImportOperationSuccessfulLines|對應匯入作業成功行|Count|總計||無維度|
|DCIMappingImportOperationFailedLines|對應匯入作業失敗行|Count|總計||無維度|
|DCIMappingImportOperationTotalLines|對應匯入作業行總計|Count|總計||無維度|
|DCIMappingImportOperationRuntimeInSeconds|對應匯入作業執行階段 (以秒為單位)|秒|總計||無維度|
|DCIOutboundProfileExportSucceeded|輸出設定檔匯出成功|Count|總計||無維度|
|DCIOutboundProfileExportFailed|輸出設定檔匯出失敗|Count|總計||無維度|
|DCIOutboundProfileExportDuration|輸出設定檔匯出持續時間|秒|總計||無維度|
|DCIOutboundKpiExportSucceeded|輸出 KPI 匯出成功|Count|總計||無維度|
|DCIOutboundKpiExportFailed|輸出 KPI 匯出失敗|Count|總計||無維度|
|DCIOutboundKpiExportDuration|輸出 KPI 匯出持續時間|秒|總計||無維度|
|DCIOutboundKpiExportStarted|輸出 KPI 匯出開始|秒|總計||無維度|
|DCIOutboundKpiRecordCount|輸出 KPI 記錄計數|秒|總計||無維度|
|DCIOutboundProfileExportCount|輸出設定檔匯出計數|秒|總計||無維度|
|DCIOutboundInitialProfileExportFailed|輸出初始設定檔匯出失敗|秒|總計||無維度|
|DCIOutboundInitialProfileExportSucceeded|輸出初始設定檔匯出成功|秒|總計||無維度|
|DCIOutboundInitialKpiExportFailed|輸出初始 KPI 匯出失敗|秒|總計||無維度|
|DCIOutboundInitialKpiExportSucceeded|輸出初始 KPI 匯出成功|秒|總計||無維度|
|DCIOutboundInitialProfileExportDurationInSeconds|輸出初始設定檔匯出持續時間 (以秒為單位)|秒|總計||無維度|
|AdlaJobForStandardKpiFailed|標準 KPI 的 Adla 作業失敗 (以秒為單位)|秒|總計||無維度|
|AdlaJobForStandardKpiTimeOut|標準 KPI 的 Adla 作業逾時 (以秒為單位)|秒|總計||無維度|
|AdlaJobForStandardKpiCompleted|標準 KPI 的 Adla 作業完成 (以秒為單位)|秒|總計||無維度|
|ImportASAValuesFailed|匯入 ASA 值失敗計數|Count|總計||無維度|
|ImportASAValuesSucceeded|匯入 ASA 值成功計數|Count|總計||無維度|
|DCIProfilesCount|設定檔執行個體計數|Count|最後一頁||無維度|
|DCIInteractionsPerMonthCount|每月互動數計數|Count|最後一頁||無維度|
|DCIKpisCount|KPI 計數|Count|最後一頁||無維度|
|DCISegmentsCount|區段計數|Count|最後一頁||無維度|
|DCIPredictiveMatchPoliciesCount|預測比對計數|Count|最後一頁||無維度|
|DCIPredictionsCount|預測計數|Count|最後一頁||無維度|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|JobEndedSuccess|成功的作業|Count|總計|成功作業計數。|無維度|
|JobEndedFailure|失敗的作業|Count|總計|失敗作業計數。|無維度|
|JobEndedCancelled|取消的作業|Count|總計|取消作業計數。|無維度|
|JobAUEndedSuccess|成功 AU 時間|秒|總計|成功作業的 AU 時間總計。|無維度|
|JobAUEndedFailure|失敗 AU 時間|秒|總計|失敗作業的 AU 時間總計。|無維度|
|JobAUEndedCancelled|取消的 AU 時間|秒|總計|取消作業的 AU 時間總計。|無維度|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|TotalStorage|儲存體總計|位元組|最大值|儲存在帳戶中的總資料量。|無維度|
|DataWritten|寫入的資料|位元組|總計|寫入帳戶的總資料量。|無維度|
|DataRead|讀取的資料|位元組|總計|從帳戶讀取的總資料量。|無維度|
|WriteRequests|寫入要求|Count|總計|帳戶的資料寫入要求計數。|無維度|
|ReadRequests|讀取要求|Count|總計|帳戶的資料讀取要求計數。|無維度|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|無維度|
|compute_limit|計算單位限制|Count|平均值|計算單位限制|無維度|
|compute_consumption_percent|計算單位百分比|百分比|平均值|計算單位百分比|無維度|
|memory_percent|記憶體百分比|百分比|平均值|記憶體百分比|無維度|
|io_consumption_percent|IO 百分比|百分比|平均值|IO 百分比|無維度|
|storage_percent|儲存體百分比|百分比|平均值|儲存體百分比|無維度|
|storage_used|已使用儲存體|位元組|平均值|已使用儲存體|無維度|
|storage_limit|儲存體限制|位元組|平均值|儲存體限制|無維度|
|active_connections|作用中的連接總數|Count|平均值|作用中的連接總數|無維度|
|connections_failed|失敗的連接總數|Count|平均值|失敗的連接總數|無維度|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|無維度|
|compute_limit|計算單位限制|Count|平均值|計算單位限制|無維度|
|compute_consumption_percent|計算單位百分比|百分比|平均值|計算單位百分比|無維度|
|memory_percent|記憶體百分比|百分比|平均值|記憶體百分比|無維度|
|io_consumption_percent|IO 百分比|百分比|平均值|IO 百分比|無維度|
|storage_percent|儲存體百分比|百分比|平均值|儲存體百分比|無維度|
|storage_used|已使用儲存體|位元組|平均值|已使用儲存體|無維度|
|storage_limit|儲存體限制|位元組|平均值|儲存體限制|無維度|
|active_connections|作用中的連接總數|Count|平均值|作用中的連接總數|無維度|
|connections_failed|失敗的連接總數|Count|平均值|失敗的連接總數|無維度|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|遙測訊息傳送嘗試|Count|總計|要嘗試傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|無維度|
|d2c.telemetry.ingress.success|已傳送的遙測訊息|Count|總計|成功傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|無維度|
|c2d.commands.egress.complete.success|完成的命令|Count|總計|裝置成功完成的雲端到裝置命令數目|無維度|
|c2d.commands.egress.abandon.success|放棄的命令|Count|總計|裝置放棄的雲端到裝置命令數目|無維度|
|c2d.commands.egress.reject.success|拒絕的命令|Count|總計|裝置拒絕的雲端到裝置命令數目|無維度|
|devices.totalDevices|裝置總計|Count|總計|向 IoT 中樞註冊的裝置數目|無維度|
|devices.connectedDevices.allProtocol|連接的裝置|Count|總計|連接至 IoT 中樞的裝置數目|無維度|
|d2c.telemetry.egress.success|已傳遞的遙測訊息|Count|總計|訊息成功寫入端點的次數 (總計)|無維度|
|d2c.telemetry.egress.dropped|捨棄的訊息|Count|總計|由於傳遞端點已無作用而卸除的訊息數目|無維度|
|d2c.telemetry.egress.orphaned|被遺棄的訊息|Count|總計|不符合任何路由 (包括後援路由) 的訊息計數|無維度|
|d2c.telemetry.egress.invalid|無效的訊息|Count|總計|因為與端點不相容而未傳遞的訊息計數|無維度|
|d2c.telemetry.egress.fallback|符合後援條件的訊息|Count|總計|寫入後援端點的訊息數目|無維度|
|d2c.endpoints.egress.eventHubs|傳遞至事件中樞端點的訊息|Count|總計|訊息成功寫入事件中樞端點的次數|無維度|
|d2c.endpoints.latency.eventHubs|事件中樞端點的訊息延遲|毫秒|平均值|訊息輸入到 IoT 中樞與訊息輸入到事件中樞端點之間的平均延遲，以毫秒為單位|無維度|
|d2c.endpoints.egress.serviceBusQueues|傳遞至服務匯流排佇列端點的訊息|Count|總計|訊息成功寫入服務匯流排佇列端點的次數|無維度|
|d2c.endpoints.latency.serviceBusQueues|服務匯流排佇列端點的訊息延遲|毫秒|平均值|訊息輸入到 IoT 中樞與訊息輸入到服務匯流排佇列端點之間的平均延遲，以毫秒為單位|無維度|
|d2c.endpoints.egress.serviceBusTopics|傳遞至服務匯流排主題端點的訊息|Count|總計|訊息成功寫入服務匯流排主題端點的次數|無維度|
|d2c.endpoints.latency.serviceBusTopics|服務匯流排主題端點的訊息延遲|毫秒|平均值|訊息輸入到 IoT 中樞與訊息輸入到服務匯流排主題端點之間的平均延遲，以毫秒為單位|無維度|
|d2c.endpoints.egress.builtIn.events|傳遞至內建端點 (訊息/事件) 的訊息|Count|總計|訊息成功寫入內建端點 (訊息/事件) 的次數|無維度|
|d2c.endpoints.latency.builtIn.events|內建端點 (訊息/事件) 的訊息延遲|毫秒|平均值|訊息輸入到 IoT 中樞與訊息輸入到內建端點 (訊息/事件) 之間的平均延遲，以毫秒為單位 |無維度|
|d2c.endpoints.egress.storage|傳遞至儲存體端點的訊息|Count|總計|訊息成功寫入儲存體端點的次數|無維度|
|d2c.endpoints.latency.storage|儲存體端點的訊息延遲|毫秒|平均值|訊息輸入到 IoT 中樞與訊息輸入到儲存體端點之間的平均延遲，以毫秒為單位|無維度|
|d2c.endpoints.egress.storage.bytes|寫入儲存體的資料|位元組|總計|寫入儲存體端點的資料量，以位元組為單位|無維度|
|d2c.endpoints.egress.storage.blobs|寫入儲存體的 Blob|Count|總計|寫入儲存體端點的 Blob 數目|無維度|
|d2c.twin.read.success|裝置的成功對應項讀取|Count|總計|裝置起始的所有成功對應項讀取的計數。|無維度|
|d2c.twin.read.failure|裝置的失敗對應項讀取|Count|總計|裝置起始的所有失敗對應項讀取的計數。|無維度|
|d2c.twin.read.size|裝置的對應項讀取回應大小|位元組|平均值|裝置起始的所有成功對應項讀取的平均值、最小值和最大值。|無維度|
|d2c.twin.update.success|裝置的成功對應項更新|Count|總計|裝置起始的所有成功對應項更新的計數。|無維度|
|d2c.twin.update.failure|裝置的失敗對應項更新|Count|總計|裝置起始的所有失敗對應項更新的計數。|無維度|
|d2c.twin.update.size|裝置的對應項更新大小|位元組|平均值|裝置起始的所有成功對應項更新的大小平均值、最小值和最大值。|無維度|
|c2d.methods.success|成功直接方法叫用|Count|總計|所有成功直接方法呼叫的計數。|無維度|
|c2d.methods.failure|失敗直接方法叫用|Count|總計|所有失敗直接方法呼叫的計數。|無維度|
|c2d.methods.requestSize|直接方法叫用的要求大小|位元組|平均值|所有成功直接方法要求的平均值、最小值和最大值。|無維度|
|c2d.methods.responseSize|直接方法叫用的回應大小|位元組|平均值|所有成功直接方法回應的平均值、最小值和最大值。|無維度|
|c2d.twin.read.success|後端的成功對應項讀取|Count|總計|後端起始的所有成功對應項讀取的計數。|無維度|
|c2d.twin.read.failure|後端的失敗對應項讀取|Count|總計|後端起始的所有失敗對應項讀取的計數。|無維度|
|c2d.twin.read.size|後端的對應項讀取回應大小|位元組|平均值|後端起始的所有成功對應項讀取的平均值、最小值和最大值。|無維度|
|c2d.twin.update.success|後端的成功對應項更新|Count|總計|後端起始的所有成功對應項更新的計數。|無維度|
|c2d.twin.update.failure|後端的失敗對應項更新|Count|總計|後端起始的所有失敗對應項更新的計數。|無維度|
|c2d.twin.update.size|後端的對應項更新大小|位元組|平均值|後端起始的所有成功對應項更新的大小平均值、最小值和最大值。|無維度|
|twinQueries.success|成功對應項查詢|Count|總計|所有成功對應項查詢的計數。|無維度|
|twinQueries.failure|失敗對應項查詢|Count|總計|所有失敗對應項查詢的計數。|無維度|
|twinQueries.resultSize|對應項查詢結果大小|位元組|平均值|所有成功對應項查詢的結果大小平均值、最小值和最大值。|無維度|
|jobs.createTwinUpdateJob.success|成功建立的對應項更新作業|Count|總計|所有成功建立的對應項更新作業計數。|無維度|
|jobs.createTwinUpdateJob.failure|建立失敗的對應項更新作業|Count|總計|所有建立失敗的對應項更新作業計數。|無維度|
|jobs.createDirectMethodJob.success|成功建立的方法叫用作業|Count|總計|所有成功建立的直接方法叫用作業計數。|無維度|
|jobs.createDirectMethodJob.failure|建立失敗的方法叫用作業|Count|總計|所有建立失敗的直接方法叫用作業計數。|無維度|
|jobs.listJobs.success|成功呼叫列出作業|Count|總計|所有成功呼叫列出作業的計數。|無維度|
|jobs.listJobs.failure|呼叫列出作業失敗|Count|總計|所有呼叫列出作業失敗的計數。|無維度|
|jobs.cancelJob.success|成功取消作業|Count|總計|所有成功呼叫取消作業的計數。|無維度|
|jobs.cancelJob.failure|取消作業失敗|Count|總計|所有呼叫取消作業失敗的計數。|無維度|
|jobs.queryJobs.success|成功作業查詢|Count|總計|所有成功呼叫查詢作業的計數。|無維度|
|jobs.queryJobs.failure|失敗作業查詢|Count|總計|所有呼叫查詢作業失敗的計數。|無維度|
|jobs.completed|已完成的工作|Count|總計|所有已完成的作業計數。|無維度|
|jobs.failed|失敗作業|Count|總計|所有失敗作業計數。|無維度|
|d2c.telemetry.ingress.sendThrottle|節流錯誤數目|Count|總計|因裝置輸送量節流而導致的節流錯誤數目|無維度|
|dailyMessageQuotaUsed|已使用的訊息總數|Count|平均值|今日已使用的總訊息數|無維度|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|RegistrationAttempts|註冊嘗試數|Count|總計|嘗試的裝置註冊數|ProvisioningServiceName，IotHubName，狀態|
|DeviceAssignments|已指派的裝置數|Count|總計|已指派到 IoT 中樞的裝置數|ProvisioningServiceName，IotHubName|
|AttestationAttempts|證明嘗試數|Count|總計|嘗試的裝置證明數|ProvisioningServiceName，狀態，通訊協定|

## <a name="microsoftdocumentdbdatabaseaccounts-cosmosdb"></a>Microsoft.DocumentDB/databaseAccounts (CosmosDB)
|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|TotalRequests|要求總數|Count|Count|進行的要求數目|DatabaseAccount，CollectionName，DatabaseName，區域，StatusCode|
|MongoRequests|Mongo 要求|Count|Count|進行的 Mongo 要求數目|DatabaseAccount，CollectionName，DatabaseName，區域，ErrorCode，CommandName|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|INREQS|傳入的傳送要求|Count|總計|命名空間的連入傳送要求總計|無維度|
|SUCCREQ|成功的要求|Count|總計|命名空間的成功要求總數|無維度|
|FAILREQ|失敗的要求|Count|總計|命名空間的失敗要求總數|無維度|
|SVRBSY|伺服器忙線錯誤|Count|總計|命名空間的伺服器忙線錯誤總數|無維度|
|INTERR|內部伺服器錯誤|Count|總計|命名空間的內部伺服器錯誤總數|無維度|
|MISCERR|其他錯誤|Count|總計|命名空間的失敗要求總數|無維度|
|INMSGS|連入訊息數 (已過時)|Count|總計|命名空間的傳入訊息總數。 此計量已過時。 請改為使用「傳入訊息數」計量|無維度|
|EHINMSGS|傳入訊息|Count|總計|命名空間的內送訊息總數|無維度|
|OUTMSGS|傳出訊息數 (已過時)|Count|總計|命名空間的傳出訊息總數。 此計量已過時。 請改為使用「傳出訊息數」計量|無維度|
|EHOUTMSGS|外送訊息|Count|總計|命名空間的外送訊息總數|無維度|
|EHINMBS|傳入位元組數 (已過時)|位元組|總計|命名空間的事件中樞傳入訊息輸送量。 此計量已過時。 請改為使用「傳入位元組數」計量|無維度|
|EHINBYTES|傳入位元組|位元組|總計|命名空間的事件中樞內送訊息輸送量|無維度|
|EHOUTMBS|傳出位元組數 (已過時)|位元組|總計|命名空間的事件中樞傳出訊息輸送量。 此計量已過時。 請改為使用「傳出位元組數」計量|無維度|
|EHOUTBYTES|傳出位元組|位元組|總計|命名空間的事件中樞傳出訊息輸送量|無維度|
|EHABL|封存待處理項目訊息|Count|總計|命名空間的事件中樞封存於待處理項目中訊息|無維度|
|EHAMSGS|封存訊息|Count|總計|命名空間中的事件中樞已封存訊息|無維度|
|EHAMBS|封存訊息輸送量|位元組|總計|命名空間中的事件中樞已封存訊息輸送量|無維度|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|ObservedMetricValue|觀察的計量值|Count|平均值|執行時由自動調整規模計算的值|MetricTriggerSource|
|MetricThreshold|計量閾值|Count|平均值|當自動調整規模執行時設定的自動調整規模閾值。|MetricTriggerRule|
|ObservedCapacity|觀察的容量|Count|平均值|執行時向自動調整規模回報的容量。|無維度|
|ScaleActionsInitiated|已起始的調整規模動作數|Count|總計|調整規模作業的方向。|ScaleDirection|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|RunsStarted|執行已啟動|Count|總計|已啟動的工作流程執行數目。|無維度|
|RunsCompleted|完成的執行|Count|總計|已完成的工作流程執行數目。|無維度|
|RunsSucceeded|已成功的執行|Count|總計|已成功的工作流程執行數目。|無維度|
|RunsFailed|失敗的執行|Count|總計|已失敗的工作流程執行數目。|無維度|
|RunsCancelled|取消的執行|Count|總計|已取消的工作流程執行數目。|無維度|
|RunLatency|執行延遲|秒|平均值|已完成的工作流程執行延遲。|無維度|
|RunSuccessLatency|執行成功的延遲|秒|平均值|已成功的工作流程執行延遲。|無維度|
|RunThrottledEvents|執行節流的事件|Count|總計|工作流程動作或觸發節流的事件數目。|無維度|
|RunFailurePercentage|執行失敗百分比|百分比|總計|已失敗的工作流程執行百分比。|無維度|
|ActionsStarted|啟動的動作 |Count|總計|已啟動的工作流程動作數目。|無維度|
|ActionsCompleted|完成的動作 |Count|總計|已完成的工作流程動作數目。|無維度|
|ActionsSucceeded|成功的動作 |Count|總計|已成功的工作流程動作數目。|無維度|
|ActionsFailed|動作的失敗|Count|總計|已失敗的工作流程動作數目。|無維度|
|ActionsSkipped|略過的動作 |Count|總計|已略過的工作流程動作數目。|無維度|
|ActionLatency|動作延遲 |秒|平均值|已完成的工作流程動作延遲。|無維度|
|ActionSuccessLatency|動作成功延遲 |秒|平均值|已成功的工作流程動作延遲。|無維度|
|ActionThrottledEvents|動作節流的事件|Count|總計|工作流程動作已節流的事件數目。|無維度|
|TriggersStarted|啟動的觸發程序 |Count|總計|已啟動的工作流程觸發程序數目。|無維度|
|TriggersCompleted|完成的觸發程序 |Count|總計|已完成的工作流程觸發程序數目。|無維度|
|TriggersSucceeded|成功的觸發程序 |Count|總計|已成功的工作流程觸發程序數目。|無維度|
|TriggersFailed|失敗的觸發程序 |Count|總計|已失敗的工作流程觸發程序數目。|無維度|
|TriggersSkipped|略過的觸發程序|Count|總計|已略過的工作流程觸發程序數目。|無維度|
|TriggersFired|引發的觸發程序 |Count|總計|已引發的工作流程觸發程序數目。|無維度|
|TriggerLatency|觸發程序延遲 |秒|平均值|已完成的工作流程觸發程序延遲。|無維度|
|TriggerFireLatency|觸發程序引發延遲 |秒|平均值|已引發的工作流程觸發程序延遲。|無維度|
|TriggerSuccessLatency|觸發程序成功延遲 |秒|平均值|已成功的工作流程觸發程序延遲。|無維度|
|TriggerThrottledEvents|觸發程序節流的事件|Count|總計|工作流程觸發程序節流的事件數目。|無維度|
|BillableActionExecutions|可計費的動作執行|Count|總計|計費的工作流程動作執行數目。|無維度|
|BillableTriggerExecutions|可計費的觸發程序執行|Count|總計|計費的工作流程觸發程序執行數目。|無維度|
|TotalBillableExecutions|可計費的總執行次數|Count|總計|計費的工作流程執行數目。|無維度|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|VipAvailability|VIP 可用性|Count|平均值|以探查結果為基礎的 VIP 端點可用性|VipAddress，VipPort|
|DipAvailability|DIP 可用性|Count|平均值|以探查結果為基礎的 DIP 端點可用性|ProtocolType，DipPort，VipAddress，VipPort，DipAddress|
|ByteCount|位元組計數|Count|總計|在期間內傳輸的位元組總數|VipAddress，VipPort，Direction|
|PacketCount|封包計數|Count|總計|在期間內傳輸的封包總數|VipAddress，VipPort，Direction|
|SYNCount|SYN 計數|Count|總計|在期間內傳輸的 SYN 封包總數|VipAddress，VipPort，Direction|
|SnatConnectionCount|SNAT 連線計數|Count|總計|在期間內建立的新 SNAT 連線總數|VipAddress，DipAddress，ConnectionState|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|PacketsInDDoS|傳入封包 DDoS|每秒計數|平均值|傳入封包 DDoS|無維度|
|PacketsDroppedDDoS|傳入封包捨棄 DDoS|每秒計數|平均值|傳入封包捨棄 DDoS|無維度|
|PacketsForwardedDDoS|傳入封包轉寄 DDoS|每秒計數|平均值|傳入封包轉寄 DDoS|無維度|
|TCPPacketsInDDoS|傳入 TCP 封包 DDoS|每秒計數|平均值|傳入 TCP 封包 DDoS|無維度|
|TCPPacketsDroppedDDoS|傳入 TCP 封包捨棄 DDoS|每秒計數|平均值|傳入 TCP 封包捨棄 DDoS|無維度|
|TCPPacketsForwardedDDoS|傳入 TCP 封包轉送 DDoS|每秒計數|平均值|傳入 TCP 封包轉送 DDoS|無維度|
|UDPPacketsInDDoS|傳入 UDP 封包 DDoS|每秒計數|平均值|傳入 UDP 封包 DDoS|無維度|
|UDPPacketsDroppedDDoS|傳入 UDP 封包捨棄 DDoS|每秒計數|平均值|傳入 UDP 封包捨棄 DDoS|無維度|
|UDPPacketsForwardedDDoS|傳入 UDP 封包轉送 DDoS|每秒計數|平均值|傳入 UDP 封包轉送 DDoS|無維度|
|BytesInDDoS|傳入位元組數 DDoS|每秒位元組|平均值|傳入位元組數 DDoS|無維度|
|BytesDroppedDDoS|傳入位元組數捨棄 DDoS|每秒位元組|平均值|傳入位元組數捨棄 DDoS|無維度|
|BytesForwardedDDoS|傳入位元組數轉送 DDoS|每秒位元組|平均值|傳入位元組數轉送 DDoS|無維度|
|TCPBytesInDDoS|傳入 TCP 位元組數 DDoS|每秒位元組|平均值|傳入 TCP 位元組數 DDoS|無維度|
|TCPBytesDroppedDDoS|傳入 TCP 位元組數捨棄 DDoS|每秒位元組|平均值|傳入 TCP 位元組數捨棄 DDoS|無維度|
|TCPBytesForwardedDDoS|傳入 TCP 位元組數轉送 DDoS|每秒位元組|平均值|傳入 TCP 位元組數轉送 DDoS|無維度|
|UDPBytesInDDoS|傳入 UDP 位元組數 DDoS|每秒位元組|平均值|傳入 UDP 位元組數 DDoS|無維度|
|UDPBytesDroppedDDoS|傳入 UDP 位元組數捨棄 DDoS|每秒位元組|平均值|傳入 UDP 位元組數捨棄 DDoS|無維度|
|UDPBytesForwardedDDoS|傳入 UDP 位元組數轉送 DDoS|每秒位元組|平均值|傳入 UDP 位元組數轉送 DDoS|無維度|
|IfUnderDDoSAttack|是否正遭受 DDoS 攻擊|Count|平均值|是否正遭受 DDoS 攻擊|無維度|
|DDoSTriggerTCPPackets|傳入 TCP 封包數以觸發 DDoS 緩和|每秒計數|平均值|傳入 TCP 封包數以觸發 DDoS 緩和|無維度|
|DDoSTriggerUDPPackets|傳入 UDP 封包數以觸發 DDoS 緩和|每秒計數|平均值|傳入 UDP 封包數以觸發 DDoS 緩和|無維度|
|VipAvailability|Availability|Count|平均值|期間內的平均 IPAddress 可用性|Port|
|ByteCount|位元組計數|Count|總計|在期間內傳輸的位元組總數|連接埠，方向|
|PacketCount|封包計數|Count|總計|在期間內傳輸的封包總數|連接埠，方向|
|SynCount|SYN 計數|Count|總計|在期間內傳輸的 SYN 封包總數|連接埠，方向|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|Throughput|Throughput|每秒位元組|平均值|應用程式閘道每秒已服務的位元組數目|無維度|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|通道頻寬|每秒位元組|平均值|通道的平均頻寬 (位元組/秒)|ConnectionName，RemoteIP|
|TunnelPeakBandwidth|通道尖峰頻寬|每秒位元組|平均值|通道的尖峰頻寬 (位元組/秒)|ConnectionName，RemoteIP|
|TunnelEgressBytes|通道輸出位元組數|位元組|平均值|五分鐘間隔的通道傳出位元組數|ConnectionName，RemoteIP|
|TunnelIngressBytes|通道輸入位元組數|位元組|平均值|五分鐘間隔的通道傳入位元組數|ConnectionName，RemoteIP|
|TunnelEgressPackets|通道輸出封包數|Count|平均值|五分鐘間隔的通道傳出封包計數|ConnectionName，RemoteIP|
|TunnelIngressPackets|通道輸入封包數|Count|平均值|五分鐘間隔的通道傳入封包計數|ConnectionName，RemoteIP|
|TunnelEgressPacketDropTSMismatch|通道輸出 TS 不相符封包捨棄|Count|平均值|五分鐘間隔從流量選取器捨棄之不符合通道的傳出封包|ConnectionName，RemoteIP|
|TunnelIngressPacketDropTSMismatch|通道輸入 TS 不符合封包捨棄|Count|平均值|五分鐘間隔從流量選取器捨棄之不符合通道的傳入封包|ConnectionName，RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|BytesIn|BytesIn|Count|總計|Azure 位元組輸入|無維度|
|BytesOut|BytesOut|Count|總計|Azure 位元組輸出|無維度|
|BitsInPerSecond|BitsInPerSecond|每秒計數|平均值|每秒輸入到 Azure 的位元數|無維度|
|BitsOutPerSecond|BitsOutPerSecond|每秒計數|平均值|每秒從 Azure 輸出的位元數|無維度|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|QpsByEndpoint|傳回的依端點查詢數|Count|總計|在指定時間範圍內傳回 Traffic Manager 端點的次數|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|依端點的端點狀態|Count|最大值|若端點探查狀態為「已啟用」則為 1，否則為 0。|EndpointName|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|registration.all|註冊作業數|Count|總計|所有成功註冊作業 (建立、更新、查詢和刪除) 的計數。 |無維度|
|registration.create|註冊建立作業|Count|總計|所有成功註冊建立的計數。|無維度|
|registration.update|註冊更新作業|Count|總計|所有成功註冊更新的計數。|無維度|
|registration.get|註冊讀取作業|Count|總計|所有成功註冊查詢的計數。|無維度|
|registration.delete|註冊刪除作業|Count|總計|所有成功註冊刪除的計數。|無維度|
|incoming|傳入訊息|Count|總計|所有成功傳送 API 呼叫的計數。 |無維度|
|incoming.scheduled|傳送已排程的推播通知|Count|總計|取消已排程的推播通知|無維度|
|incoming.scheduled.cancel|取消已排程的推播通知|Count|總計|取消已排程的推播通知|無維度|
|scheduled.pending|暫止已排程的通知|Count|總計|暫止已排程的通知|無維度|
|installation.all|安裝管理作業|Count|總計|安裝管理作業|無維度|
|installation.get|取得安裝作業|Count|總計|取得安裝作業|無維度|
|installation.upsert|建立或更新安裝作業|Count|總計|建立或更新安裝作業|無維度|
|installation.patch|修補安裝作業|Count|總計|修補安裝作業|無維度|
|installation.delete|刪除安裝作業|Count|總計|刪除安裝作業|無維度|
|outgoing.allpns.success|成功通知|Count|總計|所有成功通知的計數。|無維度|
|outgoing.allpns.invalidpayload|承載錯誤|Count|總計|因為 PNS 傳回錯誤承載錯誤而失敗的推播計數。|無維度|
|outgoing.allpns.pnserror|外部通知系統錯誤|Count|總計|因為與 PNS 通訊發生問題 (不包括驗證問題) 而失敗的推播計數。|無維度|
|outgoing.allpns.channelerror|通道錯誤|Count|總計|因為通道無效、未與正確的應用程式相關聯、已節流處理或過期，導致失敗的推播計數。|無維度|
|outgoing.allpns.badorexpiredchannel|錯誤或過期的通道錯誤|Count|總計|因為註冊中的通道/權杖/registrationId 過期或無效，導致失敗的推播計數。|無維度|
|outgoing.wns.success|WNS 成功通知|Count|總計|所有成功通知的計數。|無維度|
|outgoing.wns.invalidcredentials|WNS 授權錯誤 (無效認證)|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。 (Windows Live 無法識別認證)。|無維度|
|outgoing.wns.badchannel|WNS 不正確的通道錯誤|Count|總計|因為無法識別註冊中的 ChannelURI (WNS 狀態︰404 找不到)，導致失敗的推播計數。|無維度|
|outgoing.wns.expiredchannel|WNS 過期通道錯誤|Count|總計|因為 ChannelURIi 過期 (WNS 狀態︰ 410 不存在) 而失敗的推播計數。|無維度|
|outgoing.wns.throttled|WNS 節流通知|Count|總計|因為 WNS 正在節流此應用程式，導致失敗的推播計數 (WNS 狀態︰406 無法接受)。|無維度|
|outgoing.wns.tokenproviderunreachable|WNS 授權錯誤 (無法連線)|Count|總計|無法連線到 Windows Live。|無維度|
|outgoing.wns.invalidtoken|WNS 授權錯誤 (無效權杖)|Count|總計|提供給 WNS 的權杖無效 (WNS 狀態︰ 401 未經授權)。|無維度|
|outgoing.wns.wrongtoken|WNS 授權錯誤 (錯誤權杖)|Count|總計|提供給 WNS 的權杖有效，但卻是給另一個應用程式 (WNS 狀態︰403 禁止)。 如果註冊中的 ChannelURI 與另一個應用程式相關聯，可能會發生這種情形。 請檢查用戶端應用程式相關聯的應用程式，就是認證位於通知中樞的同一個應用程式。|無維度|
|outgoing.wns.invalidnotificationformat|WNS 無效通知格式|Count|總計|通知的格式無效 (WNS 狀態︰400)。 請注意，WNS 不會拒絕所有無效承載。|無維度|
|outgoing.wns.invalidnotificationsize|WNS 無效通知大小錯誤|Count|總計|通知承載太大 (WNS 狀態︰413)。|無維度|
|outgoing.wns.channelthrottled|WNS 通道已節流處理|Count|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (WNS 回應標頭︰X-WNS-NotificationStatus:channelThrottled)。|無維度|
|outgoing.wns.channeldisconnected|WNS 通道已中斷連線|Count|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (WNS 回應標頭︰X-WNS-DeviceConnectionStatus: disconnected)。|無維度|
|outgoing.wns.dropped|WNS 已捨棄通知|Count|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (X-WNS-NotificationStatus: dropped 但不是 X-WNS-DeviceConnectionStatus: disconnected)。|無維度|
|outgoing.wns.pnserror|WNS 錯誤|Count|總計|因為與 WNS 通訊錯誤而未傳遞通知。|無維度|
|outgoing.wns.authenticationerror|WNS 驗證錯誤|Count|總計|因為與 Windows Live 通訊錯誤、無效認證或不正確的權杖，導致未傳遞通知。|無維度|
|outgoing.apns.success|APNS 成功通知|Count|總計|所有成功通知的計數。|無維度|
|outgoing.apns.invalidcredentials|APNS 授權錯誤|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無維度|
|outgoing.apns.badchannel|APNS 不正確的通道錯誤|Count|總計|因為權杖無效而失敗的推播計數 (APNS 狀態碼︰8)。|無維度|
|outgoing.apns.expiredchannel|APNS 過期通道錯誤|Count|總計|由 APNS 意見反應通道宣告失效的權杖計數。|無維度|
|outgoing.apns.invalidnotificationsize|APNS 無效通知大小錯誤|Count|總計|因為承載太大而失敗的推播計數 (APNS 狀態碼︰7)。|無維度|
|outgoing.apns.pnserror|APNS 錯誤|Count|總計|因為與 APNS 通訊錯誤而失敗的推播計數。|無維度|
|outgoing.gcm.success|GCM 成功通知|Count|總計|所有成功通知的計數。|無維度|
|outgoing.gcm.invalidcredentials|GCM 授權錯誤 (無效認證)|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無維度|
|outgoing.gcm.badchannel|GCM 不正確的通道錯誤|Count|總計|因為無法識別註冊中的 registrationId 而失敗的推播計數 (GCM 結果：無效註冊)。|無維度|
|outgoing.gcm.expiredchannel|GCM 過期通道錯誤|Count|總計|因為註冊中的 registrationId 過期而失敗的推播計數 (GCM 結果：NotRegistered)。|無維度|
|outgoing.gcm.throttled|GCM 已將通知節流處理|Count|總計|因為 GCM 已將此應用程式節流處理而失敗的推播計數 (GCM 狀態碼︰501-599 或結果：Unavailable)。|無維度|
|outgoing.gcm.invalidnotificationformat|GCM 無效通知格式|Count|總計|因為承載未正確格式化而失敗的推播計數 (GCM 結果︰InvalidDataKey 或 InvalidTtl)。|無維度|
|outgoing.gcm.invalidnotificationsize|GCM 無效通知大小錯誤|Count|總計|因為承載太大而失敗的推播計數 (GCM 結果︰MessageTooBig)。|無維度|
|outgoing.gcm.wrongchannel|GCM 不正確的通道錯誤|Count|總計|因為註冊中的 registrationId 未與目前的應用程式相關聯，導致失敗的推播計數 (GCM 結果：InvalidPackageName)。|無維度|
|outgoing.gcm.pnserror|GCM 錯誤|Count|總計|因為與 GCM 通訊錯誤而失敗的推播計數。|無維度|
|outgoing.gcm.authenticationerror|GCM 驗證錯誤|Count|總計|因為 PNS 不接受提供的認證、已封鎖認證，或應用程式中未正確設定 SenderId，導致失敗的推播計數 (GCM 結果︰MismatchedSenderId)。|無維度|
|outgoing.mpns.success|MPNS 成功通知|Count|總計|所有成功通知的計數。|無維度|
|outgoing.mpns.invalidcredentials|MPNS 無效認證|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無維度|
|outgoing.mpns.badchannel|MPNS 不正確的通道錯誤|Count|總計|因為無法識別註冊中的 ChannelURI 而失敗的推播計數 (MPNS 狀態︰404 找不到)。|無維度|
|outgoing.mpns.throttled|MPNS 已將通知節流處理|Count|總計|因為 MPNS 正在節流此應用程式，導致失敗的推播計數 (MPNS 狀態︰406 無法接受)。|無維度|
|outgoing.mpns.invalidnotificationformat|MPNS 無效通知格式|Count|總計|因為通知的承載太大而失敗的推播計數。|無維度|
|outgoing.mpns.channeldisconnected|MPNS 通道已中斷連線|Count|總計|因為註冊中的 ChannelURI 中斷連線而失敗的推播計數 (MPNS 狀態︰412 找不到)。|無維度|
|outgoing.mpns.dropped|MPNS 已捨棄通知|Count|總計|MPNS 已捨棄的推播計數 (MPNS 回應標頭︰X-NotificationStatus: QueueFull 或 Suppressed)。|無維度|
|outgoing.mpns.pnserror|MPNS 錯誤|Count|總計|因為與 MPNS 通訊錯誤而失敗的推播計數。|無維度|
|outgoing.mpns.authenticationerror|MPNS 驗證錯誤|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無維度|
|notificationhub.pushes|所有外寄通知|Count|總計|通知中樞的所有外寄通知|無維度|
|incoming.all.requests|所有傳入要求|Count|總計|針對通知中樞傳入的要求總數|無維度|
|incoming.all.failedrequests|所有傳入的失敗要求|Count|總計|針對通知中樞傳入的失敗要求總數|無維度|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|SearchLatency|搜尋延遲|秒|平均值|搜尋服務的平均搜尋延遲|無維度|
|SearchQueriesPerSecond|每秒搜尋查詢|每秒計數|平均值|搜尋服務的每秒搜尋查詢|無維度|
|ThrottledSearchQueriesPercentage|節流的搜尋查詢百分比|百分比|平均值|已針對搜尋查詢進行節流的搜尋服務百分比|無維度|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|CPUXNS|每個命名空間的 CPU 使用量|百分比|最大值|服務匯流排進階命名空間 CPU 使用量計量|無維度|
|WSXNS|每個命名空間的記憶體大小使用量|百分比|最大值|服務匯流排進階命名空間記憶體使用量計量|無維度|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|無維度|
|physical_data_read_percent|資料 IO 百分比|百分比|平均值|資料 IO 百分比|無維度|
|log_write_percent|記錄 IO 百分比|百分比|平均值|記錄 IO 百分比|無維度|
|dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|無維度|
|storage|資料庫大小總計|位元組|最大值|資料庫大小總計|無維度|
|connection_successful|成功的連線|Count|總計|成功的連線|無維度|
|connection_failed|失敗的連線|Count|總計|失敗的連線|無維度|
|blocked_by_firewall|遭到防火牆封鎖|Count|總計|遭到防火牆封鎖|無維度|
|deadlock|死結|Count|總計|死結|無維度|
|storage_percent|資料庫大小百分比|百分比|最大值|資料庫大小百分比|無維度|
|xtp_storage_percent|記憶體內部 OLTP 儲存體百分比|百分比|平均值|記憶體內部 OLTP 儲存體百分比|無維度|
|workers_percent|背景工作角色百分比|百分比|平均值|背景工作角色百分比|無維度|
|sessions_percent|工作階段百分比|百分比|平均值|工作階段百分比|無維度|
|dtu_limit|DTU 限制|Count|平均值|DTU 限制|無維度|
|dtu_used|已使用 DTU|Count|平均值|已使用 DTU|無維度|
|dwu_limit|DWU 限制|Count|最大值|DWU 限制|無維度|
|dwu_consumption_percent|DWU 百分比|百分比|最大值|DWU 百分比|無維度|
|dwu_used|已使用 DWU|Count|最大值|已使用 DWU|無維度|
|dw_cpu_percent|DW 節點層級 CPU 百分比|百分比|平均值|DW 節點層級 CPU 百分比|dw_logical_node_id|
|dw_physical_data_read_percent|DW 節點層級資料 IO 百分比|百分比|平均值|DW 節點層級資料 IO 百分比|dw_logical_node_id|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|無維度|
|database_cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|DatabaseResourceId|
|physical_data_read_percent|資料 IO 百分比|百分比|平均值|資料 IO 百分比|無維度|
|database_physical_data_read_percent|資料 IO 百分比|百分比|平均值|資料 IO 百分比|DatabaseResourceId|
|log_write_percent|記錄 IO 百分比|百分比|平均值|記錄 IO 百分比|無維度|
|database_log_write_percent|記錄 IO 百分比|百分比|平均值|記錄 IO 百分比|DatabaseResourceId|
|dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|無維度|
|database_dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|DatabaseResourceId|
|storage_percent|儲存體百分比|百分比|平均值|儲存體百分比|無維度|
|workers_percent|背景工作角色百分比|百分比|平均值|背景工作角色百分比|無維度|
|database_workers_percent|背景工作角色百分比|百分比|平均值|背景工作角色百分比|DatabaseResourceId|
|sessions_percent|工作階段百分比|百分比|平均值|工作階段百分比|無維度|
|database_sessions_percent|工作階段百分比|百分比|平均值|工作階段百分比|DatabaseResourceId|
|eDTU_limit|eDTU 限制|Count|平均值|eDTU 限制|無維度|
|storage_limit|儲存體限制|位元組|平均值|儲存體限制|無維度|
|eDTU_used|已使用 eDTU|Count|平均值|已使用 eDTU|無維度|
|storage_used|已使用儲存體|位元組|平均值|已使用儲存體|無維度|
|database_storage_used|已使用儲存體|位元組|平均值|已使用儲存體|DatabaseResourceId|
|xtp_storage_percent|記憶體內部 OLTP 儲存體百分比|百分比|平均值|記憶體內部 OLTP 儲存體百分比|無維度|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|DatabaseResourceId, ElasticPoolResourceId|
|storage_used|已使用儲存體|位元組|平均值|已使用儲存體|ElasticPoolResourceId|
|database_storage_used|已使用儲存體|位元組|平均值|已使用儲存體|DatabaseResourceId, ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|UsedCapacity|已使用容量|位元組|平均值|帳戶使用的容量|無維度|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType，GeoType，ApiName|
|Ingress|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType，ApiName|
|Egress|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType，ApiName|
|SuccessServerLatency|成功伺服器延遲|毫秒|平均值|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|GeoType，ApiName|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|平均值|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType，ApiName|
|Availability|Availability|百分比|平均值|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType，ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|BlobCapacity|Blob 容量|位元組|平均值|儲存體帳戶之 Blob 服務所使用的儲存體量 (位元組)。|BlobType|
|BlobCount|Blob 計數|Count|平均值|儲存體帳戶之 Blob 服務中的 Blob 數目。|BlobType|
|ContainerCount|Blob 容器計數|Count|平均值|儲存體帳戶之 Blob 服務中的容器數目。|無維度|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType，GeoType，ApiName|
|Ingress|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType，ApiName|
|Egress|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType，ApiName|
|SuccessServerLatency|成功伺服器延遲|毫秒|平均值|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|GeoType，ApiName|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|平均值|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType，ApiName|
|Availability|Availability|百分比|平均值|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType，ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|TableCapacity|資料表容量|位元組|平均值|儲存體帳戶之資料表服務所使用的儲存體量 (位元組)。|無維度|
|TableCount|資料表計數|Count|平均值|儲存體帳戶之資料表服務中的資料表數目。|無維度|
|TableEntityCount|資料表實體計數|Count|平均值|儲存體帳戶之資料表服務中的資料表實體數目。|無維度|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType，GeoType，ApiName|
|Ingress|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType，ApiName|
|Egress|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType，ApiName|
|SuccessServerLatency|成功伺服器延遲|毫秒|平均值|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|GeoType，ApiName|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|平均值|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType，ApiName|
|Availability|Availability|百分比|平均值|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType，ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|QueueCapacity|佇列容量|位元組|平均值|儲存體帳戶之佇列服務所使用的儲存體量 (位元組)。|無維度|
|QueueCount|佇列計數|Count|平均值|儲存體帳戶之佇列服務中的佇列數目。|無維度|
|QueueMessageCount|佇列訊息計數|Count|平均值|儲存體帳戶之佇列服務中的佇列訊息大約數目。|無維度|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType，GeoType，ApiName|
|Ingress|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType，ApiName|
|Egress|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType，ApiName|
|SuccessServerLatency|成功伺服器延遲|毫秒|平均值|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|GeoType，ApiName|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|平均值|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType，ApiName|
|Availability|Availability|百分比|平均值|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType，ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|FileCapacity|檔案容量|位元組|平均值|儲存體帳戶之檔案服務所使用的儲存體量 (位元組)。|無維度|
|FileCount|檔案計數|Count|平均值|儲存體帳戶之檔案服務中的檔案數目。|無維度|
|FileShareCount|檔案共用計數|Count|平均值|儲存體帳戶之檔案服務中的檔案共用數目。|無維度|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType，GeoType，ApiName|
|Ingress|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType，ApiName|
|Egress|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType，ApiName|
|SuccessServerLatency|成功伺服器延遲|毫秒|平均值|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|GeoType，ApiName|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|平均值|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType，ApiName|
|Availability|Availability|百分比|平均值|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType，ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|ResourceUtilization|SU % 使用率|百分比|最大值|SU % 使用率|無維度|
|InputEvents|輸入事件|Count|總計|輸入事件|無維度|
|InputEventBytes|輸入事件位元組|位元組|總計|輸入事件位元組|無維度|
|LateInputEvents|延遲輸入事件|Count|總計|延遲輸入事件|無維度|
|OutputEvents|輸出事件|Count|總計|輸出事件|無維度|
|ConversionErrors|資料轉換錯誤|Count|總計|資料轉換錯誤|無維度|
|Errors|執行階段錯誤|Count|總計|執行階段錯誤|無維度|
|DroppedOrAdjustedEvents|次序錯誤事件|Count|總計|次序錯誤事件|無維度|
|AMLCalloutRequests|函式要求|Count|總計|函式要求|無維度|
|AMLCalloutFailedRequests|失敗的函式要求|Count|總計|失敗的函式要求|無維度|
|AMLCalloutInputEvents|函式事件|Count|總計|函式事件|無維度|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|CpuPercentage|CPU 百分比|百分比|平均值|CPU 百分比|執行個體|
|MemoryPercentage|記憶體百分比|百分比|平均值|記憶體百分比|執行個體|
|DiskQueueLength|磁碟佇列長度|Count|總計|磁碟佇列長度|執行個體|
|HttpQueueLength|Http 佇列長度|Count|總計|Http 佇列長度|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (不包括函式)

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|秒|總計|CPU 時間|執行個體|
|Requests|Requests|Count|總計|Requests|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http101|Http 101|Count|總計|Http 101|執行個體|
|Http2xx|Http 2xx|Count|總計|Http 2xx|執行個體|
|Http3xx|Http 3xx|Count|總計|Http 3xx|執行個體|
|Http401|Http 401|Count|總計|Http 401|執行個體|
|Http403|Http 403|Count|總計|Http 403|執行個體|
|Http404|Http 404|Count|總計|Http 404|執行個體|
|Http406|Http 406|Count|總計|Http 406|執行個體|
|Http4xx|Http 4xx|Count|總計|Http 4xx|執行個體|
|Http5xx|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|MemoryWorkingSet|記憶體工作集|位元組|平均值|記憶體工作集|執行個體|
|AverageMemoryWorkingSet|平均記憶體工作集|位元組|平均值|平均記憶體工作集|執行個體|
|AverageResponseTime|平均回應時間|秒|平均值|平均回應時間|執行個體|
|FunctionExecutionUnits|函式執行單位|Count|平均值|函式執行單位|執行個體|
|FunctionExecutionCount|函式執行計數|Count|平均值|函式執行計數|執行個體|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (函式)

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http5xx|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|MemoryWorkingSet|記憶體工作集|位元組|平均值|記憶體工作集|執行個體|
|AverageMemoryWorkingSet|平均記憶體工作集|位元組|平均值|平均記憶體工作集|執行個體|
|FunctionExecutionUnits|函式執行單位|Count|平均值|函式執行單位|執行個體|
|FunctionExecutionCount|函式執行計數|Count|平均值|函式執行計數|執行個體|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|秒|總計|CPU 時間|執行個體|
|Requests|Requests|Count|總計|Requests|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http101|Http 101|Count|總計|Http 101|執行個體|
|Http2xx|Http 2xx|Count|總計|Http 2xx|執行個體|
|Http3xx|Http 3xx|Count|總計|Http 3xx|執行個體|
|Http401|Http 401|Count|總計|Http 401|執行個體|
|Http403|Http 403|Count|總計|Http 403|執行個體|
|Http404|Http 404|Count|總計|Http 404|執行個體|
|Http406|Http 406|Count|總計|Http 406|執行個體|
|Http4xx|Http 4xx|Count|總計|Http 4xx|執行個體|
|Http5xx|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|MemoryWorkingSet|記憶體工作集|位元組|平均值|記憶體工作集|執行個體|
|AverageMemoryWorkingSet|平均記憶體工作集|位元組|平均值|平均記憶體工作集|執行個體|
|AverageResponseTime|平均回應時間|秒|平均值|平均回應時間|執行個體|
|FunctionExecutionUnits|函式執行單位|Count|平均值|函式執行單位|執行個體|
|FunctionExecutionCount|函式執行計數|Count|平均值|函式執行計數|執行個體|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|Requests|Requests|Count|總計|Requests|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http101|Http 101|Count|總計|Http 101|執行個體|
|Http2xx|Http 2xx|Count|總計|Http 2xx|執行個體|
|Http3xx|Http 3xx|Count|總計|Http 3xx|執行個體|
|Http401|Http 401|Count|總計|Http 401|執行個體|
|Http403|Http 403|Count|總計|Http 403|執行個體|
|Http404|Http 404|Count|總計|Http 404|執行個體|
|Http406|Http 406|Count|總計|Http 406|執行個體|
|Http4xx|Http 4xx|Count|總計|Http 4xx|執行個體|
|Http5xx|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|AverageResponseTime|平均回應時間|秒|平均值|平均回應時間|執行個體|
|CpuPercentage|CPU 百分比|百分比|平均值|CPU 百分比|執行個體|
|MemoryPercentage|記憶體百分比|百分比|平均值|記憶體百分比|執行個體|
|DiskQueueLength|磁碟佇列長度|Count|總計|磁碟佇列長度|執行個體|
|HttpQueueLength|Http 佇列長度|Count|總計|Http 佇列長度|執行個體|
|ActiveRequests|使用中的要求|Count|總計|使用中的要求|執行個體|
|TotalFrontEnds|前端總數|Count|平均值|前端總數|執行個體|
|SmallAppServicePlanInstances|小型 App Service 方案背景工作角色|Count|平均值|小型 App Service 方案背景工作角色|執行個體|
|MediumAppServicePlanInstances|中型 App Service 方案背景工作角色|Count|平均值|中型 App Service 方案背景工作角色|執行個體|
|LargeAppServicePlanInstances|大型 App Service 方案背景工作角色|Count|平均值|大型 App Service 方案背景工作角色|執行個體|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|計量|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|
|WorkersTotal|背景工作角色總數|Count|平均值|背景工作角色總數|執行個體|
|WorkersAvailable|可用的背景工作角色|Count|平均值|可用的背景工作角色|執行個體|
|WorkersUsed|已使用的背景工作角色|Count|平均值|已使用的背景工作角色|執行個體|

## <a name="next-steps"></a>後續步驟
* [了解 Azure 監視器中的計量](monitoring-overview-metrics.md)
* [建立計量警示](insights-receive-alert-notifications.md)
* [將計量匯出至儲存體、事件中樞或 Log Analytics](monitoring-overview-of-diagnostic-logs.md)
