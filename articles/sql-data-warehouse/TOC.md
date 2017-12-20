# 概觀

## [關於 Azure SQL 資料倉儲](sql-data-warehouse-overview-what-is.md)

# 快速入門

## [建立與連線 - 入口網站](create-data-warehouse-portal.md)

# 教學課程
## [1 - 從 Blob 載入資料](load-data-from-azure-blob-storage-using-polybase.md)

# 概念
## 服務功能
### [MPP 架構](massively-parallel-processing-mpp-architecture.md)
### [效能層級](performance-tiers.md)
### [資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [資料倉儲備份](sql-data-warehouse-backups.md)
### [稽核](sql-data-warehouse-auditing-overview.md)
### [容量限制](sql-data-warehouse-service-capacity-limits.md)
### [常見問題集](sql-data-warehouse-overview-faq.md)

## 安全性
### [概觀](sql-data-warehouse-overview-manage-security.md)
### [驗證](sql-data-warehouse-authentication.md)


## 移轉至 SQL 資料倉儲
### [概觀](sql-data-warehouse-overview-migrate.md)
### [移轉結構描述](sql-data-warehouse-migrate-schema.md)
### [移轉程式碼](sql-data-warehouse-migrate-code.md)
### [移轉資料](sql-data-warehouse-migrate-data.md)

## 載入與移動資料
### [概觀](design-elt-data-loading.md)
### [最佳作法](guidance-for-loading-data.md)


## 整合
### [概觀](sql-data-warehouse-overview-integrate.md)
### [SQL Database 彈性查詢](how-to-use-elastic-query-with-sql-data-warehouse.md)


## 監視與微調
### [指導方針](resource-classes-for-workload-management.md)
### [資料行存放區壓縮](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [監視](sql-data-warehouse-manage-monitor.md)
### [疑難排解](sql-data-warehouse-troubleshoot.md)

## 開發資料倉儲
### [概觀](sql-data-warehouse-overview-develop.md)
### [資料倉儲元件](sql-data-warehouse-overview-workload.md)

### 資料表
#### [概觀](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [資料類型](sql-data-warehouse-tables-data-types.md)
#### [分散式資料表](sql-data-warehouse-tables-distribute.md)
#### [索引數](sql-data-warehouse-tables-index.md)
#### [身分識別](sql-data-warehouse-tables-identity.md)
#### [分割數](sql-data-warehouse-tables-partition.md)
#### [複寫的資料表](design-guidance-for-replicated-tables.md)
#### [統計資料](sql-data-warehouse-tables-statistics.md)
#### [暫存](sql-data-warehouse-tables-temporary.md)

### 查詢
#### [動態 SQL](sql-data-warehouse-develop-dynamic-sql.md)
#### [依據選項分組](sql-data-warehouse-develop-group-by-options.md)
#### [標籤](sql-data-warehouse-develop-label.md)

### T-SQL 語言元素
#### [迴圈](sql-data-warehouse-develop-loops.md)
#### [預存程序](sql-data-warehouse-develop-stored-procedures.md)
#### [交易](sql-data-warehouse-develop-transactions.md)
#### [交易的最佳做法](sql-data-warehouse-develop-best-practices-transactions.md)
#### [使用者定義的結構描述](sql-data-warehouse-develop-user-defined-schemas.md)
#### [變數指派](sql-data-warehouse-develop-variable-assignment.md)
#### [檢視](sql-data-warehouse-develop-views.md)

# 使用說明指南
## 服務功能
### [還原資料倉儲 - 入口網站](sql-data-warehouse-restore-database-portal.md)
### [還原資料倉儲 - PowerShell](sql-data-warehouse-restore-database-powershell.md)
### [還原資料倉儲 - REST API](sql-data-warehouse-restore-database-rest-api.md)

## 安全性
### [啟用加密 (入口網站)](sql-data-warehouse-encryption-tde.md)
### [啟用加密 - T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [威脅偵測](sql-data-warehouse-security-threat-detection.md)


## 載入與移動資料
### [AdventureWorks](sql-data-warehouse-load-sample-databases.md)
### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [來自 Blob 儲存體的 PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [從 SQL Server 載入 PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)


## 整合
### [設定 SQL Database 彈性查詢](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
### [新增 Azure 串流分析作業](sql-data-warehouse-integrate-azure-stream-analytics.md)
### [使用機器學習服務](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [使用 Power BI 視覺化資料](sql-data-warehouse-get-started-visualize-with-power-bi.md)

## 監視與微調
### [分析工作負載](analyze-your-workload.md)

## 相應放大
### [管理計算 - 入口網站](sql-data-warehouse-manage-compute-portal.md)
### [管理計算 - PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [管理計算 - REST API](sql-data-warehouse-manage-compute-rest-api.md)
### [自動化計算層級](manage-compute-with-azure-functions.md)


# 參考


## T-SQL
### [完整參考](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [SQL DW 語言元素](sql-data-warehouse-reference-tsql-language-elements.md)
### [SQL DW 陳述式](sql-data-warehouse-reference-tsql-statements.md)
## [系統檢視表](sql-data-warehouse-reference-tsql-system-views.md)
## [PowerShell Cmdlet](sql-data-warehouse-reference-powershell-cmdlets.md)

# 資源
## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=databases)
## [論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [價格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [功能要求](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [服務更新](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [支援](sql-data-warehouse-get-started-create-support-ticket.md)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## 合作夥伴
### [商業智慧](sql-data-warehouse-partner-business-intelligence.md)
### [資料整合](sql-data-warehouse-partner-data-integration.md)
### [資料管理](sql-data-warehouse-partner-data-management.md)
