---
title: "效能建議 - Azure SQL Database |Microsoft Docs"
description: "Azure SQL Database 會提供可改善 SQL Database 查詢效能的建議。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/20/2017
ms.author: sstein
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 53bfbd602c2c395d510529eacd5b8075b20437ab
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="performance-recommendations"></a>效能建議

Azure SQL Database 會學習和配合您的應用程式，並提供自訂的建議讓您將 SQL Database 的效能最大化。 同時藉由分析 SQL Database 的使用記錄來持續評估效能。 根據資料庫的獨特工作負載模式提供建議，並協助改善其效能。

> [!TIP]
> [自動調整](sql-database-automatic-tuning.md)是建議的效能調整方法。 [Intelligent Insights](sql-database-intelligent-insights.md) 是建議的效能監視方法。 
>

## <a name="create-index-recommendations"></a>建立索引建議
Azure SQL Database 會持續監視正在執行的查詢，並找出可改善效能的索引。 一旦確信遺漏特定索引，將會建立新的 [建立索引] 建議。 評估一段時間之後，Azure SQL Database 會確定索引所能提升的效能。 根據評估的顯現效能，將建議分類為 [高]、[中] 或 [低]。 

使用建議建立的索引永遠都會標記為 auto_created 索引。 查看 sys.indexes 檢視，可以得知哪些索引為 auto_created。 自動建立的索引不會封鎖 ALTER/RENAME 命令。 如果您嘗試卸除已有自動建立之索引的資料行，命令會執行並隨該命令卸除自動建立的索引。 一般索引會對已編製索引的資料行封鎖 ALTER/RENAME 命令。

一旦套用索引建立建議時，Azure SQL Database 會比較基準效能和查詢效能。 如果新的索引可提升效能，建議會標示為成功並提供影響報告。 如果該索引毫無助益，即會自動還原。 如此一來，Azure SQL Database 可確保使用的建議只會改善資料庫效能。

任何「建立索引」建議都有後端停止原則，過去 20 分鐘的資料庫或集區 DTU 使用量高於 80% ，或儲存體使用量高於 90% 時不允許套用建議。 在此情況下，將會延遲建議。

## <a name="drop-index-recommendations"></a>卸除索引建議
除了偵測遺漏的索引，Azure SQL Database 會持續分析現有索引的效能。 如果未使用索引，Azure SQL Database 會建議卸除它。 在兩種情況下會建議使用卸除索引：
* 索引是另一個索引的複本 (編製和內含索引的資料行、分割、結構描述及篩選器相同)
* 索引長時間都未使用 (93 天)

卸除索引建議在實作後也會經過驗證。 如果效能有所提升，即會提供影響報告。 偵測到效能降低時，將會還原建議。


## <a name="parameterize-queries-recommendations"></a>參數化查詢建議
當您有一或多個查詢不斷被重新編譯，但結果都是相同的查詢執行計劃時，即會出現**參數化查詢**建議。 這樣可讓您套用強制參數化，將查詢計劃加入快取，並在未來重複使用，改善效能並減少資源使用量。 

針對 SQL Server 發出的每個查詢一開始需要重新編譯，以產生執行計劃。 每個產生的計畫都會加入至計畫快取，而相同查詢的後續執行可以從快取中重複使用這個計畫，而不需要進一步編譯。 

如果應用程式所傳送查詢包含非參數化的值，則該應用程式會導致效能額外負荷，其中會針對每個這類具有不同參數值的查詢，再次編譯執行計劃。 在許多情況下，具有不同參數值的相同查詢都會產生相同執行計劃，但這些計畫仍會個別加入至計劃快取。 重新編譯執行計劃會使用資料庫資源、增加查詢持續時間，以及造成計劃快取溢位，因而導致系統從快取中回收計劃。 您可以在資料庫上設定強制參數化選項，來改變這個 SQL Server 行為。 

為了協助您評估此建議的影響，我們提供了實際 CPU 使用量和預計 CPU 使用量 (如同已套用建議) 之間的比較。 除了可節省 CPU 之外，您的查詢持續期間將會降低花費在編譯的時間。 這也可讓計畫快取中的額外負荷降低不少，讓大多數的計畫可以保留在快取中並重複使用。 您可以按一下 [套用] 命令，快速且輕鬆地套用此建議。 

一旦套用此建議之後，它將在幾分鐘內於您的資料庫上啟用強制參數化，而它將開始監視程序，此程序大約會持續 24 小時。 經過這段期間之後，您就能看到驗證報告，其中顯示資料庫在套用建議前後 24 小時的 CPU 使用量。 SQL Database 建議程式有一項安全機制，會在偵測到效能變差時，自動還原套用的建議。

## <a name="fix-schema-issues-recommendations"></a>修正結構描述問題建議
當 SQL Database 服務注意到 Azure SQL Database上發生結構描述數目異常狀況相關的 SQL 錯誤時，即會出現**修正結構描述問題**建議。 您的資料庫在一小時內遇到多個結構描述相關的錯誤時 (無效的資料行名稱、無效的物件名稱等)，通常會出現此建議。

「結構描述問題」是 SQL Server 中一個語法錯誤的類別，當 SQL 查詢定義與資料庫結構描述定義不符時即會發生此問題。 例如，查詢預期的其中一個資料行可能在目標資料表中遺失，反之亦然。 

當 Azure SQL Database 服務注意到 Azure SQL Database 上發生結構描述數目異常狀況相關的 SQL 錯誤時，即會出現「修正結構描述問題」建議。 下表顯示與結構描述問題相關的錯誤：

| SQL 錯誤碼 | 訊息 |
| --- | --- |
| 201 |程序或函數 '' 必須有參數 ''，但未提供。 |
| 207 |無效的資料行名稱 '*'。 |
| 208 |無效的物件名稱 '*'。 |
| 213 |資料行名稱或提供的數值數量與資料表定義不相符。 |
| 2812 |找不到預存程序 ' *'。 |
| 8144 |程序或函數 * 指定了太多的引數。 |

## <a name="next-steps"></a>後續步驟
監視建議，並繼續套用建議以改善效能。 資料庫工作負載會動態地持續變更。 SQL Database 建議程式會繼續監視並提供可能改善資料庫效能的建議。 

* 如需了解如何自動調整資料庫索引和查詢執行計畫，請參閱 [Azure SQL Database 自動調整](sql-database-automatic-tuning.md)。
* 如需了解如何使用效能問題的自動化診斷與根本原因分析來自動監視資料庫效能，請參閱 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md)。
* 如需如何在 Azure 入口網站中使用效能建議的步驟，請參閱 [Azure 入口網站中的效能建議](sql-database-advisor-portal.md)。
* 請參閱[查詢效能深入解析](sql-database-query-performance.md)，以了解和檢視排名最前面查詢的效能影響。



