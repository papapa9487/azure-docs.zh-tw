---
title: "建立虛擬網路 - Azure PowerShell | Microsoft Docs"
description: "了解如何使用 PowerShell 建立虛擬網路。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a31f4f12-54ee-4339-b968-1a8097ca77d3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e7072ddf51570d46578111e2e392e3cbea53f2aa
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-virtual-network-using-powershell"></a>使用 PowerShell 建立虛擬網路

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure 有兩個部署模型：Azure Resource Manager 和傳統。 Microsoft 建議透過 Resource Manager 部署模型建立資源。 若要深入了解兩個模型的差異，請閱讀[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。
 
本文說明如何使用 PowerShell 透過 Resource Manager 部署模型建立 VNet。 您也可以使用其他工具透過 Resource Manager 建立 VNet，或從下列清單中選取不同選項以透過傳統部署模型建立 VNet︰

> [!div class="op_single_selector"]
> * [入口網站](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [CLI](virtual-networks-create-vnet-arm-cli.md)
> * [範本](virtual-networks-create-vnet-arm-template-click.md)
> * [入口網站 (傳統)](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell (傳統)](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [CLI (傳統)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>建立虛擬網路

若要使用 PowerShell 建立虛擬網路，請完成下列步驟︰

1. 遵循 [如何安裝並設定 Azure PowerShell](/powershell/azure/overview) 中的下列步驟來安裝和設定 Azure PowerShell。

2. 如有必要，建立新的資源群組，如下所示。 在此案例中，會建立名為 *TestRG*的資源群組。 如需資源群組的詳細資訊，請瀏覽 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。

    ```powershell   
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    預期的輸出：

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/[Subscription Id]/resourceGroups/TestRG    
3. 建立新的 VNet，名為 *TestVNet*：

    ```powershell
    New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location centralus
    ```

    預期的輸出：

        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                   : W/"[Id]"
        ProvisioningState          : Succeeded
        Tags                       : 
        AddressSpace               : {
                                   "AddressPrefixes": [
                                     "192.168.0.0/16"
                                   ]
                                  }
        DhcpOptions                : {}
        Subnets                    : []
        VirtualNetworkPeerings     : []
4. 將虛擬網路物件儲存在收數中：

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

   > [!TIP]
   > 執行 `$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus` 可以將步驟 3 和 4 結合在一起。
   > 

5. 將子網路加入到新的 VNet 變數中：

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
    ```

    預期的輸出：
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {}
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24"
                                  }
                                ]
        VirtualNetworkPeerings     : []

6. 對您想要建立的每個子網路重複上述步驟 5。 以下命令會為本案例建立 *BackEnd* 子網路：

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24
    ```

7. 雖然建立了子網路，但是它們目前只以本機變數的形式存在，並用來擷取您在前述步驟 4 中建立的 VNet。 若要儲存對 Azure 的變更，請執行下列命令：

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```
   
    預期的輸出：
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {
                                  "DnsServers": []
                                }
        Subnets               : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  },
                                  {
                                    "Name": "BackEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                    "AddressPrefix": "192.168.2.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  }
                                ]
        VirtualNetworkPeerings : []

## <a name="next-steps"></a>後續步驟

了解如何連接︰

- 虛擬機器 (VM) 至虛擬網路；請閱讀[建立 Windows VM](../virtual-machines/virtual-machines-windows-ps-create.md)。 但不是如文章中的步驟建立 VNet 和子網路，而是選取現有的 VNet 和子網路來連接 VM。
- 虛擬網路至其他虛擬網路；請閱讀[連接 VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。
- 虛擬網路至內部部署網路；使用網站對網站虛擬私人網路 (VPN) 或 ExpressRoute 線路。 如需了解做法，請閱讀[使用網站對網站 VPN 將 VNet 連接到內部部署網路](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)以及[將 VNet 連結至 ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-arm.md)。
