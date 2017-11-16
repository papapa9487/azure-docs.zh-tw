# 概觀

## [什麼是 SQL 資料倉儲？](sql-data-warehouse-overview-what-is.md)
## [資料倉儲工作負載](sql-data-warehouse-overview-workload.md)

# 開始使用

## [初學者教學課程](sql-data-warehouse-get-started-tutorial.md)
## [彈性查詢教學課程](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
## [最佳作法](sql-data-warehouse-best-practices.md)
## [管理](sql-data-warehouse-overview-manage.md)

# 作法

## 服務功能
### [MPP 架構](massively-parallel-processing-mpp-architecture.md)
### [效能層級](performance-tiers.md)
### [資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [容量限制](sql-data-warehouse-service-capacity-limits.md)
### [常見問題集](sql-data-warehouse-overview-faq.md)

## 備份與還原

### [備份概觀](sql-data-warehouse-backups.md)
### [還原概觀](sql-data-warehouse-restore-database-overview.md)
#### [Azure 入口網站](sql-data-warehouse-restore-database-portal.md)
#### [Azure PowerShell](sql-data-warehouse-restore-database-powershell.md)
#### [REST](sql-data-warehouse-restore-database-rest-api.md)

## 連線

### [概觀](sql-data-warehouse-connect-overview.md)
### [SSMS](sql-data-warehouse-query-ssms.md)
### [Visual Studio](sql-data-warehouse-query-visual-studio.md)
### [安裝 Visual Studio](sql-data-warehouse-install-visual-studio.md)
### [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
### [連接字串](sql-data-warehouse-connection-strings.md)

## 建立
### [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
### [Azure PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
### [T-SQL](sql-data-warehouse-get-started-create-database-tsql.md)

## 開發

### [概觀](sql-data-warehouse-overview-develop.md)

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

## 整合

### [概觀](sql-data-warehouse-overview-integrate.md)
### [Data Factory](sql-data-warehouse-integrate-azure-data-factory.md)
### [機器學習服務](sql-data-warehouse-integrate-azure-machine-learning.md)
### [機器學習服務教學課程](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI](sql-data-warehouse-integrate-power-bi.md)
### [Power BI 視覺效果](sql-data-warehouse-get-started-visualize-with-power-bi.md)
### [串流分析](sql-data-warehouse-integrate-azure-stream-analytics.md)

### [使用 SQL Database 和 SQL 資料倉儲進行彈性查詢](how-to-use-elastic-query-with-sql-data-warehouse.md)

## 載入

### 概念
#### [概觀](sql-data-warehouse-overview-load.md)
#### [PolyBase 指導](sql-data-warehouse-load-polybase-guide.md)

### 教學課程
#### [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)

### 使用說明指南
#### [範例資料](sql-data-warehouse-load-sample-databases.md)
#### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
#### [BCP](sql-data-warehouse-load-with-bcp.md)
#### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
#### [來自 Blob 儲存體的 PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
#### [從 SQL Server 載入 PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
#### [RedGate](sql-data-warehouse-load-with-redgate.md)
#### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)

## 移轉

### [概觀](sql-data-warehouse-overview-migrate.md)
### [移轉公用程式](sql-data-warehouse-migrate-migration-utility.md)
### [移轉結構描述](sql-data-warehouse-migrate-schema.md)
### [移轉程式碼](sql-data-warehouse-migrate-code.md)
### [移轉資料](sql-data-warehouse-migrate-data.md)
### [移轉至進階儲存體](sql-data-warehouse-migrate-to-premium-storage.md)

## 管理計算

### [概觀](sql-data-warehouse-manage-compute-overview.md)
### [Azure 入口網站](sql-data-warehouse-manage-compute-portal.md)
### [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [REST API](sql-data-warehouse-manage-compute-rest-api.md)
### [T-SQL](sql-data-warehouse-manage-compute-tsql.md)
### [自動化計算層級](manage-compute-with-azure-functions.md)

### 資源類別
#### [指導方針](resource-classes-for-workload-management.md)
#### [分析工作負載](analyze-your-workload.md)

## 效能

### [概觀](sql-data-warehouse-overview-manage-user-queries.md)
### [資料行存放區壓縮](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [監視](sql-data-warehouse-manage-monitor.md)

## 安全性

### [概觀](sql-data-warehouse-overview-manage-security.md)
### [稽核](sql-data-warehouse-auditing-overview.md)
### [稽核下層用戶端](sql-data-warehouse-auditing-downlevel-clients.md)
### [驗證](sql-data-warehouse-authentication.md)
### [加密](sql-data-warehouse-encryption-tde.md)
### [使用 T-SQL 加密](sql-data-warehouse-encryption-tde-tsql.md)
### [威脅偵測](sql-data-warehouse-security-threat-detection.md)

## 疑難排解
### [疑難排解](sql-data-warehouse-troubleshoot.md)

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
