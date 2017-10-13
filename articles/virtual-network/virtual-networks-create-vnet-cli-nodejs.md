---
title: "使用 Azure CLI 1.0 來建立虛擬網路 | Microsoft Docs"
description: "了解如何使用 Azure CLI 1.0 來建立虛擬網路 | Resource Manager。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.openlocfilehash: f0649c5c8c04dda72d2f147601efb37217f9bade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>使用 Azure CLI 建立虛擬網路

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure 有兩個部署模型：Azure Resource Manager 和傳統。 Microsoft 建議透過 Resource Manager 部署模型建立資源。 若要深入了解兩個模型的差異，請閱讀[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 2.0](virtual-networks-create-vnet-arm-cli.md) - 適用於資源管理部署模型的新一代 CLI
- [Azure CLI 1.0](#create-a-virtual-network) – 適用於傳統和資源管理部署模型的 CLI (本文章)

 
[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>建立虛擬網路

若要使用 Azure CLI 建立虛擬網路，請完成下列步驟︰

1. 依照[安裝及設定 Azure CLI](../cli-install-nodejs.md) 文章中的下列步驟來安裝和 Azure CLI。

2. 建立 VNet 和子網路：

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    預期的輸出：
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    使用的參數：

   * **--vnet**。 即將建立的 VNet 名稱。 在本文案例中為 *TestVNet*
   * **-e (或 --address-space)**。 VNet 位址空間。 在本文案例中為 *192.168.0.0*
   * **-i (或 -cidr)**。 CIDR 格式的網路遮罩。 在本文案例中為 *16*。
   * **-n (或 --subnet-name**)。 第一個子網路的名稱。 在本文案例中為 *FrontEnd*。
   * **-p (或 --subnet-start-ip)**。 子網路的起始 IP 位址或子網路位址空間。 在本文案例中為 *192.168.1.0*。
   * **-r (或 --subnet-cidr)**。 CIDR 格式的子網路網路遮罩。 在本文案例中為 *24*。
   * **-l (或 --location)**。 在其中建立 VNet 的 Azure 區域。 在本文案例中為「美國中部」 。

3. 建立子網路：

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    預期的輸出：

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    使用的參數：

   * **-t (或 --vnet-name)**。 將會建立子網路的 VNet 名稱。 在本文案例中為 *TestVNet*。
   * **-n (或 --name)**。 新子網路的名稱。 在本文案例中為 *BackEnd*。
   * **-a (或 --address-prefix)**。 子網路 CIDR 區塊。 在本文案例中為 *192.168.2.0/24*。
   
4. 檢視新 VNet 的屬性：

    ```azurecli
    azure network vnet show
    ```
   
    預期的輸出：
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>後續步驟

了解如何連接︰

- 虛擬機器 (VM) 至虛擬網路；請閱讀[建立 Linux VM](../virtual-machines/linux/quick-create-cli.md)。 但不是如文章中的步驟建立 VNet 和子網路，而是選取現有的 VNet 和子網路來連接 VM。
- 虛擬網路至其他虛擬網路；請閱讀[連接 VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。
- 虛擬網路至內部部署網路；使用網站對網站虛擬私人網路 (VPN) 或 ExpressRoute 線路。 如需了解做法，請閱讀[使用站台對站台 VPN 將 VNet 連接到內部部署網路](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)和[將 VNet 連結至 ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)。