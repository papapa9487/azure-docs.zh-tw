---
title: "管理 Azure SQL 資料倉儲中的計算能力 (概觀) | Microsoft Docs"
description: "Azure SQL 資料倉儲中的效能相應放大功能。 藉由調整 DWU 以相應放大或暫停和繼續計算資源來節省成本。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/22/2017
ms.author: elbutter
ms.openlocfilehash: abe22f542a79714f6e894870872ee6b76ffe7633
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>管理 Azure SQL 資料倉儲中的計算能力 (概觀)
> [!div class="op_single_selector"]
> * [概觀](sql-data-warehouse-manage-compute-overview.md)
> * [入口網站](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

SQL 資料倉儲的架構分隔儲存體和計算功能，可單獨進行調整。 如此一來，可以調整計算以符合與資料量無關的效能需求。 這個架構的自然結果為計算[計費][billed]和儲存體無關。 

本概觀說明擴充如何與 SQL 資料倉儲搭配使用，以及如何運用暫停、繼續和調整 SQL 資料倉儲的功能。 請參閱[資料倉儲單位 (DWU)][data warehouse units (DWUs)] 頁面以了解 DWU 和效能有何相關。 

## <a name="how-compute-management-operations-work-in-sql-data-warehouse"></a>計算管理作業如何在 SQL 資料倉儲中運作
SQL 資料倉儲架構是由控制節點、計算節點及分散到 60 個散發套件的儲存層所組成。 

在 SQL 資料倉儲中的正常作用中工作階段期間，您系統的前端節點會管理中繼資料並包含分散式查詢最佳化工具。 此前端節點以下為計算節點和儲存層。 針對 DWU 400，您的系統有一個前端節點、四個計算節點和儲存層，其中包含 60 個散發套件。 

在進行調整或暫停作業時，系統會先刪除所有傳入的查詢，並會復原交易，以確保一致的狀態。 針對調整作業，僅在這個交易回復完成後調整才會發生。 相應增加作業中，系統會佈建計算節點的額外所需數目，然後開始將計算節點重新附加至儲存層。 相應減少作業中，會釋放不需要的節點，其餘的計算節點會將其本身重新附加至適當數目的散發套件。 暫停作業中，會釋放所有的計算節點，而且您的系統會經歷各種中繼資料作業以將最後系統保持在穩定的狀態。

| DWU  | \# 個計算節點 | 每節點 \# 個散發 |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |

管理計算所需的三個主要功能如下︰

1. 暫停
2. 繼續
3. 調整

每個這些作業可能需要幾分鐘的時間才能完成。 如果您是自動調整/暫停/繼續，可能想要實作邏輯以確保在進行其他動作之前，先確認特定作業已完成。 

透過各種端點檢查資料庫狀態可讓您正確地實作這類作業的自動化。 入口網站會在作業完成時提供通知和資料庫的目前狀態，但不允許以程式設計方式檢查狀態。 

>  [!NOTE]
>
>  計算管理功能並未跨所有端點存在。
>
>  

|              | 暫停/繼續 | 調整 | 檢查資料庫狀態 |
| ------------ | ------------ | ----- | -------------------- |
| Azure 入口網站 | 是          | 是   | **否**               |
| PowerShell   | 是          | 是   | 是                  |
| REST API     | 是          | 是   | 是                  |
| T-SQL        | **否**       | 是   | 是                  |



<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>調整計算

SQL 資料倉儲中的效能測量單位為[資料倉儲單位 (DWU)][data warehouse units (DWUs)]，這是計算資源的抽象量值，例如 CPU、記憶體和 I/O 頻寬。 想要調整其系統效能的使用者可透過各種方式來完成，例如透過入口網站、T-SQL 和 REST API。 

### <a name="how-do-i-scale-compute"></a>如何調整計算？
計算電源會藉由變更 DWU 設定來管理您的 SQL 資料倉儲。 隨著您針對特定作業新增更多的 DWU，效能會[以線性方式][linearly]提升。  當您將系統相應增加或減少時，我們提供的 DWU 供應項目可確保您的效能會明顯變更。 

若要調整 DWU，您可以使用任何一種個別的方法。

* [使用 Azure 入口網站調整計算能力][Scale compute power with Azure portal]
* [使用 PowerShell 調整計算能力][Scale compute power with PowerShell]
* [使用 REST API 調整計算能力][Scale compute power with REST APIs]
* [使用 TSQL 調整計算能力][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>應該使用多少 DWU 呢？

為了要了解您理想的 DWU 值，嘗試在載入您的資料之後相應增加和相應減少並執行幾個查詢。 由於調整很快速，您可以在一小時內嘗各種效能層級。 

> [!Note] 
> SQL 資料倉儲是專為處理大量資料所設計。 若要查看其進行調整的實際能力，尤其是在較大的 DWU，您想要使用接近或超過 1 TB 的大型資料集。

尋找您的工作負載的最佳 DWU 的建議事項︰

1. 若是開發過程中的資料倉儲，可選取較少的 DWU 效能等級。  DW400 或 DW200 是不錯的起點。
2. 監視應用程式效能，觀察比較所選 DWU 數目與您觀察到的效能。
3. 透過假設線性標尺，判斷需要將效能加快或減慢多少才能達到您需求的最佳效能等級。
4. 增加或減少 DWU 數目與您想要讓您的工作負載更快或更慢執行成正比。 
5. 繼續進行調整，直到達到您業務需求的最佳效能為止。

> [!NOTE]
>
> 如果工作可以在計算節點之間分割，則查詢效能只會隨更多的平行處理增加。 如果您發現縮放比例不會變更您的效能，請造訪我們的效能微調文章，以檢查您的資料是否平均地分散，或是您是否進行大量的資料移動。 

### <a name="when-should-i-scale-dwus"></a>何時調整 DWU？
調整 DWU 會改變下列重要案例︰

1. 以線性方式變更掃描、彙總和 CTAS 陳述式的系統效能
2. 使用 PolyBase 載入時，增加讀取器和寫入的數目
3. 並行查詢和並行存取插槽數目上限

DWU 調整時機的建議︰

1. 執行大量資料載入或轉換作業之前，相應增加 DWU 以使您的資料更快速可供使用。
2. 在尖峰營業時間內，調整以適應大量的並行查詢。 

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>暫停計算
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

若要暫停資料庫，您可以使用任何一種個別的方法。

* [使用 Azure 入口網站暫停計算][Pause compute with Azure portal]
* [使用 PowerShell 暫停計算][Pause compute with PowerShell]
* [使用 REST API 暫停計算][Pause compute with REST APIs]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>繼續計算
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

若要繼續資料庫，您可以使用任何一種個別的方法。

* [使用 Azure 入口網站繼續計算][Resume compute with Azure portal]
* [使用 PowerShell 繼續計算][Resume compute with PowerShell]
* [使用 REST API 繼續計算][Resume compute with REST APIs]

<a name="check-compute-bk"></a>

## <a name="check-database-state"></a>檢查資料庫狀態 

若要繼續資料庫，您可以使用任何一種個別的方法。

- [使用 T-SQL 檢查資料庫狀態][Check database state with T-SQL]
- [使用 PowerShell 檢查資料庫狀態][Check database state with PowerShell]
- [使用 REST API 檢查資料庫狀態][Check database state with REST APIs]

## <a name="permissions"></a>權限

調整資料庫需要 [ALTER DATABASE][ALTER DATABASE] 中所述的權限。  暫停和繼續需要 [SQL DB 參與者][SQL DB Contributor]權限，特別是 Microsoft.Sql/servers/databases/action。

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>後續步驟
請參閱下列文章，以瞭解其他一些關鍵效能概念：

* [工作負載和並行管理][Workload and concurrency management]
* [資料表設計概觀][Table design overview]
* [資料表散發][Table distribution]
* [索引資料表的編製][Table indexing]
* [資料表分割][Table partitioning]
* [資料表統計資料][Table statistics]
* [最佳作法][Best practices]

<!--Image reference-->

<!--Article references-->
[data warehouse units (DWUs)]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[billed]: https://azure.microsoft.com/en-us/pricing/details/sql-data-warehouse/
[linearly]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
