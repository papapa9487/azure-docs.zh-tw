---
title: "使用 Azure Cosmos DB 輸入繫結建立 HTTP 觸發程序 | Microsoft Docs"
description: "了解如何使用 Azure Functions 搭配 HTTP 觸發程序來查詢 Azure Cosmos DB。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: mvc
ms.date: 09/25/2017
ms.author: mimig
ms.openlocfilehash: 86a660309fd3fd80f10f706ff460af2309c12174
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-functions-http-trigger-with-an-azure-cosmos-db-input-binding"></a>使用 Azure Cosmos DB 輸入繫結建立 Azure Functions HTTP 觸發程序

Azure Cosmos DB 是無結構描述又無伺服器的全域散發多模型資料庫。 Azure Function 是無伺服器的計算服務，可讓您視需要執行程式碼。 這兩個 Azure 服務結合起來就形成無伺服器架構的基礎，可讓您專注於建立更好的應用程式，不必為了符合計算和資料庫需求而擔心佈建和維護伺服器。

本教學課程以[適用於 .NET 的圖形 API 快速入門](create-graph-dotnet.md)中建立的程式碼為基礎。 本教學課程會新增包含 [HTTP 觸發程序](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-bindings-http-webhook.md#http-trigger)的 Azure Function。 此 HTTP 觸發程序會使用 Azure Cosmos DB [輸入繫結](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-triggers-bindings.md)，從快速入門中建立的圖形資料庫擷取資料。 這個特定 HTTP 觸發程序會查詢 Azure Cosmos DB 中的資料，但 Azure Cosmos DB 中的輸入繫結可用來擷取您的函式需要的任何資料輸入值。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 建立 Azure Function 專案 
> * 建立 HTTP 觸發程序
> * 發佈 Azure Function
> * 將 Azure Function 連線至 Azure Cosmos DB 資料庫

## <a name="prerequisites"></a>必要條件

- [Visual Studio 2017 15.3 版](https://www.visualstudio.com/vs/preview/)，包括 **Azure 開發**工作負載。

    ![安裝包含 Azure 開發工作負載的 Visual Studio 2017](./media/tutorial-functions-http-trigger/functions-vs-workloads.png)
    
- 安裝或升級至 Visual Studio 2017 版本 15.3 之後，您必須手動更新 Visual Studio 2017 Tools for Azure Functions。 您可以從 [工具] 功能表的 [延伸模組和更新...] 下的 > [更新] > [Visual Studio Marketplace] > [Azure Functions and Web Jobs Tools] > [更新] 更新工具。

- 完成[使用圖形 API 建置 .NET 應用程式](tutorial-develop-graph-dotnet.md)教學課程，或從 [azure-cosmos-db-graph-dotnet-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started) GitHub 存放庫取得範例程式碼並建置專案。
 
## <a name="build-a-function-in-visual-studio"></a>在 Visual Studio 中建置函式

1. 在 [方案總管] 中，以滑鼠右鍵按一下方案節點，然後選擇 [新增] > [新增專案]，將 **Azure Functions** 專案新增至方案。 從對話方塊方塊中選擇 [Azure Functions]，並命名為 **PeopleDataFunctions**。

   ![將 Azure Function 專案新增至方案](./media/tutorial-functions-http-trigger/01-add-function-project.png)

2. 建立 Azure Functions 專案之後，需要執行幾個相關的 NuGet 更新和安裝。 

    a. 為了確保取得最新的 Functions SDK，請使用 NuGet 管理員來更新 **Microsoft.NET.Sdk.Functions** 套件。 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 套件]。 在 [已安裝] 索引標籤中，選取 [Microsoft.NET.Sdk.Functions]，然後按一下 [更新]。

   ![更新 NuGet 套件](./media/tutorial-functions-http-trigger/02-update-functions-sdk.png)

    b. 在 [瀏覽] 索引標籤中，輸入 **azure.graphs** 尋找 **Microsoft.Azure.Graphs** 套件，然後按一下 [安裝]。 此套件包含「圖形 API .NET 用戶端 SDK」。

   ![安裝圖形 API](./media/tutorial-functions-http-trigger/03-add-azure-graphs.png)

    c. 在 瀏覽 索引標籤中，輸入 **mono.csharp** 尋找 **Mono.CSharp** 套件，然後按一下安裝。

   ![安裝 Mono.CSharp](./media/tutorial-functions-http-trigger/04-add-mono.png)

3. [方案總管] 現在應該包含您已安裝的套件，如下所示。 
   
   接下來，我們必須撰寫一些程式碼，因此我們要將新的 **Azure Function** 項目新增至專案。 

    a. 在 [方案總管] 中，以滑鼠右鍵按一下專案節點，然後選擇 [ 新增] >  [新增項目]。   
    b. 在 新增項目 對話方塊中，選取 Visual C# 項目，選取 Azure Function，輸入 **Search** 作為專案名稱，然後按一下新增。  
 
   ![建立名為 Search 的新函式](./media/tutorial-functions-http-trigger/05-add-function.png)

4. Azure Function 會回應 HTTP 要求，因此這裡適合使用 Http 觸發程序範本。
   
   在 [新增 Azure Function] 方塊中，選取 [Http 觸發程序]。 我們也需要「公開」此 Azure Function，因此將 [存取權限] 設為 [匿名]，讓每個人都可以使用。 按一下 [確定] 。

   ![將存取權限設為匿名](./media/tutorial-functions-http-trigger/06-http-trigger.png)

5. 將 Search.cs 新增至 Azure Function 專案之後，請複製這些 **using** 陳述式來取代現有的 using 陳述式：

   ```csharp
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Microsoft.Azure.Documents.Linq;
   using Microsoft.Azure.Graphs;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.Http;
   using Microsoft.Azure.WebJobs.Host;
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Linq;
   using System.Net;
   using System.Net.Http;
   using System.Threading.Tasks;
   ```

6. 接下來，使用下列程式碼取代 Azure Function 的類別程式碼。 此程式碼會針對所有人員，或由 `name` 查詢字串參數所識別的特定人員，而使用圖形 API 來搜尋 Azure Cosmos DB 資料庫。

   ```csharp
   public static class Search
   {
       static string endpoint = ConfigurationManager.AppSettings["Endpoint"];
       static string authKey = ConfigurationManager.AppSettings["AuthKey"];

       [FunctionName("Search")]
       public static async Task<HttpResponseMessage> Run(
           [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
           TraceWriter log)
       {
           log.Info("C# HTTP trigger function processed a request.");

           // the person objects are free-form in structure
           List<dynamic> results = new List<dynamic>();

           // open the client's connection
           using (DocumentClient client = new DocumentClient(
               new Uri(endpoint),
               authKey,
               new ConnectionPolicy
               {
                   ConnectionMode = ConnectionMode.Direct,
                   ConnectionProtocol = Protocol.Tcp
               }))
           {
               // get a reference to the database the console app created
               Database database = await client.CreateDatabaseIfNotExistsAsync(
                   new Database
                   {
                       Id = "graphdb"
                   });

               // get an instance of the database's graph
               DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
                   UriFactory.CreateDatabaseUri("graphdb"),
                   new DocumentCollection { Id = "graphcollz" },
                   new RequestOptions { OfferThroughput = 1000 });

               // build a gremlin query based on the existence of a name parameter
               string name = req.GetQueryNameValuePairs()
                   .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                   .Value;

               IDocumentQuery<dynamic> query = (!String.IsNullOrEmpty(name))
                   ? client.CreateGremlinQuery<dynamic>(graph, string.Format("g.V('{0}')", name))
                   : client.CreateGremlinQuery<dynamic>(graph, "g.V()");

               // iterate over all the results and add them to the list
               while (query.HasMoreResults)
                   foreach (dynamic result in await query.ExecuteNextAsync())
                       results.Add(result);
           }

           // return the list with an OK response
           return req.CreateResponse<List<dynamic>>(HttpStatusCode.OK, results);
       }
   }
   ```

   基本上，此程式碼和植入資料庫的原始主控台應用程式使用相同的連線邏輯，只是加上簡單的查詢來擷取相符記錄。

## <a name="debug-the-azure-function-locally"></a>在本機偵錯 Azure 函式

現在程式碼已完成，您可以使用 Azure Function 的本機偵錯工具和模擬器，在本機執行程式碼來進行測試。

1. 您必須以 Azure Cosmos DB 連線資訊將程式碼設定在本機執行，程式碼才能正確執行。 您可以使用 local.settings.json 檔案將 Azure Function 設定在本機執行，就像使用 App.config 檔案來設定如何執行原始主控台應用程式一樣。

    若要這樣做，請將下列程式碼新增至 local.settings.json，然後複製 GraphGetStarted 專案的 App.Config 檔案中的 Endpoint 和 AuthKey，如下圖所示。

   ```json
    "Endpoint": "",
    "AuthKey": ""
    ```

   ![在 local.settings.json 檔案中設定端點和授權金鑰](./media/tutorial-functions-http-trigger/07-local-functions-settings.png)

2. 將啟始專案變更為新的 Functions 應用程式。 在 [方案總管] 中，以滑鼠右鍵按一下 [PeopleDataFunctions]，然後選取 [設定為啟始專案]。

3. 在 方案總管 中，以滑鼠右鍵按一下 **PeopleDataFunctions** 專案中的 相依性，然後按一下新增參考。 從清單中，選取 System.Configuration，然後按一下確定。

3. 現在可以執行應用程式。 Azure Function 程式碼已裝載且可供使用，請按 F5 以啟動本機偵錯工具 func.exe。

   在 func.exe 初始輸出結束時，我們看到 Azure Function 裝載於 localhost:7071。 這有助於在用戶端測試此應用程式。

   ![測試用戶端](./media/tutorial-functions-http-trigger/08-functions-emulator.png)

4. 若要測試 Azure Function，請使用 [Visual Studio Code](http://code.visualstudio.com/) 搭配 Huachao Mao 的擴充：[REST 用戶端](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)。 REST 用戶端支援按一下滑鼠右鍵來傳送本機或遠端 HTTP 要求。 

    若要這樣做，請建立名為 test-function-locally.http 的新檔案，並新增下列程式碼：

    ```http
    get http://localhost:7071/api/Search

    get http://localhost:7071/api/Search?name=ben
   ```

    現在，以滑鼠右鍵按一下第一行程式碼，然後選取 [傳送要求]，如下圖所示。

   ![從 Visual Studio 程式碼傳送 REST 要求](./media/tutorial-functions-http-trigger/09-rest-client-in-vs-code.png)

   您會看到本機執行的 Azure Function 標頭、JSON 本文內容及所有項目傳來的原始 HTTP 回應。

   ![REST 回應](./media/tutorial-functions-http-trigger/10-general-results.png)

5. 現在，選取第二行程式碼，然後選取 [傳送要求]。 我們可以新增 `name` 查詢字串參數並指定已知在資料庫中存在的值，以篩選 Azure Function 傳回的結果。

   ![篩選 Azure Function 的結果](./media/tutorial-functions-http-trigger/11-search-for-ben.png)

驗證 Azure Function 且看似正常運作之後，最後一步就是發佈到 Azure App Service 並設定為在雲端中執行。

## <a name="publish-the-azure-function"></a>發佈 Azure 函式

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [發佈]。

   ![發佈新的專案](./media/tutorial-functions-http-trigger/12-publish-function.png)

2. 我們已準備好發佈至雲端，在公開情節下進行測試。 在 發佈 索引標籤上，選取 Azure Function 應用程式，選取 新建 以在您的 Azure 訂用帳戶中建立 Azure Function，然後按一下發佈。

   ![建立新的 Azure Function 應用程式](./media/tutorial-functions-http-trigger/13-publish-panel.png)

3. 在 [發佈] 對話方塊中，執行下列動作：
   
    a. 在 [應用程式名稱] 中，提供函式的唯一名稱。

    b. 在 [訂用帳戶] 中，選取要使用的 Azure 訂用帳戶。
   
    c. 在 [資源群組] 中，建立新的資源群組，並使用與應用程式名稱相同的名稱。
   
    d. 在 [App Service 方案] 中，按一下 [新增]，建立以消費為基礎的新 App Service 方案，因為我們對無伺服器 Azure Function 採用計次付費方法。 在 設定 App Service 方案 頁面上使用預設值，然後按一下確定。
   
    e. 在 [儲存體帳戶] 中，也按一下 [新增] 來建立可用於 Azure Function 的新儲存體帳戶，以防我們需要支援 Blob、資料表或佇列來觸發執行其他功能。 在 儲存體帳戶 頁面上使用預設值，然後按一下確定。

    f. 然後按一下對話方塊中的 [建立] 按鈕，在您的 Azure 訂用帳戶中建立所有資源。 Visual Studio 會下載發行設定檔 (簡單的 XML 檔案)，並在您下次發佈 Azure Function 程式碼時使用。

   ![建立儲存體帳戶](./media/tutorial-functions-http-trigger/14-new-function-app.png)

    Visual Studio 接著會顯示 [發佈] 頁面，讓您在變更 Function 且需要重新發佈時使用。 您目前不需要對該頁面採取任何動作。

4. 發佈 Azure Function 之後，您可以前往 [Azure 入口網站](https://portal.azure.com/)頁面來設定您的 Azure Function。 在那裡，您可以看到 Azure Function 的 [應用程式設定] 連結。 開啟此連結，以您的個人資料設定即時 Azure Function 來連線至 Azure Cosmos DB 資料庫。

   ![檢閱應用程式設定](./media/tutorial-functions-http-trigger/15-function-in-portal.png)

5. 就如同您稍早在主控台應用程式的 App.config 檔案和 Azure Function 應用程式的 local.settings.json 檔案中所做的一樣，您必須將 Azure Cosmos DB 資料庫的 Endpoint 和 AuthKey 新增至已發佈的函式。 如此一來，您永遠不必簽入包含金鑰的設定程式碼。您可以在入口網站中設定金鑰，並確定金鑰不是儲存在原始檔控制中。 若要新增每個值，請按一下 [新增設定] 按鈕，從 app.config 新增 **Endpoint** 和您的值，然後再按一次 [新增設定]，並新增 **AuthKey** 和您的自訂值。 新增並儲存值之後，您的設定看起來應該如下所示。

   ![設定 Endpoint 和 AuthKey](./media/tutorial-functions-http-trigger/16-app-settings.png)

6. 在您的 Azure 訂用帳戶中正確設定 Azure Function 之後，您同樣可以使用 Visual Studio Code REST 用戶端擴充來查詢公開的 Azure Function URL。 將下列兩行程式碼新增至 test-function-locally.http，然後執行每一行來測試此函式。 以您的函式名稱取代 URL 中的函式名稱。

    ```json
    get https://peoplesearchfunction.azurewebsites.net/api/Search

    get https://peoplesearchfunction.azurewebsites.net/api/Search?name=thomas
    ```

    此函式會使用從 Azure Cosmos DB 擷取的資料來回應。

    ![使用 REST 用戶端來查詢 Azure Function](./media/tutorial-functions-http-trigger/17-calling-function-from-code.png)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列操作：

> [!div class="checklist"]
> * 建立 Azure Function 專案 
> * 建立 HTTP 觸發程序
> * 發佈 Azure Function
> * 將 Function 連線至 Azure Cosmos DB 資料庫

您現在可以繼續進行＜概念＞一節了解有關 Cosmos DB 的詳細資訊。

> [!div class="nextstepaction"]
> [全球發佈](distribute-data-globally.md) 

本文是以 [Brady Gaster 的無結構描述和無伺服器](http://www.bradygaster.com/category/%20Serverless%20&%20Schemaless) 部落格系列中的一個部落格為基礎。 請前往他的部落格來閱讀系列中的其他文章。
