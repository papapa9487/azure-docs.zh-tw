## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶

使用 [az storage account create](/cli/azure/storage/account#create) 命令建立一般用途的標準儲存體帳戶。 一般用途的標準儲存體帳戶可以用於所有四個服務：Blob、檔案、資料表和佇列。 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>指定儲存體帳戶認證

對於本教學課程中的大部分命令，Azure CLI 需要您的儲存體帳戶認證。 儘管有幾個選項可以這麼做，其中一種最輕鬆的方式就是設定 `AZURE_STORAGE_ACCOUNT` 和 `AZURE_STORAGE_ACCESS_KEY` 環境變數。

首先，使用 [az storage account keys list](/cli/azure/storage/account/keys#list) 命令顯示您的儲存體帳戶金鑰：

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

現在，請設定 `AZURE_STORAGE_ACCOUNT`和`AZURE_STORAGE_ACCESS_KEY` 環境變數。 您可以在 Bash 殼層中使用 `export` 命令來這樣做：

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_ACCESS_KEY="myStorageAccountKey"
```