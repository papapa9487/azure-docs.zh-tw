---
title: "使用 Azure CLI 1.0 針對 Linux VM 開啟連接埠 | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 部署模型和 Azure CLI 1.0 對 Linux VM 開啟連接埠 / 建立端點"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 847bc76c37ed929851712ba1c12463a01032e267
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-using-the-azure-cli-10"></a>在 Azure 中使用 Azure CLI 1.0 針對 Linux VM 開啟連接埠和端點
您可以透過在子網路或 VM 網路介面上建立網路篩選，對 Azure 中的虛擬機器 (VM) 開啟連接埠或建立端點。 您可將控制輸入和輸出流量的這些篩選器放在可接收流量的資源所附加的網路安全性群組上。 讓我們使用連接埠 80 上的 Web 流量的常見範例。 這篇文章說明如何使用 Azure CLI 1.0 來開啟連接埠至 VM。


## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#quick-commands) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](nsg-quickstart.md) - 適用於資源管理部署模型的新一代 CLI


## <a name="quick-commands"></a>快速命令
若要建立「網路安全性群組」和規則，您需要安裝 [the Azure CLI 1.0](../../cli-install-nodejs.md) 和使用 Resource Manager 模式：

```azurecli
azure config mode arm
```

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包括 myResourceGroup、myNetworkSecurityGroup 和 myVnet。

適當地輸入您自己的名稱和位置來建立「網路安全性群組」。 下列範例會在 eastus 位置中建立名為 myNetworkSecurityGroup 的網路安全性群組：

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

新增規則以允許流向您 Web 伺服器的 HTTP 流量 (或針對自己的案例 (例如 SSH 存取或資料庫連接) 進行調整)。 下列範例會建立名為 myNetworkSecurityGroupRule 的規則以允許連接埠 80 上的 TCP 流量︰

```azurecli
azure network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --destination-port-range 80 \
    --access allow
```

將「網路安全性群組」與 VM 的網路介面 (NIC) 建立關聯。 下列範例將名為 myNic 的現有 NIC 與名為 myNetworkSecurityGroup 的網路安全性群組建立關聯：

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --name myNic
```

或者，您也可以將「網路安全性群組」與虛擬網路的子網路建立關聯，而不是只與單一 VM 上的網路介面建立關聯。 下列範例將 myVnet 虛擬網路中名為 mySubnet 的現有子網路，與名為 myNetworkSecurityGroup 的網路安全性群組建立關聯：

```azurecli
azure network vnet subnet set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>網路安全性群組的詳細資訊
這裡的快速命令可讓您使流向您 VM 的流量開始正常運作。 「網路安全性群組」提供許多絕佳的功能和細微性來控制對您資源的存取。 您可以深入了解 [建立網路安全性群組和 ACL 規則](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)。

您可以在 Azure Resource Manager 範本中定義網路安全性群組和 ACL 規則。 深入了解 [使用範本建立網路安全性群組](../../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果您需要使用連接埠轉送來將唯一的外部連接埠對應至您 VM 上的內部連接埠，請使用負載平衡器和「網路位址轉譯」(NAT) 規則。 例如，您可能會想要對外公開 TCP 連接埠 8080，然後讓流量導向到 VM 上的 TCP 連接埠 80。 您可以深入了解 [建立網際網路面向的負載平衡器](../../load-balancer/load-balancer-get-started-internet-arm-cli.md)。

## <a name="next-steps"></a>後續步驟
在此範例中，您建立了簡單的規則來允許 HTTP 流量。 您可以從下列文章中，找到有關建立更詳細環境的資訊︰

* [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)
* [什麼是網路安全性群組 (NSG)？](../../virtual-network/virtual-networks-nsg.md)
* [負載平衡器的 Azure Resource Manager 概觀](../../load-balancer/load-balancer-arm.md)

