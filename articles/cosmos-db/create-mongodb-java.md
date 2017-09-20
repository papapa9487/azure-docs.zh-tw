---
title: "Azure CosmosDB︰使用 Java 和 MongoDB API 建置主控台應用程式 | Microsoft Docs"
description: "提供 Java 程式碼範例，您可用來連線及查詢 Azure Cosmos DB MongoDB API"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 1683afd842294b3b45ae4d0e53bbecdccadc1ed5
ms.contentlocale: zh-tw
ms.lasthandoff: 05/31/2017

---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-java-and-the-azure-portal"></a>Azure CosmosDB︰使用 Java 和 Azure 入口網站建置 MongoDB API 主控台應用程式

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門會示範如何使用 Azure 入口網站建立 Azure Cosmos DB 帳戶、文件資料庫和集合。 您會接著建置和部署以 [MongoDB Java 驅動程式](https://docs.mongodb.com/ecosystem/drivers/java/)為基礎的主控台應用程式。 

## <a name="prerequisites"></a>必要條件

您必須具備下列必要條件，才能執行此範例：
* JDK 1.7+ (如果您沒有 JDK 則執行 `apt-get install default-jdk`)
* Maven (如果您沒有 Maven 則執行 `apt-get install maven`)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>新增集合

將新資料庫命名為 **db**，以即將新集合命名為 **coll**。

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 Github 複製 MongoDB API 應用程式、設定連接字串，然後執行它。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟 Git 終端機視窗 (例如 Git Bash)，然後使用 `cd` 來切換到工作目錄。  

2. 執行下列命令來複製範例存放庫。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. 然後在 Visual Studio 中開啟方案檔案。 

## <a name="review-the-code"></a>檢閱程式碼

讓我們快速檢閱應用程式中所發生的事情。 請開啟 `Program.cs` 檔案，您會發現這些程式碼行建立 Azure Cosmos DB 資源。 

* 已初始化 DocumentClient。

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* 已建立新的資料庫和集合。

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* 已使用 `MongoCollection.insertOne` 插入一些文件

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* 已使用 `MongoCollection.find` 執行一些查詢

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。

1. 從 [帳戶] 選取 [快速入門]，選取 Java，然後將連接字串複製到剪貼簿

2. 開啟 `Program.java` 檔案，以連接字串取代 MongoClientURI 建構函式的引數。 您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 
    
## <a name="run-the-console-app"></a>執行主控台應用程式

1. 在終端機中執行 `mvn package` 以安裝必要的 npm 模組

2. 在終端機中執行 `mvn exec:java -D exec.mainClass=GetStarted.Program` 來啟動您的 Java 應用程式。

您現在可以使用 [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) 來查詢、修改及處理這項新資料。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源：

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 建立集合，以及如何執行主控台應用程式。 您現在可以將其他資料匯入到 Cosmos DB 帳戶。 

> [!div class="nextstepaction"]
> [將 MongoDB 資料匯入到 Azure Cosmos DB](mongodb-migrate.md)



