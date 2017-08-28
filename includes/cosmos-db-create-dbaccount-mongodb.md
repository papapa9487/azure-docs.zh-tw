1. 在新的視窗中，登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在左側功能表中，依序按一下 [新增]、[資料庫]，並在 [Azure Cosmos DB] 下按一下 [建立]。
   
   ![Azure 入口網站的螢幕擷取畫面，其中反白顯示 [其他服務] 和 Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. 在 [新增帳戶] 刀鋒視窗中，指定想要的 Azure Cosmos DB 帳戶組態。 

    在使用 Azure Cosmos DB 時，您可以選擇下列四種程式設計模型的其中一種︰Gremlin (圖形)、MongoDB、SQL (DocumentDB) 和資料表 (索引鍵-值)。 
       
    在本快速入門中，我們會針對 MongoDB API 進行程式設計，因此您會在填寫表單時選擇 [MongoDB]。 但是，如果您有用於社交媒體應用程式的圖形資料、來自目錄應用程式的文件資料，或索引鍵/值 (資料表) 資料，請了解 Azure Cosmos DB 可以提供高度可用、全域分散式的資料庫服務平台來供您所有的任務關鍵性應用程式使用。

    使用表格中的資訊作為指南來填寫 [新增帳戶] 刀鋒視窗。
 
    ![[新增 Azure Cosmos DB] 刀鋒視窗的螢幕擷取畫面](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)
   
    設定|建議的值|說明
    ---|---|---
    ID|唯一值|您選擇用來識別 Azure Cosmos DB 帳戶的唯一名稱。 documents.azure.com 會附加到您所提供的識別碼以建立 URI，因此，請使用可供辨識的唯一識別碼。 此識別碼只能包含小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。
    API|MongoDB|在本文稍後，我們會針對 [MongoDB API](../articles/documentdb/documentdb-protocol-mongodb.md) 進行程式設計。|
    訂用帳戶|*您的訂用帳戶*|您要用於 Azure Cosmos DB 帳戶的 Azure 訂用帳戶。 
    資源群組|與識別碼相同的值|您帳戶的新資源群組名稱。 為求簡化，您可以使用和識別碼相同的名稱。 
    位置|最接近使用者的區域|用來主控 Azure Cosmos DB 帳戶的地理位置。 請選擇最接近使用者的位置，以便他們能以最快速度存取到資料。

4. 按一下 [建立]  來建立帳戶。
5. 在工具列上，按一下 [通知] 以監視部署程序。

    ![「部署已啟動」通知](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  當部署完成時，從 [所有資源] 圖格開啟新的帳戶。 

    ![[所有資源] 圖格上的 DocumentDB 帳戶](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
