---
title: "SQL 資料倉儲中的資料表概觀 | Microsoft Docs"
description: "開始使用 Azure SQL 資料倉儲資料表。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 2114d9ad-c113-43da-859f-419d72604bdf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: d736ad0dbfb109500a150d2bc3f7c40b16206fdb
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2017
---
# <a name="overview-of-tables-in-sql-data-warehouse"></a>SQL 資料倉儲中的資料表概觀
> [!div class="op_single_selector"]
> * [概觀][Overview]
> * [資料類型][Data Types]
> * [散發][Distribute]
> * [索引][Index]
> * [資料分割][Partition]
> * [統計資料][Statistics]
> * [暫存][Temporary]
> 
> 

開始在 SQL 資料倉儲中建立資料表很簡單。  基本 [CREATE TABLE][CREATE TABLE] 語法會遵循您處理其他資料庫時可能已經熟悉的一般語法。  若要建立資料表，只需要替您的資料表命名、替您的資料行命名，以及定義每個資料行的資料類型。  如果您已在其他資料庫中建立資料表，您應該會非常熟悉此範例。

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

上述範例會建立名為 Customers 的資料表，其中包含 FirstName 和 LastName 兩個資料行。  每個資料行的資料類型均定義為 VARCHAR(25)，這會將資料限制為 25 個字元。  資料表的這些基本屬性以及其他屬性，大多與其他資料庫相同。  每個資料行都會定義資料類型，以確保資料的完整性。  加入索引，即可藉由降低 I/O 來改善效能。  加入資料分割，可改善您需要修改資料時的效能。

[重新命名][RENAME] SQL 資料倉儲資料表的方式如下：

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>分散式資料表
**散發資料行**是 SQL 資料倉儲等分散式系統所導入的新基本屬性。  散發資料行名符其實。  此資料行可決定如何在幕後散發或分割您的資料。  當您建立資料表，但不指定散發資料行時，系統會使用 **循環配置資源**自動散發資料表。  雖然在某些情況下，循環配置資源資料表可能已足夠，但定義散發資料行可大幅減少期間查詢的資料移動，因而讓效能達到最佳化。  在資料表只有少量資料的情況下，選擇使用**複寫**分佈類型建立資料表會將資料複製到每個計算節點，並儲存查詢執行時間的資料移動。 若要深入了解如何選取散發資料行，請參閱[散發資料表][Distribute]。

## <a name="indexing-and-partitioning-tables"></a>編製資料表的索引和分割資料表
隨著您更加擅長使用 SQL 資料倉儲並想要讓效能達到最佳化，您會想要深入了解資料表設計。  若要深入了解，請參閱[資料表資料類型][Data Types]、[散發資料表][Distribute]、[編製資料表的索引][Index]和[分割資料表][Partition]等文章。

## <a name="table-statistics"></a>資料表統計資料
為了讓 SQL 資料倉儲達到最佳效能，統計資料極為重要。  由於 SQL 資料倉儲尚無法為您自動建立和更新統計資料 (如同您在 Azure SQL Database 中所預期)，我們的[統計資料][Statistics]一文可能是您所閱讀的其中一篇最重要的文章，它可以確保您在查詢上獲得最佳效能。

## <a name="temporary-tables"></a>暫存資料表
暫存資料表只存在於您的登入期間，其他使用者看不到此種資料表。  暫存資料表可以防止其他人看見暫存結果，也會減少清除的需求。  由於暫存資料表也會使用本機儲存體，所以可為某些作業提供更快的效能。  如需有關暫存資料表的詳細資料，請參閱[暫存資料表][Temporary]一文。

## <a name="external-tables"></a>外部資料表
外部資料表 (也稱為 Polybase 資料表) 是可從 SQL 資料倉儲查詢的資料表，但會指向 SQL 資料倉儲外部的資料。  例如，您可以建立指向 Azure Blob 儲存體上檔案的外部資料表。  如需有關如何建立及查詢外部資料表的詳細資料，請參閱[使用 Polybase 載入資料][Load data with Polybase]。  

## <a name="unsupported-table-features"></a>不支援的資料表功能
雖然 SQL 資料倉儲包含其他資料庫所提供的許多相同資料表功能，但仍有一些功能尚未提供支援。  以下是一些尚未支援的資料表功能清單。

| 不支援的功能 |
| --- |
| 主索引鍵、外部索引鍵、唯一和檢查[資料表條件約束][Table Constraints] |
| [唯一索引][Unique Indexes] |
| [計算資料行][Computed Columns] |
| [疏鬆資料行][Sparse Columns] |
| [使用者定義的類型][User-Defined Types] |
| [順序][Sequence] |
| [觸發程序][Triggers] |
| [索引檢視表][Indexed Views] |
| [同義字][Synonyms] |

## <a name="table-size-queries"></a>資料表大小查詢
識別資料表在每個散發 (共 60 個) 中所耗用的空間和資料列數的其中一種簡單方式，就是使用 [DBCC PDW_SHOWSPACEUSED][DBCC PDW_SHOWSPACEUSED]。

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

不過，使用 DBCC 命令相當受限。  動態管理檢視 (DMV) 可讓您查看更多詳細資料，以及讓您更進一步控制查詢結果。  首先建立這個檢視，而本篇和其他文章中的許多範例將會參考此檢視。

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>資料表空間摘要
此查詢會傳回資料表的資料列和空格。  這是很實用的查詢，可查看哪些資料表是最大的資料表，以及其屬於循環配置資源、複寫或雜湊散發資料表。  若為雜湊散發資料表，也會顯示散發資料行。  在大部分情況下，最大的資料表應該是具有叢集資料行存放區索引的雜湊散發資料表。

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>依散發類型的資料表空間
```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>依索引類型的資料表空間
```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>散發空間摘要
```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>後續步驟
若要深入了解，請參閱[資料表資料類型][Data Types]、[散發資料表][Distribute]、[編製資料表的索引][Index]、[分割資料表][Partition]、[維護資料表統計資料][Statistics]及[暫存資料表][Temporary]等文章。  若要深入了解最佳作法，請參閱 [SQL Data 資料倉儲最佳作法][SQL Data Warehouse Best Practices]。

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md
[Load data with Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Table Constraints]: https://msdn.microsoft.com/library/ms188066.aspx
[Computed Columns]: https://msdn.microsoft.com/library/ms186241.aspx
[Sparse Columns]: https://msdn.microsoft.com/library/cc280604.aspx
[User-Defined Types]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequence]: https://msdn.microsoft.com/library/ff878091.aspx
[Triggers]: https://msdn.microsoft.com/library/ms189799.aspx
[Indexed Views]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonyms]: https://msdn.microsoft.com/library/ms177544.aspx
[Unique Indexes]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
