---
title: "使用圖形 API 來建置 Azure Cosmos DB Node.js 應用程式 | Microsoft Docs"
description: "提供可用來連線及查詢 Azure Cosmos DB 的 Node.js 程式碼範例。"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/29/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 60cb187cf40f72fce86c421891bea02d3d6d708a
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB：使用圖形 API 來建置 Node.js 應用程式

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門文章會示範如何使用 Azure 入口網站建立適用於圖形 API (預覽) 的 Azure Cosmos DB 帳戶、資料庫和圖形。 您會接著使用開放原始碼 [Gremlin Node.js](https://www.npmjs.com/package/gremlin) 驅動程式來建置和執行主控台應用程式。  

## <a name="prerequisites"></a>必要條件

您必須具備下列必要條件，才能執行此範例：
* [Node.js](https://nodejs.org/en/) 0.10.29 版或更新版本
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>新增圖形

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 Github 複製圖形 API 應用程式、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟 Git 終端機視窗 (例如 Git Bash)，然後變更 (透過 `cd` 命令) 到工作目錄。  

2. 執行下列命令來複製範例存放庫。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. 在 Visual Studio 中開啟解決方案檔案。 

## <a name="review-the-code"></a>檢閱程式碼

讓我們快速檢閱應用程式中發生了什麼。 請開啟 `app.js` 檔案，您會發現下列程式碼。 

* 已建立 Gremlin 用戶端。

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

  這些組態都位於我們下一節編輯的 `config.js`。

* 已使用 `client.execute` 方法執行一系列的 Gremlin 步驟。

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>更新您的連接字串

1. 更新 config.js 檔案。 

2. 在 config.js 中，使用 Azure 入口網站的 [概觀] 頁面中的 [Gremlin URI] 值填入 config.endpoint 金鑰。 

    `config.endpoint = "GRAPHENDPOINT";`

    ![在 Azure 入口網站的 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/create-graph-nodejs/gremlin-uri.png)

   如果 [Gremlin URI] 值空白，您可以從入口網站的 [金鑰] 頁面產生此值，方法是使用 [URI]，移除https://，然後將文件變更為圖形。

   Gremlin 端點必須是沒有通訊協定/連接埠號碼的主機名稱，像是 `mygraphdb.graphs.azure.com` (而不是 `https://mygraphdb.graphs.azure.com` 或 `mygraphdb.graphs.azure.com:433`)。

3. 在 config.js 中，使用 Azure 入口網站的 [金鑰] 頁面中的 [主要金鑰] 值填入 config.primaryKey 值。 

    `config.primaryKey = "PRIMARYKEY";`

   ![[Azure 入口網站索引鍵] 刀鋒視窗](./media/create-graph-nodejs/keys.png)

4. 針對 config.database 和 config.collection 的值，輸入資料庫名稱和圖形 (容器) 名稱。 

您完成的 config.js 檔案範例應如下所示︰

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "testgraphacct.graphs.azure.com";
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>執行主控台應用程式

1. 開啟終端機視窗並變更 (透過 `cd` 命令) 至專案內含 package.json 檔案的安裝目錄。  

2. 執行 `npm install` 以安裝必要的 npm 模組，包括 `gremlin`。

3. 在終端機中執行 `node app.js` 來啟動您的節點應用程式。

## <a name="browse-with-data-explorer"></a>使用資料總管進行瀏覽

您現在可以回到 Azure 入口網站中的 [資料總管]，檢視、查詢、修改並使用新的圖形資料。

在 [資料總管] 中，新的資料庫會出現在 [圖形] 窗格中。 展開資料庫，後面接著該集合，然後按一下 [圖形]。

當您按一下 [套用篩選條件] 時，範例應用程式所產生的資料會顯示在 [圖形] 索引標籤內的下一個窗格中。

試著使用 `.has('firstName', 'Thomas')` 完成 `g.V()` 以測試篩選條件。 請注意此值會區分大小寫。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請將您在本文章中建立的所有資源刪除，方法是執行下列動作： 

1. 在 Azure 入口網站的左側導覽功能表中，按一下 [資源群組]，然後按一下您所建立之資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]、輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本文章中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 來建立圖形，以及如何執行應用程式。 您現在可以使用 Gremlin 來建置更複雜的查詢，以及實作強大的圖形周遊邏輯。 

> [!div class="nextstepaction"]
> [使用 Gremlin 進行查詢](tutorial-query-graph.md)

