1. 在 [方案總管] 中，以滑鼠右鍵按一下專案並選取 [發佈]。 選擇 新建，然後按一下發佈。 

    ![發佈新建的函式應用程式](./media/functions-vstools-publish/functions-vstools-publish-new-function-app.png)

2. 如果您已將 Visual Studio 連線到您的 Azure 帳戶，請案一下 [新增帳戶...]。  

3. 在 [建立 App Service] 對話方塊中，使用如下表中指定的 [主機] 設定： 

    ![Azure 本機執行階段](./media/functions-vstools-publish/functions-vstools-publish.png)

    | 設定      | 建議的值  | 說明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **應用程式名稱** | 全域唯一的名稱 | 用以唯一識別新函式應用程式的名稱。 |
    | **訂用帳戶** | 選擇您的訂用帳戶 | 要使用的 Azure 訂用帳戶。 |
    | **[資源群組](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  要在其中建立函式應用程式的資源群組名稱。 |
    | **[App Service 方案](../articles/azure-functions/functions-scale.md)** | 取用方案 | 當您建立新方案時，請務必選擇 [大小] 之下的 [取用]。  |
    | **[儲存體帳戶](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** | 全域唯一的名稱 | 使用現有的儲存體帳戶或建立新帳戶。   |

4. 按一下 [建立] 以在 Azure 中使用這些設定來建立函式應用程式。 佈建完成後，請記下 [網站 URL] 值，這是 Azure 中您的函式應用程式位址。 

    ![Azure 本機執行階段](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
