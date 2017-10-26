---
title: "在 Azure 中建立連接多個 NIC 的 Linux VM | Microsoft Docs"
description: "了解如何使用 Azure CLI 或 Resource Manager 範本，來建立連接多個 NIC 的 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 93a32ae7ec0cf73825791e8c8bc3d388cf999ece
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="create-a-linux-virtual-machine-with-multiple-nics-using-the-azure-cli-10"></a>使用 Azure CLI 1.0 建立連接多個 NIC 的 Linux 虛擬機器
您可以在 Azure 中，建立連接多個虛擬網路介面 (NIC) 的虛擬機器 (VM)。 常見案例是有不同的子網路可用於前端和後端連線，或者專門用來監視或備份解決方案的網路。 本文提供快速命令來建立連接多個 NIC 的 VM。 不同的 [VM 大小](sizes.md) 支援不同數量的 NIC，因此可據以調整您的 VM。

> [!WARNING]
> 當您建立 VM 時，必須連接多個 NIC - 您無法使用 Azure CLI 1.0 將 NIC 新增至現有的 VM。 您可以[使用 Azure CLI 2.0 將 NIC 新增至現有的 VM](multiple-nics.md)。 您也可以[根據原始虛擬磁碟建立 VM](copy-vm.md)，並且在部署 VM 時建立多個 NIC。


## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#create-supporting-resources) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](multiple-nics.md) - 適用於資源管理部署模型的新一代 CLI


## <a name="create-supporting-resources"></a>建立支援資源
確定您已登入 [Azure CLI](../../cli-install-nodejs.md)，並且使用的是 Resource Manager 模式：

```azurecli
azure config mode arm
```

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 myResourceGroup、mystorageaccount 和 myVM。

首先，建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli
azure group create myResourceGroup --location eastus
```

建立儲存體帳戶以放置您的 VM。 下列範例會建立名為 mystorageaccount 的儲存體帳戶：

```azurecli
azure storage account create mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --kind Storage \
    --sku-name PLRS
```

建立要與 VM 連線的虛擬網路。 下列範例會建立名為 myVnet 的虛擬網路，位址首碼為 192.168.0.0/16：

```azurecli
azure network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefixes 192.168.0.0/16
```

建立兩個虛擬網路子網路 - 一個用於前端流量，另一個用於後端流量。 下列範例會建立兩個子網路，名為 mySubnetFrontEnd 和 mySubnetBackEnd：

```azurecli
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetFrontEnd \
    --address-prefix 192.168.1.0/24
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>建立及設定多個 NIC
您可以深入了解更多有關 [使用 Azure CLI 部署多個 NIC](../../virtual-machines/linux/multiple-nics.md)的詳細資訊，包括撰寫可進行迴圈程序的指令碼來建立所有的 NIC。

下列範例會建立兩個 NIC，名為 myNic1 與 myNic2，以及一個連線到各個子網路的 NIC：

```azurecli
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic1 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetFrontEnd
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic2 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetBackEnd
```

通常您也可以建立[網路安全性群組](../../virtual-network/virtual-networks-nsg.md)或[負載平衡器](../../load-balancer/load-balancer-overview.md)來協助管理，以及將流量分散到您的 VM。 下列範例建立名為 myNetworkSecurityGroup 的網路安全性群組：

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

使用 `azure network nic set`將您的 NIC 繫結至網路安全性群組。 下列範例會將 myNic1 和 myNic2 與 myNetworkSecurityGroup 繫結：

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>建立 VM 並附加 NIC
建立 VM 時，您現在可以指定多個 NIC。 不使用 `--nic-name` 提供單一 NIC，而改用 `--nic-names`，並提供以逗號分隔的 NIC 清單。 當您選取 VM 大小時也需多加注意。 您可以新增至 VM 的 NIC 總數是有限制的。 深入了解 [Linux VM 大小](sizes.md)。 下列範例示範如何指定多個 NIC，然後是使用多個 NIC 支援的 VM 大小 (Standard_DS2_v2)：

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="create-multiple-nics-using-resource-manager-templates"></a>使用 Resource Manager 範本建立多個 NIC
Azure Resource Manager 範本會使用宣告式 JSON 檔案來定義您的環境。 您可以閱讀 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。 Resource Manager 範本提供一種方式，可在部署期間建立資源的多個執行個體，例如建立多個 NIC。 您使用 *copy* 來指定要建立的執行個體數目：

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

深入了解[使用 *copy* 建立多個執行個體](../../resource-group-create-multiple.md)。 

您也可以使用 `copyIndex()`，然後在資源名稱後面附加一個數字，讓您能夠建立 `myNic1`、`myNic2`，依此類推。以下顯示附加索引值的範例：

```json
"name": "[concat('myNic', copyIndex())]", 
```

您可以閱讀 [使用 Resource Manager 範本建立多個 NIC](../../virtual-network/virtual-network-deploy-multinic-arm-template.md)的完整範例。

## <a name="next-steps"></a>後續步驟
嘗試建立具有多個 NIC 的 VM 時，請務必檢閱 [Linux VM 大小](sizes.md) 。 注意每個 VM 大小所支援的 NIC 數目上限。 

請記住，您無法在現有 VM 中新增其他 NIC，您必須在部署 VM 時建立所有 NIC。 小心規劃您的部署，以確定您一開始就會有所有需要的網路連線。

