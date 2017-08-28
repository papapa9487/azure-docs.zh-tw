## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#create) 命令來建立 VM。 

下列範例會建立名為 myVM 的 VM，並建立 SSH 金鑰 (如果它們不存在於預設金鑰位置)。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

建立 VM 後，Azure CLI 會顯示類似下列範例的資訊。 記下 `publicIpAddress`。 此位址用來存取 VM。

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>針對 Web 流量開啟連接埠 80 

依預設只能透過 SSH 連線至 Azure 中部署的 Linux VM。 因為此 VM 即將成為 Web 伺服器，所以您需要從網際網路開啟連接埠 80。 使用 [az vm open-port](/cli/azure/vm#open-port) 命令來開啟所需的連接埠。  
 
```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>透過 SSH 連線到您的 VM


如果您還不知道您 VM 的公用 IP 位址，請執行 [az network public-ip list](/cli/azure/network/public-ip#list) 命令：


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

使用下列命令，建立與虛擬機器的 SSH 工作階段。 替換為您虛擬機器的正確公用 IP 位址。 在此範例中，IP 位址是 *40.68.254.142*。

```bash
ssh azureuser@40.68.254.142
```

