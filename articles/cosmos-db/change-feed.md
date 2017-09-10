---
title: "使用 Azure Cosmos DB 中的變更摘要支援 | Microsoft Docs"
description: "使用 Azure Cosmos DB 的變更摘要支援來追蹤文件中的變更，並執行以事件為基礎的處理 (例如觸發程序)，以及讓快取和分析系統保持最新狀態。"
keywords: "變更摘要"
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 08/15/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 160fbc98e0f3dcc7d17cbe0c7f7425811596a896
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的變更摘要支援
[Azure Cosmos DB](../cosmos-db/introduction.md) 是一項快速且有彈性的全域複製資料庫服務，可用來儲存大量的交易式和操作資料，且具有可預測的讀取和寫入個位數毫秒延遲。 這使得它適合用於 IoT、遊戲、零售，以及操作記錄應用程式。 這些應用程式中的一個常見設計模式是，追蹤對 Azure Cosmos DB 資料所做的變更，並根據這些變更來更新具體化檢視、執行即時分析、將資料封存到冷儲存體，以及在特定事件上觸發通知。 Azure Cosmos DB 中的**變更摘要支援**允許您針對每一個模式建置有效率且可調整的解決方案。

透過變更摘要支援，Azure Cosmos DB 提供一份 Azure Cosmos DB 集合內的文件排序清單，並以文件的修改順序做出排序。 此摘要可用來接聽集合內的資料修改，並執行下列動作：

* 於插入或修改文件時觸發 API 呼叫
* 執行即時 (串流) 更新處理
* 與快取、搜尋引擎或資料倉儲進行資料同步處理

Azure Cosmos DB 中的變更會加以保存，且可進行非同步處理，並分散到一或多個取用者以進行平行處理。 讓我們看看適用於變更摘要的 API，以及您如何使用它們來建置可調整的即時應用程式。 本文示範如何使用 Azure Cosmos DB 變更摘要和 DocumentDB API。 

![使用 Azure Cosmos DB 變更摘要來提供即時分析和事件導向的計算案例](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> 目前，我們只針對 DocumentDB API 提供變更摘要支援。圖形 API 和資料表 API 尚未獲得支援。

## <a name="use-cases-and-scenarios"></a>使用個案和案例
變更摘要允許透過大量寫入來有效處理大型資料集，並提供查詢整個資料集以識別已變更之項目的替代方式。 例如，您可以有效率地執行下列工作︰

* 透過儲存在 Azure Cosmos DB 中的資料，來更新快取、搜尋索引或資料倉儲。
* 實作應用程式層級的資料階層處理和封存，也就是在 Azure Cosmos DB 中儲存「熱資料」，並將「冷資料」淘汰到 [Azure Blob 儲存體](../storage/common/storage-introduction.md)或 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)。
* 使用 [Apache Hadoop](run-hadoop-with-hdinsight.md) 在資料上實作批次分析。
* 透過 Azure Cosmos DB [在 Azure 上實作 Lambda 管線 (英文)](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)。 Azure Cosmos DB 提供一個可調整的資料庫解決方案，可以處理擷取和查詢，並實作具有低 TCO 的 Lambda 架構。 
* 透過不同的分割配置，執行零停機時間移轉至另一個 Azure Cosmos DB 帳戶。

**搭配 Azure Cosmos DB 使用 Lambda 管線進行擷取和查詢：**

![適用於擷取和查詢的 Azure Cosmos DB 型 Lambda 管線](./media/change-feed/lambda.png)

您可以使用 Azure Cosmos DB 來接收與儲存來自裝置、感應器、基礎結構和應用程式的事件資料，並透過 [Azure 串流分析](../stream-analytics/stream-analytics-documentdb-output.md)、[Apache Storm](../hdinsight/hdinsight-storm-overview.md) 或 [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md) 即時處理這些事件。 

在您的 [serverless](http://azure.com/serverless) Web 和行動應用程式內，您可以追蹤例如變更客戶設定檔、喜好設定或位置等的事件，以觸發像是使用 [Azure Functions](../azure-functions/functions-bindings-documentdb.md) 或[應用程式服務](https://azure.microsoft.com/services/app-service/)傳送推播通知到客戶裝置的特定動作。 例如，如果您使用 Azure Cosmos DB 來建置遊戲，就可以根據完成遊戲的分數，使用變更摘要來實作即時排行榜。

## <a name="how-change-feed-works-in-azure-cosmos-db"></a>變更摘要在 Azure Cosmos DB 中的運作方式
Azure Cosmos DB 提供以累加方式讀取對 Azure Cosmos DB 集合做出之更新的功能。 此變更摘要具有下列屬性：

* 變更會在 Azure Cosmos DB 中持續保存，並且可以非同步處理。
* 集合內的文件變更會立即在變更摘要中提供。
* 對文件所做的每項變更皆會在變更摘要中出現一次，且用戶端會管理其檢查點邏輯。 變更摘要的處理器程式庫提供自動檢查點和「至少一次」語意。
* 變更記錄檔中只會包含指定文件的最新變更。 中繼變更可能無法使用。
* 變更摘要會依照各個資料分割索引鍵值內的修改順序排序。 跨資料分割索引鍵值順序不會是固定的。
* 變更可以從任何時間點同步處理，也就是說，可用變更沒有固定的資料保留期限。
* 變更會以資料分割索引鍵區塊為單位提供。 這項功能可讓大型集合的變更由多個取用者/伺服器平行處理。
* 應用程式可以在同一個集合上同時要求多個變更摘要。

根據預設，所有帳戶都會啟用 Azure Cosmos DB 的變更摘要。 就像是 Azure Cosmos DB 的任何其他作業，您可以在寫入區域或任何[讀取區域](distribute-data-globally.md)中使用[佈建輸送量](request-units.md)來讀取變更摘要。 變更摘要包含對集合內文件進行的插入與更新作業。 您可以擷取刪除項目，方法是在您的文件內設定「虛刪除」旗標來取代刪除動作。 或者，您可以透過 [TTL 功能](time-to-live.md)針對您的文件設定有限的逾期期限 (例如 24 小時)，並使用該屬性的值擷取刪除項目。 使用此解決方案，您必須在比 TTL 逾期期限更短的時間間隔內處理變更。 文件集合內每個資料分割索引鍵範圍都可使用變更摘要，因此可以分散到一或多個取用者以進行平行處理。 

![Azure Cosmos DB 變更摘要的分散式處理](./media/change-feed/changefeedvisual.png)

關於如何在用戶端程式碼中實作變更摘要，我們提供了幾個選項供您選擇。 接下來的幾節會說明如何使用 Azure Cosmos DB REST API 和 DocumentDB SDK 來實作變更摘要。 不過，如果您使用 .NET 應用程式，則建議您使用新的[變更摘要處理器程式庫](#change-feed-processor)來處理變更摘要所傳來的事件，因為該程式庫可簡化分割區變更的讀取作業，並讓多個執行緒平行運作。 

## <a id="rest-apis"></a>使用 REST API 和 DocumentDB SDK
Azure Cosmos DB 提供彈性的儲存體及輸送量容器，稱為**集合**。 集合內的資料會針對延展性和效能，使用[資料分割索引鍵](partition-data.md)以邏輯方式分組。 Azure Cosmos DB 提供各種用來存取此資料的 API，包括依識別碼查閱 (Read/Get)、查詢及讀取摘要 (掃描)。 變更摘要可以透過將兩個新的要求標頭填入至 DocumentDB 的 `ReadDocumentFeed` API 來取得，並且可以跨分割區索引鍵的範圍來平行處理。

### <a name="readdocumentfeed-api"></a>ReadDocumentFeed API
讓我們快速看一下 ReadDocumentFeed 的運作方式。 Azure Cosmos DB 支援透過 `ReadDocumentFeed` API 讀取集合內文件的摘要。 例如，下列要求會傳回 `serverlogs` 集合內文件的頁面。 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/serverlogs HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

可以使用 `x-ms-max-item-count` 標頭限制結果，且可以透過重新提交要求 (包含先前回應中傳回的 `x-ms-continuation` 標頭) 繼續先前的讀取。 當從單一用戶端執行時，`ReadDocumentFeed` 會以序列方式逐一查看資料分割之間的結果。 

**序列讀取文件摘要**

您也可以使用其中一個支援的 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 來擷取文件摘要。 例如，下列程式碼片段會示範如何在 .NET 中使用 [ReadDocumentFeedAsync 方法](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync?view=azure-dotnet)。

```csharp
FeedResponse<dynamic> feedResponse = null;
do
{
    feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
}
while (feedResponse.ResponseContinuation != null);
```

### <a name="distributed-execution-of-readdocumentfeed"></a>ReadDocumentFeed 的分散式執行
針對包含數 TB 以上資料或需內嵌大量更新的集合，序列執行單一用戶端電腦的讀取摘要可能不夠實際。 為了支援這些巨量資料案例，Azure Cosmos DB 提供在多個用戶端讀取者/取用者之間明確分散 `ReadDocumentFeed` 呼叫的 API。 

**分散式讀取文件摘要**

若要提供可調整的累加式變更處理，Azure Cosmos DB 會根據分割區索引鍵的範圍，支援變更摘要 API 的向外延展模型。

* 您可以針對執行 `ReadPartitionKeyRanges` 呼叫的集合取得資料分割索引鍵範圍的清單。 
* 針對每個資料分割索引鍵範圍，您可以執行 `ReadDocumentFeed` 來讀取該範圍內資料分割索引鍵的文件。

### <a name="retrieving-partition-key-ranges-for-a-collection"></a>擷取集合的資料分割索引鍵範圍
您可以藉由要求集合內的 `pkranges` 資源來擷取資料分割索引鍵範圍。 例如，下列要求會擷取 `serverlogs` 集合的資料分割索引鍵範圍清單：

    GET https://querydemo.documents.azure.com/dbs/bigdb/colls/serverlogs/pkranges HTTP/1.1
    x-ms-date: Tue, 15 Nov 2016 07:26:51 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dEConYmRgDExu6q%2bZ8GjfUGOH0AcOx%2behkancw3LsGQ8%3d
    x-ms-consistency-level: Session
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: querydemo.documents.azure.com

此要求會傳回下列包含資料分割索引鍵範圍相關中繼資料的回應：

    HTTP/1.1 200 Ok
    Content-Type: application/json
    x-ms-item-count: 25
    x-ms-schemaversion: 1.1
    Date: Tue, 15 Nov 2016 07:26:51 GMT

    {
       "_rid":"qYcAAPEvJBQ=",
       "PartitionKeyRanges":[
          {
             "_rid":"qYcAAPEvJBQCAAAAAAAAUA==",
             "id":"0",
             "_etag":"\"00002800-0000-0000-0000-580ac4ea0000\"",
             "minInclusive":"",
             "maxExclusive":"05C1CFFFFFFFF8",
             "_self":"dbs\/qYcAAA==\/colls\/qYcAAPEvJBQ=\/pkranges\/qYcAAPEvJBQCAAAAAAAAUA==\/",
             "_ts":1477100776
          },
          ...
       ],
       "_count": 25
    }


**資料分割索引鍵範圍屬性**：每個資料分割索引鍵範圍包含下表中的中繼資料屬性︰

<table>
    <tr>
        <th>標頭名稱</th>
        <th>說明</th>
    </tr>
    <tr>
        <td>id</td>
        <td>
            <p>資料分割索引鍵範圍的識別碼。 這是每個集合內穩定且唯的一識別碼。</p>
            <p>必須在下列呼叫中使用，以依據資料分割索引鍵範圍讀取變更。</p>
        </td>
    </tr>
    <tr>
        <td>maxExclusive</td>
        <td>資料分割索引鍵範圍的最大資料分割索引鍵雜湊值。 供內部使用。</td>
    </tr>
    <tr>
        <td>minInclusive</td>
        <td>資料分割索引鍵範圍的最小資料分割索引鍵雜湊值。 供內部使用。</td>
    </tr>       
</table>

您可以使用其中一個支援的 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 來執行此動作。 例如，下列程式碼片段示範如何在 .NET 中使用 [ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync?view=azure-dotnet) 方法來擷取分割區索引鍵範圍。

```csharp
string pkRangesResponseContinuation = null;
List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

do
{
    FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri, 
        new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
}
while (pkRangesResponseContinuation != null);
```

Azure Cosmos DB 支援針對每個分割區索引鍵範圍進行文件擷取，方法是設定選擇性的 `x-ms-documentdb-partitionkeyrangeid` 標頭。 

### <a name="performing-an-incremental-readdocumentfeed"></a>執行累加式 ReadDocumentFeed
ReadDocumentFeed 支援下列在 Azure Cosmos DB 集合中進行累加式變更處理的案例/工作：

* 從開始 (也就是集合建立開始) 讀取所有文件變更。
* 從目前時間讀取包含未來更新的所有文件變更，或自從使用者指定時間以來的任何變更。
* 從集合邏輯版本 (ETag) 讀取所有文件變更。 您可以根據從累加式讀取摘要要求傳回的 ETag，將您的取用者設為檢查點。

變更包含對文件進行的插入和更新。 若要擷取刪除項目，您必須在您的文件內使用「虛刪除」屬性，或使用[內建 TTL 屬性](time-to-live.md)在變更摘要中指示暫止刪除。

下表列出 ReadDocumentFeed 作業的[要求](/rest/api/documentdb/common-documentdb-rest-request-headers.md)和[回應標頭](/rest/api/documentdb/common-documentdb-rest-response-headers.md)。

**累加式 ReadDocumentFeed 的要求標頭**：

<table>
    <tr>
        <th>標頭名稱</th>
        <th>說明</th>
    </tr>
    <tr>
        <td>A-IM</td>
        <td>必須設定為「累加式摘要」或省略</td>
    </tr>
    <tr>
        <td>If-None-Match</td>
        <td>
            <p>No header：傳回從一開始 (集合建立) 的所有變更</p>
            <p>"*"：傳回集合內資料的所有新變更</p>           
            <p>&lt;etag&gt;：如果設定為集合 ETag，則傳回邏輯時間戳記之後所做的所有變更</p>
        </td>
    </tr>
    <tr>    
        <td>If-Modified-Since</td> 
        <td>RFC 1123 時間格式；如果已指定 If-None-Match，則忽略</td> 
    </tr> 
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>用來讀取資料的資料分割索引鍵範圍識別碼。</td>
    </tr>
</table>

**累加式 ReadDocumentFeed 的回應標頭**：

<table> <tr>
        <th>標頭名稱</th>
        <th>說明</th>
    </tr>
    <tr>
        <td>etag</td>
        <td>
            <p>回應中最後傳回文件的邏輯序號 (LSN)。</p>
            <p>透過在 If-None-Match 中重新提交此值，可以繼續執行累加式 ReadDocumentFeed。</p>
        </td>
    </tr>
</table>

以下是從邏輯版本/ETag `28535`和資料分割索引鍵範圍 = `16` 傳回集合中所有累加變更的範例要求：

    GET https://mydocumentdb.documents.azure.com/dbs/bigdb/colls/bigcoll/docs HTTP/1.1
    x-ms-max-item-count: 1
    If-None-Match: "28535"
    A-IM: Incremental feed
    x-ms-documentdb-partitionkeyrangeid: 16
    x-ms-date: Tue, 22 Nov 2016 20:43:01 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dzdpL2QQ8TCfiNbW%2fEcT88JHNvWeCgDA8gWeRZ%2btfN5o%3d
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

變更會依照資料分割索引鍵範圍內每個資料分割索引鍵值內的時間加以排序。 跨資料分割索引鍵值順序不會是固定的。 如果結果無法容納於單一頁面，您可以透過重新提交包含 `If-None-Match` 標頭的要求，以及和上一個回應中的 `etag` 相同的值，來讀取下一頁的結果。 如果多個文件已在預存程序或觸發程序內以交易式方式插入或更新，則文件會在相同的回應頁面中傳回。

> [!NOTE]
> 使用變更摘要時，若在預存程序或觸發程序內插入或更新多份文件，您在頁面中取得的傳回項目可能會比 `x-ms-max-item-count` 中指定的項目更多。 

使用 .NET SDK (1.17.0) 時，在 `ChangeFeedOptions` 中設定欄位 `StartTime`，以在呼叫 `CreateDocumentChangeFeedQuery` 時，直接傳回自 `StartTime` 以來的變更文件。 藉由使用 REST API 指定 `If-Modified-Since`，您的要求並不會傳回文件本身，而會傳回接續權杖或回應標頭中的 `etag`。 若要傳回修改指定時間的文件，接續權杖 `etag` 必須在下一個要求中與 `If-None-Match` 搭配使用，以傳回實際文件。 

.NET SDK 提供 [CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery?view=azure-dotnet) 和 [ChangeFeedOptions](/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) 協助程式類別來存取對集合所做的變更。 下列程式碼片段示範如何使用來自單一用戶端的 .NET SDK 來擷取從一開始的所有變更。

```csharp
private async Task<Dictionary<string, string>> GetChanges(
    DocumentClient client,
    string collection,
    Dictionary<string, string> checkpoints)
{
    string pkRangesResponseContinuation = null;
    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

    do
    {
        FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
            collectionUri, 
            new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

        partitionKeyRanges.AddRange(pkRangesResponse);
        pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    }
    while (pkRangesResponseContinuation != null);

    foreach (PartitionKeyRange pkRange in partitionKeyRanges)
    {
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);

        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collection,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = 1
            });

        while (query.HasMoreResults)
        {
            FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

            foreach (DeviceReading changedDocument in readChangesResponse)
            {
                Console.WriteLine(changedDocument.Id);
            }

            checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
        }
    }

    return checkpoints;
}
```
下列程式碼片段示範如何使用變更摘要支援和先前的函式，透過 Azure Cosmos DB 即時處理變更。 第一次呼叫會傳回集合中所有的文件，而第二次呼叫只會傳回於最後一個檢查點之後建立的兩個文件。

```csharp
// Returns all documents in the collection.
Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

// Returns only the two documents created above.
checkpoints = await GetChanges(client, collection, checkpoints);
```

您也可以使用用戶端邏輯以選擇性地處理事件，來篩選變更摘要。 例如，以下是使用用戶端 LINQ 處理僅來自裝置感應器之溫度變更事件的程式碼片段。

```csharp
FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

foreach (DeviceReading changedDocument in 
    readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
{
    // trigger an action, like call an API
}
```

## <a id="change-feed-processor"></a>變更摘要處理器程式庫
另一個選項是使用 [Azure Cosmos DB 變更摘要處理器程式庫](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed)，以協助您輕鬆地將來自變更摘要的事件處理分散到多個取用者。 程式庫很適合用來在 .NET 平台上建置變更摘要讀取程式。 透過其他 Cosmos DB SDK 所含方法來使用變更摘要處理器程式庫時，所能簡化的某些工作流程包括： 

* 當資料儲存在多個分割區時，提取變更摘要的更新
* 將資料從某個集合移動或複寫到另一個集合
* 平行執行因資料和變更摘要有更新所觸發的動作 

雖然在 Cosmos SDK 中使用 API 可讓您精確地存取每個分割區中的變更摘要更新，但使用變更摘要處理器程式庫卻能簡化分割區變更的讀取作業，並讓多個執行緒平行運作。 您不必手動讀取每個容器的變更並儲存每個分割區的接續權杖，變更摘要處理器會自動使用租用機制來為您管理分割區變更的讀取作業。

此程式庫是以 NuGet 套件的形式來提供：[Microsoft.Azure.Documents.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)，並可從原始程式碼取得來作為 Github [範例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)。 

### <a name="understanding-change-feed-processor-library"></a>了解變更摘要處理器程式庫 

用來實作變更摘要處理器的主要元件有四個：受監視的集合、租用集合、處理器主機和取用者。 

**受監視的集合：**受監視的集合是將會產生變更摘要的資料。 對於受監視集合所進行的任何插入和變更都會反映在集合的變更摘要中。 

**租用集合：**租用集合會協調如何處理多個背景工作角色的變更摘要。 另外會有一個集合用來儲存租用 (每個分割區一個租用)。 在不同帳戶儲存此租用集合，並讓其寫入區域更靠近變更摘要處理器的執行所在位置會有好處。 租用物件包含下列屬性： 
* 擁有者：指定擁有租用的主機
* 接續：指定特定分割區在變更摘要中的位置 (接續權杖)
* 時間戳記：上次更新租用的時間；時間戳記可用來檢查是否將租用視為過期 

**處理器主機：**每一部主機都會根據主機的其他執行個體中有多少個執行個體擁有作用中租用，來決定要處理的分割區數量。 
1.  主機在啟動時會取得租用，以平衡分配所有主機的工作負載。 主機會定期更新租用，以便讓租用保持作用中狀態。 
2.  主機會對其每個讀取的租用設置最後一個接續權杖的檢查點。 為確保能夠安全地進行並行存取，主機會檢查每個租用更新的 ETag。 主機也支援其他檢查點策略。  
3.  關機後，主機會將所有租用釋出，但會保留接續資訊，以便之後能夠繼續讀取預存的檢查點。 

目前，主機數目不能大於分割區 (租用) 數目。

**取用者：**取用者 (或背景工作角色) 是負責執行每個主機所起始之變更摘要處理活動的執行緒。 每個處理器主機都可以有多個取用者。 每個取用者會從其獲派到的分割區讀取變更摘要，並向其主機通知所發生的變更和已過期的租用。

為了進一步了解變更摘要處理器的這四個元素是如何一起運作的，我們來看一下下圖中的範例。 受監視的集合會儲存文件，並使用 "city" 來作為分割區索引鍵。 我們可以看到，藍色的分割區包含 "city" 欄位為 "A 到 E" 的文件，依此類推。 主機有兩部，每一部都有兩個取用者從四個分割區進行平行讀取。 箭號顯示從變更摘要中的特定地點進行讀取的取用者。 在第一個分割區中，較深的藍色代表未讀取的變更，較淺的藍色則代表變更摘要上已讀取的變更。 主機會使用租用集合來儲存「接續」值，以追蹤每個取用者目前的讀取位置。 

![使用 Azure Cosmos DB 變更摘要處理器主機](./media/change-feed/changefeedprocessornew.png)

### <a name="using-change-feed-processor-library"></a>使用變更摘要處理器程式庫 
下節說明如何在將來源集合的變更複寫到目的地集合的情況下使用變更摘要處理器程式庫。 在這裡，來源集合是變更摘要處理器內的受監視集合。 

**安裝和納入變更摘要處理器 NuGet 套件** 

在安裝變更摘要處理器 NuGet 套件之前，請先安裝： 
* Microsoft.Azure.DocumentDB 1.13.1 版或更新版本 
* Newtonsoft.Json 9.0.1 版或更新版本 安裝 `Microsoft.Azure.DocumentDB.ChangeFeedProcessor` 並將它納入以作為參考。

**建立受監視的租用集合和目的地集合** 

若要使用變更摘要處理器程式庫，您必須先建立租用集合再執行處理器主機。 我們再次地建議，請將租用集合儲存在不同帳戶，並讓其寫入區域更靠近變更摘要處理器的執行所在位置。 在這個資料移動範例中，我們需要先建立目的地集合再執行變更摘要處理器主機。 在程式碼範例中，我們會呼叫 Helper 方法來建立受監視的租用集合和目的地集合 (如果您還沒有這些集合)。 

> [!WARNING]
> 建立集合會牽涉到定價，因為您會將輸送量保留供應用程式與 Azure Cosmos DB 通訊使用。 如需詳細資訊，請瀏覽[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

建立處理器主機

`ChangeFeedProcessorHost` 類別能為事件處理器實作提供安全執行緒、多處理序、安全的執行階段環境，進而提供檢查點和分割區租用管理。 若要使用 `ChangeFeedProcessorHost` 類別，您可以實作 `IChangeFeedObserver`。 這個介面包含三個方法：

* `OpenAsync`：當變更摘要觀察者開啟時，系統就會呼叫這個函式。 您可以修改這個函式，以在取用者/觀察者開啟時執行特定動作。  
* `CloseAsync`：當變更摘要觀察者終止時，系統就會呼叫這個函式。 您可以修改這個函式，以在取用者/觀察者關閉時執行特定動作。  
* `ProcessChangesAsync`：當變更摘要有新的文件變更時，系統就會呼叫這個函式。 您可以修改這個函式，以對每個變更摘要更新執行特定動作。  

在我們的範例中，我們會透過 `DocumentFeedObserver` 類別實作 `IChangeFeedObserver` 介面。 在這裡，`ProcessChangesAsync` 函式會將文件從變更摘要中更新插入 (更新) 到目的地集合。 這個範例可用於將資料從某個集合移到另一個集合，以變更資料集的分割區索引鍵。 

執行處理器主機

在開始事件的處理之前，您可以自訂變更摘要選項和變更摘要主機選項。 
```csharp
    // Customizable change feed option and host options 
    ChangeFeedOptions feedOptions = new ChangeFeedOptions();

    // ie customize StartFromBeginning so change feed reads from beginning
    // can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
    feedOptions.StartFromBeginning = true;

    ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();

    // ie. customizing lease renewal interval to 15 seconds
    // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
    feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

```
下表摘要說明可自訂的特定欄位。 

**變更摘要選項**：
<table>
    <tr>
        <th>屬性名稱</th>
        <th>說明</th>
    </tr>
    <tr>
        <td>MaxItemCount</td>
        <td>取得或設定要在 Azure Cosmos DB 資料庫服務中的列舉作業傳回的項目數上限。</td>
    </tr>
    <tr>
        <td>PartitionKeyRangeId</td>
        <td>取得或設定 Azure Cosmos DB 資料庫服務中目前要求的分割區索引鍵範圍識別碼。</td>
    </tr>
    <tr>
        <td>RequestContinuation</td>
        <td>取得或設定 Azure Cosmos DB 資料庫服務中的要求接續權杖。</td>
    </tr>
        <tr>
        <td>SessionToken</td>
        <td>取得或設定要在 Azure Cosmos DB 資料庫服務中用於工作階段一致性的工作階段權杖。</td>
    </tr>
        <tr>
        <td>StartFromBeginning</td>
        <td>取得或設定 Azure Cosmos DB 資料庫服務中的變更摘要應該從開頭 (true) 或目前位置 (false) 來開始。 根據預設，它會從目前位置 (false) 來開始。</td>
    </tr>
</table>

**變更摘要主機選項**:
<table>
    <tr>
        <th>屬性名稱</th>
        <th>類型</th>
        <th>說明</th>
    </tr>
    <tr>
        <td>LeaseRenewInterval</td>
        <td>時間範圍</td>
        <td>目前由 ChangeFeedEventHost 執行個體所持有之分割區的所有租用間隔。</td>
    </tr>
    <tr>
        <td>LeaseAcquireInterval</td>
        <td>時間範圍</td>
        <td>工作的啟動間隔，這些工作可用來計算分割區是否有平均地分散到已知的主機執行個體。</td>
    </tr>
    <tr>
        <td>LeaseExpirationInterval</td>
        <td>時間範圍</td>
        <td>租用花在代表分割區之租用的間隔。 未在此間隔內更新的租用將會過期，而且分割區的擁有權會移轉給另一個 ChangeFeedEventHost 執行個體。</td>
    </tr>
    <tr>
        <td>FeedPollDelay</td>
        <td>時間範圍</td>
        <td>在目前所有的變更都清空後，每次輪詢分割區以了解摘要上是否有新變更時所要延遲的時間。</td>
    </tr>
    <tr>
        <td>CheckpointFrequency</td>
        <td>CheckpointFrequency</td>
        <td>要對租用設置檢查點的頻率。</td>
    </tr>
    <tr>
        <td>MinPartitionCount</td>
        <td>int</td>
        <td>主機的分割區計數下限。</td>
    </tr>
    <tr>
        <td>MaxPartitionCount</td>
        <td>int</td>
        <td>主機可以提供的分割區數目上限。</td>
    </tr>
    <tr>
        <td>DiscardExistingLeases</td>
        <td>Bool</td>
        <td>在主機啟動時，是否應該刪除所有的現有租用，以及是否要讓主機從全新狀態來開始。</td>
    </tr>
</table>


若要啟動事件處理，請具現化 `ChangeFeedProcessorHost`，並為 Azure Cosmos DB 集合提供適當的參數。 然後，呼叫 `RegisterObserverAsync` 以在執行階段註冊您的 `IChangeFeedObserver` (在此範例中為 DocumentFeedObserver) 實作。 此時，主機會嘗試使用「窮盡」演算法，來取得 Azure Cosmos DB 集合內每個分割區索引鍵範圍的租用。 這些租用會延續一段指定時間，然後必須更新。 新節點 (在本案例中為背景工作角色執行個體) 在上線時會保留租用，而且隨著時間的推移，負載會隨著每個主機嘗試取得更多租用而轉移到不同節點。 

在程式碼範例中，我們使用 Factory 類別 (DocumentFeedObserverFactory.cs) 來建立觀察者，並使用 `RegistObserverFactoryAsync` 來註冊觀察者。 

```csharp
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
    {
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);

        await host.RegisterObserverFactoryAsync(docObserverFactory);

        Console.WriteLine("Running... Press enter to stop.");
        Console.ReadLine();

        await host.UnregisterObserversAsync();
    }
```
經過一段時間後，均衡的局面將會出現。 此動態功能可讓您將 CPU 架構自動調整套用至消費者，以便進行向上和向下調整。 如果 Azure Cosmos DB 中出現可用變更的速度超出取用者的處理能力，可以使用取用者上的 CPU 提升來引發背景工作執行個體計數的自動調整。

## <a name="next-steps"></a>後續步驟
* 嘗試 [GitHub 上的 Azure Cosmos DB 變更摘要程式碼範例 (英文)](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)
* 使用 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 或 [REST API](/rest/api/documentdb/) 開始撰寫程式碼。

