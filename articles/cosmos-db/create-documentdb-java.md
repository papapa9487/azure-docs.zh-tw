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
ms.custom: quick start connect, mvc, devcenter
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 10/16/2017
ms.author: mimig
ms.openlocfilehash: 7f59394fdb51c59be65e51fa1a4594b947635ace
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2017
---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB︰使用 Java 和 Azure 入口網站建立文件資料庫

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可使用 Azure Cosmos DB 快速地建立和查詢受管理的文件、資料表及圖形資料庫。

本快速入門會使用 Azure Cosmos DB 適用的 Azure 入口網站工具建立文件資料庫。 本快速入門也會顯示如何使用 [DocumentDB Java API](documentdb-sdk-java.md) 快速建立 Java 主控台應用程式。 本快速入門中的指示可運用在任何足以執行 Java 應用程式的作業系統上。 完成本快速入門，您就會熟悉如何在 UI 中或以程式設計的方式，建立和修改文件資料庫資源 (不論您偏好哪種方式)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

此外： 

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * 在 Ubuntu 上，執行 `apt-get install default-jdk` 來安裝 JDK。
    * 務必設定 JAVA_HOME 環境變數，以指向 JDK 安裝所在的資料夾。
* [下載](http://maven.apache.org/download.cgi)和[安裝 ](http://maven.apache.org/install.html) [Maven](http://maven.apache.org/) 二進位封存檔
    * 在 Ubuntu 上，您可以執行 `apt-get install maven` 來安裝 Maven。
* [Git](https://www.git-scm.com/)
    * 在 Ubuntu 上，您可以執行 `sudo apt-get install git` 來安裝 Git。

## <a name="create-a-database-account"></a>建立資料庫帳戶

您必須先使用 Azure Cosmos DB 建立 SQL (DocumentDB) 資料庫帳戶，才可以建立文件資料庫。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>新增集合

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>新增範例資料

您現在可以使用 [資料總管] 將資料新增至您的新集合。

1. 展開 [項目] 集合，按一下 [文件] > [新文件]。

   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/create-documentdb-java/azure-cosmosdb-data-explorer-new-document.png)
  
2. 現在將文件新增至具有下列結構的集合，然後按一下 [儲存]。

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

    ![將 json 資料複製在 Azure 入口網站的 [資料總管] 中並按一下 [儲存]](./media/create-documentdb-java/azure-cosmosdb-data-explorer-save-document.png)

3.  多建立並儲存一份文件，將其中的 `id` 變更為 2，並適當變更其他屬性。 新文件可擁有您想要的任何結構，因為 Azure Cosmos DB 不會對您的資料強加任何結構描述。

## <a name="query-your-data"></a>查詢資料

您現可在 [資料總管] 中，使用查詢來擷取和篩選您的資料。

1. 依預設，此查詢會設為 `SELECT * FROM c`。 此預設查詢會擷取並顯示集合中的所有文件。 

    ![[資料總管] 中的預設查詢為 `SELECT * FROM c`](./media/create-documentdb-java/azure-cosmosdb-data-explorer-query.png)

2. 若要變更查詢，您可按一下 [編輯篩選條件] 按鈕、新增 `ORDER BY c._ts DESC` 至查詢述詞方塊，然後再按一下 [套用篩選條件]。

    ![新增 ORDER BY c._ts DESC 並按一下 [套用篩選]，以變更預設查詢](./media/create-documentdb-java/azure-cosmosdb-data-explorer-edit-query.png)

此經過修改的查詢清單會根據文件的時間戳記，依遞減順序列出文件，因此現會最先列出您的第二份文件。 若您熟悉 SQL 語法，則可在此方塊中輸入任一受支援的 [SQL 查詢](documentdb-sql-query.md)。 

其會在 [資料總管] 中完成工作。 繼續使用程式碼之前，請注意您也可以使用 [資料總管] 來建立預存程序、UDF 和觸發程序，以執行伺服器端商務邏輯及調整輸送量。 [資料總管] 會公開 API 中所有可用的內建程式設計資料存取，但可讓您在 Azure 入口網站中輕鬆存取您的資料。

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在讓我們切換為使用程式碼。 我們會從 GitHub 複製 DocumentDB API 應用程式、設定連接字串，然後執行它。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟 git 終端機視窗 (例如 git bash)，並使用 `cd` 命令變更至要安裝範例應用程式的資料夾。 

    ```bash
    cd "C:\git-samples"
    ```

2. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 這些程式碼片段皆是取自以下資料夾中安裝的 `Program.java` 檔案：C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted。 或者也可以直接跳至[更新您的連接字串](#update-your-connection-string)。 

* `DocumentClient` 初始化。 [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) 提供適用於 Azure Cosmos DB 資料庫服務的用戶端邏輯表示法。 此用戶端會用於設定和執行針對服務的要求。

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* [資料庫](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._database)建立。

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection) 建立。

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* 使用 [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument) 方法建立文件。

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* 使用 [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments) 方法，透過 JSON 執行 SQL 查詢。

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

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。 這可讓您的應用程式與託管資料庫進行通訊。

1. 在 [Azure 入口網站](http://portal.azure.com/)中，按一下 [金鑰]。 

    使用畫面右方的複製按鈕來複製最高值，亦即 URI。

    ![在 Azure 入口網站的 [金鑰] 頁面中，檢視並複製存取金鑰](./media/create-documentdb-java/keys.png)

2. 從以下資料夾中開啟 `Program.java` 檔案：C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted。 

3. 從入口網站將 URI 值貼上至 `https://FILLME.documents.azure.com` 的行 45。

4. 返回入口網站並複製 PRIMARY KEY 值，如螢幕擷取畫面中所示。 從入口網站將 PRIMARY KEY 值貼上至 `FILLME` 的行 46。

    getStartedDemo method 現應如下所示： 
    
    ```java
    private void getStartedDemo() throws DocumentClientException, IOException {
        this.client = new DocumentClient("https://youraccountname.documents.azure.com:443/",
                "your-primary-key...RJhQrqQ5QQ==", 
                new ConnectionPolicy(),
                ConsistencyLevel.Session);
    ```

## <a name="run-the-app"></a>執行應用程式

1. 在 git 終端機視窗中，`cd` 至 azure-cosmos-db-documentdb-java-getting-started 資料夾。

    ```git
    cd "C:\git-samples\azure-cosmos-db-documentdb-java-getting-started"
    ```

2. 在 git 終端機視窗中，輸入 `mvn package` 以安裝必要的 Java 套件。

3. 在 git 終端機視窗中，執行 `mvn exec:java -D exec.mainClass=GetStarted.Program` 以啟動 Java 應用程式。

    終端機視窗會顯示已建立 FamilyDB 資料庫的通知。 按任一鍵來建立集合，然後切換到 [資料總管]，您會看到其現已包含 FamilyDB 資料庫。
    
    繼續按下按鍵來建立文件，然後執行查詢。
    
    在程式結束時，系統會從您帳戶刪除所有來自此應用程式的資源，因您不會產生任何費用。 

    ![主控台輸出](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 [資料總管] 來建立 Azure Cosmos DB 帳戶、文件資料庫和集合，以及如何執行應用程式來以程式設計方式執行同樣的作業。 您現可將其他資料匯入至 Azure Cosmos DB 集合。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)


