### <a name="prerequisites"></a>必要條件
* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* [Azure Blob 儲存體帳戶](../articles/storage/common/storage-create-storage-account.md)，包括儲存體帳戶名稱和其存取金鑰。 這項資訊會列在 Azure 入口網站儲存體帳戶的屬性中。 深入了解 [Azure 儲存體](../articles/storage/common/storage-introduction.md)。

在於邏輯應用程式中使用您的「Azure Blob 儲存體」帳戶之前，請先連線到您的「Azure Blob 儲存體」帳戶。 您可以在 Azure 入口網站上，從邏輯應用程式內輕鬆完成此操作。  

請使用下列步驟來連線到您的「Azure Blob 儲存體」帳戶：  

1. 建立邏輯應用程式。 在 Logic Apps 設計工具中，新增一個觸發程序，然後新增一個動作。 從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入 "blob"。 選取其中一個動作︰  
   
    ![Azure Blob 儲存體連接的建立步驟](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. 如果您之前尚未建立與 Azure 儲存體的任何連線，系統將會提示您輸入連線詳細資料：   
   
    ![Azure Blob 儲存體連接的建立步驟](./media/connectors-create-api-azureblobstorage/connection-details.png)  
3. 輸入儲存體帳戶詳細資料。 具有星號的屬性為必要項目。
   
   | 屬性 | 詳細資料 |
   | --- | --- |
   | 連線名稱 * |為連接器輸入任何名稱。 |
   | Azure 儲存體帳戶名稱 * |輸入儲存體帳戶名稱。 儲存體帳戶名稱會顯示在 Azure 入口網站的儲存體屬性中。 |
   | Azure 儲存體帳戶存取金鑰 * |輸入儲存體帳戶金鑰。 存取金鑰會顯示在 Azure 入口網站的儲存體屬性中。 |
   
    這些認證會用來授權邏輯應用程式連線並存取資料。 
4. 選取 [ **建立**]。
5. 請注意，已建立連線。 現在，請繼續進行您邏輯應用程式中的其他步驟： 
   
    ![Azure Blob 儲存體連接的建立步驟](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

