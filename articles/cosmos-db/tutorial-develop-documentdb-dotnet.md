---
title: "Azure Cosmos DB：使用 .NET 搭配 DocumentDB API 進行開發 | Microsoft Docs"
description: "了解如何使用 .NET 搭配 Azure Cosmos DB 的 DocumentDB API 進行開發"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 871731adc849c575d97ed6735b87457811668ed5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmosdb-develop-with-the-documentdb-api-in-net"></a>Azure CosmosDB：使用 .NET 搭配 DocumentDB API 進行開發

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本教學課程示範如何使用 Azure 入口網站來建立 Azure Cosmos DB 帳戶，然後使用 [DocumentDB .NET API](documentdb-introduction.md) 來建立具有[分割區索引鍵](documentdb-partition-data.md#partition-keys)的文件資料庫和集合。 透過在建立集合時定義分割區索引鍵，您的應用程式便已做好準備，可隨著資料成長毫不費力地進行調整。 

本教學課程使用 [DocumentDB .NET API](documentdb-sdk-dotnet.md) 來涵蓋下列工作：

> [!div class="checklist"]
> * 建立 Azure Cosmos DB 帳戶
> * 建立具有分割區索引鍵的資料庫和集合
> * 建立 JSON 文件
> * 更新文件
> * 查詢已分割的集合
> * 執行預存程序
> * 刪除文件
> * 刪除資料庫

## <a name="prerequisites"></a>必要條件
請確定您具有下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* 如果尚未安裝 Visual Studio 2017，您可以下載並使用**免費的** [Visual Studio 2017 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

## <a name="create-an-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

我們將從在 Azure 入口網站中建立 Azure Cosmos DB 帳戶開始著手。

> [!TIP]
> * 已經有 Azure Cosmos DB 帳戶？ 如果是，請直接跳到[設定您的 Visual Studio 方案](#SetupVS)
> * 您是否已有 Azure DocumentDB 帳戶？ 如果是，您的帳戶現在會是 Azure Cosmos DB 帳戶，且您可以直接跳到[設定您的 Visual Studio 方案](#SetupVS)。  
> * 如果您使用「Azure Cosmos DB 模擬器」，請依照 [Azure Cosmos DB 模擬器](local-emulator.md)的步驟來設定模擬器，然後直接跳到[設定您的 Visual Studio 方案](#SetupVS)。 
>
>

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>設定您的 Visual Studio 方案
1. 在電腦上開啟 **Visual Studio**。
2. 從 [檔案] 功能表中，選取 [新增]，然後選擇 [專案]。
3. 在 新增專案  對話方塊中，選取 範本 / Visual C# / 主控台應用程式 (.NET Framework)、為專案命名，然後按一下確定。
   ![[新增專案] 視窗的螢幕擷取畫面](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-new-project-2.png)

4. 在 方案總管 中，以滑鼠右鍵按一下 Visual Studio 方案底下的新主控台應用程式，然後按一下管理 NuGet 套件...
    
    ![專案的滑鼠右鍵功能表的螢幕擷取畫面](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. 在 [NuGet] 索引標籤中按一下 [瀏覽]，然後在搜尋方塊中輸入 **documentdb**。
<!---stopped here--->
6. 在結果中尋找 [Microsoft.Azure.DocumentDB]，然後按一下 [安裝]。
   「Azure Cosmos DB 用戶端程式庫」的套件識別碼是 [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)。
   ![用於尋找「Azure Cosmos DB 用戶端 SDK」之「NuGet 功能表」的螢幕擷取畫面](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    如果您收到關於檢閱方案變更的訊息，請按一下 [確定]。 如果您收到關於接受授權的訊息，請按一下 [我接受]。

## <a id="Connect"></a>將參考新增到您的專案
本教學課程中的剩餘步驟會提供在您的專案中建立和更新 Azure Cosmos DB 資源所需的 DocumentDB API 程式碼片段。

首先，請將下列參考新增到您的應用程式。
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>連接您的應用程式

接著，在應用程式中新增下列兩個常數和您的「用戶端」變數。

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

然後，返回 [Azure 入口網站](https://portal.azure.com)以擷取您的端點 URL 和主要金鑰。 必須提供端點 URL 和主要金鑰，您的應用程式才能了解要在連接到哪裡，而 Azure Cosmos DB 才會信任您的應用程式連接。

在 Azure 入口網站中，瀏覽至您的 Azure Cosmos DB 帳戶，按一下 金鑰，然後按一下讀寫金鑰。

從入口網站中複製 URI，然後將它貼到 program.cs 檔案中的 `<your endpoint URL>`。 然後從入口網站複製「主要金鑰」，並將它貼到 `<your primary key>`。 請務必將您值中的 `<` 和 `>` 移除。

![NoSQL 教學課程用來建立 C# 主控台應用程式之 Azure 入口網站的螢幕擷取畫面。 顯示 Azure Cosmos DB 帳戶，其中 Azure Cosmos DB 帳戶刀鋒視窗上醒目提示 [金鑰]，[金鑰] 刀鋒視窗上則醒目提示 [URI] 和 [主要金鑰] 的值](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>將 DocumentClient 具現化

現在，請建立 **DocumentClient** 的新執行個體。

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>建立資料庫

接著，使用來自 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 之 **DocumentClient** 類別的 [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) 方法或 [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) 方法，來建立 Azure Cosmos DB [資料庫](documentdb-resources.md#databases)。 資料庫是分割給多個集合之 JSON 文件儲存體的邏輯容器。

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>選定分割區索引鍵 

集合是用來儲存文件的容器。 它們是邏輯資源，並且可以[跨一或多個實體分割區](partition-data.md)。 [分割區索引鍵](documentdb-partition-data.md)是您文件內的屬性 (或路徑)，可用來在伺服器或分割區之間散發資料。 具有相同分割區索引鍵的所有文件都會儲存在相同的分割區中。 

判斷分割區索引鍵是在建立集合之前所需進行的重要決策。 分割區索引鍵是您文件內的屬性 (或路徑)，可供 Azure Cosmos DB 用來在多個伺服器或分割區之間散發資料。 Cosmos DB 會將分割區索引鍵值進行雜湊處理，然後使用雜湊的結果來判斷要在其中儲存文件的分割區。 具有相同分割區索引鍵的所有文件都會儲存在相同的分割區中，而且在建立集合之後，即無法變更分割區索引鍵。 

針對本教學課程，我們會將分割區索引鍵設定為 `/deviceId`，讓單一裝置的所有資料都儲存在單一分割區中。 您想要選擇一個擁有大量值的分割區索引鍵，其中每個值的使用頻率都大致相同，以確保 Cosmos DB 可以在資料成長時平衡負載，並且達到該集合的完整輸送量。 

如需有關資料分割的詳細資訊，請參閱[如何在 Azure Cosmos DB 中進行資料分割和調整？](partition-data.md) 

## <a id="CreateColl"></a>建立集合 

既然我們已知道分割區索引鍵 `/deviceId`，我們將使用 **DocumentClient** 類別的 [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) 方法或 [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) 方法來建立[集合](documentdb-resources.md#collections)。 集合是 JSON 文件和任何相關 JavaScript 應用程式邏輯的容器。 

> [!WARNING]
> 建立集合會牽涉到定價，因為您會將輸送量保留供應用程式與 Azure Cosmos DB 通訊使用。 如需更多詳細資料，請瀏覽我們的[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

此方法會對 Azure Cosmos DB 進行 REST API 呼叫，而服務則會根據要求的輸送量佈建一些分割區。 您可以隨著效能需求的演變，使用 SDK 或 [Azure 入口網站](set-throughput.md)來變更集合的輸送量。

## <a id="CreateDoc"></a>建立 JSON 文件
我們將把一些 JSON 文件插入到 Azure Cosmos DB。 您可以使用 **DocumentClient** 類別的 [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) 方法來建立[文件](documentdb-resources.md#documents)。 文件會是使用者定義的 (任意) JSON 內容。 此範例類別包含一個裝置讀取，以及一個對 CreateDocumentAsync 的呼叫，以將新的裝置讀取插入到集合。

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted 
// as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```
## <a name="read-data"></a>讀取資料

我們將使用 ReadDocumentAsync 方法，依文件的分割區索引鍵和識別碼來讀取文件。 請注意，讀取包括 PartitionKey 值 (對應至 REST API 中的 `x-ms-documentdb-partitionkey` 要求標頭)。

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>更新資料

現在，我們將使用 ReplaceDocumentAsync 方法來更新一些資料。

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);
```

## <a name="delete-data"></a>刪除資料

現在，我們將使用 DeleteDocumentAsync 方法，依文件的分割區索引鍵和識別碼來刪除文件。

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>查詢已分割的集合

當您查詢已分割集合中的資料時，Azure Cosmos DB 會自動將查詢路由傳送到與篩選中所指定分割區索引鍵值 (如果有的話) 對應的分割區。 例如，此查詢只會路由傳送至包含資料分割索引鍵 "XMS-0001" 的資料分割。

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
下列查詢沒有根據資料分割索引鍵 (DeviceId) 的篩選，且已展開至對資料分割索引執行它的所有資料分割。 請注意，您必須指定 EnableCrossPartitionQuery (REST API 中的`x-ms-documentdb-query-enablecrosspartition` )，才能讓 SDK 跨資料分割執行查詢。

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

## <a name="parallel-query-execution"></a>平行查詢執行
Azure Cosmos DB DocumentDB SDKs 1.9.0 和更新版本支援平行查詢執行選項，可讓您對已分割的集合執行低延遲查詢 (即使它們需要觸及大量的分割區也一樣)。 例如，下列查詢設定為跨資料分割平行執行。

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
若要管理平行執行查詢，您可以調整下列參數︰

* 藉由設定 `MaxDegreeOfParallelism`，您可以控制平行處理原則的程度，亦即與集合的資料分割同時的網路連線數上限。 如果您將此設定為 -1，平行處理原則的程度是由 SDK 管理。 如果 `MaxDegreeOfParallelism` 未指定或設為 0 (這是預設值)，將會有連往集合資料分割的單一網路連線。
* 您可藉由設定 `MaxBufferedItemCount`，來權衡取捨查詢延遲和用戶端記憶體使用量。 如果您省略這個參數或將此設定為 -1，平行查詢執行期間緩衝處理的項目數是由 SDK 管理。

在相同的集合狀態下，平行查詢會以和序列執行相同的順序傳回結果。 執行包含排序 (ORDER BY 和/或 TOP) 的跨資料分割查詢時，DocumentDB SDK 會跨資料分割發出平行查詢，並合併用戶端中已部分排序的結果來產生全域排序的結果。

## <a name="execute-stored-procedures"></a>執行預存程序
最後，您可以透過將下列程式碼新增到專案中，對具有相同裝置識別碼的文件執行不可部分完成的交易，例如，如果您正在維護單一文件中的彙總或裝置最新狀態。

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```

就這麼簡單！ 這些就是 Azure Cosmos DB 應用程式的主要元件，此應用程式會使用分割區索引鍵來有效率地調整在各個分割區的資料散發。  

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本教學課程所建立的所有資源：

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的唯一名稱。 
2. 在資源群組頁面上，按一下 刪除，在文字方塊中輸入要刪除之資源的名稱，然後按一下刪除。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列操作： 

> [!div class="checklist"]
> * 建立 Azure Cosmos DB 帳戶
> * 建立具有資料分割索引鍵的資料庫和集合
> * 建立 JSON 文件
> * 更新文件
> * 查詢已分割的集合
> * 執行預存程序
> * 刪除文件
> * 刪除資料庫

您現在可以繼續進行到下一個教學課程，以將其他資料匯入到 Cosmos DB 帳戶。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)
