---
title: "Azure Cosmos DB 常見問題集 | Microsoft Docs"
description: "取得關於 Azure Cosmos DB (一種全域散發的多模型資料庫服務) 之常見問題集的解答。 了解產能、效能層級和調整。"
keywords: "資料庫問題, 常見問題集, Database questions, frequently asked questions, documentdb, azure, Microsoft azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: e9808af4aa875c5199279825325688afc69e6de6
ms.contentlocale: zh-tw
ms.lasthandoff: 08/11/2017

---
# <a name="azure-cosmos-db-faq"></a>Azure Cosmos DB 常見問題集
## <a name="azure-cosmos-db-fundamentals"></a>Azure Cosmos DB 基本概念
### <a name="what-is-azure-cosmos-db"></a>什麼是 Azure Cosmos DB？
Azure Cosmos DB 是一種可進行全域複寫的多模型資料庫服務，可在無結構描述的資料上進行豐富的查詢、協助提供可設定且可靠的效能，並支援快速開發。 這些完全都是透過受管理的平台，背後有 Microsoft Azure 強大的功能與先進的技術做後盾。 

如果關鍵需求在於可預測的輸送量、高可用性、低延遲性和無結構描述的資料模型，則 Azure Cosmos DB 就是最適合 Web、行動裝置、遊戲和 IoT 應用程式的解決方案。 它能提供結構描述的彈性和豐富的編製索引能力，並利用整合式 JavaScript 來包含多文件交易式支援。 

如需更多資料庫問題、解答，以及部署和使用此服務的指示，請參閱 [Azure Cosmos DB 文件頁面](https://azure.microsoft.com/documentation/services/cosmos-db/)。

### <a name="what-happened-to-documentdb"></a>DocumentDB 有何改變？
DocumentDB API 是適用於 Azure Cosmos DB 的其中一個支援 API 和資料模型。 此外，Azure Cosmos DB 支援使用圖形 API (預覽)、資料表 API (預覽) 和 MongoDB API。 如需詳細資訊，請參閱 [DocumentDB 客戶的問題](#moving-to-cosmos-db)。

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>如何在 Azure 入口網站中取得我的 DocumentDB 帳戶？
在 Azure 入口網站中，按一下左窗格的 [Azure Cosmos DB] 圖示。 如果您之前已有 DocumentDB 帳戶，現在就能擁有 Azure Cosmos DB 帳戶，且不會向您收取任何費用。

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB 有哪些一般使用案例？
對於新的 Web、行動裝置、遊戲和 IoT 應用程式，若其中自動調整規模、可預測的效能、毫秒回應時間的快速排序，以及查詢無結構描述之資料的能力都很重要，則 Azure Cosmos DB 是個不錯的選擇。 Azure Cosmos DB 適合用來進行快速開發，且支援應用程式資料模型的持續反覆運算。 管理使用者產生之內容和資料的應用程式，就是 [Azure Cosmos DB 的常見使用案例](use-cases.md)。 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB 如何提供可預測的效能？
[要求單位](request-units.md) (RU) 是 Azure Cosmos DB 的輸送量計算單位。 1 RU 的輸送量會對應至 1KB 文件的 GET 輸送量。 根據完成作業所需的輸送量，Azure Cosmos DB 中的每個作業 (包括讀取、寫入、SQL 查詢和預存程序執行) 具有決定性的 RU 值。 您可以就單一 RU 計量來思考，而不是思考 CPU、IO 和記憶體以及它們分別如何影響您的應用程式輸送量。

您可以按照每秒輸送量 RU 的佈建輸送量保留每個 Azure Cosmos DB 容器。 對於任何規模的應用程式，您可以將個別要求設為基準以測量其 RU 值，以及佈建容器來處理所有要求的要求單位總數。 您也可以隨著應用程式發展需求，相應增加或減少容器的輸送量。 如需有關要求單位的詳細資訊，以及幫助您判斷容器需求的協助，請參閱[估計輸送量需求](request-units.md#estimating-throughput-needs)並嘗試使用[輸送量計算機 (英文)](https://www.documentdb.com/capacityplanner)。 這裡的「容器」一詞指的是 DocumentDB API 集合、圖形 API 圖形、MongoDB API 集合及資料表 API 資料表。 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB 如何支援各種資料模型，例如索引鍵/值、單欄式資料、文件和圖形？

索引鍵/值、單欄式資料、文件和圖形都是原生支援的模型，因為 Azure Cosmos DB 內建 ARS (原子、記錄、序列) 設計。 原子、記錄、序列可以輕易地對應並且投射至各種資料模型。 目前已有適用於各種模型子集合的 API (DocumentDB、MongoDB、資料表、圖形 API)，未來會有更多用於其他特定資料模型的 API。

Azure Cosmos DB 不需要結構描述的索引引擎能夠自動建立所內嵌之資料的索引，且不需要查詢任何結構描述或開發者的次要索引。 引擎依賴一組邏輯索引配置 (反向式、單欄式、樹狀目錄)，可將儲存配從索引和查詢的處理子系統分離。 Cosmos DB 也可以擴充支援一些有線網路通訊協定和 API，並將它們有效地轉譯成核心資料模型 (1) 和邏輯索引配置，(2) 造就其原生支援多個資料模型的獨特能力。

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB 符合 HIPAA 規範嗎？
是，Azure Cosmos DB 符合 HIPAA 規範。 HIPAA 建立了使用、洩漏與保護個別辨識健全狀況資訊的需求。 如需詳細資訊，請參閱 [Microsoft 信任中心](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)。

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的儲存體限制有哪些？
對於容器可以儲存在 Azure Cosmos DB 中的資料總量，沒有任何限制。

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的輸送量限制有哪些？
對於容器可以在 Azure Cosmos DB 中支援的總輸送量，沒有任何限制。 其主要概念在於，將您的工作負載大致平均分配在數量夠大的資料分割索引鍵之間。

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB 的費用是多少？
如需詳細資料，請參閱 [Azure Cosmos DB 定價詳細資料](https://azure.microsoft.com/pricing/details/cosmos-db/)頁面。 Azure Cosmos DB 使用量費用取決於已佈建的容器數目、容器在線上的時數，以及每個容器的佈建輸送量。 這裡的「容器」一詞指的是 DocumentDB API 集合、圖形 API 圖形、MongoDB API 集合及資料表 API 資料表。 

### <a name="is-a-free-account-available"></a>有免費的帳戶嗎？
如果您不熟悉 Azure，可以註冊 [Azure 免費帳戶](https://azure.microsoft.com/free/)，就能得到 30 天免費試用以及信用額度，讓您試用所有 Azure 服務。 如果您有 Visual Studio 訂用帳戶，也享有[免費的 Azure 信用額度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，可用於所有 Azure 服務。 

您也可以免費使用 [Azure Cosmos DB 模擬器](local-emulator.md)在本機開發及測試應用程式，而不需建立 Azure 訂用帳戶。 如果您滿意應用程式在 Azure Cosmos DB 模擬器中的運作方式，就可以切換成使用雲端的 Azure Cosmos DB 帳戶。

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>如何取得 Azure Cosmos DB 的其他協助？
如果您需要任何協助，請透過 [Stack Overflow (英文)](http://stackoverflow.com/questions/tagged/azure-cosmosdb) 或 [MSDN 論壇](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)聯繫我們，或是傳送郵件給 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) 來安排與 Azure Cosmos DB 工程小組的一對一談話。 

## <a name="set-up-azure-cosmos-db"></a>設定 Azure Cosmos DB
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>如何註冊 Azure Cosmos DB？
Azure 入口網站中已提供 Azure Cosmos DB。 首先，請註冊 Azure 訂用帳戶。 註冊後，您就可以將 DocumentDB API、圖形 API (預覽)、資料表 API (預覽) 或 MongoDB API 帳戶加入您的 Azure 訂用帳戶。

### <a name="what-is-a-master-key"></a>什麼是主要金鑰？
主要金鑰是可存取帳戶的所有資源的安全性權杖。 擁有此金鑰的人，對資料庫帳戶中的所有資源具有讀取和寫入存取權。 分配主要金鑰時，務必謹慎。 [Azure 入口網站][azure-portal]的 [金鑰] 刀鋒視窗提供主要金鑰和次要金鑰。 如需金鑰的詳細資訊，請參閱 [檢視、複製和重新產生存取金鑰](manage-account.md#keys)。

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>設定 PreferredLocations 的目標區域有哪些？ 
PreferredLocations 值可以設定為任何提供 Cosmos DB 的 Azure 區域。 如需可用區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>當我透過 Azure 的資料中心在世界各地散發資料時，應注意哪些事項？ 
所有的 Azure 區域均提供 Azure Cosmos DB ，如同 [Azure 區域](https://azure.microsoft.com/regions/)頁面所指出。 由於它是核心服務，因此每個新資料中心都會有一個 Azure Cosmos DB。 

設定區域時，請記住 Azure Cosmos DB 涉及主權和政府雲端。 也就是如果您在主權區域中建立帳戶，便無法覆寫到該主權區域之外的位置。 同樣地，您也無法從外部帳戶覆寫到其他主權位置。 

## <a name="develop-against-the-documentdb-api"></a>針對 DocumentDB API 進行開發

### <a name="how-do-i-start-developing-against-the-documentdb-api"></a>如何開始針對 DocumentDB API 進行開發？
[Azure 入口網站][azure-portal]中已提供 Microsoft DocumentDB API。 首先，您必須註冊 Azure 訂用帳戶。 註冊 Azure 訂用帳戶之後，您可以將 DocumentDB API 容器加入您的 Azure 訂用帳戶。 如需加入 Azure Cosmos DB 帳戶的相關指示，請參閱[建立 Azure Cosmos DB 資料庫帳戶](create-documentdb-dotnet.md#create-account)。 如果您過去已有 DocumentDB 帳戶，現在就會擁有 Azure Cosmos DB 帳戶。 

[SDK](documentdb-sdk-dotnet.md) 適用於 .NET、Python、Node.js、JavaScript 和 Java。 開發人員也可以使用 [RESTful HTTP API](/rest/api/documentdb/)，經由各種平台和語言來與 Azure Cosmos DB 資源互動。

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>我可以存取一些現有的範例作為有利的開端嗎？
GitHub 上提供 DocumentDB API [.NET](documentdb-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](documentdb-nodejs-samples.md) 和 [Python](documentdb-python-samples.md) SDK 的範例。


### <a name="does-the-documentdb-api-database-support-schema-free-data"></a>DocumentDB API 資料庫支援無結構描述的資料嗎？
是，DocumentDB API 可讓應用程式儲存任意的 JSON 文件，而不需要結構描述定義或提示。 透過 Azure Cosmos DB SQL 查詢介面，就可立即查詢資料。  

### <a name="does-the-documentdb-api-support-acid-transactions"></a>DocumentDB API 支援 ACID 交易嗎？
是，DocumentDB API 支援以 JavaScript 預存程序和觸發程序表達的跨文件交易。 交易以每個集合的單一分割為範圍，且以 ACID 語意執行，也就是「全有或全無」，與其他並行執行的程式碼和使用者要求隔離。 如果伺服器端執行 JavaScript 應用程式碼期間擲回例外狀況，則會回復整個交易。 如需交易的詳細資訊，請參閱 [資料庫程式交易](programming.md#database-program-transactions)。

### <a name="what-is-a-collection"></a>什麼是集合？
集合是一組文件及其相關聯的 JavaScript 應用程式邏輯。 集合是計費實體，其[成本](performance-levels.md)是由使用的輸送量和儲存體所決定。 集合可以跨越一或多個資料分割或伺服器，也可以進行調整以處理幾乎無限量的儲存體或輸送量。

集合也是 Azure Cosmos DB 的帳務實體。 每個集合根據佈建的輸送量和使用的儲存體空間，以小時為單位計費。 如需詳細資訊，請參閱 [Azure Cosmos DB 價格](https://azure.microsoft.com/pricing/details/cosmos-db/)。 

### <a name="how-do-i-create-a-database"></a>我如何建立資料庫？
您可以使用 [Azure 入口網站](https://portal.azure.com) (如[新增集合](create-documentdb-dotnet.md#create-collection)所述)、其中一個 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 或 [REST API](/rest/api/documentdb/) 來建立資料庫。 

### <a name="how-do-i-set-up-users-and-permissions"></a>我如何設定使用者和權限？
您可以使用其中一個 [Cosmos DB API SDK](documentdb-sdk-dotnet.md) 或 [REST API](/rest/api/documentdb/) 來建立使用者和權限。  

### <a name="does-the-documentdb-api-support-sql"></a>DocumentDB API 支援 SQL 嗎？
SQL 查詢語言是 SQL 支援的查詢功能增強子集。 Azure Cosmos DB 的 SQL 查詢語言提供豐富的階層式和關聯式運算子，且透過 JavaScript 型使用者定義函式 (UDF) 支援擴充性。 JSON 文法允許將 JSON 文件模型化為節點加上標籤的樹狀結構，這是由 Azure Cosmos DB 的自動編製索引技術及 Azure Cosmos DB 的 SQL 查詢方言所使用。 如需使用 SQL 文法的詳細資訊，請參閱 [QueryDocumentDB][query] 一文。

### <a name="does-the-documentdb-api-support-sql-aggregation-functions"></a>DocumentDB API 支援 SQL 彙總函式嗎？
DocumentDB API 透過 SQL 文法，藉由彙總函式 `COUNT`、`MIN`、`MAX`、`AVG` 和 `SUM` 來支援任何規模的低延遲彙總。 如需詳細資訊，請參閱[彙總函式](documentdb-sql-query.md#Aggregates)。

### <a name="how-does-the-documentdb-api-provide-concurrency"></a>DocumentDB API 如何提供並行存取？
DocumentDB API 透過 HTTP 實體標記或 ETag，支援開放式並行存取控制 (OCC)。 每個 DocumentDB API 資源都有一個 ETag，並且會在每次更新文件時，於伺服器上設定此 ETag。 ETag 標頭和目前的值會包含於所有回應訊息中。 Etag 可與 If-Match 標頭搭配使用，讓伺服器能夠決定是否應該更新資源。 If-Match 值是要經過檢查的 ETag 值。 如果 ETag 值符合伺服器的 ETag 值，就會更新資源。 如果 ETag 不再是最新狀態，則伺服器會拒絕該作業，並提供「HTTP 412 前置條件失敗」回應碼。 用戶端接著會重新擷取資源，以取得該資源目前的 ETag 值。 此外，ETag 可以與 If-None-Match 標頭搭配使用，來判斷是否需要重新擷取資源。

若要在 .NET 中使用開放式並行存取，可使用 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) 類別。 如需 .NET 範例，請參閱 GitHub 上 DocumentManagement 範例中的 [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs)。

### <a name="how-do-i-perform-transactions-in-the-documentdb-api"></a>如何在 DocumentDB API 中執行交易？
DocumentDB API 透過 JavaScript 預存程序和觸發程序，支援語言整合式交易。 指令碼內的所有資料庫作業都會在快照隔離的情況下執行。 如果是單一資料分割集合，執行範圍將限制為該集合。 如果集合已經過資料分割，執行範圍將限制為集合內具有相同資料分割索引鍵值的文件。 文件版本 (ETag) 的快照是在交易開始時取得，且只有當指令碼成功執行時才會認可。 如果 JavaScript 擲回錯誤，則會回復交易。 如需詳細資訊，請參閱 [Azure Cosmos DB 的伺服器端 JavaScript 程式設計](programming.md)。

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>如何將大量文件插入 Cosmos DB？
您可以利用任一種方式將文件大量插入 Azure Cosmos DB：

* 資料移轉工具，如 [Azure Cosmos DB 的資料庫移轉工具](import-data.md)中所述。
* 預存程序，如 [Azure Cosmos DB 的伺服器端 JavaScript 程式設計](programming.md)中所述。

### <a name="does-the-documentdb-api-support-resource-link-caching"></a>DocumentDB API 支援資源連結快取嗎？
是，因為 Azure Cosmos DB 是一項 RESTful 服務，資源連結是固定不變且可快取的。 DocumentDB API 用戶端可以指定「If-None-Match」標頭來讀取任何類似資源的文件或集合，然後在伺服器版本變更後更新其本機複本。

### <a name="is-a-local-instance-of-documentdb-api-available"></a>DocumentDB API 的本機執行個體可供使用嗎？
是。 [Azure Cosmos DB 模擬器](local-emulator.md)提供 Cosmos DB 服務的高逼真度模擬。 它支援與 Azure Cosmos DB 完全相同的功能，包括支援建立和查詢 JSON 文件、佈建和擴充集合，以及執行預存程序和觸發程序。 您可以使用 Azure Cosmos DB 模擬器來開發及測試應用程式，並且只需對 Azure Cosmos DB 的連接端點進行單一組態變更，就能將它們部署至全球規模的 Azure。

## <a name="develop-against-the-api-for-mongodb"></a>針對 MongoDB 開發 API
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>什麼是適用於 MongoDB 的 Azure Cosmos DB API？
適用於 MongoDB 的 Azure Cosmos DB API 是一個相容性層級，可讓應用程式使用社群支援的現有 Apache MongoDB API 和驅動程式，輕鬆且透明地與原生 Azure Cosmos DB 資料庫引擎通訊。 開發人員現在可以使用現有的 MongoDB 工具鏈結和技術，建置充分利用 Azure Cosmos DB 的應用程式。 開發人員能從 Azure Cosmos DB 的獨特功能受益，包括自動編製索引、備份維護、有金錢補償的服務等級協定 (SLA) 等。

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>如何連線我的 API for MongoDB 資料庫？
直接前往 [Azure 入口網站](https://portal.azure.com)，是連接適用於 MongoDB 之 Azure Cosmos DB API 最快的方式。 移至您的帳戶，然後在左側瀏覽功能表中按一下 [快速入門]。 [快速入門] 是取得程式碼片段以連接資料庫的最佳方式。 

Azure Cosmos DB 會強制執行嚴格的安全性需求和標準。 Azure Cosmos DB 帳戶需要驗證和透過 SSL 的安全通訊，因此請務必使用 TLSv1.2。

如需詳細資訊，請參閱[連線到 API for MongoDB 資料庫](connect-mongodb-account.md)。

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>API for MongoDB 資料庫是否有其他錯誤碼？
除了常見的 MongoDB 錯誤碼，MongoDB API 有自己專用的錯誤碼：


| 錯誤               | 代碼  | 說明  | 方案  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 取用的要求單位總數已超過針對集合佈建的要求單位率並已進行節流。 | 請考慮從 Azure 入口網站調整集合的輸送量或重試一次。 |
| ExceededMemoryLimit | 16501 | 做為多租用戶服務，作業已超出用戶端的記憶體配額。 | 透過更嚴格的查詢準則來縮小作業的範圍，或經由 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)連絡支援人員。 <br><br>範例：*&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api-preview"></a>利用資料表 API (預覽) 進行開發

### <a name="terms"></a>詞彙 
Azure Cosmos DB：資料表 API (預覽) 是指 Azure Cosmos DB 針對在組建 2017 中宣告之資料表支援所提供的進階供應項目。 

標準資料表 SDK 是現有的 Azure 儲存體資料表 SDK。 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>如何使用新的資料表 API (預覽) 供應項目？ 
[Azure 入口網站][azure-portal]中已提供 Azure Cosmos DB 資料表 API。 首先，您必須註冊 Azure 訂用帳戶。 註冊之後，您可以將 Azure Cosmos DB 資料表 API 帳戶加入 Azure 訂用帳戶，接著再將資料表加入帳戶。 

在預覽期間，[SDK](../cosmos-db/table-sdk-dotnet.md) 適用於 .NET，您可以從完成[資料表 API](../cosmos-db/create-table-dotnet.md) 快速入門文章來開始。

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>我需要新的 SDK，才能使用資料表 API (預覽) 嗎？ 
是，[Windows Azure 儲存體進階資料表 (預覽) SDK](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable) 可在 NuGet 上取得。 其他資訊位於 [Azure Cosmos DB 資料表 .NET API：下載和版本資訊](https://github.com/Microsoft/azure-docs-pr/cosmos-db/table-sdk-dotnet.md)頁面上。 

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>如何提供關於 SDK 和 Bug 的意見反應？
您可以使用以下任何方式來分享意見反應：

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [MSDN 論壇](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>我需要哪一個連接字串，才能連接資料表 API (預覽)？
連接字串為：
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com
```
您可以從 Azure 入口網站的 [金鑰] 頁面取得連接字串。 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-api-preview"></a>如何在新的資料表 API (預覽) 中覆寫要求選項的組態設定？
如需組態設定的相關資訊，請參閱 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。 您可以在用戶端應用程式內將設定加入 app.config 中的 appsettings 區段，藉以變更它們。

    <appSettings>
        <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
        <add key="TableThroughput" value="<PositiveIntegerValue"/>
        <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
        <add key="TableUseGatewayMode" value="True|False"/>
        <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
    </appSettings>


### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-standard-table-sdk"></a>使用現有標準資料表 SDK 的客戶需要改變什麼嗎？
無。 使用現有標準資料表 SDK 的現有或新客戶不需要改變任何項目。 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>如何檢視儲存在 Azure Cosmos DB 中的資料表 資料，以便與資料表 API (預覽) 搭配使用？ 
您可以使用 Azure 入口網站來瀏覽資料。 您也可以使用資料表 API (預覽) 程式碼或下一個回答所述的工具。 

### <a name="which-tools-work-with-the-table-api-preview"></a>哪些工具可與資料表 API (預覽) 搭配使用？ 
您可以使用舊版的 Azure 檔案總管 (0.8.9)。

能夠彈性地以先前指定之格式採用連接字串的工具，均可支援新的資料表 API (預覽)。 [Azure 儲存體用戶端工具](../storage/storage-explorers.md)頁面上提供一份資料表工具清單。 

### <a name="do-powershell-or-azure-cli-work-with-the-new-table-api-preview"></a>PowerShell 或 Azure CLI 可以與新的資料表 API (預覽) 搭配運作嗎？
我們計劃針對資料表 API (預覽) 加入對 PowerShell 或 Azure CLI 的支援。 

### <a name="is-the-concurrency-on-operations-controlled"></a>可以在作業中控制並行存取嗎？
是，開放式並行存取會透過使用 Etag 機制來提供。 

### <a name="is-the-odata-query-model-supported-for-entities"></a>是否針對實體支援 OData 查詢模型？ 
是，資料表 API (預覽) 支援 OData 查詢和 LINQ 查詢。 

### <a name="can-i-connect-to-the-standard-azure-table-and-the-new-premium-table-api-preview-side-by-side-in-the-same-application"></a>我可以在同一個應用程式中並行連接標準 Azure 資料表和新的進階資料表 API (預覽) 嗎？ 
是，您可以建立兩個不同的 CloudTableClient 執行個體，並透過連接字串讓它們分別指向自己的 URI，藉此進行連線。

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-new-offering"></a>如何將現有的 Azure 資料表儲存體應用程式移轉到這個新的供應項目？
若要在現有的資料表儲存體資料上利用新的資料表 API 供應項目，請連絡 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)。 

### <a name="what-is-the-roadmap-for-this-service-and-when-will-you-offer-other-standard-table-api-functionality"></a>這項服務的藍圖是什麼，你們何時能提供其他標準資料表 API 功能？
當我們繼續朝向公開上市邁進時，計劃要加入 SAS 權杖、ServiceContext、統計資料、用戶端加密、分析和其他功能的支援。 您可以在 [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api) (英文) 上提供意見反應給我們。 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>假設我一開始就有 *n* GB 的資料，而我的資料將隨著時間過去成長為 1 TB，我該如何針對此服務完成儲存體大小的擴充？ 
Azure Cosmos DB 的設計乃藉由使用水平調整提供無限制的儲存體。 這項服務能監視並有效地增加您的儲存體。 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>如何監視資料表 API (預覽) 供應項目？
您可以使用資料表 API (預覽) 的 [計量] 窗格，來監視要求和儲存體使用量。 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>如何計算我需要的輸送量？
您可以使用的容量估計工具來計算作業所需的 TableThroughput。 如需詳細資訊，請參閱[估算要求單位和資料儲存體 (英文)](https://www.documentdb.com/capacityplanner)。 通常，您可以將實體表示為 JSON，並為您的作業提供編號。 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>我可以在本機搭配使用新的資料表 API (預覽) SDK 與模擬器嗎？
是，當您使用新的 SDK 時，可以搭配使用資料表 API (預覽) 和本機模擬器。 若要下載新模擬器，請前往[使用 Azure Cosmos DB 模擬器進行本機開發和測試](local-emulator.md)。 app.config 中的 StorageConnectionString 值必須是：

```
DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081`. 
```

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>我現有的應用程式可以與資料表 API (預覽) 搭配運作嗎？ 
新資料表 API (預覽) 的介面區可以在進行 .NET API 的建立、刪除、更新、查詢作業中，與現有的 Azure 標準資料表 SDK 相容。 請確認您具有資料列索引鍵，因為資料表 API (預覽) 需要資料分割索引鍵和資料列索引鍵。 在我們努力達成將該服務供應項目公開上市的目標時，也計劃加入更多 SDK 支援。

### <a name="do-i-need-to-migrate-my-existing-azure-table-based-applications-to-the-new-sdk-if-i-do-not-want-to-use-the-table-api-preview-features"></a>如果我不想使用資料表 API (預覽) 功能，還需要將現有的 Azure 資料表式應用程式移轉到新的 SDK 嗎？
否，您可以建立和使用現有的標準資料表資產，而不會產生任何形式的中斷。 不過，如果您不使用新的資料表 API (預覽)，就無法從自動索引、額外的一致性選項或全域散發中獲益。 

### <a name="how-do-i-add-replication-of-the-data-in-the-premium-table-api-preview-across-multiple-regions-of-azure"></a>如何跨 Azure 的多個區域，在進階資料表 API (預覽) 中加入對資料的複寫？
您可以使用 Azure Cosmos DB 入口網站的[全域複寫設定](tutorial-global-distribution-documentdb.md#portal)，加入適合應用程式的區域。 若要開發全域散發的應用程式，您還應該使用設為本地區域的 PreferredLocation 資訊來加入您的應用程式，以提供低讀取延遲。 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-premium-table-api-preview"></a>如何在進階資料表 API (預覽) 中變更帳戶的主要寫入區域？
您可以使用 Azure Cosmos DB 的全球複寫入口網站窗格來加入一個區域，然後容錯移轉至所需的區域。 如需指示，請參閱[使用多區域 Azure Cosmos DB 帳戶進行開發](regional-failover.md)。 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>當我散發資料時，如何設定慣用的讀取區域以取得低延遲？ 
若要協助讀取本機位置，請使用 app.config 檔案中的 PreferredLocation 金鑰 。 對於現有的應用程式，如果您已設定 LocationMode，資料表 API (預覽) 會擲回錯誤。 請移除該程式碼，因為進階資料表 API (預覽) 會從 app.config 檔案取用這項資訊。 如需詳細資訊，請參閱 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api-preview"></a>如何看待資料表 API (預覽) 中的一致性層級？ 
Azure Cosmos DB 可在一致性、高可用性及延遲之間提供完全合乎邏輯的取捨。 Azure Cosmos DB 資料表 API (預覽) 提供開發人員五種一致性層級，所以您可以在資料表層級選擇合適的一致性模型，以及在查詢資料時進行個別要求。 當用戶端連線時，它可以指定一致性層級。 您可以透過 app.config 中有關 TableConsistencyLevel 機碼值的設定來變更層級。 

資料表 API (預覽) 會利用「讀取您自己的寫入」來提供低延遲的讀取，並採用工作階段一致性的預設值。 如需詳細資訊，請參閱[一致性層級](consistency-levels.md)。 

根據預設，Azure 資料表儲存體提供區域內的強式一致性，以及次要位置中最終的一致性。 

### <a name="does-azure-cosmos-db-offer-more-consistency-levels-than-standard-tables"></a>Azure Cosmos DB 提供的一致性層級是否比標準資料表多？
是，如需如何從 Azure Cosmos DB 分散式本質獲益的相關資訊，請參閱[一致性層級](consistency-levels.md)。 由於我們提供一致性層級保證，因此您可以放心使用。 如需詳細資訊，請參閱 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>啟用全域散發時，複寫資料需要多久的時間？
我們會永久認可當地區域中的資料，並在幾毫秒之內將資料立即推送到其他區域。 這項複寫操作只取決於資料中心的來回時間 (RTT)。 若要深入了解 Azure Cosmos DB 的全域散發能力，請參閱 [Azure Cosmos DB︰Azure 上分散在世界各地的資料庫服務](distribute-data-globally.md)。

### <a name="can-the-read-request-consistency-level-be-changed"></a>可以變更讀取要求一致性層級嗎？
有了 Azure Cosmos DB，您可以設定容器層級的一致性層級 (在資料表中)。 透過 SDK，您可以在 app.config 檔案中提供 TableConsistencyLevel 索引鍵的值，藉以變更層級。 可能的值包括：強式、限定過期、工作階段、一致前置詞或最終。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的 Tunable 資料一致性層級](consistency-levels.md)。 主要概念在於，您無法將要求一致性層級設定為高於資料表設定的值。 例如，您無法將資料表的一致性層級設定為「最終」，而將要求一致性層級設定為「強式」。 

### <a name="how-does-the-premium-table-api-preview-account-handle-failover-if-a-region-goes-down"></a>區域中斷時，進階資料表 API (預覽) 帳戶如何處理容錯移轉？ 
進階資料表 API (預覽) 會藉助 Azure Cosmos DB 的全域散發平台。 若要確保您的應用程式可以容許資料中心停機，請在 Azure Cosmos DB 入口網站中針對該帳戶至少啟用一個以上的區域，[使用多區域 Azure Cosmos DB 帳戶進行開發](regional-failover.md)。 您可以使用入口網站來設定區域的優先順序，[使用多區域 Azure Cosmos DB 帳戶進行開發](regional-failover.md)。 

您可以針對帳戶加入任意數目的區域，並藉由提供容錯移轉優先順序來控制容錯移轉的目標位置。 當然，若要使用的資料庫，您還需要在目標位置提供應用程式。 如果您這樣做，您的客戶就不會經歷停機時間。 用戶端 SDK 會自動連接。 也就是說，它可以偵測到區域已關閉，並自動容錯移轉至新的區域。

### <a name="is-the-premium-table-api-preview-enabled-for-backups"></a>進階資料表 API (預覽) 是否擁有備份能力？
是，進階資料表 API (預覽) 會藉助 Azure Cosmos DB 的平台來進行備份。 備份會自動執行。 如需詳細資訊，請參閱[使用 Azure Cosmos DB 進行線上備份及還原](online-backup-and-restore.md)。

 
### <a name="does-the-table-api-preview-index-all-attributes-of-an-entity-by-default"></a>資料表 API (預覽) 預設會為實體的所有屬性編製索引嗎？
是，根據預設，它會為實體的所有屬性編製索引。 如需詳細資訊，請參閱 [Azure Cosmos DB：索引編製原則](indexing-policies.md)。 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>這表示我不需要建立多個索引來滿足查詢嗎？ 
是，Azure Cosmos DB 會為所有屬性提供自動編製索引，而不需任何結構描述定義。 這項資訊能讓開發人員專注於應用程式，而不需擔心索引的建立和管理。 如需詳細資訊，請參閱 [Azure Cosmos DB：索引編製原則](indexing-policies.md)。

### <a name="can-i-change-the-indexing-policy"></a>我可以變更索引編制原則嗎？
是，您可以提供索引定義來變更索引編制原則。 如需詳細資訊，請參閱 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。 您需要適當地進行編碼和逸出這些設定。 

利用 app.config 檔案內的字串 json 格式：
```
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB 做為平台時似乎還有很多功能，例如排序、彙總、階層及其他功能。 你們會將這些功能加入資料表 API 嗎？ 
在預覽中，資料表 API 提供的查詢功能與 Azure 資料表儲存體相同。 Azure Cosmos DB 也支援排序、彙總、地理空間查詢、階層及各種不同的內建函式。 在未來的服務更新中，我們會在資料表 API 內提供額外功能。 如需詳細資訊，請參閱[適用於 Azure Cosmos DB DocumentDB API 的 SQL 查詢](../documentdb/documentdb-sql-query.md)。
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>我何時應該變更資料表 API (預覽) 的 TableThroughput？
滿足以下任一條件時，您應該變更 TableThroughput：
* 您即將要執行資料擷取、轉換及載入 (ETL)，或想要在短時間內上傳大量資料。 
* 您需要更多來自後端容器的輸送量。 例如，您發現使用的輸送量比佈建的輸送量還多，而您正要開始進行節流。 如需詳細資訊，請參閱[設定 Azure Cosmos DB 容器輸送量](set-throughput.md)。

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-preview-table"></a>我可以相應增加或減少資料表 API (預覽) 資料表的輸送量嗎？ 
是，您可以使用 Azure Cosmos DB 入口網站的調整窗格來調整輸送量。 如需詳細資訊，請參閱[設定輸送量](set-throughput.md)。

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>新佈建的資料表是否有預設的 TableThroughput 設定？
是，如果您不透過 app.config 覆寫 TableThroughput，也不在 Azure Cosmos DB 中使用預先建立的容器，服務會建立輸送量為 400 的資料表。
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-standard-table-api"></a>對於標準資料表 API 的現有客戶，是否有任何定價變更？
無。 對於現有的標準資料表 API 客戶，沒有任何價格上的變更。 

### <a name="how-is-the-price-calculated-for-the-table-api-preview"></a>如何針對資料表 API (預覽) 計算價格？ 
價格取決於配置的 TableThroughput。 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>如何在資料表 API (預覽) 供應項目中處理資料表上的任何節流？ 
如果要求速率超過基礎容器之佈建輸送量的容量，您將會收到錯誤，而 SDK 將套用重試原則來重試呼叫。

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-premium-table-api-preview-offering-of-azure-cosmos-db"></a>為何我需要選擇 PartitionKey 和 RowKey 以外的輸送量，才能運用 Azure Cosmos DB 的進階資料表 API (預覽) 供應項目？
如果您未在 app.config 中提供輸送量，Azure Cosmos DB 將為您的容器設定預設輸送量。 

Azure Cosmos DB 能提供效能和延遲上的保證，限定作業時的上限。 當引擎能針對租用戶作業強制執行控管時，這項保證是可行的。 設定 TableThroughput 能確保您享有保證的輸送量和延遲，因為平台會保留產能，保證作業成功。 

透過輸送量規格，您可以彈性地變更它來從應用程式季節性當中獲得好處、滿足輸送量需求和節省成本。

### <a name="azure-storage-sdk-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-new-azure-cosmos-db-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Azure 儲存體 SDK 對我而言非常便宜，因為我只需要支付資料的儲存費用，而且我很少查詢。 即使我尚未執行單一交易或儲存任何項目，新的 Azure Cosmos DB 供應項目似乎還是會向我收取費用。 可以請您說明嗎？

Azure Cosmos DB 已設計為全域散發的 SLA 型系統，並提供可用性、延遲和輸送量的保證。 當您在 Azure Cosmos DB 中保留輸送量時，與其他系統輸送量不同的是，它會提供保證。 Azure Cosmos DB 會提供客戶要求的其他功能，如次要索引和全域散發。 在預覽期間，我們提供輸送量最佳化的模型。我們的終極目標是提供儲存體最佳化的模型，以符合客戶的需求。 

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-table-storage-with-the-table-api-preview-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>將資料嵌入資料表儲存體時，我從未收過「配額已滿」通知 (包括資料分割已滿)。 當我使用資料表 API (預覽) 時，卻收到了這則訊息。 這個供應項目會限制我，並強迫我變更我現有的應用程式嗎？

Azure Cosmos DB 是 SLA 型系統，它提供無限制的調整和延遲、輸送量、可用性、一致性保證。 若要獲得保證的進階效能，您必須確定資料大小和索引是可管理且可調整的。 每個資料分割索引鍵的實體或項目數目限制為 10 GB，這是為了確保我們能夠提供絕佳的查閱和查詢效能。 為了確保您的應用程式能夠針對 Azure 儲存體進行更佳的調整，建議您「避免」建立常用資料分割 (將所有資訊儲存在一個資料分割，然後進行查詢)。 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>這樣說來，新的資料表 API (預覽) 仍然需要 PartitionKey 和 RowKey 嗎？ 
是。 因為資料表 API (預覽) 的介面區與資料表儲存體 SDK 類似，所以資料分割索引鍵能提供有效率的資料散發方式。 資料列索引鍵在該資料分割內是唯一的。 資料列索引鍵必須存在且不能為 Null，如同在標準 SDK 中。 RowKey 的長度是 255 個位元組，而 PartitionKey 為 100 個位元組 (即將提高為 1 KB)。 

### <a name="what-are-the-error-messages-for-the-table-api-preview"></a>資料表 API (預覽) 的錯誤訊息將有哪些？
由於此預覽可與標準資料表相容，因此大多數的錯誤將與標準資料表的錯誤相對應。 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api-preview"></a>當我嘗試在資料表 API (預覽) 中逐一建立許多資料表時，為什麼要進行節流？
Azure Cosmos DB 是 SLA 型系統，可提供延遲、輸送量、可用性及一致性的保證。 由於它是佈建的系統，因此會保留資源以保證這些需求。 系統會偵測到快速建立的資料表，並加以節流。 我們建議您查看資料表的建立速率，並降低為低於每分鐘 5 個。 請記住，資料表 API (預覽) 是佈建的系統。 只要您一佈建，就必須開始支付費用。 

## <a name="develop-against-the-graph-api-preview"></a>針對圖形 API (預覽) 進行開發
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>如何將圖形 API (預覽) 的功能套用到 Azure Cosmos DB？
您可以使用擴充程式庫套用圖形 API (預覽) 的功能。 這個程式庫稱為 Microsoft Azure 圖形，您可以在 NuGet 上取得。 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>看來你們支援 Gremlin 圖形周遊語言。 您們是否計劃加入更多形式的查詢？
是，我們計劃在未來加入其他查詢機制。 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>如何使用新的圖形 API (預覽) 供應項目？ 
若要開始使用，請完成[圖形 API](../cosmos-db/create-graph-dotnet.md) 快速入門文章。

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>DocumentDB 客戶的問題
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>為什麼你們要移動到 Azure Cosmos DB？ 

Azure Cosmos DB 是全球大規模發佈之雲端資料庫的下一個大躍進。 身為 DocumentDB 客戶的您，現可存取這套具突破性的系統和 Azure Cosmos DB 所提供的功能。

Azure Cosmos DB 始於 2010 年的「Project Florence」，目的在於解決 Microsoft 開發人員建置大規模應用程式時面臨的難題。 建置全球發佈的應用程式不是 Microsoft 獨有的問題，於是我們在 2015 年以 Azure DocumentDB 的形式將這項技術的第一代提供給 Azure 開發人員使用。 

從那時候起，我們便已新增一些功能並引進重要的全新功能。 Azure Cosmos DB 於焉產生。 在這個版本中，DocumentDB 客戶 (與其資料) 會自動無縫轉換為 Azure Cosmos DB 客戶。 這些功能位於核心資料庫引擎，以及全球發佈、彈性延展性和領先業界之完整 SLA 的領域。 具體來說，我們已逐步提升 Azure Cosmos DB 資料庫引擎，使其能夠有效地將所有熱門資料模型、類型系統和 API 對應至 Azure Cosmos DB 的基礎資料模型。 

這項工作的目前開發人員表徵就是新支援 [Gremlin](../cosmos-db/graph-introduction.md) 和[表格儲存體 API](../cosmos-db/table-introduction.md)。 這只是開端。 我們計劃逐漸新增其他熱門 API 和較新的資料模型，更進一步提升全球規模的效能和儲存體。 

我們必須指出，DocumentDB 的 [SQL 方言](../documentdb/documentdb-sql-query.md)始終都是基礎 Cosmos DB 能夠支援的許多 API 之一。 對於使用完全受管理服務 (例如 Azure Cosmos DB) 的開發人員，服務唯一的介面就是服務所公開的 API。 現有的 DocumentDB 客戶絲毫沒有改變。 在 Azure Cosmos DB 中，您會獲得與 DocumentDB 完全相同的 SQL API。 現在 (以及在未來)，您可以存取先前無法存取的其他功能。 

我們不斷努力的另一個表徵就是擴充輸送量和儲存體的全球和彈性延展性基礎。 我們對全球發佈子系統提供了幾項基礎增強功能。 在許多開發人員專用的功能中，一致的前置詞一致性模型是其中之一 (總計有五個定義完善的一致性模型)。 我們將會在時機成熟時發行許多更有趣的功能。 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>我該怎麼做才能確保 DocumentDB 資源繼續在 Azure Cosmos DB 上執行？

您不需要進行任何變更。 DocumentDB 資源現在就是 Azure Cosmos DB 資源，而且在此移轉發生時不會中斷服務。

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>我需要對我的應用程式進行哪些變更才能使用 Azure Cosmos DB？

不需要進行任何變更。 類別、命名空間和 NuGet 套件名稱均未變更。 一如往常，我們建議讓您的 SDK 保持最新狀態，以便利用最新的功能和改善功能。 

### <a name="whats-changed-in-the-azure-portal"></a>Azure 入口網站有何變更？

DocumentDB 不再顯示為入口網站中的 Azure 服務。 取而代之的是新 Azure Cosmos DB 圖示，如下圖所示。 您可繼續使用所有的集合，就跟之前沒兩樣，且依然可調整輸送量、變更一致性及監視 SLA。 資料總管 (預覽) 的功能已增強。 您現在可以檢視和編輯文件、建立和執行查詢，並透過單一頁面使用預存程序、觸發程序和 UDF，如下圖所示： 

![Azure Cosmos DB 集合刀鋒視窗](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>價格是否有所變動？

否，在 Azure Cosmos DB 上執行應用程式的成本跟以前一樣。

### <a name="are-there-changes-to-the-slas"></a>SLA 是否有所變動？

否，可用性、一致性、延遲和輸送量的 SLA 均維持不變，仍顯示在入口網站中。 如需詳細資訊，請參閱 [Azure Cosmos DB 的 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)。
   
![具有範例資料的待辦事項應用程式](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

