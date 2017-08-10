---
title: "如何在 Azure 中平衡 Linux 虛擬機器的負載 | Microsoft Docs"
description: "了解如何使用 Azure Load Balancer，跨三部 Linux VM 建立高可用性且安全的應用程式"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 9dd85d38a64f0557fb4ef250b0e177e21bb84e53
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---

# <a name="how-to-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application"></a>如何平衡 Azure 中 Linux 虛擬機器的負載以建立高可用性應用程式
負載平衡會將傳入要求分散到多部虛擬機器，藉此提供高可用性。 在本教學課程中，您會了解 Azure Load Balancer 的不同元件，以分散流量並提供高可用性。 您會了解如何：

> [!div class="checklist"]
> * 建立 Azure Load Balancer
> * 建立負載平衡器健全狀況探查
> * 建立負載平衡器流量規則
> * 使用 cloud-init 建立基本的 Node.js 應用程式
> * 建立虛擬機器並連結至負載平衡器
> * 檢視作用中的負載平衡器
> * 新增和移除虛擬機器的負載平衡器


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="azure-load-balancer-overview"></a>Azure Load Balancer 概觀
Azure Load Balancer 是 Layer-4 (TCP、UDP) 負載平衡器，可將連入流量分散於狀況良好的 VM 來提供高可用性。 負載平衡器健康狀態探查會監視每部 VM 上指定的連接埠，且只會將流量分散至作業 VM。

您可定義含有一或多個公用 IP 位址的前端 IP 組態。 此前端 IP 組態允許透過網際網路存取您的負載平衡器和應用程式。 

虛擬機器會使用他其虛擬網路介面卡 (NIC) 連線至負載平衡器。 若要將流量分散至 VM，後端位址集區包含已連線至負載平衡器之虛擬 (NIC) 的 IP 位址。

為了控制流量，您可以定義特定連接埠的負載平衡器規則以及對應至您的 VM 的通訊協定。

如果您依照先前的教學課程來[建立虛擬機器擴展集](tutorial-create-vmss.md)，則會為您建立負載平衡器。 系統已為您將上述所有元件設定為擴展集的一部分。


## <a name="create-azure-load-balancer"></a>建立 Azure Load Balancer
本節將詳細說明如何建立及設定負載平衡器的每個元件。 請先使用 [az group create](/cli/azure/group#create) 建立資源群組，才可建立負載平衡器。 下列範例會在 eastus 位置建立名為 myResourceGroupLoadBalancer 的資源群組：

```azurecli-interactive 
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址
若要存取網際網路上您的應用程式，您需要負載平衡器的公用 IP 位址。 使用 [az network public-ip create](/cli/azure/network/public-ip#create) 建立公用 IP 位址。 下列範例會在 myResourceGroupLoadBalancer 資源群組中建立名為 myPublicIP 的公用 IP 位址：

```azurecli-interactive 
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>建立負載平衡器
使用 [az network lb create](/cli/azure/network/lb#create) 建立負載平衡器。 下列範例會建立名為 myLoadBalancer 的負載平衡器並將 myPublicIP 位址指派給前端 IP 組態：

```azurecli-interactive 
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>建立健康狀態探查
若要讓負載平衡器監視您應用程式的狀態，請使用健康狀態探查。 健康狀態探查會根據 VM 對健康狀態檢查的回應，以動態方式從負載平衡器輪替中新增或移除 VM。 根據預設，在 15 秒的間隔內連續發生兩次失敗後，VM 就會從負載平衡器分配中移除。 您可根據通訊協定或您應用程式的特定健康狀態檢查頁面，建立健康狀態探查。 

下列範例會建立 TCP 探查。 您也可以建立自訂 HTTP 探查，以進行更精細的健康狀態檢查。 使用自訂 HTTP 探查時，您必須建立健康狀態檢查頁面，例如 healthcheck.js。 此探查必須對負載平衡器傳回 **HTTP 200 OK** 回應，以將主機保留在輪替中。

若要建立 TCP 健康狀態探查，請使用 [az network lb probe create](/cli/azure/network/lb/probe#create)。 下列範例會建立名為 myHealthProbe 的健康狀態探查：

```azurecli-interactive 
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則
負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量，以及所需的來源和目的地連接埠。 若要確定只有狀況良好的 VM 可接收流量，您也可定義要使用的健康狀態探查。

使用 [az network lb rule create](/cli/azure/network/lb/rule#create) 建立負載平衡器規則。 下列範例會建立名為 myLoadBalancerRule 的規則、使用 myHealthProbe 健康狀態探查，以及平衡連接埠 80 上的流量︰

```azurecli-interactive 
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>設定虛擬網路
請先建立支援的虛擬網路資源，才可部署一些 VM 及測試您的平衡器。 如需虛擬網路的詳細資訊，請參閱[管理 Azure 虛擬網路](tutorial-virtual-network.md)教學課程。

### <a name="create-network-resources"></a>建立網路資源
使用 [az network vnet create](/cli/azure/network/vnet#create) 建立虛擬網路。 下列範例會建立名為 myVnet 的虛擬網路和名為 mySubnet 的子網路：

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

若要新增網路安全性群組，請使用 [az network nsg create](/cli/azure/network/nsg#create)。 下列範例建立名為 myNetworkSecurityGroup 的網路安全性群組：

```azurecli-interactive 
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create) 建立網路安全性群組規則。 下列範例建立名為 myNetworkSecurityGroupRule 的網路安全性群組規則：

```azurecli-interactive 
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

使用 [az network nic create](/cli/azure/network/nic#create) 建立虛擬 NIC。 下列範例會建立三個虛擬 NIC。 (您在下列步驟中針對應用程式建立的每部 VM 都有一個虛擬 NIC)。 您可以隨時建立其他虛擬 NIC 和 VM，並將它們新增至負載平衡器︰

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

## <a name="create-virtual-machines"></a>建立虛擬機器

### <a name="create-cloud-init-config"></a>建立 Cloud-init 組態
在[如何在首次開機時自訂 Linux 虛擬機器](tutorial-automate-vm-deployment.md)的先前教學課程中，您已了解如何使用 cloud-init 自動進行 VM 自訂。 您可以使用相同的 cloud-init 組態檔來安裝 NGINX 和執行簡單的 'Hello World' Node.js 應用程式。 建立名為 cloud-init.txt 的檔案並貼上下列組態︰

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-virtual-machines"></a>建立虛擬機器
若要改善您應用程式的高可用性，請將 VM 放在可用性設定組中。 如需可用性設定組的詳細資訊，請參閱[如何建立高可用性虛擬機器](tutorial-availability-sets.md)。

使用 [az vm availability-set create](/cli/azure/vm/availability-set#create) 建立可用性設定組。 下列範例會建立名為 myAvailabilitySet 的可用性設定組：

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet \
    --platform-fault-domain-count 3 \
    --platform-update-domain-count 2
```

您現在可以使用 [az vm create](/cli/azure/vm#create) 建立 VM。 下列範例會建立三部 VM 並產生 SSH 金鑰 (如果尚未存在的話)︰

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image Canonical:UbuntuServer:14.04.4-LTS:latest \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

建立及設定這三部 VM 需要幾分鐘的時間。 負載平衡器會自動偵測應用程式何時在每部 VM 上執行。 當應用程式正在執行時，負載平衡器規則就開始分配流量。


## <a name="test-load-balancer"></a>測試負載平衡器
使用 [az network public-ip show](/cli/azure/network/public-ip#show) 取得負載平衡器的公用 IP 位址。 下列範例會取得稍早建立的 myPublicIP IP 位址︰

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

接著，您可以在 Web 瀏覽器中輸入公用 IP 位址。 應用程式隨即顯示，包括負載平衡器分散流量之 VM 的主機名稱，如下列範例所示：

![執行 Node.js 應用程式](./media/tutorial-load-balancer/running-nodejs-app.png)

若要查看負載平衡器如何將流量分散於執行您應用程式的這三部 VM，您可以強制重新整理您的 Web 瀏覽器。


## <a name="add-and-remove-vms"></a>新增和移除 VM
您可能需要在執行您應用程式的 VM 上執行維護，例如安裝 OS 更新。 若要處理您應用程式增加的流量，您可能需要新增額外的 VM。 本節說明如何在負載平衡器中移除或新增 VM。

### <a name="remove-a-vm-from-the-load-balancer"></a>從負載平衡器移除 VM
您可以使用 [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove) 從後端位址集區移除 VM。 下列範例會從 myLoadBalancer 移除myVM2 的虛擬 NIC：

```azurecli-interactive 
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

若要查看負載平衡器如何將流量分散到其餘兩部執行您應用程式的 VM，您可以強制重新整理您的 Web 瀏覽器。 您現在可以在 VM 上執行維護，例如安裝 OS 更新或執行 VM 重新開機。

### <a name="add-a-vm-to-the-load-balancer"></a>將 VM 新增至負載平衡器
在執行 VM 維護之後，或者如果需要擴充容量，您可以使用 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add) 將 VM 新增至後端位址集區。 下列範例會將 myVM2 的虛擬 NIC 新增至 myLoadBalancer：

```azurecli-interactive 
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>後續步驟
在本教學課程中，您建立負載平衡器，並將 VM 連結到它。 您已了解如何︰

> [!div class="checklist"]
> * 建立 Azure Load Balancer
> * 建立負載平衡器健全狀況探查
> * 建立負載平衡器流量規則
> * 使用 cloud-init 建立基本的 Node.js 應用程式
> * 建立虛擬機器並連結至負載平衡器
> * 檢視作用中的負載平衡器
> * 新增和移除虛擬機器的負載平衡器

請前進到下一個教學課程，以深入了解 Azure 虛擬網路元件。

> [!div class="nextstepaction"]
> [管理 VM 和虛擬網路](tutorial-virtual-network.md)

