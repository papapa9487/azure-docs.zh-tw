若要連接到 Azure Redis 快取執行個體，快取用戶端需要主機名稱、連接埠和快取金鑰。 某些用戶端可能會以稍有不同的名稱來參考這些項目。 您可以在 Azure 入口網站中或使用 Azure CLI 等命令列工具來擷取此資訊。

### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>使用 Azure 入口網站來擷取主機名稱、連接埠及存取金鑰
若要使用 Azure 入口網站來擷取主機名稱、連接埠及存取金鑰，請在 [Azure 入口網站](https://portal.azure.com)中[瀏覽](../articles/redis-cache/cache-configure.md#configure-redis-cache-settings)至您的快取，然後按一下 [資源] 功能表中的 [存取金鑰] 和 [屬性]。 

![Redis 快取設定](media/redis-cache-access-keys/redis-cache-hostname-ports-keys.png)

### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>使用 Azure CLI 來擷取主機名稱、連接埠及存取金鑰
若要使用 Azure CLI 2.0 來擷取主機名稱和連接埠，您可以呼叫 [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show)，而若要擷取金鑰，您可以呼叫 [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys)。 下列指令碼會呼叫下列兩個命令，並將主機名稱、連接埠和金鑰回應到主控台。

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

如需有關此指令碼的詳細資訊，請參閱[取得 Azure Redis 快取的主機名稱、連接埠和金鑰](../articles/redis-cache/scripts/cache-keys-ports.md)。 如需有關 Azure CLI 2.0 的詳細資訊，請參閱[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 和[開始使用 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)。
