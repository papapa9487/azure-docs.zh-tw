1. 在新的瀏覽器視窗中，登入 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下 [新增] > [資料庫] > [Azure Cosmos DB]。
   
   ![Azure 入口網站的 [資料庫] 窗格](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. 在 [新增帳戶] 頁面中，輸入新的 Azure Cosmos DB 帳戶的設定。 

    設定|建議的值|說明
    ---|---|---
    ID|*輸入唯一名稱*|輸入唯一名稱來識別此 Azure Cosmos DB 帳戶。 因為 documents.azure.com 會附加到您所提供的識別碼以建立 URI，請使用可供辨識的唯一識別碼。<br><br>識別碼只能包含小寫字母、數字及連字號 (-) 字元，且必須包含 3 到 50 個字元。
    API|Gremlin (圖形)|API 會決定要建立的帳戶類型。 Azure Cosmos DB 會提供五個 API，以符合應用程式的需求︰SQL (文件資料庫)、Gremlin (圖形資料庫)、MongoDB (文件資料庫)、Azure 資料表及 Cassandra，目前各自需要個別的帳戶。 <br><br>選取 [Gremlin (圖形)]，因為在本快速入門中，您將使用 Gremlin 語法建立可查詢的圖形。<br><br>[深入了解圖形 API](../articles/cosmos-db/graph-introduction.md)
    訂用帳戶|*您的訂用帳戶*|選取您要用於此 Azure Cosmos DB 帳戶的 Azure 訂用帳戶。 
    資源群組|*輸入上面識別碼中所提供的同一個唯一名稱*|為您的帳戶輸入新的資源群組名稱。 為求簡化，您可以使用和識別碼相同的名稱。 
    位置|*選取最接近使用者的區域*|選取用來主控 Azure Cosmos DB 帳戶的地理位置。 使用最接近使用者的位置，以便他們能以最快速度存取資料。
    啟用異地備援| 保留空白 | 這會在第二個 (配對) 區域建立資料庫的複本。 將此項保留空白。  
    釘選到儀表板 | 選取 | 選取此方塊，以便將新的資料庫帳戶新增至入口網站儀表板以方便存取。

    然後按一下 [ **建立**]。

    ![Azure Cosmos DB 的新帳戶刀鋒視窗](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

4. 建立帳戶需要幾分鐘的時間。 在建立帳戶期間，入口網站會在右側顯示 [部署 Azure Cosmos DB] 圖格，您可能需要在儀表板上向右捲動以查看圖格。 另外在畫面頂端附近還會顯示一個進度列。 您可以查看任何進度區域。

    ![Azure 入口網站的 [通知] 窗格](./media/cosmos-db-create-dbaccount-graph/deploying-cosmos-db.png)

    一旦建立帳戶後，[恭喜您！已建立 Azure Cosmos DB 帳戶] 頁面隨即顯示。 