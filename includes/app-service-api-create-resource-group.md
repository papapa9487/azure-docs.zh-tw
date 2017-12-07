使用 [az group create](/cli/azure/group#az_group_create) 命令來建立資源群組。

[!INCLUDE [resource group intro text](resource-group.md)]

下列範例會在 westeurope 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

若要查看可用的位置，請執行 `az appservice list-locations` 命令。 您通常會在附近的區域中建立資源。
