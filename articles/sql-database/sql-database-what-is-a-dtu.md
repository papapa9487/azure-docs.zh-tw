---
title: "SQL Database：DTU 是什麼？ | Microsoft Docs"
description: "了解 Azure SQL Database 交易單元是什麼。"
keywords: "資料庫選項, 資料庫效能"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: 89e3e9ce-2eeb-4949-b40f-6fc3bf520538
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 04/14/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: da3399b9c6642435dc7b40ed1c843217c984d15e
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>資料庫交易單位 (DTU) 和彈性資料庫交易單位 (eDTU)
本文說明資料庫交易單位 (DTU) 和彈性資料庫交易單位 (eDTU)，以及當您達到 DTU 或 eDTU 上限時會發生什麼狀況。  

## <a name="what-are-database-transaction-units-dtus"></a>何謂資料庫交易單位 (DTU)？
針對[服務層](sql-database-single-database-resources.md)中特定效能等級的單一 Azure SQL Database，Microsoft 保證該資料庫具備特定的資源層級 (與 Azure 雲端的其他任何資料庫無關)，並提供可預測的效能等級。 此資源數量會計算為資料庫交易單位 (DTU) 數量，且為 CPU、記憶體、I/O (資料與交易記錄 I/O) 的混合測量。 這些資源的比率，原本是由專為一般實際 OLTP 工作負載所設計的 [OLTP 基準測試工作負載](sql-database-benchmark-overview.md)來判定。 若您的工作負載超過這些任一資源的數量，系統即會節流處理輸送量，因而導致效能變慢和逾時。 您工作負載使用的資源，不會影響到 Azure 雲端中其他 SQL Database 的可用資源，且其他工作負載所用的資源亦不會影響到您 SQL Database 的可用資源。

![週框方塊](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU 最適合用於了解處於不同效能等級與服務層之各 Azure SQL Database 間的相對量。 例如，藉由提升資料庫的效能等級來使 DTU 加倍，等於讓該資料庫可用的資源集合加倍。 例如，相較於具有 5 個 DTU 的 Basic 資料庫，具有 1750 個 DTU 的 Premium P11 資料庫可提供 350 倍的 DTU 計算能力。  

若要深入探索您工作負載的資源 (DTU) 耗用，請使用 [Azure SQL Database 查詢效能深入解析](sql-database-query-performance.md)執行以下動作：

- 依 CPU/持續時間/執行計數識別排名最前面的查詢，對其進行微調可能有助於改善效能。 例如，I/O 密集使用的查詢可能會因使用[記憶體內最佳化技術](sql-database-in-memory.md)獲得助益，可更加妥善地運用處於特定服務層和效能等級的可用記憶體。
- 向下鑽研查詢的詳細資料，以檢視其文字和資源使用量的歷程記錄。
- 存取效能微調建議，其會顯示 [SQL Database Advisor](sql-database-advisor.md) 執行的動作。

您可以在應用程式停機時間最短 (通常平均少於四秒) 的情況下，隨時 [變更服務層](sql-database-service-tiers.md) 。 對於許多企業和應用程式而言，只要能夠建立資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 但如果您有無法預測的使用模式，則管理成本和商務模式就會變得相當困難。 針對此案例您可使用彈性集區，以在集區中的多個資料庫之間共用特定數量的 eDTU。

![SQL Database 簡介：不同層級和等級的單一資料庫 DTU](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>何謂彈性資料庫交易單位 (eDTU)？
與其無論是否需要皆針對 SQL Database 提供隨時可用的專屬資源集 (DTU)，您可選擇將資料庫置於 SQL Database 上的[彈性集區](sql-database-elastic-pool.md)以在各資料庫之間共用資源集區。 彈性集區中的共用資源，是以彈性資料庫交易單位 (eDTU) 來測量。 彈性集區提供符合成本效益的簡單解決方案，以管理多個不同且具備無法預測的使用模式資料庫的效能目標。 在彈性集區中，您可確保沒有任何一個資料庫使用集區中的所有資源，且針對彈性集區中的資料庫隨時提供最低限度的可用資源數量。 

![SQL Database 簡介：不同層級和等級的 eDTU](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

集區以固定價格提供固定數目的 eDTU。 在彈性集區內，會給予個別資料庫彈性以在設定的界限內自動調整。 在沈重的負載之下，資料庫可能耗用較多 eDTU 以符合需求，而負載較輕的資料庫則耗用較少 eDTU，直到完全無負載的資料庫不使用任何 eDTU 為止。 針對整個集區佈建資源，而不是針對每個資料庫佈建資源，可簡化管理工作並可預測集區的預算。

其他 eDTU 可以新增至現有集區，而不會造成資料庫停機，且對集區中資料庫沒有影響。 同樣地，如果不再需要額外 eDTU，則隨時可以從現有集區中移除。 您可以增減集區中的資料庫，或限制資料庫在沈重負載下可以使用的 eDTU，以便為其他資料庫保留 eDTU。 如果資料庫可預測未充分使用的資源，您可以將它移出集區並設定為具有可預測資源量的單一資料庫。

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>如何判斷我的工作負載所需的 DTU 數目？
如果您希望將現有的內部部署或 SQL Server 虛擬機器工作負載移轉至 Azure SQL Database，您可以使用 [DTU 計算機](http://dtucalculator.azurewebsites.net/) 來估計所需的 DTU 數目。 對於現有的 Azure SQL Database 工作負載，您可以使用 [SQL Database 查詢效能深入解析](sql-database-query-performance.md) 來了解您的資料庫資源耗用量 (DTU)，以深入了解如何最佳化您的工作負載。 您也可以使用 [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV，取得最近一小時的資源耗用資訊。 或者，也可查詢目錄檢視 [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx)，以取得最近 14 天的相同資料，雖然五分鐘平均值的精確度較低。

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>如何得知我能受益於彈性資源集區？
集區適合於具備特定使用模式的大量資料庫。 針對指定的資料庫，此模式的特徵是低平均使用量與相對不頻繁的使用量高峰。 SQL Database 會自動評估現有 SQL Database 伺服器中資料庫過去的資源使用量，並在 Azure 入口網站中建議適當的集區組態。 如需詳細資訊，請參閱 [何時應該使用彈性集區？](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>當我達到 DTU 上限時會發生什麼狀況？
效能層級會受校正和管理，以提供所需資源來將您的資料庫工作負載執行到您所選服務層/效能層級允許的上限。 如果您的工作負載達到 CPU/資料 IO/記錄檔 IO 限制的其中一項限制，您可繼續接收允許之最大層級的資源，但您可能會經歷較長的查詢延遲。 這些限制並不會導致任何錯誤，但除非是速度慢到使查詢開始逾時，否則會使工作負載速度變慢。如果您達到最大允許並行使用者工作階段/要求 (背景工作執行緒) 的限制，您會看到明確的錯誤。 如需 CPU、記憶體、資料 I/O 和交易記錄檔 I/O 以外的資源限制資訊，請參閱 [Azure SQL Database 資源限制]( sql-database-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) 。

## <a name="next-steps"></a>後續步驟
* 如需適用於單一資料庫與彈性集區之可用 DTU 和 eDTU 的相關資訊，以及 CPU、記憶體、資料 I/O 和交易記錄 I/O 以外之資源限制的相關資訊，請參閱[服務層](sql-database-service-tiers.md)。
* 請參閱 [SQL Database 查詢效能深入解析](sql-database-query-performance.md) ，以了解您的 (DTU) 耗用量。
* 請參閱 [SQL Database 基準測試概觀](sql-database-benchmark-overview.md) ，以了解 OLTP 基準測試工作負載用來判斷 DTU 混合的方法。

