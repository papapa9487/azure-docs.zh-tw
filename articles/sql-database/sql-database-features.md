---
title: "Azure SQL Database 功能概觀 | Microsoft Docs"
description: "此頁面會提供 Azure SQL Database 邏輯伺服器和資料庫的概觀，並包含每個所列出功能連結的功能支援矩陣。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 10/25/2017
ms.author: carlrab
ms.openlocfilehash: 1505b85a8828cd64045a982ece48e49d85ea3314
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-features"></a>Azure SQL Database 功能

Azure SQL Database 與 SQL Server 共用通用基底程式碼，並在資料庫層級支援大部分相同的功能。 Azure SQL Database 和 SQL Server 之間的主要功能差異是執行個體層級。 

我們會持續新增 Azure SQL Database 的功能。 因此我們鼓勵您造訪我們的 Azure 服務更新網頁，並使用它的篩選條件：

* 篩選出 [SQL Database 服務](https://azure.microsoft.com/updates/?service=sql-database)。
* 篩選出 SQL Database 功能的「正式運作 [(GA)」公告](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 。

## <a name="sql-server-and-sql-database-feature-support"></a>SQL Server 及 SQL Database 功能支援

下表列出 SQL Server 的主要功能，並提供是否支援每項特定功能的相關資訊以及功能詳細資訊連結。 如需移轉現有資料庫解決方案時所要考量的 Transact-SQL 差異，請參閱[解決移轉至 SQL Database 期間的 Transact-SQL 差異](sql-database-transact-sql-information.md)。


| **SQL Server 功能** | **在 Azure SQL Database 中支援** | 
| --- | --- |  
| [一律加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 是 - 請參閱[憑證存放區](sql-database-always-encrypted.md)和[金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md)|
| [AlwaysOn 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 每個資料庫皆包含高可用性。 [Azure SQL Database 的業務連續性概觀](sql-database-geo-replication-overview.md)會討論災害復原 |
| [連結資料庫](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 否 |
| [應用程式角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | 是 |
|[稽核](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [是](sql-database-auditing.md)|
| [自動微調](/sql/relational-databases/automatic-tuning/automatic-tuning)| [是](sql-database-automatic-tuning.md)|
| [BACPAC 檔案 (匯出)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 是 - 請參閱 [SQL Database 匯出](sql-database-export.md) |
| [BACPAC 檔案 (匯入)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 是 - 請參閱 [SQL Database 匯入](sql-database-import.md) |
| [備份命令](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 否 - 請參閱[自動備份](sql-database-automated-backups.md) |
| [內建函式](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大部分 - 請參閱個別函式 |
| [變更資料擷取](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 否 |
| [變更追蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | 是 |
| [定序陳述式](https://docs.microsoft.com/sql/t-sql/statements/collations) | 是 |
| [資料行存放區索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 是 - [僅限進階版](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| [通用語言執行平台 (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 否 |
| [自主資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | 是 |
| [自主使用者](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | 是 |
| [控制流程語言關鍵字](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | 是 |
| [跨資料庫查詢](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | 部分 - 請參閱[彈性查詢](sql-database-elastic-query-overview.md) |
| [資料指標](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 是 | 
| [資料壓縮](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | 是 |
| [Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 否 |
| [資料庫鏡像](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 否 |
| [資料庫組態設定](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | 是 |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 否 |
| [資料庫快照集](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 否 |
| [資料類型](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | 是 |  
| [DBCC 陳述式](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大部分 - 請參閱個別陳述式 |
| [DDL 陳述式](https://docs.microsoft.com/sql/t-sql/statements/statements) | 是 |
| [DDL 觸發程序](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 僅限資料庫 |
| [分散式交易 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 否 - 請參閱[彈性交易](sql-database-elastic-transactions-overview.md) |
| [DML 陳述式](https://docs.microsoft.com/sql/t-sql/queries/queries) | 是 |
| [DML 觸發程序](https://docs.microsoft.com/en-us/sql/relational-databases/triggers/create-dml-triggers) | 大部分 - 請參閱個別陳述式 | 
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 部分 - 請參閱個別 DMV |
|[動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking)|[是](sql-database-dynamic-data-masking-get-started.md)|
| [事件通知](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 否 - 請參閱[警示](sql-database-insights-alerts-portal.md) |
| [運算式](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |是 |
| [擴充事件](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 部分請參閱 [SQL Database 中的擴充事件](sql-database-xevent-db-diff-from-svr.md) |
| [擴充預存程序](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 否 |
| [檔案和檔案群組](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 僅限主要檔案群組 |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 否 |
| [全文檢索搜尋](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | 不支援第三方斷詞工具 |
| [函式](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大部分 - 請參閱個別函式 |
| [圖表處理](/sql/relational-databases/graphs/sql-graph-overview) | 是 |
| [記憶體內部最佳化](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 是 - [僅限進階版](sql-database-in-memory.md) |
| [JSON 資料支援](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | 是 |
| [語言元素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 大部分 - 請參閱個別元素 |  
| [連結的伺服器](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 請參閱[彈性查詢](sql-database-elastic-query-horizontal-partitioning.md) |
| [記錄傳送](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 每個資料庫皆包含高可用性。 [Azure SQL Database 的業務連續性概觀](sql-database-geo-replication-overview.md)會討論災害復原 |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 否 |
| [最低記錄大量匯入](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 否 |
| [修改系統資料](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 否 |
| [線上索引作業](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | 是 |
| [運算子](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 大部分 - 請參閱個別運算子 |
| [資料庫還原時間點](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 是 - 請參閱 [SQL Database 復原](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 否 |
| [原則式管理](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 否 |
| [述詞](https://docs.microsoft.com/sql/t-sql/queries/predicates) | 是 |
| [R 服務](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | 預覽版；請參閱[機器學習服務的新功能](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  |
| [資源管理員](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 否 |
| [RESTORE 陳述式](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 否 | 
| [從備份還原資料庫](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 僅限從內建的備份 - 請參閱[SQL Database 復原](sql-database-recovery-using-backups.md) |
| [資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | 是 |
| [語意搜尋](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 否 |
| [序號](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | 是 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 否 |
| [伺服器組態設定](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 否 |
| [SET 陳述式](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 大部分 - 請參閱個別陳述式 
| [空間](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | 是 |
| [SQL Server 代理程式](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 否 - 請參閱[彈性工作](sql-database-elastic-jobs-getting-started.md) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 請參閱 [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [SQL Server 稽核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 否 - 請參閱 [SQL Database 稽核](sql-database-auditing.md) |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 請參閱 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | 是 |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 否 - 請參閱[擴充事件](sql-database-xevent-db-diff-from-svr.md) |
| [SQL Server 複寫](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [僅限交易和快照複寫訂閱者](sql-database-cloud-migrate.md) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 否 |
| [預存程序](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | 是 |
| [系統預存函式](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 部分 - 請參閱個別函式 |
| [系統預存程序](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 請參閱個別預存程序 |
| [系統資料表](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 請參閱個別資料表 |
| [系統目錄檢視](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 請參閱個別檢視 |
| [資料表分割](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 是 - 僅限主要檔案群組 |
| [暫存資料表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | 僅限本機和資料庫範圍的全域暫存資料表 |
| [暫存資料表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | 是 |
| [變數](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 是 | 
| [透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 是 |
| [Windows Server 容錯移轉叢集](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 每個資料庫皆包含高可用性。 [Azure SQL Database 的業務連續性概觀](sql-database-geo-replication-overview.md)會討論災害復原 |
| [XML 索引](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | 是 |

## <a name="next-steps"></a>後續步驟

- 如需了解 Azure SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database？](sql-database-technical-overview.md)
- 如需 Transact-SQL 支援和差異的詳細資訊，請參閱[解決移轉至 SQL Database 期間的 Transact-SQL 差異](sql-database-transact-sql-information.md)。
