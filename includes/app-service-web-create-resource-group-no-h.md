在 Cloud Shell 中，使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。

[!INCLUDE [resource group intro text](resource-group.md)]

下列範例會在「西歐」位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

您通常會在附近的區域中建立資源群組和資源。 若要查看所有支援的 Azure Web Apps 位置，請執行 `az appservice list-locations` 命令。 