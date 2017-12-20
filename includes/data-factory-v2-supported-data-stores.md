| 類別 | 資料存放區 | 支援作為來源 | 支援作為接收器 | 受 [Azure IR](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) 支援 | 受[自我裝載 IR](../articles/data-factory/concepts-integration-runtime.md#self-hosted-integration-runtime) 支援 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **Azure** |[Azure Blob 儲存體](../articles/data-factory/connector-azure-blob-storage.md) |✓ |✓  |✓  |✓  |
| &nbsp; |[Azure Cosmos DB](../articles/data-factory/connector-azure-cosmos-db.md) |✓ |✓  |✓  |✓   |
| &nbsp; |[Azure Data Lake Store](../articles/data-factory/connector-azure-data-lake-store.md) |✓  |✓  |✓  |✓  |
| &nbsp; |[適用於 MySQL 的 Azure 資料庫](../articles/data-factory/connector-azure-database-for-mysql.md) |✓ | |✓  |✓  |
| &nbsp; |[適用於 PostgreSQL 的 Azure 資料庫](../articles/data-factory/connector-azure-database-for-postgresql.md) |✓ | |✓  |✓  |
| &nbsp; |[Azure 檔案儲存體](../articles/data-factory/connector-azure-file-storage.md) |✓ |✓  |✓  |✓   |
| &nbsp; |[Azure SQL Database](../articles/data-factory/connector-azure-sql-database.md) |✓  |✓  |✓  |✓   |
| &nbsp; |[Azure SQL 資料倉儲](../articles/data-factory/connector-azure-sql-data-warehouse.md) |✓  |✓  |✓  |✓   |
| &nbsp; |[Azure 搜尋服務索引](../articles/data-factory/connector-azure-search.md) | |✓  |✓  |✓  |
| &nbsp; |[Azure 資料表儲存體](../articles/data-factory/connector-azure-table-storage.md) |✓ |✓  |✓  |✓  |
| **資料庫/資料倉儲** |[Amazon Redshift](../articles/data-factory/connector-amazon-redshift.md) |✓  | |✓  |✓  |
| &nbsp; |[DB2](../articles/data-factory/connector-db2.md) |✓ | |✓  |✓  |
| &nbsp; |[Drill 搶鮮版 (Beta)](../articles/data-factory/connector-drill.md) |✓ | |✓  |✓  |
| &nbsp; |[Google BigQuery 搶鮮版 (Beta)](../articles/data-factory/connector-google-bigquery.md) |✓ | |✓  |✓  |
| &nbsp; |[Greenplum 搶鮮版 (Beta)](../articles/data-factory/connector-greenplum.md) |✓ | |✓  |✓  |
| &nbsp; |[HBase](../articles/data-factory/connector-hbase.md) |✓ | |✓  |✓  |
| &nbsp; |[Hive](../articles/data-factory/connector-hive.md) |✓ | |✓  |✓  |
| &nbsp; |[Apache Impala 搶鮮版 (Beta)](../articles/data-factory/connector-impala.md) |✓ | |✓  |✓  |
| &nbsp; |[Informix](../articles/data-factory/connector-odbc.md#ibm-informix-source) |✓ | | |✓  |
| &nbsp; |[MariaDB](../articles/data-factory/connector-mariadb.md) |✓ | |✓  |✓  |
| &nbsp; |[Microsoft Access](../articles/data-factory/connector-odbc.md#microsoft-access-source) |✓ | | |✓  |
| &nbsp; |[MySQL](../articles/data-factory/connector-mysql.md) |✓ | | |✓  |
| &nbsp; |[Oracle](../articles/data-factory/connector-oracle.md) |✓ |✓  |✓  |✓  |
| &nbsp; |[Phoenix](../articles/data-factory/connector-phoenix.md) |✓ | |✓  |✓  |
| &nbsp; |[PostgreSQL](../articles/data-factory/connector-postgresql.md) |✓ | | |✓  |
| &nbsp; |[Presto 搶鮮版 (Beta)](../articles/data-factory/connector-presto.md) |✓ | |✓  |✓  |
| &nbsp; |[SAP Business Warehouse](../articles/data-factory/connector-sap-business-warehouse.md) |✓ | | |✓  |
| &nbsp; |[SAP HANA](../articles/data-factory/connector-sap-hana.md) |✓ |✓  | |✓  |
| &nbsp; |[Spark](../articles/data-factory/connector-spark.md) |✓ | |✓  |✓  |
| &nbsp; |[SQL Server](../articles/data-factory/connector-sql-server.md) |✓ |✓  |✓  |✓  |
| &nbsp; |[Sybase](../articles/data-factory/connector-sybase.md) |✓ | | |✓  |
| &nbsp; |[Teradata](../articles/data-factory/connector-teradata.md) |✓ | | |✓  |
| **NoSQLs** |[Cassandra](../articles/data-factory/connector-cassandra.md) |✓ | |✓  |✓  |
| &nbsp; |[Couchbase 搶鮮版 (Beta)](../articles/data-factory/connector-couchbase.md) |✓ | |✓  |✓  |
| &nbsp; |[MongoDB](../articles/data-factory/connector-mongodb.md) |✓ | |✓  |✓  |
| **檔案** |[Amazon S3](../articles/data-factory/connector-amazon-simple-storage-service.md) |✓  | |✓  |✓  |
| &nbsp; |[檔案系統](../articles/data-factory/connector-file-system.md) |✓ |✓  |✓  |✓   |
| &nbsp; |[FTP](../articles/data-factory/connector-ftp.md) |✓ | |✓  |✓  |
| &nbsp; |[HDFS](../articles/data-factory/connector-hdfs.md) |✓ | |✓  |✓  |
| &nbsp; |[SFTP](../articles/data-factory/connector-sftp.md) |✓ | |✓  |✓  |
| **一般通訊協定** |[一般 HTTP](../articles/data-factory/connector-http.md) |✓ | |✓  |✓  |
| &nbsp; |[泛型 OData](../articles/data-factory/connector-odata.md) |✓ | |✓  |✓  |
| &nbsp; |[一般 ODBC](../articles/data-factory/connector-odbc.md) |✓ |✓  | |✓  |
| **服務和其他項目** |[Amazon Marketplace Web Service 搶鮮版 (Beta)](../articles/data-factory/connector-amazon-marketplace-web-service.md) |✓ | |✓  |✓  |
| &nbsp; |[Concur 搶鮮版 (Beta)](../articles/data-factory/connector-concur.md) |✓ | |✓  |✓  |
| &nbsp; |[Dynamics 365](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓  |✓  |✓  |
| &nbsp; |[Dynamics CRM](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓  |✓  |✓  |
| &nbsp; |[GE Historian](../articles/data-factory/connector-odbc.md#ge-historian-source) |✓ | | |✓  |
| &nbsp; |[HubSpot 搶鮮版 (Beta)](../articles/data-factory/connector-hubspot.md) |✓ | |✓  |✓  |
| &nbsp; |[Jira 搶鮮版 (Beta)](../articles/data-factory/connector-jira.md) |✓ | |✓  |✓  |
| &nbsp; |[Magento 搶鮮版 (Beta)](../articles/data-factory/connector-magento.md) |✓ | |✓  |✓  |
| &nbsp; |[Oracle Eloqua 搶鮮版 (Beta)](../articles/data-factory/connector-oracle-eloqua.md) |✓ | |✓  |✓  |
| &nbsp; |[Paypal 搶鮮版 (Beta)](../articles/data-factory/connector-paypal.md) |✓ | |✓  |✓  |
| &nbsp; |[QuickBooks 搶鮮版 (Beta)](../articles/data-factory/connector-quickbooks.md) |✓ | |✓  |✓  |
| &nbsp; |[Salesforce](../articles/data-factory/connector-salesforce.md) |✓ |✓  |✓  |✓  |
| &nbsp; |[Salesforce 服務雲端](../articles/data-factory/connector-salesforce.md) |✓ |✓  |✓  |✓  |
| &nbsp; |[SAP Cloud for Customer (C4C)](../articles/data-factory/connector-sap-cloud-for-customer.md) |✓ |✓  |✓  |✓  |
| &nbsp; |[ServiceNow 搶鮮版 (Beta)](../articles/data-factory/connector-servicenow.md) |✓ | |✓  |✓  |
| &nbsp; |[Shopify 搶鮮版 (Beta)](../articles/data-factory/connector-shopify.md) |✓ | |✓  |✓  |
| &nbsp; |[Square 搶鮮版 (Beta)](../articles/data-factory/connector-square.md) |✓ | |✓  |✓  |
| &nbsp; |[Web 資料表 (HTML 資料表)](../articles/data-factory/connector-web-table.md) |✓ | |✓  |✓  |
| &nbsp; |[Xero 搶鮮版 (Beta)](../articles/data-factory/connector-xero.md) |✓ | |✓  |✓  |
| &nbsp; |[Zoho 搶鮮版 (Beta)](../articles/data-factory/connector-zoho.md) |✓ | |✓  |✓  |

> [!NOTE]
> 任何標示為「搶鮮版 (Beta)」的連接器都表示您可以試用並提供意見反應給我們，但請勿在生產環境中使用。