---
title: "資料載入指引 - Azure SQL 資料倉儲 | Microsoft Docs"
description: "使用 Azure SQL 資料倉儲載入資料及執行 ELT 的建議。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/13/2017
ms.author: barbkess
ms.openlocfilehash: 8903be1361d1574a5d81b69223f608ccb7a698ea
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="guidance-for-loading-data-into-azure-sql-data-warehouse"></a>將資料載入 Azure SQL 資料倉儲的指引
將資料載入 Azure SQL 資料倉儲的建議和效能最佳化。 

- 若要深入了解 PolyBase 及如何設計擷取、載入和轉換 (ELT) 程序，請參閱[設計 SQL 資料倉儲的 ELT](design-elt-data-loading.md)。
- 如需載入教學課程，請參閱[使用 PolyBase 將資料從 Azure Blob 儲存體載入 SQL 資料倉儲中](load-data-from-azure-blob-storage-using-polybase.md)。


## <a name="extract-source-data"></a>擷取來源資料

將資料從 SQL Server 或 Azure SQL 資料倉儲匯出成 ORC 檔案格式時，由於 java 的記憶體不足錯誤，可能會限制大量文字的資料行只能有 50 個資料行。 要解決這個問題，只能匯出部分資料行。


## <a name="land-data-to-azure"></a>將資料置於 Azure
PolyBase 無法載入具有超過 1 百萬個位元組之資料的資料列。 當您將資料放入 Azure Blob 儲存體或 Azure Data Lake Store 中的文字檔案時，這些檔案必須有少於 1 百萬個位元組的資料。 不論資料表結構描的定義為何，此一限制皆成立。

共置您的儲存層與資料倉儲，可將延遲降至最低。

## <a name="data-preparation"></a>資料準備

每種檔案格式具有不同的效能特性。 若要最快載入，使用壓縮的分隔文字檔案。 UTF-8 和 UTF-16 效能之間的差異最小。

將大型的壓縮檔案分成較小的壓縮檔案。

## <a name="create-designated-loading-users"></a>建立指定的載入使用者
若要以適當的計算資源執行載入，請建立為了執行載入而指定的載入使用者。 將每個載入使用者指派給特定的資源類別。 若要執行載入，請以其中一個載入使用者的身分登入，然後執行載入。 載入會利用使用者的資源類別來執行。  相較於嘗試變更使用者的資源類別，以符合目前的資源類別需求，這個方法比較簡單。

此程式碼會為 staticrc20 資源類別建立載入使用者。 它為使用者提供資料庫的控制權限，然後將使用者新增為 staticrc20 資料庫角色的成員。 若要使用 staticRC20 資源類別的資源執行載入，只需以 LoaderRC20 身分登入並執行載入。 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

在靜態資源類別，而不是動態資源類別之下執行載入。 不論您的[服務層級](performance-tiers.md#service-levels)為何，使用靜態資源類別可保證相同的資源。 如果您使用動態資源類別，資源就會根據您的服務層級而有所不同。 對於動態類別，較低服務層級表示您可能需要對您的載入使用者使用較大的資源類別。

### <a name="example-for-isolating-loading-users"></a>隔離載入使用者的範例

通常會需要多個使用者可將資料載入 SQL DW 中。 因為 [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] 需要資料庫的 CONTROL 權限，您將得到具有所有結構描述之控制存取的多個使用者。 若要限制這種情況，您可以使用 DENY CONTROL 陳述式。

例如，請考慮將資料庫結構描述 schema_A 用於 dept A 以及 schema_B 用於 dept B，讓資料庫使用者 user_A 和 user_B 個別成為 dept A 及 B 中載入的 PolyBase 之使用者。 這兩個使用者皆已獲得 CONTROL 資料庫權限。 結構描述 A 和 B 的建立者現在是使用 DENY 鎖定其結構描述：

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

user_A 和 user_B 現在已從其他部門的結構描述加以鎖定。


## <a name="load-to-a-staging-table"></a>載入至暫存表格

若要載入速度最快，載入堆積暫存表格 round_robin。 這是將資料從 Azure 儲存體層移到 SQL 資料倉儲的最有效方式。

如果您預期會有大量載入作業，將資料倉儲相應增加。

一次只執行一項載入作業，以取得最佳載入效能

### <a name="optimize-columnstore-index-loads"></a>最佳化資料行存放區索引載入

資料行存放區索引需要大量的記憶體，才能將資料壓縮成高品質的資料列群組。 為了最佳的壓縮和索引效率，資料行存放區索引需要將 1,048,576 個資料列壓縮到每個資料列群組中。 這是每個資料列群組的資料列數目上限。 當記憶體不足時，資料行存放區索引可能無法達到最大的壓縮率。 這反而會影響查詢效能。 如需深入探討，請參閱[資料行存放區記憶體最佳化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。

- 若要確保載入使用者有足夠的記憶體可達到最大的壓縮率，請使用是中型或大型資源類別成員的載入使用者。 
- 載入足夠的資料列，完全填滿新的資料列群組。 在大量載入期間，每 1,048,576 個資料列會直接移至資料行存放區。 載入少於 102,400 個資料列時，系統會將資料列傳送至差異存放區，以將資料列保存在叢集索引中，直到有足夠的資料列可供壓縮為止。 如果您載入太少資料列，這些資料列可能全都會移至差異存放區，並不會立即壓縮成資料行存放區格式。


### <a name="handling-loading-failures"></a>處理載入失敗

使用外部資料表的載入可能會失敗，並顯示「查詢已中止 -- 從外部來源讀取時已達最大拒絕閾值」錯誤訊息。 這表示您的外部資料包含 *「錯誤」* 記錄。 如果實際的資料類型/資料行數目不符合外部資料表的資料行定義，或資料不符合指定的外部檔案格式，則會將資料記錄視為「錯誤」。 

若要修正此問題，請確定您的外部資料表及外部檔案格式定義皆正確，且這些定義與您的外部資料相符。 萬一外部資料記錄的子集有錯誤，您可以使用 CREATE EXTERNAL TABLE DDL 中的拒絕選項，選擇拒絕這些查詢記錄。



## <a name="insert-data-into-production-table"></a>將資料插入生產資料表中
以下是將資料列插入生產資料表中的建議。


### <a name="batch-insert-statements"></a>Batch INSERT 陳述式
使用 [INSERT 陳述式](/sql/t-sql/statements/insert-transact-sql.md)單次載入小型資料表、或甚至定期重新載入查閱，可能會和使用像 `INSERT INTO MyLookup VALUES (1, 'Type 1')` 這樣的陳述式一樣正常執行。  單一插入的效率不如執行大量載入。 

如果您整天有數千個或更多單一插入，請將插入分批，以便進行大量載入。  開發將單一插入附加至檔案的程序，然後建立另一個可定期載入檔案的程序。

### <a name="create-statistics-after-the-load"></a>建立載入後的統計資料

為了改善查詢效能，在首次載入資料或資料發生重大變更之後，建立所有資料表的所有資料行統計資料非常重要。  如需統計資料的詳細說明，請參閱 [統計資料][統計資料]。 下列範例會在 Customer_Speed 資料表的五個資料行上建立統計資料。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>輪替儲存體金鑰
基於安全性考量，請定期變更您 blob 儲存體的存取金鑰。 您的 blob 儲存體帳戶有兩個儲存體金鑰。 如此一來，您就可以轉換金鑰。

若要輪替 Azure 儲存體帳戶金鑰：

1. 建立以次要儲存體存取金鑰為基礎的第二個資料庫範圍認證。
2. 建立以新的認證為基礎的第二個外部資料來源。
3. 卸除並建立外部資料表，使其指向新的外部資料來源。 

將外部資料表移轉到新的資料來源之後，請執行清除工作：

1. 卸除第一個外部資料來源。
2. 卸除以主要儲存體存取金鑰為基礎的第一個資料庫範圍認證。
3. 登入 Azure 並重新產生主要存取金鑰，以供下一次輪替。


## <a name="next-steps"></a>後續步驟
若要監視載入程序，請參閱[使用 DMV 監視工作負載](sql-data-warehouse-manage-monitor.md)。



