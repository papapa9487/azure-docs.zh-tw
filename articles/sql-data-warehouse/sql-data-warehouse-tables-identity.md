---
title: "使用 IDENTITY 建立 Surrogate 索引鍵 |Microsoft Docs"
description: "了解如何使用 IDENTITY 在資料表上建立 Surrogate 索引鍵。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/13/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 3ab5d159e6eaeb830135fe134e108b0e4de4b7d6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="create-surrogate-keys-by-using-identity"></a>使用 IDENTITY 建立 Surrogate 索引鍵
> [!div class="op_single_selector"]
> * [概觀][Overview]
> * [資料類型][Data Types]
> * [散發][Distribute]
> * [索引][Index]
> * [資料分割][Partition]
> * [統計資料][Statistics]
> * [暫存][Temporary]
> * [身分識別][Identity]
> 
> 

許多資料製造模型者在設計資料倉儲模型時，都喜歡在其資料表上建立 Surrogate 索引鍵。 您可以使用 IDENTITY 屬性，在不影響載入效能的情況下，以簡單又有效的方式達成此目標。 

## <a name="get-started-with-identity"></a>開始使用 IDENTITY
您在初次建立資料表時，可以使用類似下列陳述式的語法，將資料表定義為具有 IDENTITY 屬性：

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

然後，您可以使用 `INSERT..SELECT` 來填入資料表。

## <a name="behavior"></a>行為
IDENTITY 屬性的設計，可在不影響載入效能的情況下，於資料倉儲的所有發佈上進行相應放大。 因此，IDENTITY 的實作便是為了達成這些目標。 本節會重點說明此實作的細節，以協助您更全面地了解它們。  

### <a name="allocation-of-values"></a>值的配置
IDENTITY 屬性並不會保證 Surrogate 值的配置順序，這會反映出 SQL Server 和 Azure SQL Database 行為。 不過，在 Azure SQL 資料倉儲中，此保證的不存在則更為明顯。 

下列範例將做出說明：

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

在上述範例中，有兩個資料列落在發佈 1 中。 第一個資料列在資料行 `C1` 中具有 1 的 Surrogate 值，而第二個資料列則具有 61 的 Surrogate 值。 這兩個值都是由 IDENTITY 屬性所產生。 不過，值的配置並不是連續的。 這是設計的行為。

### <a name="skewed-data"></a>偏斜資料 
資料類型的值範圍會平均分散於發佈上。 如果分散式資料表受到偏斜資料的影響，則可供資料類型使用的值範圍可能會提前耗盡。 例如，如果所有資料最後都位於單一發佈中，則該資料表實際上將只能存取該資料類型六十分之一的值。 因此，IDENTITY 屬性僅限用於 `INT` 和 `BIGINT` 資料類型。

### <a name="selectinto"></a>SELECT..INTO
將現有的 IDENTITY 資料行選取至新的資料表時，新的資料行會繼承 IDENTITY 屬性，除非下列其中一項條件成立：
- SELECT 陳述式包含聯結。
- 多個 SELECT 陳述式使用 UNION 進行聯結。
- IDENTITY 資料行在 SELECT 清單中列出多次。
- IDENTITY 資料行是運算式的一部分。
    
如果這些條件的其中之一成立，資料行會建立為「非 NULL」，而不是繼承 IDENTITY 屬性。

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT
CREATE TABLE AS SELECT (CTAS) 會遵循針對 SELECT..INTO 記錄的相同 SQL Server 行為。 不過，您無法在陳述式 `CREATE TABLE` 部分的資料行定義中指定 IDENTITY 屬性。 您也無法在 CTAS 的 `SELECT` 部分使用 IDENTITY 函式。 若要填入資料表，您必須使用 `CREATE TABLE` 來定義資料表，接著使用 `INSERT..SELECT` 來填入它。

## <a name="explicitly-insert-values-into-an-identity-column"></a>明確地將值插入 IDENTITY 資料行 
SQL 資料倉儲支援 `SET IDENTITY_INSERT <your table> ON|OFF` 語法。 您可以使用此語法來明確地將值插入 IDENTITY 資料行。

許多資料製造模型者喜歡在其維度的特定資料列中使用預先定義的負數值。 其中一個範例為 -1 或「未知的成員」資料列。 

下一個指令碼會示範如何使用 SET IDENTITY_INSERT 明確地加入此資料列：

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```    

## <a name="load-data-into-a-table-with-identity"></a>使用 IDENTITY 將資料載入資料表

IDENTITY 屬性的存在會對您的資料載入程式碼造成一些影響。 本節會重點說明使用 IDENTITY 將資料載入資料表的一些基本模式。 

### <a name="load-data-with-polybase"></a>使用 PolyBase 載入資料
若要使用 IDENTITY 將資料載入資料表並產生 Surrogate 索引鍵，請建立資料表，然後使用 INSERT..SELECT 或 INSERT..VALUES 來執行載入。

下列範例會重點說明基本模式：
 
```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> 目前在使用 IDENTITY 資料行將資料載入資料表時，並無法使用 `CREATE TABLE AS SELECT`。
> 

如需使用大量複製程式 (BCP) 工具載入資料的詳細資訊，請參閱下列文章：

- [使用 PolyBase 載入][]
- [PolyBase 最佳做法][]

### <a name="load-data-with-bcp"></a>使用 BCP 載入資料
BCP 是一種命令列工具，您可以用來將資料載入 SQL 資料倉儲。 它的其中一個參數 (-E) 可控制使用 IDENTITY 資料行將資料載入資料表時的 BCP 行為。 

指定 -E 時，保留在具 IDENTITY 之資料行的輸入檔案中的值將會被保留下來。 如果「未」指定 -E，系統會忽略此資料行中的值。 如果沒有包含 IDENTITY 資料行，則會以正常方式載入資料。 值會根據屬性的增量和種子原則產生。

如需使用 BCP 載入資料的詳細資訊，請參閱下列文章：

- [使用 BCP 載入][]
- [MSDN 中的 BCP][]

## <a name="catalog-views"></a>目錄檢視
SQL 資料倉儲支援 `sys.identity_columns` 目錄檢視。 此檢視可以用來識別具有 IDENTITY 屬性的資料行。

為了協助您深入了解資料庫結構描述，此範例示範如何整合 `sys.identity_columns` 與其他系統目錄檢視：

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>限制
下列案例不能使用 IDENTITY 屬性：
- 資料行資料類型不是 INT 或 BIGINT
- 資料行也是散發索引鍵
- 資料表為外部資料表 

SQL 資料倉儲中不支援下列相關函式：

- [IDENTITY()][]
- [@@IDENTITY][]
- [SCOPE_IDENTITY][]
- [IDENT_CURRENT][]
- [IDENT_INCR][]
- [IDENT_SEED][]
- [DBCC CHECK_IDENT()][]

## <a name="tasks"></a>工作

本節提供一些範例程式碼，可在您使用 IDENTITY 資料行時用來執行一般工作。

> [!NOTE] 
> 在下列所有工作中，資料行 C1 都會是 IDENTITY。
> 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>找出資料表的最大配置值
使用 `MAX()` 函式來判斷針對分散式資料表所配置的最大值：

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>找出 IDENTITY 屬性的種子和增量
您可以透過下列查詢，來使用目錄檢視以探索資料表的識別值增量和種子設定值： 

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>後續步驟

* 若要深入了解開發資料表，請參閱[資料表概觀][Overview]、[資料表的資料類型][Data Types]、[散發資料表][Distribute]、[編製資料表的索引][Index]、[分割資料表][Partition]和[暫存資料表][Temporary]。 
* 如需最佳做法的詳細資訊，請參閱 [SQL 資料倉儲最佳做法][SQL Data Warehouse Best Practices]。  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Identity]: ./sql-data-warehouse-tables-identity.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

[使用 bcp 載入]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp/
[使用 PolyBase 載入]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase/
[PolyBase 最佳做法]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide/


<!--MSDN references-->
[Identity property]: https://msdn.microsoft.com/library/ms186775.aspx
[sys.identity_columns]: https://msdn.microsoft.com/library/ms187334.aspx
[IDENTITY()]: https://msdn.microsoft.com/library/ms189838.aspx
[@@IDENTITY]: https://msdn.microsoft.com/library/ms187342.aspx
[SCOPE_IDENTITY]: https://msdn.microsoft.com/library/ms190315.aspx
[IDENT_CURRENT]: https://msdn.microsoft.com/library/ms175098.aspx
[IDENT_INCR]: https://msdn.microsoft.com/library/ms189795.aspx
[IDENT_SEED]: https://msdn.microsoft.com/library/ms189834.aspx
[DBCC CHECK_IDENT()]: https://msdn.microsoft.com/library/ms176057.aspx

[MSDN 中的 bcp]: https://msdn.microsoft.com/library/ms162802.aspx
  

<!--Other Web references-->  
