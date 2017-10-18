# 概觀
## [何謂 Azure 搜尋服務？](search-what-is-azure-search.md)
# 開始使用
## [選擇 SKU](search-sku-tier.md)
## [建立服務](search-create-service-portal.md)
## [建立索引](search-what-is-an-index.md)
### [Azure 入口網站](search-create-index-portal.md)
### [.NET](search-create-index-dotnet.md)
### [REST](search-create-index-rest-api.md)
## [增資料](search-what-is-data-import.md)
### [Azure 入口網站](search-import-data-portal.md)
### [.NET](search-import-data-dotnet.md)
### [REST](search-import-data-rest-api.md)
## [搜尋索引](search-query-overview.md)
### [Azure 入口網站](search-explorer.md)
### [.NET](search-query-dotnet.md)
### [REST](search-query-rest-api.md)
# 教學課程
## [.NET](search-howto-dotnet-sdk.md)
## [.NET 同義字預覽](search-synonyms-tutorial-sdk.md)
## [入口網站](search-get-started-portal.md)
## [Node.js](search-get-started-nodejs.md)
## [Java](search-get-started-java.md)
# 作法
## 規劃和設計
### [服務限制](search-limits-quotas-capacity.md)
### [服務延展性](search-capacity-planning.md)
### [多組織用戶的樣式](search-modeling-multitenant-saas-applications.md)

## 開發
### [API 版本](search-api-versions.md)
### [升級 SDK](search-dotnet-sdk-migration.md)
### [升級 REST API](search-api-migration.md)
### [模型的複雜資料類型](search-howto-complex-data-types.md)
### [處理並行更新](search-howto-concurrency.md)
### [程式碼範例](https://azure.microsoft.com/resources/samples/?service=search)

## 管理
### 管理 Azure 搜尋服務
#### [Azure 入口網站](search-manage.md)
#### Azure [Azure PowerShell](search-manage-powershell.md)
### [監視使用量和統計資料](search-monitor-usage.md)
### [搜尋流量分析](search-traffic-analytics.md)
### [效能與最佳化](search-performance-optimization.md)
## 載入資料
### [索引子概觀](search-indexer-overview.md)
### [Azure Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)
### [Azure 資料表儲存體索引子](search-howto-indexing-azure-tables.md)
### [Azure SQL 索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
### [Azure Cosmos DB 索引子](search-howto-index-documentdb.md)
### [索引 CSV blob](search-howto-index-csv-blobs.md)
### [索引 JSON blob](search-howto-index-json-blobs.md)
### [在 Azure VM 上設定連接至 SQL Server 的索引子](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
### [索引器中的欄位對應](search-indexer-field-mappings.md)
##  搜尋
### [全文檢索搜尋如何運作](search-lucene-query-architecture.md)
### 查詢建構
#### [簡單查詢語法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
#### [Lucene 查詢語法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
#### [Lucene 語法查詢範例](search-query-lucene-examples.md)
#### [篩選運算式語法](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
### Azure 搜尋服務中的分析器
#### [概觀](search-analyzers.md)
#### [語言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)
#### [自訂分析器](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)
### [分頁結果](search-pagination-page-layout.md)
### [評分](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
### [建議](https://docs.microsoft.com/rest/api/searchservice/suggesters)
### [多面向導覽](search-faceted-navigation.md)
### [同義字預覽](search-synonyms.md)
### [安全性篩選](search-security-trimming-for-azure-search.md)
# 參考

## [.NET](/dotnet/api/?term=microsoft.azure.search)
## [.NET (管理)](/dotnet/api/?term=microsoft.azure.management.search)
## [Python (管理)](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.search.html)
## [REST](/rest/api/searchservice)
## [REST (管理)](/rest/api/searchmanagement)
## [服務 REST (預覽)](search-api-2015-02-28-preview.md)

# 資源

## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=web-mobile)
## [FAQ - 常見問題](search-faq-frequently-asked-questions.md)
## [價格](https://azure.microsoft.com/pricing/details/search/)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [服務更新](https://azure.microsoft.com/updates/?product=search)
## 課程工具與教學課程
### [影片和教學課程](search-video-demo-tutorial-list.md)
### [Virtual academy](https://mva.microsoft.com/training-courses/using-windows-azure-search-10540?l=ADkxnd97_9304984382)
## 示範網站
### [搜尋分析器示範](http://alice.unearth.ai/)
### [即時示範應用程式](https://searchsamples.azurewebsites.net/)
### [作業列表應用程式](http://aka.ms/azjobsdemo)
## 合作夥伴與社群
### [Azure 搜尋服務 Github](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)
### [MSDN 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureSearch)
### [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-search)
### [部落格：模型關聯式資料](http://blogs.technet.com/b/onsearch/archive/2015/09/08/modeling-the-adventureworks-inventory-database-for-azure-search.aspx)
### [部落格：多層級 Facet](http://blogs.technet.com/b/onsearch/archive/2015/09/09/multi-level-taxonomy-facets-in-azure-search.aspx)



