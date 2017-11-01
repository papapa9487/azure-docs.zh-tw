---
title: "什麼是 Azure SQL 資料倉儲中的資料倉儲單位 (DWU、cDWU)？ | Microsoft Docs"
description: "Azure SQL 資料倉儲中的效能相應放大功能。 藉由調整 DWU、cDWU 以相應放大，或暫停和繼續計算資源來節省成本。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 93f0d21c7214487ffa0c2c5e27bd6e468920418c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>資料倉儲單位 (DWU) 和計算資料倉儲單位 (cDWU)
說明適用於 Azure SQL 資料倉儲的資料倉儲單位 (DWU) 和計算資料倉儲單位 (cDWU)。 包含選擇理想的資料倉儲單位數，以及如何變更其數目的建議。 

## <a name="what-are-data-warehouse-units"></a>什麼是資料倉儲單位？
利用 SQL 資料倉儲，將 CPU、記憶體和 IO 組合到稱為資料倉儲單位 (DWU) 的計算縮放單位中。 DWU 代表計算資源和效能之抽象且標準化的量值。 藉由變更服務等級，您可以改變配置給系統的 DWU 數目，接著調整系統的效能與成本。 

如果您願意為較高的效能支付費用，可以增加資料倉儲單位數。 如果您只願為較低的效能支付費用，請降低資料倉儲單位數。 儲存體和計算成本會分別計費，因此，變更資料倉儲單位不會影響儲存體成本。

資料倉儲單位的效能是以這些資料倉儲工作負載計量為根據：

- 標準資料倉儲查詢會以多快的速度掃描大量資料列，然後執行複雜的彙總？ 這個作業是 I/O 和 CPU 密集型作業。
- 資料倉儲可以多快的速度從 Azure 儲存體 Blob 或 Azure Data Lake 擷取資料？ 這個作業是網路和 CPU 密集型作業。 
- [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL 命令可以多快的速度複製資料表？ 這個作業牽涉到從儲存體讀取資料、跨應用裝置的節點散發資料，以及重新寫入至儲存體。 這個作業是 CPU、IO 和網路密集型作業。

增加 DWU：
- 以線性方式變更掃描、彙總和 CTAS 陳述式的系統效能
- 針對 PolyBase 載入作業增加讀取器和寫入器的數目
- 增加並行查詢和並行位置的數目上限。

## <a name="performance-tiers-and-data-warehouse-units"></a>效能層級和資料倉儲單位

每一個效能層級都會使用與其資料倉儲單位稍有不同的量值單位。 這項差異會在縮放單位直接轉換為帳單時反映於發票上。

- 針對彈性最佳化的效能層級會以資料倉儲單位 (DWU) 來測量。
- 針對計算最佳化的效能層級會以計算資料倉儲單位 (cDWU) 來測量。 

DWU 和 cDWU 均支援將計算相應增加或減少，並且在您不需使用資料倉儲時暫停計算。 這些作業全都依需求指定。 針對計算最佳化的效能層級也會使用計算節點上的本機磁碟型快取來改善效能。 當您調整或暫停系統時，快取就會失效，因此，需要一段快取準備時間，才能達到最佳效能。  

當您增加資料倉儲單位時，正是以線性方式增加運算資源。 針對計算最佳化的效能層級會提供最佳查詢效能和最高規模，但具有較高的項目價格。 它是專為持續具有效能需求的企業而設計的。 這些系統會充分利用快取。 

### <a name="capacity-limits"></a>容量限制
根據預設，每部伺服器 (例如 myserver.database.windows.net) 都有配額，來限制該執行個體上資料庫的大小與範圍。 伺服器可以裝載 SQL DW 和 SQL DB 資料庫，這些資料庫全都必須符合配額。 這個配額是以資料庫交易單位 (DTU) 來測量，預設會設定為 54,000，以允許最多 6000 個 cDWU。 此配額僅是安全限制。 您可以藉由建立支援票證並選取 [配額] 作為要求類型來增加配額。 

若要計算您的 DTU 需求，請將下列乘數套用至您的 DTU 計算：

| 效能層級 | 測量單位 | DTU 乘數 | 範例                   |
|:----------------:|----------------:|---------------:|--------------------------:|
| 彈性       |  DWU            | 7.5            | DW6000 x 7.5 = 45,000 DTU |
| 計算          | cDWU            | 9              | DW6000 x 7.5 = 54,000 DTU |

您可以在入口網站中檢視目前的 DTU 耗用量以查看 SQL Server 屬性。

## <a name="how-many-data-warehouse-units-do-i-need"></a>我需要多少個資料倉儲單位？
理想的資料倉儲單位數大部分取決於您的工作負載，以及您已載入系統的資料量。

尋找您工作負載之最佳 DWU 的步驟：

1. 在開發期間，從使用針對彈性最佳化的效能層級來選取較小的 DWU 開始。  由於此階段的考量是功能驗證，因此，針對彈性最佳化的效能層級是一個合理的選項。 DW200 是個不錯的起點。 
2. 當您的測試資料載入系統時監視應用程式效能，觀察比較所選 DWU 數目與您觀察到的效能。
3. 針對定期的尖峰活動期間，識別任何其他需求。 如果工作負載在活動中顯示明顯的尖峰與低谷，而且有很好的理由經常進行調整，則偏好使用針對彈性最佳化的效能層級。
4. 如果您需要超過 1000 個 DWU，則偏好使用針對計算最佳化的效能層級，因為這可提供最佳效能。

SQL 資料倉儲是一個相應放大系統，可以佈建大量的計算以及查詢相當大量的資料。 若要查看真正用以調整的功能 (尤其是在較大的 DWU 上)，建議您在進行調整以確定有足夠資料可提供給 CPU 時調整資料集。 針對調整測試，我們建議至少使用 1 TB。

> [!NOTE]
>
> 如果工作可以在計算節點之間分割，則查詢效能只會隨更多的平行處理增加。 如果您發現調整並未變更效能，則可能需要調整資料表設計和/或您的查詢。 如需查詢調整指引，請參閱下列[效能](sql-data-warehouse-overview-manage-user-queries.md)文章。 

## <a name="permissions"></a>權限

變更資料倉儲單位需要 [ALTER DATABASE][ALTER DATABASE] 中所述的權限。 

## <a name="view-current-dwu-settings"></a>檢視目前的 DWU 設定

檢視目前的 DWU 設定：

1. 在 Visual Studio 中開啟 [SQL Server 物件總管]。
2. 連接到與邏輯 SQL Database 伺服器相關聯的 master 資料庫。
3. 從 sys.database_service_objectives 動態管理檢視中選取。 下列是一個範例： 

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>變更資料倉儲單位

### <a name="azure-portal"></a>Azure 入口網站
變更 DWU 或 cDWU：

1. 開啟 [Azure 入口網站](https://portal.azure.com)、開啟您的資料庫，然後按一下 [調整]。

2. 在 [調整] 下方，將滑桿向左或右移動來變更 DWU 設定。

3. 按一下 [儲存] 。 確認訊息隨即出現。 按一下 [是] 以確認或 [否] 以取消。

### <a name="powershell"></a>PowerShell
若要變更 DWU 或 cDWU，請使用 [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase] PowerShell Cmdlet。 下列範例會將裝載在 MyServer 伺服器上的資料庫 MySQLDW 的服務等級目標設定為 DW1000。

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

### <a name="t-sql"></a>T-SQL
利用 T-SQL，您可以檢視目前的 DWU 或 cDWU 設定、變更設定，以及檢查進度。 

變更 DWU 或 cDWU：

1. 連接到與您的邏輯 SQL Database 伺服器相關聯的 master 資料庫。
2. 使用 [ALTER DATABASE][ALTER DATABASE] TSQL 陳述式。 下例範例會將資料庫 MySQLDW 的服務等級目標設定為 DW1000。 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API

若要變更 DWU，請使用 [建立或更新資料庫][建立或更新資料庫] REST API。 下例會將裝載在 MyServer 伺服器上的資料庫 MySQLDW 的服務等級目標設定為 DW1000。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```


## <a name="check-status-of-dwu-changes"></a>檢查 DWU 變更的狀態

DWU 變更可能需要幾分鐘的時間才能完成。 如果正在進行自動調整，請考慮實作邏輯，以確保在繼續進行其他動作之前，已完成特定作業。 

透過各種端點檢查資料庫狀態，可讓您正確實作自動化。 入口網站會在作業完成時提供通知和資料庫的目前狀態，但不允許以程式設計方式檢查狀態。 

您無法使用 Azure 入口網站，檢查相應放大作業的資料庫狀態。

檢查 DWU 變更的狀態：

1. 連接到與您的邏輯 SQL Database 伺服器相關聯的 master 資料庫。
2. 提交下列查詢以檢查資料庫狀態。


```sql
SELECT    *
FROM      sys.databases
;
```

3. 提交下列查詢以檢查作業的狀態

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

此 DMV 會傳回您的 SQL 資料倉儲上各種管理作業的相關資訊，例如，作業和作業的狀態 (不是 IN_PROGRESS 就是 COMPLETED)。

## <a name="the-scaling-workflow"></a>調整工作流程

當您起始調整規模作業時，系統會先刪除所有開啟的工作階段，回復所有開啟的交易以確保一致性狀態。 針對調整規模作業，只有在這個交易回復完成後調整才會發生。  

- 針對相應增加作業，系統會佈建額外的計算，然後重新連接到儲存層。 
- 針對相應減少作業，不需要的節點會從儲存體卸離，並重新連接到剩餘的節點。

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

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md


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
