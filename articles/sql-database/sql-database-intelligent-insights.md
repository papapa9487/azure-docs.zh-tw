---
title: "Intelligent Insights 監視資料庫使用情況 - Azure SQL Database | Microsoft Docs"
description: "Intelligent Insights 可讓您了解資料庫效能發生什麼問題"
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
ms.openlocfilehash: c3b11dd50fa8c94d3bf80e02a8a319030c375133
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="intelligent-insights"></a>Intelligent Insights

「Intelligent Insights 可讓您了解資料庫效能發生什麼問題。」

Azure SQL Database 內建的智慧功能會透過人工智慧持續監視資料庫使用情況，並偵測導致效能不佳的干擾性事件。 一旦偵測到，就會執行詳細的分析來產生含有該問題智慧型評估的診斷記錄。 此評估包含資料庫效能問題的根本原因分析，並且會儘可能提供效能改進建議 - 亦即「智慧型深入解析」 

## <a name="what-can-intelligent-insights-do-for-you"></a>Intelligent Insights 可以為您做什麼？

Intelligent Insights 是 Azure 內建智慧功能的一項獨特功能，可提供下列價值：

- 主動監視
- 量身打造的效能深入解析
- 提早偵測到資料庫效能降低
- 所偵測到問題的根本原因分析 (RCA)
- 效能改進建議
- 以數十萬個資料庫向外延展的功能
- 為 DevOps 資源及擁有權總成本帶來正面影響

## <a name="how-does-intelligent-insights-work"></a>Intelligent Insights 如何運作？

Intelligent Insights 會將過去 1 小時的資料庫工作負載與過去 7 天的基準工作負載做比較，來分析 Azure SQL Database 效能。 資料庫工作負載是由系統判斷為對資料庫效能最重要的查詢 (例如重複次數最多和最大型的查詢) 所組成。 每個資料庫根據其結構、資料、使用方式及應用程式都是獨一無二的，因此所產生的每個工作負載基準對個別執行個體來說都是特定且唯一的。 在與工作負載基準無關的方面，Intelligent Insights 還會監視絕對作業臨界值，並偵測有關等候時間過長的問題、重大例外狀況，以及有關可能影響效能之查詢參數化的問題。

使用人工智慧從多個觀察的計量偵測到效能降低問題之後，就會執行分析來輸出診斷記錄，其中含有關於資料庫發生之情況的智慧型深入解析。 Intelligent Insights 可讓您從資料庫效能問題一開始出現到解決為止，都能輕鬆追蹤情況。 做法是追蹤所偵測到之每個問題在其整個生命週期 (從初始問題偵測、驗證效能改進，到其完成為止) 中的狀態。 系統會每隔 15 分鐘在診斷記錄中提供一次更新。 

<center>![伺服器](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)</center>

用來測量和偵測資料庫效能問題的計量根據的是查詢持續時間、逾時要求、過長的等候時間及發生錯誤的要求，在本文件的[偵測計量](sql-database-intelligent-insights.md#detection-metrics)一節中會有進一步的詳細說明。

## <a name="degradations-detected"></a>偵測到的效能降低

已識別出的 Azure SQL Database 效能降低會記錄在診斷記錄中，其中會包含由下列屬性組成的智慧項目：

| 屬性             | 詳細資料              |
| :------------------- | ------------------- |
| 資料庫資訊  | 偵測到有深入解析之資料庫的相關中繼資料，例如資源 URI |
| 觀察的時間範圍 | 偵測到之深入解析的期間開始和結束時間 |
| 受影響的計量 | 導致產生深入解析的計量： <ul><li>查詢持續時間增加 [秒]</li><li>過長的等候時間 [秒]</li><li>逾時的要求 [百分比]</li><li>發生錯誤的要求 [百分比]</li></ul>|
| 影響值 | 所測量計量的值 |
| 受影響的查詢和錯誤碼 | 查詢雜湊或錯誤碼。 這些可用來與受影響的查詢輕鬆相互關聯。 系統會提供由查詢持續時間增加、等候時間、逾時計數或錯誤碼所組成的計量。 |
| 偵測 | 發生事件時在資料庫識別出的偵測。 一共有 15 個偵測模式。 請參閱[使用 Intelligent Insights 針對資料庫效能問題進行疑難排解](sql-database-intelligent-insights-troubleshoot-performance.md)。 |
| 根本原因分析 | 所識別之問題的「根原因分析」(RCA)，此分析會以人類看得懂的格式顯示。 有些深入解析可能會在可能的情況下包含效能改進建議。 |
|||

## <a name="issues-state-lifecycle-active-verifying-and-completed"></a>問題狀態生命週期：作用中、正在驗證及已完成

診斷記錄中記錄的效能問題會以問題生命週期的三種狀態之一標示：作用中、正在驗證及已完成。 偵測到效能問題之後，只要 Azure SQL 內建的智慧功能將其視為現存問題，該問題就會標示為「作用中」。 當系統將問題視為已緩和時，就會對其進行驗證並將問題狀態變更為「正在驗證」。 當 Azure SQL 內建的智慧功能將問題視為已解決時，就會將問題狀態標示為「已完成」。

## <a name="using-intelligent-insights"></a>使用 Intelligent Insights

如 [Azure SQL Database 計量和診斷記錄](sql-database-metrics-diag-logging.md)所述，Intelligent Insights 診斷記錄可以傳送給 Azure Log Analytics、「Azure 事件中樞」及「Azure 儲存體」。 將記錄傳送給上述其中一個目標之後，即可使用 Microsoft 或協力廠商工具，利用該日誌來開發自訂警示和監視。 如需了解使用 Intelligent Insights 進行 Azure SQL Database 效能疑難排解的做法，請參閱[使用 Intelligent Insights 針對 Azure SQL Database 效能問題進行疑難排解](sql-database-intelligent-insights-troubleshoot-performance.md)。

## <a name="built-in-intelligent-insights-analytics-with-azure-log-analytics"></a>Azure Log Analytics 的內建 Intelligent Insights 分析 

Azure Log Analytics 解決方案除了 Intelligent Insights 診斷記錄資料之外，還提供報告和警示功能。 以下是「Azure SQL 分析」中的 Intelligent Insights 報告範例。

![伺服器](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

設定讓 Intelligent Insights 診斷記錄將資料串流處理至「Azure SQL 分析」之後，您便可以[使用 Azure SQL 分析來監視 Azure SQL Database](../log-analytics/log-analytics-azure-sql.md)。

## <a name="custom-integrations-of-intelligent-insights-log"></a>自訂的 Intelligent Insights 記錄整合

若要使用 Microsoft 或協力廠商工具來開發自訂警示和監視，請參閱[使用 Intelligent Insights 資料庫效能診斷記錄](sql-database-intelligent-insights-use-diagnostics-log.md)。

## <a name="how-to-set-up-intelligent-insights-with-azure-event-hub"></a>如何設定 Intelligent Insights 搭配 Azure 事件中樞

- 透過[將 Azure 診斷記錄串流至事件中樞](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)，設定讓 Intelligent Insights 將記錄事件串流處理至「Azure 事件中樞」。
- 透過[如何處理事件中樞中的計量和診斷記錄](sql-database-metrics-diag-logging.md#stream-into-azure-storage)，將「Azure 事件中樞」用於自訂的監視和警示。 

## <a name="how-to-set-up-intelligent-insights-with-azure-storage"></a>如何設定 Intelligent Insights 搭配 Azure 儲存體

- 透過[串流到 Azure 儲存體中](sql-database-metrics-diag-logging.md#stream-into-azure-storage)，設定以「Azure 儲存體」儲存 Intelligent Insights。

## <a name="detection-metrics"></a>偵測計量

用於產生 Intelligent Insights 之偵測模型的計量是以監視下列各項為基礎：

- 查詢持續時間
- 逾時要求
- 過長的等候時間 
- 發生錯誤的要求

查詢持續時間和逾時要求會作為偵測資料庫工作負載效能相關問題的主要模型。 這是因為它們會直接測量工作負載發生什麼情況。 為了偵測所有可能的工作負載效能降低情況，會使用過長的等候時間和發生錯誤的要求作為額外的模型，以指出影響工作負載效能的問題。

系統會自動將對工作負載所做的變更及對資料庫發出的查詢要求數所做的變更納入考量，以自動判斷正常和異常的資料庫效能臨界值。

將會透過以科學方式衍生並將所偵測到之每個問題分類的資料模型，將所有計量以各種關聯性一起納入考量。 透過智慧型深入解析提供的資訊包括所偵測到之效能問題的詳細資料、問題發生的根本原因分析，以及在可能的情況下針對所監視之 Azure SQL Database 提出的效能改進建議。

## <a name="query-duration"></a>查詢持續時間

查詢持續時間效能降低模型會分析個別的查詢，並偵測與效能基準相比，花費在編譯和執行查詢上的時間是否增加。

當 Azure SQL Database 內建的智慧功能偵測到查詢編譯或查詢執行時間大幅增加而影響到工作負載效能時，就會將這些查詢標示為具有查詢持續時間效能降低問題。 

Intelligent Insights 診斷記錄會輸出效能降低之查詢的查詢雜湊，指出效能降低是否與查詢編譯或執行時間增加及查詢持續時間增加有關。

## <a name="timeout-requests"></a>逾時要求

逾時要求效能降低模型會分析個別的查詢，並偵測與效能基準期間相比，查詢執行層級的逾時及資料庫層級的整體要求逾時是否增加。

由於有些查詢可能甚至在達到執行階段之前就發生逾時，因此 Azure SQL Database 內建的智慧功能也會以已中止的背景工作角色與所發出的要求作為方法，來測量和分析已到達資料庫的所有查詢 (不論是否已達到執行階段)。 

在所執行之查詢的逾時數目或已中止之要求背景工作角色的數目增加到超出系統所管理的臨界值時，診斷記錄中就會填入智慧型深入解析。

所產生的深入解析會包含逾時的要求數、逾時的查詢數，並指出效能降低是否與執行階段或整體資料庫層級的逾時增加有關。 當系統認為逾時增加對資料庫效能有重大影響時，就會將這些查詢標示為具有逾時效能降低問題。 

## <a name="excessive-wait-times"></a>過長的等候時間

過長的等候時間模型會監視個別的資料庫查詢，並偵測超出系統所管理之絕對臨界值的異常高查詢等候統計資料。 您可以利用新的 SQL Server 功能「查詢存放區等候統計資料」(sys.query_store_wait_stats) 功能來觀察下列查詢過長等候時間計量：

- 達到資源限制
- 達到彈性集區資源限制
- 過多的背景工作或工作階段執行緒數目
- 過多的資料庫鎖定
- 記憶體壓力
- 其他等候統計資料

當達到資源限制或彈性集區資源限制時，即表示訂用帳戶上或彈性集區中的可用資源耗用量已增加到超出象徵工作負載效能降低的絕對臨界值。 當背景工作或工作階段執行緒數目過多時，即表示所起始的背景工作執行緒或工作階段數目已超出象徵工作負載效能降低的絕對臨界值。

當資料庫鎖定過多時，即表示資料庫上的鎖定計數已超出象徵工作負載效能降低的絕對臨界值。 發生記憶體壓力時，即表示要求授與記憶體的執行緒數目已增加到超出象徵工作負載效能降低的絕對臨界值。

其他等候統計資料偵測所表示的情況則是，透過「查詢存放區等候統計資料」測量的其他計量超出象徵工作負載效能降低的絕對臨界值。

一旦偵測到過長的等候時間，Intelligent Insights 診斷記錄就會根據可用的資料，輸出效能降低之影響和受影響查詢的雜湊、導致查詢在執行中等候的計量詳細資料，以及測量到的等候時間。

## <a name="errored-requests"></a>發生錯誤的要求

發生錯誤的要求效能降低模型會監視個別的查詢，並偵測與基準期間相比，發生錯誤的查詢數目是否增加。 此模型也會監視已達到 Azure 資料庫內建智慧功能所管理之絕對臨界值的重大例外狀況。 系統會自動將在受監視期間內，對資料庫和帳戶發出以進行任何工作負載變更的查詢要求數目納入考量。

當系統認為在與所發出的整體要求數目相關的發生錯誤要求數測量到的增加，對工作負載效能有重大影響時，就會將受影響的查詢標示為具有發生錯誤的要求效能降低問題。

Intelligent insights 記錄會輸出發生錯誤的要求計數、效能降低是與發生錯誤的要求數增加有關還是與達到其中一個受監視的重大例外狀況臨界值有關的指示，以及所測量到的效能降低時間。 

如果任何一個受監視的重大例外狀況超出系統所管理的絕對臨界值，就會產生含有重大例外狀況詳細資料的智慧型深入解析。

## <a name="next-steps"></a>後續步驟
* 了解如何[使用 Intelligent Insights 針對 Azure SQL Database 效能問題進行疑難排解](sql-database-intelligent-insights-troubleshoot-performance.md)
* 使用 [Intelligent Insights Azure SQL Database 效能診斷記錄](sql-database-intelligent-insights-use-diagnostics-log.md)
* 了解如何[使用 Azure SQL 分析來監視 Azure SQL Database](../log-analytics/log-analytics-azure-sql.md)
* 了解如何[收集並取用來自 Azure 資源的記錄資料](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)



