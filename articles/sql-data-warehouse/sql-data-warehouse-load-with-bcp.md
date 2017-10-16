---
title: "使用 bcp 將資料載入 SQL 資料倉儲 | Microsoft Docs"
description: "瞭解 bcp 是什麼及如何用於資料倉儲案例。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 7596eac10fdf53380d85128265430ce07b551fe3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-with-bcp"></a>使用 bcp 載入資料
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

**[bcp][bcp]** 是命令列大量載入公用程式，可讓您在 SQL Server、資料檔和 SQL 資料倉儲之間複製資料。 使用 bcp 公用程式將大量資料列匯入 SQL 資料倉儲資料表，或將 SQL Server 資料表中的資料匯出成資料檔案。 除非與 queryout 選項一起使用，否則 bcp 不需 TRANSACT-SQL 方面的知識。

bcp 是將較小的資料集移入和移出 SQL 資料倉儲資料庫的一種快速又輕鬆的方式。 透過 bcp 載入/擷取時，建議的確切資料量取決於您對 Azure 資料中心的網路連線。  一般而言，維度資料表可輕易透過 bcp 載入和擷取，不過，不建議將 bcp 用於載入或擷取大量資料。  Polybase 是載入和擷取大量資料的建議工具，因為它會善用 SQL 資料倉儲的大量平行處理架構。

您可以透過 bcp：

* 使用簡單的命令列公用程式將資料載入 SQL 資料倉儲。
* 使用簡單的命令列公用程式從 SQL 資料倉儲擷取資料。

此教學課程將為您示範如何：

* 使用 bcp in 命令將資料匯入資料表
* 使用 bcp out 命令將資料匯出資料表

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>先決條件
若要逐步執行本教學課程，您需要：

* SQL 資料倉儲資料庫
* 已安裝的 bcp 命令列公用程式
* 已安裝的 SQLCMD 命令列公用程式

> [!NOTE]
> 您可以從 [Microsoft 下載中心][Microsoft Download Center]下載 bcp 和 sqlcmd 公用程式。
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>將資料匯入 SQL 資料倉儲
在本教學課程中，您將在 Azure SQL 資料倉儲中建立資料表，並將資料匯入資料表。

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>步驟 1：在 Azure SQL 資料倉儲中建立資料表
從命令提示字元，使用 sqlcmd 執行下列查詢，以在您的執行個體上建立資料表︰

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

> [!NOTE]
> 如需有關在 SQL 資料倉儲上建立資料表和 WITH 子句中可用選項的詳細資訊，請參閱[資料表概觀][Table Overview]或 [CREATE TABLE 語法][CREATE TABLE syntax]。
> 
> 

### <a name="step-2-create-a-source-data-file"></a>步驟 2：建立來源資料檔
開啟 [記事本]，將下列幾行資料複製到新的文字檔，然後將此檔案儲存到本機暫存目錄 C:\Temp\DimDate2.txt。

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

> [!NOTE]
> 請務必記得 bcp.exe 不支援 UTF-8 檔案編碼。 使用 bcp.exe 時，請使用 ASCII 檔案或 UTF-16 編碼的檔案。
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>步驟 3：連接並匯入資料
在 bcp 中，您可以使用下列命令來連接並匯入資料 (請適當地取代其中的值)：

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

您可以使用 sqlcmd 執行下列查詢以確認資料已載入︰

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

這應該會傳回下列結果：

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

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>步驟 4：建立新載入資料的統計資料
Azure 資料倉儲尚未支援自動建立或自動更新統計資料。 為了獲得查詢的最佳效能，在首次載入資料，或是資料中發生重大變更之後，建立所有資料表的所有資料行統計資料非常重要。 如需統計資料的詳細說明，請參閱「開發」主題群組中的「[統計資料][Statistics]」主題。 以下是快速範例，說明如何在此範例中建立載入資料表的統計資料

在 sqlcmd 提示字元中執行下列 CREATE STATISTICS 陳述式：

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>從 SQL 資料倉儲匯出資料
在本教學課程中，您將從 Azure SQL 資料倉儲中的資料表建立資料檔案。 我們會將上面建立的資料匯出至新的資料檔案，稱為 DimDate2_export.txt。

### <a name="step-1-export-the-data"></a>步驟 1：匯出資料
在 bcp 公用程式中，您可以使用下列命令來連接並匯出資料 (請適當地取代其中的值)：

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
您可以開啟新的檔案來確認資料已正確匯出。 檔案中的資料應該符合以下文字：

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

> [!NOTE]
> 由於分散式系統的本質，資料順序在不同 SQL 資料倉儲資料庫中可能不相同。 另一個選項是使用 bcp 的 **queryout** 函式來撰寫查詢擷取，而不是匯出整個資料表。
> 
> 

## <a name="next-steps"></a>後續步驟
如需載入的概觀，請參閱[將資料載入 SQL 資料倉儲][Load data into SQL Data Warehouse]。
如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][SQL Data Warehouse development overview]。

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
