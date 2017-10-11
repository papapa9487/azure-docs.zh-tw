---
title: "將資料從 SQL Server 載入 Azure SQL 資料倉儲 (bcp) | Microsoft Docs"
description: "對於小型資料，請使用 bcp 將資料從 SQL Server 匯出至一般檔案，以及將資料直接匯入 Azure SQL 資料倉儲。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: f87d8d7c-8276-43c5-90e7-d4ccc0e3a224
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: dae7b5f7456f4ec0daf60d55f9c38b780896ff83
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>將資料從 SQL Server 載入 Azure SQL 資料倉儲 (一般檔案)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

對於小型資料集，您可以使用 bcp 命令列公用程式從 SQL Server 匯出資料，然後將它直接載入到 Azure SQL 資料倉儲。

在本教學課程中，您將使用 bcp 來：

* 使用 bcp out 命令從 SQL Server 匯出資料表 (或建立簡單的範例檔案)
* 從一般檔案將資料表匯入到 SQL 資料倉儲。
* 建立已載入資料的統計資料。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="before-you-begin"></a>開始之前
### <a name="prerequisites"></a>必要條件
若要逐步執行本教學課程，您需要：

* SQL 資料倉儲資料庫
* 已安裝的 bcp 命令列公用程式
* 已安裝的 sqlcmd 命令列公用程式

您可以從 [Microsoft 下載中心][Microsoft Download Center]下載 bcp 和 sqlcmd 公用程式。

### <a name="data-in-ascii-or-utf-16-format"></a>ASCII 或 UTF-16 格式的資料
如果您使用您自己的資料嘗試本教學課程，您的資料必須使用 ASCII 或 UTF-16 編碼，因為 bcp 不支援 UFT-8。 

PolyBase 支援 UTF-8，但尚未支援 UTF-16。 請注意，如果您想要結合 bcp 與 PolyBase，從 SQL Server 匯出資料後，您必須將資料轉換為 UTF-8。 

## <a name="1-create-a-destination-table"></a>1.建立目的資料表
在 SQL 資料倉儲中定義將成為載入目的地資料表的資料表。 資料表中的資料行必須對應到資料檔的每一個資料列中的資料。

若要建立資料表，請開啟命令提示字元並使用 sqlcmd.exe 執行下列命令︰

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```


## <a name="2-create-a-source-data-file"></a>2.建立來源資料檔
開啟 [記事本]，將下列幾行資料複製到新的文字檔，然後將此檔案儲存到本機暫存目錄 C:\Temp\DimDate2.txt。 此資料是 ASCII 格式。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(選擇性) 若要從 SQL Server 資料庫匯出您自己的資料，請開啟命令提示字元並執行下列命令。 使用您自己的資訊取代 TableName、ServerName、DatabaseName、Username 和 Password。

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## <a name="3-load-the-data"></a>3.載入資料
若要載入資料，請開啟命令提示字元並執行下列命令，使用您自己的資訊取代 ServerName、DatabaseName、Username 和 Password。

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

使用此命令來確認已正確載入資料

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

結果應該如下所示：

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="4-create-statistics"></a>4.建立統計資料
SQL 資料倉儲尚未支援自動建立或自動更新統計資料。 為了獲得最佳查詢效能，在首次載入資料或資料發生任何重大變更之後，建立所有資料表的所有資料行統計資料非常重要。 如需統計資料的詳細說明，請參閱[統計資料][Statistics]。 

執行下列命令以建立新載入資料的統計資料。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5.從 SQL 資料倉儲匯出資料
為了好玩，您可以匯出您剛從 SQL 資料倉儲載入的資料。  匯出命令完全與從 SQL Server 匯出的命令相同。

不過，結果有所差異。 因為資料儲存在 SQL 資料倉儲內的分散位置，所以當您匯出資料時，每個計算節點會將資料寫入至輸出檔。 輸出檔中的資料順序與輸入檔中的資料順序可能不同。

### <a name="export-a-table-and-compare-exported-results"></a>匯出資料表和比較匯出的結果
若要查看匯出的資料，請開啟命令提示字元並使用您自己的參數執行此命令。 ServerName 是您的 Azure 邏輯 SQL Server 名稱。

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
您可以開啟新的檔案來確認資料已正確匯出。 檔案中的資料應該符合以下文字，但可能以不同的順序排序：

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="export-the-results-of-a-query"></a>此查詢的結果如下：
您可以使用 bcp 的 **queryout** 函數匯出查詢結果，而不是匯出整份資料表。 

## <a name="next-steps"></a>後續步驟
如需載入的概觀，請參閱[將資料載入 SQL 資料倉儲][Load data into SQL Data Warehouse]。
如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][SQL Data Warehouse development overview]。
如需有關在 SQL 資料倉儲上建立資料表的詳細資訊，請參閱[資料表概觀][Table Overview]或 [CREATE TABLE 語法][CREATE TABLE syntax]。

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
