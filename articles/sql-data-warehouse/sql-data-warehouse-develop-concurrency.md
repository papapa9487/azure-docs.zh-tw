---
title: "SQL 資料倉儲中的並行存取和工作負載管理 | Microsoft Docs"
description: "了解 Azure SQL 資料倉儲中的並行存取和工作負載管理以開發解決方案。"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: b1ab2a8253684c62be650eed2ea5f69c62188a22
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>SQL 資料倉儲中的並行存取和工作負載管理
為了大規模傳遞可預測的效能，Microsoft Azure SQL 資料倉儲可協助您控制並行存取層級和資源配置，例如記憶體和 CPU 優先順序。 本文介紹並行存取和工作負載管理的概念；說明如何實作這兩種功能，以及如何在資料倉儲中控制它們。 SQL 資料倉儲工作負載管理的目的，是要幫助您支援多使用者環境。 它並不適用於多租用戶工作負載。

## <a name="concurrency-limits"></a>並行存取限制
SQL 資料倉儲最多允許 1,024 個並行存取連線。 這 1,024 個連線全都可以並行提交查詢。 不過，為達到最佳輸送量，SQL 資料倉儲可能會將某些查詢排入佇列，以確保每個查詢能夠獲得最少的記憶體授與。 佇列會在查詢執行時發生。 藉由在到達並行存取限制時將查詢排入佇列，SQL 資料倉儲就可透過確保作用中查詢能夠取得急需的記憶體資源存取權，來增加總輸送量。  

並行存取限制受到兩個概念所控制：「並行查詢」和「並行存取插槽」。 若要執行查詢，它必須在查詢並行限制和並行存取插槽配置內執行。

* 並行查詢是在同一時間執行的查詢。 「SQL 資料倉儲」在較大的 DWU 大小上最多可支援 32 個並行查詢。
* 並行存取插槽是根據 DWU 所配置。 每 100 個 DWU 提供 4 個並行存取插槽。 例如，DW100 會配置 4 個並行存取插槽，而 DW1000 會配置 40 個。 根據查詢的 [資源類別](#resource-classes) 而定，每個查詢會使用一或多個並行存取插槽。 smallrc 資源類別中執行的查詢會使用一個並行存取插槽。 較高資源類別中執行的查詢會使用更多的並行存取插槽。

下表說明在不同的 DWU 大小，並行查詢和並行存取插槽的限制。

### <a name="concurrency-limits"></a>並行存取限制
| DWU | 並行查詢上限 | 配置的並行存取插槽 |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

當符合以上其中一個臨界值時，新的查詢將以先進先出順序排入佇列和執行。  當一個查詢完成，而查詢與插槽數目低於限制時，就會釋放已排入佇列的查詢。 

> [!NOTE]  
> 只針對動態管理檢視 (DMV) 或目錄檢視執行的 SELECT 查詢不受任何並行存取限制所控管。 不論在系統上執行的查詢數目多寡，您都可監視該系統。
> 
> 

## <a name="resource-classes"></a>資源類別
資源類別可協助您控制指定給查詢的記憶體配置以及 CPU 週期。 您可以利用資料庫角色的形式對使用者指派兩種資源類別。 這兩種資源類別如下所示：
1. 動態資源類別 (**smallrc、mediumrc、largerc、xlargerc**) 會根據目前的 DWU 來配置數量不一的記憶體。 這表示當您相應增加為較大的 DWU 時，您的查詢會自動獲得更多記憶體。 
2. 靜態資源類別 (**staticrc10、staticrc20、staticrc30、staticrc40、staticrc50、staticrc60、staticrc70、staticrc80**) 則一律會配置相同數量的記憶體，不論目前的 DWU 有多大 (前提是 DWU 本身有足夠的記憶體)。 這表示在較大的 DWU 中，您將可以在每個資源類別中同時執行更多查詢。

採用 **smallrc** 和 **staticrc10** 的使用者會獲得較小量的記憶體，且可利用較高的並行存取能力。 相反地，指派給 **xlargerc** 或 **staticrc80** 的使用者會獲得大量的記憶體，因此可並行執行的查詢較少。

根據預設，每位使用者都是小型資源類別 **smallrc** 的成員。 `sp_addrolemember` 程序可用來增加資源類別，`sp_droprolemember` 則可用來減少資源類別。 例如，此命令會將 loaduser 的資源類別增加為 **largerc**︰

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>不採用資源類別的查詢

有幾種查詢類別不會因較大的記憶體配置而受益。 系統會忽略它們的資源類別配置，並一律改為在小型資源類別中執行這些查詢。 如果這些查詢一律會在小型資源類別中執行，它們就能在並行存取插槽有壓力時執行，而且只會取用所需的插槽。 如需詳細資訊，請參閱 [資源類別例外狀況](#query-exceptions-to-concurrency-limits) 。

## <a name="details-on-resource-class-assignment"></a>資源類別指派的詳細資料


資源類別的其他詳細資料︰

*  權限，才能變更使用者的資源類別。
* 雖然您可以將使用者新增至一或多個較高的資源類別，但動態資源類別的優先順序高於靜態資源類別。 也就是說，如果您將使用者同時指派給 **mediumrc** (動態) 和 **staticrc80** (靜態)，則系統會採用 **mediumrc** 資源類別。
 * 當您將使用者指派給特定資源類別類型中的多個資源類別 (多個動態資源類別或多個靜態資源類別) 時，系統會採用最高的資源類別。 也就是說，如果您將使用者同時指派給 mediumrc 和 largerc，則系統會採用較高的資源類別 (largerc)。 而且，如果您將使用者指派給 **staticrc20** 和 **statirc80**，系統會採用 **staticrc80**。
* 無法變更系統管理使用者的資源類別。

如需詳細範例，請參閱 [變更使用者資源類別的範例](#changing-user-resource-class-example)。

## <a name="memory-allocation"></a>記憶體配置
提升使用者的資源類別有利有弊。 提升使用者的資源類別可讓查詢存取更多記憶體，這表示查詢可能執行得更快。  不過，較高的資源類別也會減少可以執行的並行查詢數目。 應該將大量記憶體配置給單一查詢，還是允許其他也需要配置記憶體的查詢並行執行，需要在這兩者之間進行取捨。 如果為某一位使用者提供高記憶體配置進行查詢，則其他使用者將無法存取該相同記憶體來執行查詢。

下表依 DWU 和資源類別來對應所配置的記憶體與每個散發套件。

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>每個散發套件的動態資源類別 (MB) 記憶體配置
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1,600 |
| DW500 |100 |400 |800 |1,600 |
| DW600 |100 |400 |800 |1,600 |
| DW1000 |100 |800 |1,600 |3,200 |
| DW1200 |100 |800 |1,600 |3,200 |
| DW1500 |100 |800 |1,600 |3,200 |
| DW2000 |100 |1,600 |3,200 |6,400 |
| DW3000 |100 |1,600 |3,200 |6,400 |
| DW6000 |100 |3,200 |6,400 |12,800 |

下表依 DWU 和靜態資源類別來對應所配置的記憶體與每個散發套件。 請注意，較高的資源類別會縮減其記憶體，以採用全域的 DWU 限制。

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>每個散發套件的靜態資源類別 (MB) 記憶體配置
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW500 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW600 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW1000 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1200 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1500 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW2000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW3000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW6000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |12,800 |

從上表中，可知在 DW2000 上以 **xlargerc** 資源類別執行的查詢，可在 60 個分散式資料庫內各取得 6,400 MB 的記憶體。  SQL 資料倉儲中有 60 個散發套件。 因此，若要計算給定資源類別中某個查詢的總記憶體配置，上述的值應該乘以 60。

### <a name="memory-allocations-system-wide-gb"></a>全系統記憶體配置 (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

從以上的系統整體記憶體配置表格中，可知以 xlargerc 資源類別於 DW2000 上執行的查詢，會在整個 SQL 資料倉儲配置總共 375 GB 的記憶體 (6,400 MB * 60 個散發套件 / 1,024 以轉換為 GB)。

靜態資源類別的計算方式也是這樣。
 
## <a name="concurrency-slot-consumption"></a>並行存取插槽耗用量  
針對在更高資源類別中執行的查詢，SQL 資料倉儲會授與更多記憶體。 記憶體是固定的資源。  因此，配置給每個查詢的記憶體越多，可執行的並行查詢就越少。 下表會以單一檢視重述上述所有概念，其中顯示 DWU 可用的並行存取插槽數目和每個資源類別所取用的插槽數目。  

### <a name="allocation-and-consumption-of-concurrency-slots"></a>並行存取插槽的配置和耗用量  
| DWU | 並行查詢上限 | 配置的並行存取插槽 | Smallrc 所使用的插槽 | Mediumrc 所使用的插槽 | Largerc 所使用的插槽 | Xlargerc 所使用的插槽 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>靜態資源類別並行存取插槽的配置和耗用量
| DWU | 並行查詢上限 | 配置的並行存取插槽 |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

從上述表格中，您會看到以 DW1000 身分執行的 SQL 資料倉儲，配置最多 32 個並行查詢，以及總數 40 個的並行存取插槽。 如果所有的使用者都以 smallrc 執行，將允許 32 個並行查詢，因為每個查詢會取用 1 個並行存取插槽。 如果 DW1000 上的所有使用者都是在 mediumrc 中執行，則會針對每個散發套件為各個查詢各配置 800 MB，每個查詢的總記憶體配置為 47 GB，且將並行存取限制為 5 位使用者 (40 個並行存取插槽 / 每位 mediumrc 使用者 8 個插槽)。

## <a name="selecting-proper-resource-class"></a>選取適當的資源類別  
理想的作法是將使用者永久指派給資源類別，而不是變更他們的資源類別。 例如，在配置了更多記憶體時，叢集資料行存放區資料表的載入會建立更高品質的索引。 若要確保載入可以取得更高的記憶體，請建立特別用來載入資料的使用者，並將使用者永久指派給較高的資源類別。
以下是幾種可供採用的最佳做法。 如上所述，SQL DW 支援兩種資源類別類型：靜態資源類別和動態資源類別。
### <a name="loading-best-practices"></a>載入的最佳做法
1.  如果預期的載入具有一般數量的資料，則靜態資源類別是不錯的選擇。 之後當您相應增加系統以獲得更多運算能力時，資料倉儲將能夠立即執行更多的並行查詢，因為載入使用者不會耗用更多的記憶體。
2.  如果在某些情況下預期會有更大的載入，則動態資源類別是不錯的選擇。 之後當您相應增加系統以獲得更多運算能力時，載入使用者會獲得更多立即可用的記憶體，因此可讓載入提高執行速度。

若要有效率地處理載入，所需的記憶體取決於所載入之資料表的性質和所處理的資料量。 例如，將資料載入到 CCI 資料表需要使用一些記憶體，來讓 CCI 資料列群組達到最適化。 如需詳細資訊，請參閱「資料行存放區索引 - 資料載入」指引。

我們會建議您採用的最佳做法是，至少提供 200 MB 的記憶體供載入使用。

### <a name="querying-best-practices"></a>查詢的最佳做法
查詢會因其複雜性而有不同的需求。 視查詢需求而定，增加每個查詢的記憶體或增加並行存取能力都能有效地增加整體的輸送量。
1.  如果預期進行的是定期的複雜查詢 (例如，要產生每日及每週報告)，而且不需要利用並行存取能力，則動態資源類別是不錯的選擇。 如果系統要處理的資料變多，相應增加資料倉儲會因此自動提供更多的記憶體供執行查詢的使用者使用。
2.  如果預期進行的是變動或日常的並行存取模式 (例如，如果透過可供廣泛存取的 Web UI 來查詢資料庫)，則靜態資源類別是不錯的選擇。 之後當您相應增加資料倉儲時，與靜態資源類別相關聯的使用者就能夠自動執行更多的並行查詢。

根據查詢需求來選取要授與的適當記憶體並不容易，因為這取決於許多因素，例如所查詢的資料量、資料表結構描述的性質，以及各種聯結、選取和群組述詞。 一般來說，配置更多的記憶體會讓查詢更快完成，但會減少整體的並行存取能力。 如果您不在意並行存取能力，則可以放心地超額配置記憶體。 若要微調輸送量，您可能需要嘗試不同的資源類別組合。

您可以使用下列預存程序，找出在給定 SLO 時要對每個資源類別授與的並行存取能力和記憶體，以及在給定資源類別時最適合非分割 CCI 資料表上的記憶體密集 CCI 作業使用的最接近資源類別：

#### <a name="description"></a>Description:  
以下是此預存程序的用途：  
1. 協助使用者找出在給定 SLO 時，要對每個資源類別授與的並行存取能力和記憶體。 為此，使用者必須同時為結構描述和 tablename 提供 NULL，如下列範例所示。  
2. 協助使用者找出在給定資源類別時，最適合非分割 CCI 資料表上的記憶體密集 CCI 作業 (載入、複製資料表、重建索引等) 使用的最接近資源類別。 此預存程序會使用資料表結構描述來找出為此所需授與的記憶體。

#### <a name="dependencies--restrictions"></a>相依性及限制：
- 此預存程序並非設計來計算分割 CCI 資料表的記憶體需求。    
- 此預存程序不會針對 CTAS/INSERT-SELECT 的 SELECT 部分考慮記憶體需求，而是會假設它是簡單的 SELECT。
- 此預存程序會使用暫存資料表，因此可用於此預存程序建立所在的工作階段。    
- 此預存程序仰賴目前的供應項目 (例如，硬體組態、DMS 組態)，如果其中有任何變更，此預存程序就無法正確運作。  
- 此預存程序仰賴目前提供的並行存取限制，如果限制有任何變更，此預存程序就無法正確運作。  
- 此預存程序仰賴現有的資源類別供應項目，如果其中有任何變更，此預存程序就無法正確運作。  

>  [!NOTE]  
>  如果您在使用所提供的參數來執行預存程序後沒有取得輸出，則可能有兩種情況。 <br />1.DW 參數包含無效的 SLO 值 <br />2.沒有符合 CCI 作業的資源類別 (若已提供資料表名稱)。 <br />例如，在 DW100 時，可供授與的最高記憶體為 400 MB，但資料表結構描述的寬度卻足以跨過 400 MB 的需求。
      
#### <a name="usage-example"></a>使用範例：
語法：  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: 提供 NULL 參數以從資料倉儲資料庫擷取目前的 DWU，或是以 'DW100' 的形式提供任何受支援的 DWU
2. @SCHEMA_NAME: 提供資料表的結構描述名稱
3. @TABLE_NAME: 提供相關的資料表名稱

執行此預存程序的範例：  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

上述範例中使用的 Table1 會以如下方式建立：  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>以下是預存程序定義：
```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(   @DWU VARCHAR(7),
      @SCHEMA_NAME VARCHAR(128),
       @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temptable (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="query-importance"></a>查詢的重要性
SQL 資料倉儲使用工作負載群組來實作資源類別。 共有八個工作負載群組，它們可跨各種 DWU 大小控制資源類別的行為。 對於任何 DWU，SQL 資料倉儲只會使用這八個工作負載群組的其中四個。 這很合理，因為每個工作負載群組都會指派給四個資源類別之一：smallrc、mediumrc、largerc 或 xlargerc。 了解這些工作負載群組的重要性在於其中某些工作負載群組會設定為較高的「重要性」 。 重要性可用於 CPU 排程。 以高重要性執行的查詢會取得比中度重要性的查詢高三倍的 CPU 週期。 因此，並行存取插槽對應也會判斷 CPU 優先順序。 當查詢取用 16 個以上的插槽時，就會以高重要性執行。

下表是每個工作負載群組的重要性對應。

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>工作負載群組與並行存取插槽數目和重要性的對應
| 工作負載群組 | 並行存取插槽對應 | MB / 散發套件 | 重要性對應 |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |中型 |
| SloDWGroupC01 |2 |200 |中型 |
| SloDWGroupC02 |4 |400 |中型 |
| SloDWGroupC03 |8 |800 |中型 |
| SloDWGroupC04 |16 |1,600 |高 |
| SloDWGroupC05 |32 |3,200 |高 |
| SloDWGroupC06 |64 |6,400 |高 |
| SloDWGroupC07 |128 |12,800 |高 |

從 **並行存取插槽的配置和耗用量** 圖表中，您可以看到 DW500 針對 smallrc、mediumrc、largerc 和 xlargerc，分別使用了 1、4、8 或 16 個並行存取插槽。 您可以在上述圖表中查看這些值，以了解每個資源類別的重要性。

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>重要性與資源類別的 DW500 對應
| 資源類別 | 工作負載群組 | 已使用的並行存取插槽 | MB / 散發套件 | 重要性 |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |中型 |
| mediumrc |SloDWGroupC02 |4 |400 |中型 |
| largerc |SloDWGroupC03 |8 |800 |中型 |
| xlargerc |SloDWGroupC04 |16 |1,600 |高 |
| staticrc10 |SloDWGroupC00 |1 |100 |中型 |
| staticrc20 |SloDWGroupC01 |2 |200 |中型 |
| staticrc30 |SloDWGroupC02 |4 |400 |中型 |
| staticrc40 |SloDWGroupC03 |8 |800 |中型 |
| staticrc50 |SloDWGroupC03 |16 |1,600 |高 |
| staticrc60 |SloDWGroupC03 |16 |1,600 |高 |
| staticrc70 |SloDWGroupC03 |16 |1,600 |高 |
| staticrc80 |SloDWGroupC03 |16 |1,600 |高 |

您可以使用下列 DMV 查詢，從資源管理員的觀點詳細查看記憶體資源配置的差異，或在進行疑難排解時，分析工作負載群組的作用中和歷史使用量。

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>採用並行存取限制的查詢
大多數查詢都受到資源類別控管。 這些查詢必須同時符合並行查詢和並行存取插槽臨界值。 使用者無法選擇從並行存取插槽模型中排除查詢。

再次提醒您，下列陳述式會採用資源類別：

* INSERT-SELECT
* UPDATE
* 刪除
* SELECT (當查詢使用者資料表時)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* 載入資料
* 資料移動服務 (DMS) 進行的資料移動作業

## <a name="query-exceptions-to-concurrency-limits"></a>並行存取限制查詢例外狀況
部分查詢不支援使用者受指派的資源類別。 這些並行存取限制例外狀況是在某特定命令所需的記憶體資源過低時才會發生，通常是因為該命令為中繼資料作業。 這些例外狀況的目標是，避免將較大的記憶體配置給永遠不需要那麼多記憶體的查詢。 在這些情況下，一律會使用預設小型資源類別 (smallrc)，而不考慮指派給使用者的實際資源類別。 例如， `CREATE LOGIN` 一律會在 smallrc 中執行。 完成此作業所需的資源非常少，因此沒必要將查詢納入並行存取插槽模型中。  這些查詢也不會受到 32 個使用者並行存取限制，不限數目的這些查詢可以執行的工作階段上限為 1,024 個工作階段。

下列陳述式未採用資源類別：

* CREATE 或 DROP TABLE
* ALTER TABLE ...SWITCH、SPLIT 或 MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE、UPDATE 或 DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE、ALTER 或 DROP USER
* CREATE、ALTER 或 DROP PROCEDURE
* CREATE 或 DROP VIEW
* INSERT VALUES
* 從系統檢視和 DMV 來 SELECT
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a> 變更使用者資源類別的範例
1. **建立登入：**開啟 SQL Server 上裝載 SQL 資料倉儲資料庫的**主要**資料庫的連接，然後執行下列命令。
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > 在主要資料庫中建立一個使用者做為 Azure SQL 資料倉儲使用者是不錯的主意。 在主要資料庫中建立使用者，使用者就能使用類似 SSMS 的工具登入而不用指定資料庫名稱。  它也可讓使用者使用物件總管來檢視 SQL Server 上的所有資料庫。  如需有關建立和管理使用者的詳細資料，請參閱[保護 SQL 資料倉儲中的資料庫][Secure a database in SQL Data Warehouse]。
   > 
   > 
2. **建立 SQL 資料倉儲使用者︰**開啟 **SQL 資料倉儲**資料庫的連接，然後執行下列命令。
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **授與權限︰**下列範例會授與 **SQL 資料倉儲** 資料庫上的 `CONTROL`。 資料庫層級的 `CONTROL`相當於 SQL Server 中的 db_owner。
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **增加資源類別︰** 使用下列查詢，將使用者加入至更高的工作負載管理角色。
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **減少資源類別︰** 使用下列查詢，從工作負載管理角色移除使用者。
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > 您無法從 smallrc 移除使用者。
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>已排入佇列的查詢偵測和其他 DMV
您可以使用 `sys.dm_pdw_exec_requests` DMV，來識別正在並行存取佇列中等候的查詢。 正在等待並行存取插槽的查詢狀態為 **暫停**。

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

工作負載管理角色可以使用 `sys.database_principals`來檢視。

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

下列查詢會顯示每位使用者指派給哪些角色。

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL 資料倉儲具有下列等候類型：

* **LocalQueriesConcurrencyResourceType**：位於並行存取插槽架構外部的查詢。 DMV 查詢及 `SELECT @@VERSION` 這類的系統函數是本機查詢的範例。
* **UserConcurrencyResourceType**：位於並行存取插槽架構內部的查詢。 針對使用者資料表的查詢代表會使用此資源類型的範例。
* **DmsConcurrencyResourceType**：資料移動作業所產生的等候。
* **BackupConcurrencyResourceType**：此等候指出正在備份資料庫。 此資源類型的最大值為 1。 如果在同一時間要求多個備份，其他備份將會排入佇列。

`sys.dm_pdw_waits` DMV 可用來查看要求正在等待哪些資源。

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

`sys.dm_pdw_resource_waits` DMV 只會顯示等待指定查詢取用的資源。 資源等候時間只會測量等候提供資源的時間，與訊號等候時間相反，後者是基礎 SQL Server 將查詢排程到 CPU 所需的時間。

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

`sys.dm_pdw_wait_stats` DMV 可用於針對等候項目進行歷史趨勢分析。

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>後續步驟
如需管理資料庫使用者和安全性的詳細資訊，請參閱[保護 SQL 資料倉儲中的資料庫][Secure a database in SQL Data Warehouse]。 如需較大資源類別如何改善叢集資料行存放區索引品質的詳細資訊，請參閱 [重建索引以提升區段品質]。

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[重建索引以提升區段品質]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

