---
title: "使用 Java 建立 Azure Cosmos DB 文件資料庫 | Microsoft Docs'"
description: "提供 Java 程式碼範例，您可用來連線及查詢 Azure Cosmos DB DocumentDB API"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 08/02/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: df1a25d703a7b8082bdabb4f7d593cb005d416fe
ms.contentlocale: zh-tw
ms.lasthandoff: 08/17/2017

---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB︰使用 Java 和 Azure 入口網站建立文件資料庫

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門會使用 Azure Cosmos DB 適用的 Azure 入口網站工具建立文件資料庫。 本快速入門也會顯示如何使用 [DocumentDB Java API](documentdb-sdk-java.md) 快速建立 Java 主控台應用程式。 本快速入門中的指示可運用在任何足以執行 Java 應用程式的作業系統上。 完成本快速入門，您就會熟悉在 UI 中或以程式設計的方式建立和修改文件資料庫資源 (不論您偏好哪種方式)。

## <a name="prerequisites"></a>必要條件

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * 在 Ubuntu 上，執行 `apt-get install default-jdk` 來安裝 JDK。
    * 務必設定 JAVA_HOME 環境變數，以指向 JDK 安裝所在的資料夾。
* [下載](http://maven.apache.org/download.cgi)和[安裝 ](http://maven.apache.org/install.html) [Maven](http://maven.apache.org/) 二進位封存檔
    * 在 Ubuntu 上，您可以執行 `apt-get install maven` 來安裝 Maven。
* [Git](https://www.git-scm.com/)
    * 在 Ubuntu 上，您可以執行 `sudo apt-get install git` 來安裝 Git。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

您必須先使用 Azure Cosmos DB 建立 SQL (DocumentDB) 資料庫帳戶，才可以建立文件資料庫。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>新增集合

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>新增範例資料

您現在可以使用 [資料總管] 將資料新增至您的新集合。

1. 在 [資料總管] 中，新的資料庫會出現在 [集合] 窗格中。 依序展開 [工作] 資料庫、[項目] 集合，按一下 [文件]，然後按一下 [新增文件]。 

   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
2. 現在將文件新增至具有下列結構的集合。

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. 將 json 新增至 [文件] 索引標籤後，按一下 [儲存]。

    ![將 json 資料複製在 Azure 入口網站的 [資料總管] 中並按一下 [儲存]](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-save-document.png)

4.  多建立並儲存一份文件，以便在其中插入 `id` 屬性的唯一值，並適當變更其他屬性。 新文件可擁有您想要的任何結構，因為 Azure Cosmos DB 不會對您的資料強加任何結構描述。

     現在按一下 [編輯篩選條件] 和 [套用篩選條件] 按鈕，即可在 [資料總管] 中使用查詢來擷取您的資料。 [資料總管] 預設會使用 `SELECT * FROM c` 來擷取集合中的所有文件，但您可以將其變更為不同的 [SQL 查詢](documentdb-sql-query.md) (例如 `SELECT * FROM c ORDER BY c._ts DESC`)，以根據時間戳記按遞減順序傳回所有的文件。 
 
     您也可以使用 [資料總管] 來建立預存程序、UDF 和觸發程序，以執行伺服器端商務邏輯及調整輸送量。 [資料總管] 會公開 API 中所有可用的內建程式設計資料存取，但可讓您在 Azure 入口網站中輕鬆存取您的資料。

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在讓我們切換為使用程式碼。 我們會從 GitHub 複製 DocumentDB API 應用程式、設定連接字串，然後執行它。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟 Git 終端機視窗 (例如 Git Bash)，然後使用 `CD` 來切換到工作目錄。  

2. 執行下列命令來複製範例存放庫。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>檢閱程式碼

讓我們快速檢閱應用程式中所發生的事情。 開啟 \src\GetStarted 資料夾中的 `Program.java` 檔案，並尋找建立 Azure Cosmos DB 資源的這些程式碼行。 

* 已初始化 `DocumentClient`。

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* 已建立新資料庫。

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* 已建立新集合。

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* 已建立一些文件。

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* 已透過 JSON 執行 SQL 查詢。

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。 這可讓您的應用程式與裝載的資料庫通訊。

1. 在 [Azure 入口網站](http://portal.azure.com/)中，於您 Azure Cosmos DB 帳戶的左側瀏覽區中，按一下 [金鑰]，然後按一下 [讀寫金鑰]。 在下一個步驟中，您將使用畫面右側的複製按鈕，將 URI 和主要金鑰複製到 `Program.java` 檔案。

    ![在 Azure 入口網站的 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/create-documentdb-dotnet/keys.png)

2. 在開啟的 `Program.java` 檔案中，從入口網站複製您的 URI 值 (使用 [複製] 按鈕)，並使它成為 `Program.java` 中 DocumentClient 建構函式的端點值。 

    `"https://FILLME.documents.azure.com"`

4. 然後，從入口網站複製您的主要金鑰值並將它貼在 “FILLME” 上，使其成為 DocumentClient 建構函式中的第二個參數。 您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 
    
## <a name="run-the-app"></a>執行應用程式

1. 在 git 終端機視窗中，`cd` 至 azure-cosmos-db-documentdb-java-getting-started 資料夾。

2. 在 git 終端機視窗中，輸入 `mvn package` 以安裝必要的 Java 套件。

3. 在 git 終端機視窗中，於終端機視窗中執行 `mvn exec:java -D exec.mainClass=GetStarted.Program` 以啟動您的 Java 應用程式。

    在終端機視窗中，您會收到表示已建立 FamilyDB 資料庫的通知，並按任一鍵繼續。 按任一鍵來建立資料庫，然後切換到 [資料總管]，您會看到它現在包含 FamilyDB 資料庫。 繼續按按鍵來建立集合和文件，然後執行查詢。 當專案完成時，便會從您的帳戶中刪除資源。 

    ![在 Azure 入口網站的 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源：

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 [資料總管] 來建立 Azure Cosmos DB 帳戶、文件資料庫和集合，以及如何執行應用程式來以程式設計方式執行同樣的作業。 您現在可以將其他資料匯入到 Cosmos DB 帳戶。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)



