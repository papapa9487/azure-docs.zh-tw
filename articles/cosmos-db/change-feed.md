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
ms.devlang: 
ms.topic: article
ms.date: 10/10/2017
ms.author: arramac
ms.openlocfilehash: 0971959fb168d92096531d1c081666cf301608cf
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2017
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的變更摘要支援

[Azure Cosmos DB](../cosmos-db/introduction.md) 是彈性、快速的全域複寫資料庫，適合用於 IoT、遊戲、零售，以及操作記錄應用程式。 這類應用程式常用的設計模式，是利用資料的變更啟動其他動作的進行。 其他的動作可能是下列其中一項： 

* 於插入或修改文件時觸發 API 通知或呼叫。
* 進行 IoT 資料流處理或執行分析。
* 藉由與快取、搜尋引擎、或資料倉儲同步處理，或將資料封存到冷儲存體，進行額外的資料移動。

Azure Cosmos DB 中的**變更摘要支援**允許您針對每一個模式建置有效率且可調整的解決方案，如下圖所示：

![使用 Azure Cosmos DB 變更摘要來提供即時分析和事件導向的計算案例](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Azure Cosmos DB 中的所有資料模型和容器均可使用變更摘要支援。 不過，讀取變更摘要使用 DocumentDB 用戶端，並將項目序列化為 JSON 格式。 因為是 JSON 格式，MongoDB 用戶端會遇到 BSON 格式文件與 JSON 格式變更摘要互不相符的情況。 

## <a name="how-does-change-feed-work"></a>變更摘要如何運作？

Azure Cosmos DB 中的變更摘要支援是靠接聽 Azure Cosmos DB 集合的任何變更而運作。 然後變更摘要會輸出已排序的文件清單，這些文件已依其修改的順序變更過。 變更會保存，可進行非同步累加處理，而輸出可配送給一或多個取用者進行平行處理。 

後文會說明，您可以三個不同的方式讀取變更摘要：

1.  [使用 Azure Functions](#azure-functions)
2.  [使用 Azure Cosmos DB SDK](#rest-apis)
3.  [使用 Azure Cosmos DB 變更摘要處理器程式庫](#change-feed-processor)

文件集合內每個分割區索引鍵範圍都可使用變更摘要，因此可以配送給一或多個取用者以進行平行處理，如下圖所示。

![Azure Cosmos DB 變更摘要的分散式處理](./media/change-feed/changefeedvisual.png)

其他詳細資料：
* 所有帳戶預設都會啟用 變更摘要。
* 和任何其他 Azure Cosmos DB 作業一樣，您可以在寫入區域或任何[讀取區域](distribute-data-globally.md)中使用[佈建輸送量](request-units.md)來讀取變更摘要。
* 變更摘要包含對集合內文件進行的插入與更新作業。 您可以擷取刪除項目，方法是在您的文件內設定「虛刪除」旗標來取代刪除動作。 或者，您可以透過 [TTL 功能](time-to-live.md)針對您的文件設定有限的逾期期限 (例如 24 小時)，並使用該屬性的值擷取刪除項目。 使用此解決方案，您必須在比 TTL 逾期期限更短的時間間隔內處理變更。
* 對文件所做的每項變更皆會在變更摘要中出現一次，且用戶端會管理其檢查點邏輯。 變更摘要的處理器程式庫提供自動檢查點和「至少一次」語意。
* 變更記錄檔中只會包含指定文件的最新變更。 中繼變更可能無法使用。
* 變更摘要會依照各個資料分割索引鍵值內的修改順序排序。 跨資料分割索引鍵值順序不會是固定的。
* 變更可以從任何時間點同步處理，也就是說，可用變更沒有固定的資料保留期限。
* 變更會以資料分割索引鍵區塊為單位提供。 這項功能可讓大型集合的變更由多個取用者/伺服器平行處理。
* 應用程式可以同時要求同一個集合的多個變更摘要。

## <a name="use-cases-and-scenarios"></a>使用個案和案例

變更摘要利用大量寫入讓大型資料集的處理更有效率，是查詢整個資料集以識別已變更之項目的另一種做法。 

例如，您可以利用變更摘要有效率地執行下列工作︰

* 透過儲存在 Azure Cosmos DB 中的資料，來更新快取、搜尋索引或資料倉儲。
* 實作應用程式層級的資料階層處理和封存，也就是在 Azure Cosmos DB 中儲存「熱資料」，並將「冷資料」淘汰到 [Azure Blob 儲存體](../storage/common/storage-introduction.md)或 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)。
* 使用 [Apache Hadoop](run-hadoop-with-hdinsight.md) 在資料上實作批次分析。
* 透過不同的分割配置，執行零停機時間移轉至另一個 Azure Cosmos DB 帳戶。
* 透過 Azure Cosmos DB [在 Azure 上實作 Lambda 管線 (英文)](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)。 Azure Cosmos DB 提供一個可調整的資料庫解決方案，可以處理擷取和查詢，並實作具有低 TCO 的 Lambda 架構。 
* 接收與儲存來自裝置、感應器、基礎結構和應用程式的事件資料，並透過 [Azure 串流分析](../stream-analytics/stream-analytics-documentdb-output.md)、[Apache Storm](../hdinsight/hdinsight-storm-overview.md) 或 [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md) 即時處理這些事件。 

下圖顯示利用 Azure Cosmos DB 進行擷取和查詢的 lambda 管線，如何使用變更摘要支援： 

![運用 Azure Cosmos DB 進行擷取和查詢的 lambda 管線](./media/change-feed/lambda.png)

此外，在您的 [serverless](http://azure.com/serverless) Web 和行動應用程式內，您可以追蹤例如變更客戶設定檔、喜好設定或位置等的事件，以觸發像是使用 [Azure Functions](#azure-functions) 傳送推播通知到客戶裝置的特定動作。 例如，如果您使用 Azure Cosmos DB 來建置遊戲，就可以根據完成遊戲的分數，使用變更摘要來實作即時排行榜。

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>使用 Azure Functions 

如果您使用 Azure Functions，要與 Azure Cosmos DB 變更摘要連線最簡單的方式，是在您的 Azure Functions 應用程式新增 Azure Cosmos DB 觸發程序。 當您在 Azure Functions 應用程式中 建立 Azure Cosmos DB 觸發程序時，需選取要連線的 Azure Cosmos DB 集合，以及集合每次變更時要觸發的函式。 

您可以在 Azure Functions 入口網站中、在 Azure Cosmos DB 入口網站中建立觸發程序，或以程式設計方式建立。 如需詳細資訊，請參閱 [Azure Cosmos DB：使用 Azure Functions 的無伺服器資料庫計算](serverless-computing-database.md)。

<a id="rest-apis"></a>
## <a name="using-the-sdk"></a>使用 SDK

Azure Cosmos DB 使用的 [DocumentDB SDK](documentdb-sdk-dotnet.md) 提供讀取和管理變更摘要的所有功能。 但是，能力愈強責任愈多。 如果您想要管理檢查點、處理文件序號，並能夠更精確地控制分割區索引鍵，那麼，使用 SDK 可能是正確的方法。

本節逐步解說如何使用 DocumentDB SDK 運用摘要變更。

1. 首先，讀取 appconfig 中的下列資源。 擷取端點和授權金鑰的指示，可於[更新連接字串](create-documentdb-dotnet.md#update-your-connection-string)中找到。

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. 遵循下列方式建立用戶端：

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. 取得分割區索引鍵範圍：

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. 為每個分割區索引鍵範圍呼叫 ExecuteNextAsync：

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

如果您有多個讀取器，可以使用 **ChangeFeedOptions** 將讀取負載分配至不同的執行緒或不同的用戶端。

就這麼簡單，只要這幾行程式碼，您便可以開始讀取變更摘要。 您可以在 [azure-cosmos-db-DocumentFeed GitHub repo](https://github.com/rsarosh/azure-cosmos-db-DocumentFeed) 取得本文程式碼的完整版。

在上述的步驟 4 程式碼中，最後一行的 **ResponseContinuation** 中有最後一個邏輯序號 (LSN)，下一次您讀取新文件時會用到這個序號 (新文件在這個序號之後)。 利用 **ChangeFeedOption** 的 **StartTime**，可以加大取得文件的範圍。 因此，如果您的 **ResponseContinuation** 是 null，但 **StartTime** 是過去的時間，則會取得自 **StartTime** 開始變更過的所有文件。 但是，如果 **ResponseContinuation** 有值，則系統會取得自 LSN 開始的所有文件。

因此，您的檢查點陣列只保留每個分割區的 LSN。 但是，如果您不想處理分割區、檢查點、LSN、開始時間等等有的沒的，更簡單的做法是使用變更摘要處理器程式庫。

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>使用變更摘要處理器程式庫 

[Azure Cosmos DB 變更摘要處理器程式庫](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed) 可幫助您輕鬆地將事件處理分散給多個取用者。 此程式庫會簡化跨分割區和多個平行運作執行緒上的變更讀取。

變更摘要處理器程式庫的主要優點是您不需要管理每個分割區和接續權杖，也不需要手動輪詢每個集合。

變更摘要處理器程式庫可以簡化跨分割區和多個平行運作執行緒上的變更讀取。  它會使用租用機制自動管理所有分割區上的變更讀取。 如您在下圖中所見，如果您啟動了使用變更摘要處理器程式庫的兩個用戶端，它們會在彼此之中分割工作。 當您繼續增加用戶端，它們會繼續在彼此之中分割工作。

![Azure Cosmos DB 變更摘要的分散式處理](./media/change-feed/change-feed-output.png)

先啟動左邊的用戶端，它開始並啟監視所有分割區，接著啟動第二個用戶端，然後第一個將部份放租用釋出給第二個用戶端。 如您所見，這是在不同電腦與用戶端之間分散工作的好方法。

請注意，如果您有兩個無伺服器的 Azure 函式使用相同的租用在監視相同的集合，則這兩個函式可能會得到不同的文件，取決於處理器程式庫決定如何處理分割區。

### <a name="understanding-the-change-feed-processor-library"></a>了解變更摘要處理器程式庫

用來實作變更摘要處理器的主要元件有四個：受監視的集合、租用集合、處理器主機和取用者。 

> [!WARNING]
> 建立集合會牽涉到定價，因為您會將輸送量保留供應用程式與 Azure Cosmos DB 通訊使用。 如需詳細資訊，請瀏覽[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

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

### <a name="working-with-the-change-feed-processor-library"></a>使用變更摘要處理器程式庫

在安裝變更摘要處理器 NuGet 套件之前，請先安裝： 

* Microsoft.Azure.DocumentDB 1.13.1 版或更新版本 
* Newtonsoft.Json 9.0.1 版或更新版本

然後安裝 [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget 套件](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)並將它加入參考。

若要實作變更摘要處理器程式庫，您必須要：

1. 實作 **DocumentFeedObserver** 物件，它會實作 **IChangeFeedObserver**。

2. 實作 **DocumentFeedObserverFactory**，它會實作 **IChangeFeedObserverFactory**。

3. 在 **DocumentFeedObserverFacory** 的 **CreateObserver** 方法中，將您在步驟 1 中建立的 **ChangeFeedObserver** 具現化然後傳回。

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. 具現化 **DocumentObserverFactory**。

5. 具現化 **ChangeFeedEventHost**：

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. 向主機註冊 **DocumentFeedObserverFactory**。

步驟 4 到 6 的程式碼是： 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

就這麼簡單！ 執行這幾個步驟之後，文件會開始進入 **DocumentFeedObserver ProcessChangesAsync** 方法。

## <a name="next-steps"></a>後續步驟

如需有關使用 Azure Cosmos DB 與 Azure Functions 的詳細資訊，請參閱 [Azure Cosmos DB：使用 Azure Functions 的無伺服器資料庫計算](serverless-computing-database.md)。

如需有關使用變更摘要處理器程式庫的詳細資訊，請看下列資源：

* [資訊頁面](documentdb-sdk-dotnet-changefeed.md) 
* [Nuget 套件](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [展示上述步驟 1-6 的範例程式碼](https://github.com/rsarosh/Cosmos-ChangeFeedProcessor)
* [GitHub 上的其他範例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

如需有關透過 SDK 使用變更摘要的詳細資訊，請看下列資源：

* [SDK 資訊頁面](documentdb-sdk-dotnet.md)
