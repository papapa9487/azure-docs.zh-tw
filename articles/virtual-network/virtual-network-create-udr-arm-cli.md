---
title: "使用 Azure CLI 2.0 控制路由和虛擬應用裝置 | Microsoft Docs"
description: "了解如何使用 Azure CLI 2.0 控制路由和虛擬應用裝置。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2017
ms.author: jdial
ms.openlocfilehash: e5d9519998346619093f443b740c8904283f76e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 建立使用者定義的路由 (UDR)

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Azure CLI](virtual-network-create-udr-arm-cli.md)
> * [範本](virtual-network-create-udr-arm-template.md)
> * [PowerShell (傳統部署)](virtual-network-create-udr-classic-ps.md)
> * [CLI (傳統部署)](virtual-network-create-udr-classic-cli.md)

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本 

您可以使用下列其中一個 CLI 版本來完成工作︰ 

- [Azure CLI 1.0](virtual-network-create-udr-arm-cli-nodejs.md) – 適用於傳統和資源管理部署模型的 CLI 
- [Azure CLI 2.0](#Create-the-UDR-for-the-front-end-subnet) - 適用於資源管理部署模型的新一代 CLI (本文章)

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下的範例 Azure CLI 命令是假設您已根據上述案例建立簡單的環境。 如果您想要以本文件顯示的方式執行命令，請先依照下列方式建置測試環境：部署[此範本](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)，按一下 [部署至 Azure]，視情況取代預設參數值，然後遵循入口網站中的指示。


## <a name="create-the-udr-for-the-front-end-subnet"></a>建立前端子網路的 UDR
若要根據上述案例建立前端子網路所需的路由表和路由，請依照下列步驟執行。

1. 使用 [az network route-table create](/cli/azure/network/route-table#create) 命令，建立前端子網路的路由表：

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --location centralus \
    --name UDR-FrontEnd
    ```

    輸出：

    ```json
    {
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
    "location": "centralus",
    "name": "UDR-FrontEnd",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "routes": [],
    "subnets": null,
    "tags": null,
    "type": "Microsoft.Network/routeTables"
    }
    ```

2. 使用 [az network route-table route create](/cli/azure/network/route-table/route#create) 命令建立路由，以將目的地為後端子網路 (192.168.2.0/24) 的所有流量傳送到 **FW1** VM (192.168.0.4)：

    ```azurecli 
    az network route-table route create \
    --resource-group testrg \
    --name RouteToBackEnd \
    --route-table-name UDR-FrontEnd \
    --address-prefix 192.168.2.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

    輸出：

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd/routes/RouteToBackEnd",
    "name": "RouteToBackEnd",
    "nextHopIpAddress": "192.168.0.4",
    "nextHopType": "VirtualAppliance",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg"
    }
    ```
    參數：

    * **--route-table-name**。 將會加入路由的路由表的名稱。 在本文案例中為 *UDR-FrontEnd*。
    * **--address-prefix**。 封包所指向位置的子網路的位址首碼。 在本文案例中為 *192.168.2.0/24*。
    * **--next-hop-type**。 將傳送流量的目標物件類型。 可能的值為 VirtualAppliance、VirtualNetworkGateway、VNETLocal、Internet 或 None。
    * **--next-hop-ip-address**。 下個躍點的 IP 位址。 在本文案例中為 *192.168.0.4*。

3. 執行 [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) 命令，將上方建立的路由表關聯至 **FrontEnd** 子網路：

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name FrontEnd \
    --route-table UDR-FrontEnd
    ```

    輸出：

    ```json
    {
    "addressPrefix": "192.168.1.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/FrontEnd",
    "ipConfigurations": null,
    "name": "FrontEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": {
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
        "location": null,
        "name": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "routes": null,
        "subnets": null,
        "tags": null,
        "type": null
        }
    }
    ```

    參數：
    
    * **--vnet-name**。 子網路所在的 VNet 名稱。 在本文案例中為 *TestVNet*。

## <a name="create-the-udr-for-the-back-end-subnet"></a>建立後端子網路的 UDR

若要根據上述案例建立後端子網路所需的路由表和路徑，完成下列步驟：

1. 執行下列命令，建立後端子網路的路由表：

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --name UDR-BackEnd \
    --location centralus
    ```

2. 執行下列命令，在上方建立的路由表中建立路由，將目的地為前端子網路 (192.168.1.0/24) 的所有流量傳送到 **FW1** VM (192.168.0.4)：

    ```azurecli
    az network route-table route create \
    --resource-group testrg \
    --name RouteToFrontEnd \
    --route-table-name UDR-BackEnd \
    --address-prefix 192.168.1.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

3. 執行下列命令，建立與 **BackEnd** 子網路關聯的路由表：

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name BackEnd \
    --route-table UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>啟用 FW1 上的 IP 轉送

若要啟用 **FW1**所使用之 NIC 中的 IP 轉送，完成下列步驟：

1. 使用 JMESPATH 篩選條件執行 [az network nic show](/cli/azure/network/nic#show) 命令，以顯示 [啟用 IP 轉送] 目前的 **enable-ip-forwarding** 值。 應該設定為 *false*。

    ```azurecli
    az network nic show \
    --resource-group testrg \
    --nname nicfw1 \
    --query 'enableIpForwarding' -o tsv
    ```

    輸出：

        false

2. 執行下列命令以啟用 IP 轉送：

    ```azurecli
    az network nic update \
    --resource-group testrg \
    --name nicfw1 \
    --ip-forwarding true
    ```

    您可檢查串流至主控台的輸出，或只是重新測試特定的 **enableIpForwarding** 值︰

    ```azurecli
    az network nic show -g testrg -n nicfw1 --query 'enableIpForwarding' -o tsv
    ```

    輸出：

        true

    參數：

    **--ip-forwarding**：*true* 或 *false*。

