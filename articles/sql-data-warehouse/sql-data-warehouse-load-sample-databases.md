---
title: "將範例資料載入 SQL 資料倉儲 | Microsoft Docs"
description: "將範例資料載入 SQL 資料倉儲"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e338ecf8-cfee-419b-b7b6-98108d381c62
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 1e0df958a2f18fe1e988168918e5cfd293f84e64
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="load-sample-data-into-sql-data-warehouse"></a>將範例資料載入 SQL 資料倉儲
請遵循下列簡單的步驟來載入和查詢 Adventure Works 範例資料庫。 這些指令碼會先使用 sqlcmd 來執行將建立資料表和檢視表的 SQL。 一旦建立資料表之後，指令碼將使用 bcp 來載入資料。  如果您還沒有安裝 sqlcmd 和 bcp，請依照下列連結來[安裝 bcp][install bcp] 和[安裝 sqlcmd][install sqlcmd]。

## <a name="load-sample-data"></a>載入範例資料
1. 下載[適用於 SQL 資料倉儲的 Adventure Works 範例指令碼][Adventure Works Sample Scripts for SQL Data Warehouse] zip 檔。
2. 請從下載的 zip 將檔案解壓縮到本機電腦上的目錄。
3. 編輯解壓縮的 aw_create.bat 檔案，並設定下列可在檔案頂端找到的變數。  請務必不要在 "=" 和參數之間留有空白。  以下是您可能會想看看如何編輯的範例。
   
    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```
4. 從 Windows 命令提示字元執行編輯過的 aw_create.bat。  請確定您所在的目錄是您儲存編輯過之 aw_create.bat 版本的位置。
   此指令碼將...
   
   * 卸除任何 Adventure Works 資料表或任何已存在資料庫中的檢視表
   * 建立 Adventure Works 資料表和檢視表
   * 使用 bcp 載入每個 Adventure Works 資料表
   * 驗證每個 Adventure Works 資料表的資料列計數
   * 收集每個 Adventure Works 資料表之所有資料行上的統計資料

## <a name="query-sample-data"></a>查詢範例資料
一旦已經將某些範例資料載入您的 SQL 資料倉儲中，就可以快速地執行幾個查詢。  若要執行查詢，請使用 Visual Studio 和 SSDT 連接 Azure SQL DW 中新建立的 Adventure Works 資料庫，如[使用 Visual Studio 查詢][query with Visual Studio]文件中所述。

可取得員工之所有資訊的簡單 select 陳述式範例：

```sql
SELECT * FROM DimEmployee;
```

使用建構 (例如 GROUP BY) 來查看每一天所有銷售之總金額的較複雜查詢範例：

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

可篩選掉特定日期前之訂單的 SELECT 搭配 WHERE 子句的範例：

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SQL 資料倉儲幾乎支援所有 SQL Server 支援的 T-SQL 建構。  所有的差異都記錄在[移轉程式碼][migrate code]文件中。

## <a name="next-steps"></a>後續步驟
現在您已經有機會搭配範例資料嘗試一些查詢，請參閱以了解如何[開發][develop]、[載入][load]，或[移轉][migrate]到 SQL 資料倉儲。

<!--Image references-->

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[query with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrate code]: sql-data-warehouse-migrate-code.md
[install bcp]: sql-data-warehouse-load-with-bcp.md
[install sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Adventure Works Sample Scripts for SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
