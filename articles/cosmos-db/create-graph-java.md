---
title: "使用 Java 建立 Azure Cosmos DB 圖形資料庫 | Microsoft Docs"
description: "提供 Java 程式碼範例，您可透過 Gremlin 用來連線及查詢 Azure Cosmos DB 中的圖形資料。"
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
ms.topic: quickstart
ms.date: 08/24/2017
ms.author: denlee
ms.openlocfilehash: 090a786b77cbe7b228f42f98dcb5f066b6fe62a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-create-a-graph-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB︰使用 Java 和 Azure 入口網站建立圖形資料庫

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門會使用 Azure Cosmos DB 適用的 Azure 入口網站工具建立圖形資料庫。 本快速入門也會顯示如何使用 OSS [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) 驅動程式，快速建立 Java 主控台應用程式。 本快速入門中的指示可運用在任何足以執行 Java 應用程式的作業系統上。 本快速入門可讓您熟悉在 UI 中或以程式設計方式建立和修改圖形資源 (不論您偏好哪種方式)。 

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

您必須先使用 Azure Cosmos DB 建立 Gremlin (圖形) 資料庫帳戶，才可以建立圖形資料庫。

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>新增圖形

您現在可以在 Azure 入口網站中使用 [資料總管] 工具，建立圖形資料庫。 

1. 在 Azure 入口網站的左導覽功能表中，按一下 [資料總管 (預覽)]。 
2. 在 [資料總管 (預覽)] 刀鋒視窗中，按一下 [新增圖形]，然後使用下列資訊填入頁面：

    ![Azure 入口網站中的資料總管](./media/create-graph-java/azure-cosmosdb-data-explorer.png)

    設定|建議的值|說明
    ---|---|---
    資料庫識別碼|sample-database|新資料庫的識別碼。 資料庫名稱的長度必須介於 1 到 255 個字元，且不能包含 `/ \ # ?` 或尾端空格。
    圖形識別碼|sample-graph|新圖形的識別碼。 圖形名稱與資料庫識別碼具有相同的字元需求。
    儲存體容量| 10 GB|保留預設值。 這是資料庫的儲存體容量。
    輸送量|400 RU|保留預設值。 如果您想要降低延遲，稍後可以相應增加輸送量。
    資料分割索引鍵|保留空白|針對本快速入門的目的，將資料分割索引鍵空白。

3. 填妥表單後，按一下 [確定]。

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 GitHub 複製圖形應用程式、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟 Git 終端機視窗 (例如 Git Bash)，然後使用 `cd` 來切換到工作目錄。  

2. 執行下列命令來複製範例存放庫。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>檢閱程式碼

讓我們快速檢閱應用程式中所發生的事情。 開啟 \src\GetStarted 資料夾中的 `Program.java` 檔案，並尋找這些程式碼行。 

* 已從 `src/remote.yaml` 中的組態初始化 Gremlin `Client`。

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

* 已使用 `client.submit` 方法執行一系列的 Gremlin 步驟。

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-string"></a>更新您的連接字串

1. 開啟 src/remote.yaml 檔案。 

3. 在 src/remote.yaml 檔案中填入您的「主機」、「使用者名稱」和「密碼」值。 不需要變更其餘的設定。

    設定|建議的值|說明
    ---|---|---
    主機|[***.graphs.azure.com]|請參閱此表之後的螢幕擷取畫面。 此值是 Azure 入口網站的 [概觀] 頁面上的 Gremlin URI 值，其以方括號括住並已移除尾端的 :443/。<br><br>此值也可以從 [金鑰] 索引標籤擷取，方法是移除https://、將文件變更為圖形，並移除尾端的:443/ 來使用 URI 值。
    使用者名稱|/dbs/sample-database/colls/sample-graph|`/dbs/<db>/colls/<coll>` 表單的資源，其中 `<db>` 是您現有的資料庫名稱，而 `<coll>` 是您現有的集合名稱。
    密碼|您的主要金鑰|請參閱此表之後的第二個螢幕擷取畫面。 此值是您的主要金鑰，可以從 Azure 入口網站 [金鑰] 頁面的 [主鑰金鑰] 方塊中擷取。 使用方塊左側的複製按鈕來複製此值。

    對於 [主機] 值，從 [概觀] 頁面複製 [Gremlin URI] 值。 如果是空的，請參閱上表「主機」列中有關從 [金鑰] 刀鋒視窗建立 Gremlin URI 的指示。
![在 Azure 入口網站的 [概觀] 頁面上檢視和複製 Gremlin URI 值](./media/create-graph-java/gremlin-uri.png)

    對於 [密碼] 值，從 [金鑰] 刀鋒視窗複製 [主要金鑰]：![在 Azure 入口網站的 [金鑰] 頁面上檢視和複製主要金鑰](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>執行主控台應用程式

1. 在 git 終端機視窗中，`cd` 至 azure-cosmos-db-graph-java-getting-started 資料夾。

2. 在 git 終端機視窗中，輸入 `mvn package` 以安裝必要的 Java 套件。

3. 在 git 終端機視窗中，於終端機視窗中執行 `mvn exec:java -D exec.mainClass=GetStarted.Program` 以啟動您的 Java 應用程式。

終端機視窗會顯示要新增至圖形的頂點。 程式完成後，請切換回您的網際網路瀏覽器中的 Azure 入口網站。 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>檢閱並新增範例資料

您現在可以移回 [資料總管] 並查看已新增到圖行的頂點，然後新增額外的資料點。

1. 在 資料總管 中，展開 **sample-database**/**sample-graph**，按一下 圖形，然後按一下新增篩選條件。 

   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. 在 [結果] 清單中，請注意已新增到圖形的新使用者。 選取 **ben**，請注意，他已連線到 robin。 您可以在圖形總管上移動頂點、放大和縮小，並展開圖形總管介面的大小。 

   ![Azure 入口網站的資料總管之圖形中的新頂點](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. 讓我們使用 [資料總管] 將一些新使用者新增至圖形。 按一下 [新增頂點] 按鈕，將資料新增至您的圖形。

   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. 輸入 person 標籤，然後輸入下列索引鍵和值，以在圖形中建立第一個頂點。 請注意，您可以在圖形中為每個人建立獨特的屬性。 只需要識別碼索引鍵。

    key|value|注意事項
    ----|----|----
    id|ashley|頂點的唯一識別碼。 如果您未指定識別碼，系統會為您產生一個。
    gender|female| 
    tech | java | 

    > [!NOTE]
    > 在本快速入門中，我們會建立非資料分割集合。 不過，如果您藉由在集合建立期間指定資料分割索引鍵來建立資料分割集合，您就必須包含資料分割索引鍵作為每個新頂點的索引鍵。 

5. 按一下 [確定] 。 您可能需要展開畫面，才能在螢幕底部看到 [確定]。

6. 再次按一下 [新增頂點] 並新增額外的新使用者。 輸入 person 標籤，然後輸入下列索引鍵和值：

    key|value|注意事項
    ----|----|----
    id|rakesh|頂點的唯一識別碼。 如果您未指定識別碼，系統會為您產生一個。
    gender|male| 
    school|MIT| 

7. 按一下 [確定] 。 

8. 按一下 [套用篩選條件] 並採用預設 `g.V()` 篩選條件。 所有使用者現在會顯示在 [結果] 清單中。 隨著您新增更多的資料，您可以使用篩選條件來限制您的結果。 [資料總管] 預設會使用 `g.V()` 來擷取圖形中的所有頂點，但您可以將其變更為不同的[圖形查詢](tutorial-query-graph.md) (例如 `g.V().count()`)，以 JSON 格式傳回圖形中所有頂點的計數。

9. 現在我們可以連線 rakesh 和 ashley。 請確定已在 [結果] 清單中選取 **ashley**，然後按一下右下方 [目標] 旁邊的編輯按鈕。 您可能需要加寬視窗，才可看到 [屬性] 區域。

   ![變更圖形中頂點的目標](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

10. 在 [目標] 方塊中輸入 rakesh，並在 [邊緣標籤] 方塊中輸入「認識」，然後按一下核取方塊。

   ![在 [資料總管] 中新增 ashley 與 rakesh 之間的連線](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

11. 現在從 [結果] 清單中選取 **rakesh** 並查看 ashley 與 rakesh 是否已連線。 

   ![[資料總管] 中連線的兩個頂點](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

    您也可以使用 [資料總管] 來建立預存程序、UDF 和觸發程序，以執行伺服器端商務邏輯及調整輸送量。 [資料總管] 會公開 API 中所有可用的內建程式設計資料存取，但可讓您在 Azure 入口網站中輕鬆存取您的資料。



## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源： 

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 刪除，在文字方塊中輸入要刪除之資源的名稱，然後按一下刪除。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 來建立圖形，以及如何執行應用程式。 您現在可以使用 Gremlin 來建置更複雜的查詢和實作強大的圖形周遊邏輯。 

> [!div class="nextstepaction"]
> [使用 Gremlin 進行查詢](tutorial-query-graph.md)

