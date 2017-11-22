## <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

函式會使用 Azure 儲存體中的一般用途帳戶來維護函式的狀態和其他資訊。 在使用 [az storage account create](/cli/azure/storage/account#create) 命令所建立的資源群組中建立一般用途的儲存體帳戶。

在下列命令中，使用全域唯一儲存體帳戶名稱來替代您看見 `<storage_name>` 預留位置的地方。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

建立儲存體帳戶後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```