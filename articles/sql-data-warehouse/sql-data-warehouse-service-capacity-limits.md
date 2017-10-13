---
title: "SQL 資料倉儲容量限制 | Microsoft Docs"
description: "SQL 資料倉儲的連接、資料庫、資料表及查詢最大值。"
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: e1eac122-baee-4200-a2ed-f38bfa0f67ce
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: 52026a58a5b6e26a660f9e1374e67036c67ac525
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse-capacity-limits"></a>SQL 資料倉儲容量限制
下表包含 Azure SQL 資料倉儲各種元件的最大允許值。

## <a name="workload-management"></a>工作負載管理
| 類別 | 說明 | 最大值 |
|:--- |:--- |:--- |
| [資料倉儲單位 (DWU)][Data Warehouse Units (DWU)] |單一 SQL 資料倉儲的最大 DWU |6000 |
| [資料倉儲單位 (DWU)][Data Warehouse Units (DWU)] |單一 SQL Server 的最大 DWU |預設為 6000<br/><br/> 每個 SQL Server (例如 myserver.database.windows.net) 的 DTU 配額為 45,000，最多允許 6000 DWU。 此配額僅是安全限制。 您可以藉由[建立支援票證][creating a support ticket]，並選取 [配額] 做為要求類型來增加配額。  若要計算 DTU 需求，將所需的總 DWU 乘以 7.5。 您可以在入口網站的 [SQL Server] 刀鋒視窗中檢視目前的 DTU 耗用量。 已暫停和未暫停的資料庫都會計入 DTU 配額。 |
| 資料庫連接 |同時開啟的工作階段 |1024<br/><br/>我們支援最多 1024 個使用中的連線，每個連線可以同時針對一個 SQL 資料倉儲資料庫提交要求。 請注意，實際可以並行執行的查詢數是有限的。 超過並行存取限制時，要求會進入內部佇列以等待處理。 |
| 資料庫連接 |準備陳述式的最大記憶體 |20 MB |
| [工作負載管理][Workload management] |並行查詢上限 |32<br/><br/> 根據預設，SQL 資料倉儲可以執行最多 32 並行查詢和佇列剩餘查詢。<br/><br/>當使用者指派給較高的資源類別，或以低的 DWU 設定 SQL 資料倉儲時，可能會降低並行處理等級。 系統總是會允許某些查詢 (例如 DMV 查詢) 執行。 |
| [Tempdb][Tempdb] |Tempdb 的大小上限 |每一 DW100 399 GB。 因此，DWU1000 Tempdb 大小為 3.99 TB |

## <a name="database-objects"></a>資料庫物件
| 類別 | 說明 | 最大值 |
|:--- |:--- |:--- |
| 資料庫 |大小上限 |在磁碟上壓縮後 240 TB<br/><br/>此空間與 tempdb 或記錄檔空間無關，因此此空間為供永久資料表專用。  叢集資料行存放區壓縮估計為 5 X。  當所有資料表都是叢集資料行存放區 (預設的資料表類型) 時，這個壓縮可讓資料庫成長約 1 PB。 |
| 資料表 |大小上限 |磁碟上壓縮後 60 TB |
| 資料表 |每個資料庫的資料表 |20 億 |
| 資料表 |每個資料表的資料行 |1024 個資料行 |
| 資料表 |每個資料行的位元組 |相依於資料行[資料類型][data type]。  char 資料類型的限制為 8000、nvarchar 為 4000 或 MAX 資料類型為 2 GB。 |
| 資料表 |每個資料列的位元組，已定義的大小 |8060 個位元組<br/><br/>每個資料列的位元組數目計算方式和使用頁面壓縮的 SQL Server 所使用的方式相同。 就像 SQL Server，SQL 資料倉儲支援資料列溢位儲存，讓 **可變長度的資料行** 能發送至超出資料列。 可變長度的資料列會發送至超出資料列，只有 24 位元組的根會儲存在主要記錄中。 如需詳細資訊，請參閱[超過 8 KB 的資料列溢位資料][Row-Overflow Data Exceeding 8 KB] MSDN 文章。 |
| 資料表 |每個資料表的資料分割 |15,000<br/><br/>為了獲得高效能，建議在仍能支援業務需求的情況下，將您需要的資料分割數目降至最低。 隨著資料分割數目增加，資料定義語言 (DDL) 和資料操作語言 (DML) 作業的負荷會加重，導致效能變慢。 |
| 資料表 |每個資料分割界限值的字元。 |4000 |
| 索引 |每個資料表的非叢集索引。 |999<br/><br/>僅適用於資料列存放區資料表。 |
| 索引 |每個資料表的叢集索引。 |1<br><br/>適用於資料列存放區資料表和資料行存放區資料表。 |
| 索引 |索引鍵的大小。 |900 個位元組。<br/><br/>僅適用於資料列存放區索引。<br/><br/>建立索引時，如果資料行中的現有資料沒有超過 900 個位元組，就可以在 varchar 資料行上建立大小上限超過 900 個位元組的索引。 不過，後續在資料行上執行 INSERT 或 UPDATE 動作時，如果總計大小超過 900 個位元組，將會失敗。 |
| 索引 |每個索引的索引鍵資料行。 |16<br/><br/>僅適用於資料列存放區索引。 叢集資料行存放區索引包含所有資料行。 |
| 統計資料 |結合資料行值的大小。 |900 個位元組。 |
| 統計資料 |每個統計資料物件的資料行。 |32 |
| 統計資料 |每個資料表的資料行上建立的統計資料。 |30,000 |
| 預存程序 |最大巢狀層級。 |8 |
| 檢視 |每個檢視表的資料行 |1,024 |

## <a name="loads"></a>載入
| 類別 | 說明 | 最大值 |
|:--- |:--- |:--- |
| PolyBase 載入 |每列 MB 數 |1<br/><br/>PolyBase 載入被限制為只能載入小於 1MB 的資料列，且不能載入至 VARCHR(MAX)、NVARCHAR(MAX) 或 VARBINARY(MAX)。<br/><br/> |

## <a name="queries"></a>查詢
| 類別 | 說明 | 最大值 |
|:--- |:--- |:--- |
| 查詢 |使用者資料表上已排入佇列的查詢。 |1000 |
| 查詢 |系統檢視表上的並行查詢。 |100 |
| 查詢 |系統檢視表上已排入佇列的查詢 |1000 |
| 查詢 |參數個數上限 |2098 |
| 批次 |大小上限 |65,536*4096 |
| SELECT 結果 |每個資料列的資料行 |4096<br/><br/>在 SELECT 結果中，每個資料列一律不超過 4096 個資料行。 不保證一定可以有 4096 個。 如果查詢計畫需要暫存資料表，可能會限定每個資料表最多 1024 個資料行。 |
| SELECT |巢狀子查詢 |32<br/><br/>SELECT 陳述式中一律不超過 32 個巢狀子查詢。 不保證一定可以有 32 個。 例如，JOIN 可以將子查詢加入查詢計畫中。 子查詢的數目也受限於可用記憶體。 |
| SELECT |每個 JOIN 的資料行 |1024 個資料行<br/><br/>JOIN 中一律不超過 1024 個資料行。 不保證一定可以有 1024 個。 如果 JOIN 計畫需要比 JOIN 結果更多資料行的暫存資料表，暫存資料表會受限於 1024 的限制。 |
| SELECT |每個 GROUP BY 資料行的位元組。 |8060<br/><br/>GROUP BY 子句中的資料行最多可以有 8060 個位元組。 |
| SELECT |每個 ORDER BY 資料行的位元組 |8060 個位元組。<br/><br/>ORDER BY 子句中的資料行最多可以有 8060 個位元組。 |
| 每個陳述式的識別項和常數 |參考的識別項和常數個數。 |65,535<br/><br/>SQL 資料倉儲會限制查詢的單一運算式中可包含的識別項和常數個數。 此限制為 65,535。 超過此數字會導致 SQL Server 錯誤 8632。 如需詳細資訊，請參閱[內部錯誤：到達運算式服務的限制][Internal error: An expression services limit has been reached]。 |

## <a name="metadata"></a>中繼資料
| 系統檢視表 | 最大資料列數 |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |最近 1000 個 SQL 要求的 DMS 背景工作角色總數。 |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |儲存在 sys.dm_pdw_exec_requests 中的最近 1000 個 SQL 要求的步驟總數。 |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |儲存在 sys.dm_pdw_exec_requests 中的最近 1000 個 SQL 要求。 |

## <a name="next-steps"></a>後續步驟
如需更多的參考資訊，請參閱 [SQL 資料倉儲參考概觀][SQL Data Warehouse reference overview]。

<!--Image references-->

<!--Article references-->
[Data Warehouse Units (DWU)]: ./sql-data-warehouse-overview-what-is.md
[SQL Data Warehouse reference overview]: ./sql-data-warehouse-overview-reference.md
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[data type]: ./sql-data-warehouse-tables-data-types.md
[creating a support ticket]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Row-Overflow Data Exceeding 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Internal error: An expression services limit has been reached]: https://support.microsoft.com/kb/913050
