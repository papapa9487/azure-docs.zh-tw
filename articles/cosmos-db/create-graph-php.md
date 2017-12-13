---
title: "快速入門：圖形 API 與 PHP - Azure Cosmos DB | Microsoft Docs"
description: "本快速入門示範如何使用 Azure Cosmos DB 圖形 API，搭配 Azure 入口網站與 PHP 建立主控台應用程式"
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
ms.assetid: dfce0de9-a326-401c-9940-406ac0414461
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: quickstart
ms.date: 12/04/2017
ms.author: lbosq
ms.openlocfilehash: fa1830706f5215939e8b2772ed2266eb9ba6ba73
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="azure-cosmos-db-create-a-graph-database-using-php-and-the-azure-portal"></a>Azure Cosmos DB︰使用 PHP 和 Azure 入口網站建立圖形資料庫

本快速入門示範如何使用 PHP 與 Azure Cosmos DB [圖形 API](graph-introduction.md)，透過從 GitHub 複製範例來建置主控台應用程式。 本快速入門也會逐步引導您使用網頁型 Azure 入口網站建立 Azure Cosmos DB 帳戶。   

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、資料表、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。  

## <a name="prerequisites"></a>必要條件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]或者，您可以[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，無須 Azure 訂用帳戶，也無須任何費用和約定付款。

此外：
* [PHP](http://php.net/) \(英文\) 5.6 或更新版本
* [Composer](https://getcomposer.org/download/)

## <a name="create-a-database-account"></a>建立資料庫帳戶

您必須先使用 Azure Cosmos DB 建立 Gremlin (圖形) 資料庫帳戶，才可以建立圖形資料庫。

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>新增圖形

您現在可以在 Azure 入口網站中使用 [資料總管] 工具，建立圖形資料庫。 

1. 按一下 [資料總管] > [新增圖形]。

    [新增圖形] 區域會顯示在最右邊，您可能需要向右捲動才會看到。

    ![Azure 入口網站資料總管 [新增圖形] 頁面](./media/create-graph-php/azure-cosmosdb-data-explorer-graph.png)

2. 在 [新增圖形] 頁面上，輸入新圖形的設定。

    設定|建議的值|說明
    ---|---|---
    資料庫識別碼|sample-database|輸入 *sample-database* 作為新資料庫的名稱。 資料庫名稱的長度必須介於 1 到 255 個字元，且不能包含 `/ \ # ?` 或尾端空格。
    圖形識別碼|sample-graph|輸入 *sample-graph* 作為新集合的名稱。 圖形名稱與資料庫識別碼具有相同的字元需求。
    儲存體容量|固定 (10 GB)|保持使用預設值 [固定 (10 GB)]。 此值是資料庫的儲存體容量。
    Throughput|400 RU|將輸送量變更為每秒 400 個要求單位 (RU/秒)。 如果您想要降低延遲，稍後可以相應增加輸送量。
    資料分割索引鍵|保留空白|針對本快速入門的目的，將資料分割索引鍵空白。

3. 填妥表單後，按一下 [確定]。

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在讓我們切換為使用程式碼。 我們將從 GitHub 複製圖形 API 應用程式、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。  

1. 開啟命令提示字元，建立名為 git-samples 的新資料夾，然後關閉命令提示字元。

    ```bash
    md "C:\git-samples"
    ```

2. 開啟 git 終端機視窗 (例如 git bash)，並使用 `cd` 命令變更至要安裝範例應用程式的資料夾。  

    ```bash
    cd "C:\git-samples"
    ```

3. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-php-getting-started.git
    ```

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 程式碼片段皆取自 C:\git-samples\azure-cosmos-db-graph-php-getting-started\ 資料夾中的 `connect.php` 檔案。 或者也可以直接跳至[更新您的連接字串](#update-your-connection-information)。 

* Gremlin `connection` 是於 `connect.php` 檔案的開頭，使用 `$db` 物件初始化。

    ```php
    $db = new Connection([
        'host' => '<your_server_address>.graphs.azure.com',
        'username' => '/dbs/<db>/colls/<coll>',
        'password' => 'your_primary_key'
        ,'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

* 已使用 `$db->send($query);` 方法執行一系列的 Gremlin 步驟。

    ```php
    $query = "g.V().drop()";
    ...
    $result = $db->send($query);
    $errors = array_filter($result);
    }
    ```

## <a name="update-your-connection-information"></a>更新您的連線資訊

現在，返回 Azure 入口網站以取得連線資訊，並將其複製到應用程式中。 這些設定可讓您的應用程式與託管資料庫進行通訊。

1. 在 [Azure 入口網站](http://portal.azure.com/)中，按一下 [金鑰]。 

    複製 URI 值的第一個部分。

    ![在 Azure 入口網站的 [金鑰] 頁面中，檢視並複製存取金鑰](./media/create-graph-php/keys.png)
2. 開啟 `connect.php` 檔案，並在第 8 行將 URI 值貼至 `your_server_address` 上。

    連線物件初始化現在看起來應該類似以下程式碼：

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.graphs.azure.com',
        'username' => '/dbs/<db>/colls/<coll>',
        'password' => 'your_primary_key'
        ,'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

3. 以您的資料庫和圖形名稱，變更連線物件中的 `username` 參數。 如果您使用建議的 `sample-database` 和 `sample-graph` 值，它看起來應該如下：

    `'username' => '/dbs/sample-database/colls/sample-graph'`

    以下是整個連線物件目前應該看起來的樣子：

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.graphs.azure.com',
        'username' => '/dbs/sample-database/colls/sample-graph',
        'password' => 'your_primary_key',
        'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

4. 在 Azure 入口網站中，使用複製按鈕複製 [主要金鑰]，然後將其貼上至密碼參數中的 `your_primary_key`。

    連線物件初始化現在看起來應該如下：

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.graphs.azure.com',
        'username' => '/dbs/sample-database/colls/sample-graph',
        'password' => '2Ggkr662ifxz2Mg==',
        'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

5. 儲存 `connect.php` 檔案。

## <a name="run-the-console-app"></a>執行主控台應用程式

1. 在 git 終端機視窗中，`cd` 至 azure-cosmos-db-graph-php-getting-started 資料夾。

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-php-getting-started"
    ```

2. 在 git 終端機視窗中，使用下列命令來安裝所需的 PHP 相依性。

   ```
   composer install
   ```

3. 在 git 終端機視窗中，使用下列命令來啟動 PHP 應用程式。
    
    ```
    php connect.php
    ```

    終端機視窗會顯示要新增至圖形的頂點。 
    
    如果遇到逾時錯誤，請檢查您已在[更新您的連線資訊](#update-your-connection-information)中正確更新連線資訊，也請嘗試重新執行最後一個命令。 
    
    程式停止後，按 Enter，然後在網際網路瀏覽器中切換回 Azure 入口網站。 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>檢閱並新增範例資料

您現在可以移回 [資料總管] 並查看已新增到圖行的頂點，然後新增額外的資料點。

1. 按一下 [資料總管]，展開 **sample-graph**，按一下 [圖形]，然後再按一下 [套用篩選條件]。 

   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/create-graph-php/azure-cosmosdb-data-explorer-expanded.png)

2. 在 [結果] 清單中，請注意已新增到圖形的新使用者。 選取 **ben**，請注意，他已連線到 robin。 您可以拖放移動周圍的頂點、捲動滑鼠滾輪執行縮放、使用雙箭號展開圖形大小。 

   ![Azure 入口網站的資料總管之圖形中的新頂點](./media/create-graph-php/azure-cosmosdb-graph-explorer-new.png)

3. 現在來加入一些新使用者。 按一下 [新增頂點] 按鈕，將資料新增至您的圖形。

   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/create-graph-php/azure-cosmosdb-data-explorer-new-vertex.png)

4. 輸入*人員*的標籤。

5. 按一下 [新增屬性]，以新增以下各項屬性。 請注意，您可以在圖形中為每個人建立獨特的屬性。 只需要識別碼索引鍵。

    key|value|注意事項
    ----|----|----
    id|ashley|頂點的唯一識別碼。 如果您未指定識別碼，系統會為您產生一個。
    gender|female| 
    tech | java | 

    > [!NOTE]
    > 在本快速入門中，我們會建立非資料分割集合。 不過，如果您藉由在集合建立期間指定資料分割索引鍵來建立資料分割集合，您就必須包含資料分割索引鍵作為每個新頂點的索引鍵。 

6. 按一下 [確定] 。 您可能需要展開畫面，才能在螢幕底部看到 [確定]。

7. 再次按一下 [新增頂點] 並新增額外的新使用者。 

8. 輸入*人員*的標籤。

9. 按一下 [新增屬性]，以新增以下各項屬性：

    key|value|注意事項
    ----|----|----
    id|rakesh|頂點的唯一識別碼。 如果您未指定識別碼，系統會為您產生一個。
    gender|male| 
    school|MIT| 

10. 按一下 [確定] 。 

11. 按一下 [套用篩選條件] 按鈕，預設的 `g.V()` 篩選條件會顯示圖形中的所有值。 所有使用者現在會顯示在 [結果] 清單中。 

    隨著您新增更多的資料，您可以使用篩選條件來限制您的結果。 依預設，[資料總管] 會使用 `g.V()` 擷取圖形中的所有頂點。 您可將其變更為不同的[圖形查詢](tutorial-query-graph.md) (例如 `g.V().count()`)，以使用 JSON 格式傳回圖形中所有頂點的計數。 若您變更篩選條件，請將篩選條件變更回 `g.V()`，然後按一下 [套用篩選條件]，即可再次顯示所有的結果。

12. 現在我們可以連線 rakesh 和 ashley。 請確定已在 [結果] 清單中選取 **ashley**，然後按一下右下方 [目標] 旁邊的編輯按鈕。 您可能需要加寬視窗，才可看到 [屬性] 區域。

   ![變更圖形中頂點的目標](./media/create-graph-php/azure-cosmosdb-data-explorer-edit-target.png)

13. 在 [目標] 方塊中輸入 rakesh，並在 [邊緣標籤] 方塊中輸入 knows，然後按一下核取方塊。

   ![在 [資料總管] 中新增 ashley 與 rakesh 之間的連線](./media/create-graph-php/azure-cosmosdb-data-explorer-set-target.png)

14. 現在從 [結果] 清單中選取 **rakesh** 並查看 ashley 與 rakesh 是否已連線。 

   ![[資料總管] 中連線的兩個頂點](./media/create-graph-php/azure-cosmosdb-graph-explorer.png)

   本教學課程的資源建立部分結束了。您可繼續新增頂點至圖形、修改現有的頂點，或是變更查詢。 現在讓我們檢閱 Azure Cosmos DB 提供的計量，然後再清除資源。 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 來建立圖形，以及如何執行應用程式。 您現在可以使用 Gremlin 來建置更複雜的查詢和實作強大的圖形周遊邏輯。 

> [!div class="nextstepaction"]
> [使用 Gremlin 進行查詢](tutorial-query-graph.md)

