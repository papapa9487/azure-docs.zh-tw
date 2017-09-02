---
title: "Azure Cosmos DB：使用 .NET 搭配圖形 API 進行開發 | Microsoft Docs"
description: "了解如何使用 .NET 搭配 Azure Cosmos DB 的 DocumentDB API 進行開發"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 94909fd1db426267eb60e5d7f4d753de82ca0377
ms.contentlocale: zh-tw
ms.lasthandoff: 05/31/2017

---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB：使用 .NET 搭配圖形 API 進行開發
Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本教學課程示範如何使用 Azure 入口網站來建立 Azure Cosmos DB 帳戶，以及如何建立圖形資料庫和容器。 應用程式會接著使用[圖形 API](graph-sdk-dotnet.md) (預覽) 來建立一個含有 4 個人的簡單社交網路，然後使用 Gremlin 來周遊及查詢圖形。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 建立 Azure Cosmos DB 帳戶 
> * 建立圖形資料庫和容器
> * 將頂點和邊緣序列化為 .NET 物件
> * 新增頂點和邊緣
> * 使用 Gremlin 來查詢圖形

## <a name="graphs-in-azure-cosmos-db"></a>Azure Cosmos DB 中的圖形
您可以使用 Azure Cosmos DB 搭配 [Microsoft.Azure.Graphs](graph-sdk-dotnet.md) 程式庫來建立、更新及查詢圖形。 Microsoft.Azure.Graph 程式庫在 `DocumentClient` 類別上提供單一擴充方法 `CreateGremlinQuery<T>` 來執行 Gremlin 查詢。

Gremlin 是一個函式程式設計語言，可支援寫入作業 (DML) 及查詢和周遊作業。 本文中涵蓋幾個範例來協助您開始使用 Gremlin。 如需 Azure Cosmos DB 中可用之 Gremlin 功能的詳細逐步解說，請參閱 [Gremlin 查詢](gremlin-support.md)。 

## <a name="prerequisites"></a>必要條件
請確定您具有下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。 
    * 或者，您可以使用 [Azure DocumentDB 模擬器](local-emulator.md)來進行本教學課程。
* [Visual Studio](http://www.visualstudio.com/)。

## <a name="create-database-account"></a>建立資料庫帳戶

我們將從在 Azure 入口網站中建立 Azure Cosmos DB 帳戶開始著手。  

> [!TIP]
> * 已經有 Azure Cosmos DB 帳戶？ 如果是，請直接跳到[設定您的 Visual Studio 方案](#SetupVS)
> * 您是否已有 Azure DocumentDB 帳戶？ 如果是，您的帳戶現在會是 Azure Cosmos DB 帳戶，且您可以直接跳到[設定您的 Visual Studio 方案](#SetupVS)。  
> * 如果您使用「Azure Cosmos DB 模擬器」，請依照 [Azure Cosmos DB 模擬器](local-emulator.md)的步驟來設定模擬器，然後直接跳到[設定您的 Visual Studio 方案](#SetupVS)。 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>設定您的 Visual Studio 方案
1. 在電腦上開啟 **Visual Studio**。
2. 從 [檔案] 功能表中，選取 [新增]，然後選擇 [專案]。
3. 在 [新增專案]  對話方塊中，選取 [範本] / [Visual C#] / [主控台應用程式 (.NET Framework)]、為專案命名，然後按一下 [確定]。
4. 在 [方案總管] 中，以滑鼠右鍵按一下 Visual Studio 方案底下的新主控台應用程式，然後按一下 [管理 NuGet 套件...]
5. 在 [NuGet] 索引標籤中，按一下 [瀏覽]，然後在搜尋方塊中輸入 **Microsoft.Azure.Graphs**，接著選取 [包含發行前版本]。
6. 在結果中尋找 **Microsoft.Azure.Graphs**，然後按一下 [安裝]。
   
   如果您收到關於檢閱方案變更的訊息，請按一下 [確定]。 如果您收到關於接受授權的訊息，請按一下 [我接受]。
   
    `Microsoft.Azure.Graphs` 程式庫提供單一的擴充方法 `CreateGremlinQuery<T>` 來執行 Gremlin 作業。 Gremlin 是一個函式程式設計語言，可支援寫入作業 (DML) 及查詢和周遊作業。 本文中涵蓋幾個範例來協助您開始使用 Gremlin。 如需 Azure Cosmos DB 中 Gremlin 功能的詳細逐步解說，請參閱 [Gremlin 查詢](gremlin-support.md)。

## <a id="add-references"></a>連接您的應用程式

在應用程式中新增下列兩個常數和您的「用戶端」變數。 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
接著，返回 [Azure 入口網站](https://portal.azure.com)以擷取您的端點 URL 和主要金鑰。 必須提供端點 URL 和主要金鑰，您的應用程式才能了解要在連接到哪裡，而 Azure Cosmos DB 才會信任您的應用程式連接。 

在 Azure 入口網站中，瀏覽至您的 Azure Cosmos DB 帳戶，按一下 [金鑰]，然後按一下 [讀寫金鑰]。 

從入口網站中複製 URI，然後將它貼到上述端點屬性中的 `Endpoint`。 然後從入口網站複製「主要金鑰」，並將它貼到上述的 `AuthKey` 屬性。 

![本教學課程用來建立 C# 應用程式的 Azure 入口網站螢幕擷取畫面。 顯示 Azure Cosmos DB 帳戶，其中 Azure Cosmos DB 瀏覽區上醒目提示 [金鑰] 按鈕，[金鑰] 刀鋒視窗上則醒目提示 [URI] 和 [主要金鑰] 的值 [keys] 
 
## <a id="instantiate"></a>將 DocumentClient 具現化 
接著，建立 **DocumentClient** 的新執行個體。  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>建立資料庫 

現在，使用來自 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 之 **DocumentClient** 類別的 [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) 方法或 [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) 方法，來建立 Azure Cosmos DB [資料庫](documentdb-resources.md#databases)。  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>建立圖形 

接著，使用 **DocumentClient** 類別的 [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) 方法或 [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) 方法來建立圖形容器。 集合是圖形實體的容器。 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 1000 }); 
``` 

## <a id="serializing"></a>將頂點和邊緣序列化為 .NET 物件
Azure Cosmos DB 使用 [GraphSON 電傳格式](gremlin-support.md)，此格式會定義頂點、邊緣及屬性的 JSON 結構描述。 Azure Cosmos DB .NET SDK 包含 JSON.NET 作為相依性，這可讓我們將 GraphSON 序列化/還原序列化為我們可以在程式碼中使用的 .NET 物件。

我們將使用一個包含 4 個人的簡單社交網路來作為範例。 我們將著眼在如何建立 `Person` 頂點、在它們之間新增 `Knows` 關聯性，然後查詢及周遊圖形以找出「朋友的朋友」關聯性。 

`Microsoft.Azure.Graphs.Elements` 命名空間提供 `Vertex`、`Edge`、`Property` 及 `VertexProperty` 類別來將 GraphSON 回應還原序列化為已妥善定義的 .NET 物件。

## <a name="run-gremlin-using-creategremlinquery"></a>使用 CreateGremlinQuery 來執行 Gremlin
Gremlin (例如 SQL) 支援讀取、寫入及查詢作業。 例如，下列程式碼片段說明如何建立頂點、邊緣、使用 `CreateGremlinQuery<T>` 來執行一些範例查詢，以及使用 `ExecuteNextAsync` 和 HasMoreResults 以非同步方式逐一查看這些結果。

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
{
    { "Cleanup",        "g.V().drop()" },
    { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
    { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
    { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
    { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
    { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
    { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
    { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
    { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
    { "CountVertices",  "g.V().count()" },
    { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
    { "Project",        "g.V().hasLabel('person').values('firstName')" },
    { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>新增頂點和邊緣

讓我們看看上一節所示的 Gremlin 陳述式，以獲得更多詳細資料。 首先，我們會使用 Gremlin 的 `addV` 方法來新增一些頂點。 例如，下列程式碼片段會建立一個 "Person" 類型的 "Thomas Andersen" 頂點，其中具有名字、姓氏及年齡的屬性。

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas')");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

然後，我們會使用 Gremlin 的 `addE` 方法在這些頂點之間建立一些邊緣。 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

我們可以使用 Gremlin 中的 `properties` 步驟來更新現有的頂點。 針對其餘範例，我們會略過對透過 `HasMoreResults` 和 `ExecuteNextAsync` 執行查詢的呼叫。

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

您可以使用 Gremlin 的 `drop` 步驟來卸除邊緣和頂點。 以下是一個示範如何刪除頂點和邊緣的程式碼片段。 請注意，卸除頂點時，會將關聯的邊緣一連串地刪除。

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>查詢圖形

您也可以使用 Gremlin 來執行查詢和周遊。 例如，下列程式碼片段會示範如何計算圖形中的頂點數目：

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
您可以使用 Gremlin 的 `has` 和 `hasLabel` 步驟，然後使用 `and`、`or` 及 `not` 來結合它們以建置更複雜的篩選：

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

您可以使用 `values` 步驟來投射查詢結果中的某些屬性：

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

到目前為止，我們只看到可在任何資料庫中運作的查詢運算子。 當您需要瀏覽至相關的邊緣和頂點時，圖形對周遊作業來說既快速又有效率。 我們將尋找 Thomas 的所有朋友。 我們的做法是使用 Gremlin 的 `outE` 步驟來尋找來自 Thomas 的外邊緣，然後使用 Gremlin 的 `inV` 步驟來周遊至來自這些邊緣的內頂點：

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

下一個查詢會透過呼叫 `outE` 和 `inV` 兩次來執行兩次跳躍，以找出 Thomas 的所有「朋友的朋友」。 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

您可以使用 Gremlin 來建置更複雜的查詢和實作強大的圖形周遊邏輯，包括混合篩選條件運算式、使用 `loop` 步驟來執行迴圈，以及使用 `choose` 步驟來實作條件式瀏覽。 深入了解您可以透過 [Gremlin 支援](gremlin-support.md)來執行哪些操作！

這樣就大功告成了，您已完成此 Azure Cosmos DB 教學課程！ 

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本教學課程所建立的所有資源。  

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列操作：

> [!div class="checklist"]
> * 建立 Azure Cosmos DB 帳戶 
> * 建立圖形資料庫和容器
> * 將頂點和邊緣序列化為 .NET 物件
> * 新增頂點和邊緣
> * 使用 Gremlin 來查詢圖形

您現在可以使用 Gremlin 來建置更複雜的查詢和實作強大的圖形周遊邏輯。 

> [!div class="nextstepaction"]
> [使用 Gremlin 進行查詢](tutorial-query-graph.md)

