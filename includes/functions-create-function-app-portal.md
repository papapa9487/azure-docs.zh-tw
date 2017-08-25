1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

1. 按一下 [計算] > [函式應用程式]，接著選取您的**訂用帳戶**。 然後，使用表格中指定的函式應用程式設定。

    ![在 Azure 入口網站中建立函式應用程式](./media/functions-create-function-app-portal/function-app-create-flow.png)

    | 設定      | 建議的值  | 說明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **應用程式名稱** | 全域唯一的名稱 | 用以識別新函式應用程式的名稱。 | 
    | **[資源群組](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | 要在其中建立函式應用程式的新資源群組名稱。 | 
    | **[主控方案](../articles/azure-functions/functions-scale.md)** |   取用方案 | 會定義如何將資源配置給函式應用程式的主控方案。 在預設**取用方案**中，您的函式會根據需要來動態新增資源。 您只需要針對函式有執行的時間來付費。   |
    | **位置** | 西歐 | 選擇與您接近的位置，或選擇與您的函式將會存取之其他服務接近的位置。 |
    | **[儲存體帳戶](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** |  全域唯一的名稱 |  函式應用程式所使用之新儲存體帳戶的名稱。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 您也可以使用現有帳戶。 |

1. 按一下 [建立] 以佈建並部署新的函式應用程式。