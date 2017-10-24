---
title: "在 SQL 資料倉儲中使用 PolyBase 的指南 | Microsoft Docs"
description: "在 SQL 資料倉儲案例中使用 PolyBase 的指導方針和建議。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4757fce1-96b3-48ea-8a51-be1385705f9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 9/13/2017
ms.custom: loading
ms.author: cakarst;barbkess
ms.openlocfilehash: e8ae0eb96200c167a8758df4ce20b51452cc59a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>在 SQL 資料倉儲中使用 PolyBase 的指南
本指南提供在 SQL 資料倉儲中使用 PolyBase 的實用資訊。

如要開始使用，請參閱[使用 PolyBase 載入資料][Load data with PolyBase]教學課程。

## <a name="rotating-storage-keys"></a>替換儲存體金鑰
有時您基於安全性考量，會想要變更存取金鑰至您的 blob 儲存體。

要執行這項工作的最佳方式是遵循稱為「替換金鑰」的程序。 您可能已經注意到您在 blob 儲存體帳戶有兩個儲存體金鑰。 如此一來您就可以轉換

替換 Azure 儲存體帳戶金鑰的程序只需要簡單的三個步驟

1. 建立以次要儲存體存取金鑰為基礎的第二個資料庫範圍認證
2. 建立以新的認證為基礎的第二個外部資料來源
3. 卸除並建立指向新的外部資料來源的外部資料表

當您移轉所有的外部資料表到新的外部資料來源時，您就可以執行清除工作：

1. 卸除第一個外部資料來源
2. 卸除以主要儲存體存取金鑰為基礎的第一個資料庫範圍認證
3. 登入 Azure 並重新產生主要存取金鑰供下一次使用



## <a name="load-data-with-external-tables"></a>使用外部資料表載入資料
此範例將 Azure blob 儲存體中的資料載入至 SQL 資料倉儲資料庫。

直接儲存資料可免除查詢時的資料傳輸時間。 搭配 columnstore 索引儲存資料可讓分析查詢的查詢效能提升 10 倍。

此範例使用 CREATE TABLE AS SELECT 陳述式來載入資料。 新的資料表繼承查詢中指名的資料行。 它會從外部資料表定義繼承這些資料行的資料型別。

CREATE TABLE AS SELECT 是高效能 TRANSACT-SQL 陳述式，可將資料平行載入到您的 SQL 資料倉儲的所有計算節點。  它原本是針對分析平台系統中的大量平行處理 (MPP) 引擎所開發，現在已納入 SQL 資料倉儲中。

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

請參閱 [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)]。

> [!NOTE]
> 使用外部資料表的載入可能會失敗，並顯示「查詢已中止 -- 從外部來源讀取時已達最大拒絕閾值」錯誤訊息。 這表示您的外部資料包含 *「錯誤」* 記錄。 如果實際的資料類型/資料行數目不符合外部資料表的資料行定義，或資料不符合指定的外部檔案格式，則會將資料記錄視為「錯誤」。 若要修正此問題，請確定您的外部資料表及外部檔案格式定義皆正確，且這些定義與您的外部資料相符。 萬一外部資料記錄的子集有錯誤，您可以使用 CREATE EXTERNAL TABLE DDL 中的拒絕選項，選擇拒絕這些查詢記錄。
> 
> 


## <a name="create-statistics-on-newly-loaded-data"></a>建立新載入資料的統計資料
Azure 資料倉儲尚未支援自動建立或自動更新統計資料。  為了獲得查詢的最佳效能，在首次載入資料，或是資料中發生重大變更之後，建立所有資料表的所有資料行統計資料非常重要。  如需統計資料的詳細說明，請參閱「開發」主題群組中的[統計資料][Statistics]主題。  以下是快速範例，說明如何在此範例中建立載入資料表的統計資料。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-with-external-tables"></a>使用外部資料表匯出資料
這一節說明如何使用外部資料表將資料從 SQL 資料倉儲匯出至 Azure Blob 儲存體。 此範例使用 CREATE EXTERNAL TABLE AS SELECT (高效能 Transact-SQL 陳述式) 將資料從所有計算節點平行匯出。

下列範例會使用 dbo.Weblogs 資料表中的資料行定義和資料從 dbo 建立外部資料表 Weblogs2014。 外部資料表定義會儲存在 SQL 資料倉儲中，而 SELECT 陳述式的結果會匯出至資料來源所指定的 blob 容器下的 "/archive/log2014/" 目錄。 以指定的文字檔案格式匯出的資料。

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```
## <a name="isolate-loading-users"></a>隔離載入使用者
通常會需要多個使用者可將資料載入 SQL DW 中。 因為 [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] 需要資料庫的 CONTROL 權限，您將得到具有所有結構描述之控制存取的多個使用者。 若要限制這種情況，您可以使用 DENY CONTROL 陳述式。

範例：考慮將資料庫結構描述 schema_A 用於 dept A 以及 schema_B 用於 dept B，讓資料庫使用者 user_A 和 user_B 個別成為 dept A 及 B 中載入的 PolyBase 之使用者。 這兩個使用者皆已獲得 CONTROL 資料庫權限。
結構描述 A 和 B 的建立者現在是使用 DENY 鎖定其結構描述：

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   
 在此情況下，user_A 和 user_B 現在應該從其他部門的結構描述加以鎖定。
 
## <a name="polybase-performance-optimizations"></a>PolyBase 效能功能和最佳化
為了達到使用 PolyBase 的最佳載入效能，我們的建議如下：
- 將大型的壓縮檔案分成較小的壓縮檔案。 目前支援的壓縮類型不可分割。 因此，效能會受到載入單一大型檔案的影響。
- 若要載入速度最快，載入堆積暫存表格 round_robin。 這是將資料從儲存層移到資料倉儲最有效率的方式。
- 每種檔案格式具有不同的效能特性。 若要最快載入，使用壓縮的分隔文字檔案。 UTF-8 和 UTF-16 效能之間的差異最小。
- 共置儲存層和資料倉儲以將延遲降至最低。
- 如果您預期會有大量載入作業，將資料倉儲相應增加。

## <a name="polybase-limitations"></a>PolyBase 的限制
SQL DW 的PolyBase 有下列限制，在設計載入作業時必須納入考量：
- 單一資料列不能比 1,000,000 個位元組更寬。 不論資料表結構描的定義為何，此一限制皆成立。
- 將資料從 SQL Server 或 Azure SQL 資料倉儲匯出成 ORC 檔案格式時，由於 java 的記憶體不足錯誤，可能會限制大量文字的資料行只能有 50 個資料行。 要解決這個問題，只能匯出部分資料行。





## <a name="next-steps"></a>後續步驟
若要了解將資料移到 SQL 資料倉儲的詳細資訊，請參閱 [資料移轉概觀][data migration overview]。

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[data migration overview]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
