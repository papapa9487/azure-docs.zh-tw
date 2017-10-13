---
title: "加入、變更或刪除 Azure 虛擬網路子網路 | Microsoft Docs"
description: "了解如何加入、變更或刪除 Azure 中的虛擬網路子網路。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.openlocfilehash: 85ba6ef3e51c339a77eb9b4198c4f87e2a64cf09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>加入、變更或刪除虛擬網路子網路

了解如何加入、變更或刪除虛擬網路子網路。 

如果您不熟悉虛擬網路，建議在加入、變更或刪除子網路之前，先閱讀 [Azure 虛擬網路概觀](virtual-networks-overview.md)和[建立、變更或刪除虛擬網路](virtual-network-manage-network.md)。 所有部署到虛擬網路的 Azure 資源都會部署到虛擬網路內的子網路。 通常，虛擬網路中會建立多個子網路，以便：
- **篩選子網路之間的流量**。 您可以將網路安全性群組套用至子網路，來篩選虛擬網路中所有資源 (例如虛擬機器) 的輸入和輸出網路流量。 若要深入了解如何建立網路安全性群組，請參閱[建立網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)。
- **控制子網路之間的路由**。 Azure 會建立預設路由，以便在子網路之間自動路由傳送流量。 您可以建立使用者定義的路由，來覆寫 Azure 預設路由。 若要深入了解使用者定義的路由，請參閱[建立使用者定義的路由](virtual-network-create-udr-arm-ps.md)。 

本文說明如何加入、變更和刪除由 Azure Resource Manager 部署模型所建立的虛擬網路子網路。
 
## <a name="before"></a>開始之前

在開始本文所述的工作之前，請先完成下列必要條件：

- 如果您不熟悉虛擬網路，建議您檢閱[建立您的第一個 Azure 虛擬網路](virtual-network-get-started-vnet-subnet.md)中的練習。 這個練習有助您更加熟悉虛擬網路。
- 若要深入了解虛擬網路的限制，請檢閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 使用您的 Azure 帳戶，登入 Azure 入口網站、Azure 命令列工具 (Azure CLI) 或 Azure PowerShell。 如果您沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果您打算使用 Azure PowerShell 命令來完成本文所述的工作，您必須先[安裝並設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure PowerShell Cmdlet。 若要取得範例中 PowerShell 命令的說明，請輸入 `get-help <command> -full`。
- 如果您打算使用 Azure CLI 命令來完成本文所述的工作，則必須完成下列兩者之一：
    - [安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure CLI。
    - 使用 Azure Cloud Shell。 您可以不安裝 CLI 及其相依項目，而是改用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 若要使用 Cloud Shell，請按一下 Azure 入口網站頂端的 Cloud Shell (**> _**) 圖示。 

  若要取得 Azure CLI 命令的說明，請輸入`az <command> --help`。

## <a name="create-subnet"></a>加入子網路

若要加入子網路：

1. 請使用已指派訂用帳戶網路參與者角色權限 (至少) 的帳戶來登入[入口網站](https://portal.azure.com)。 若要深入了解如何將角色和權限指派給帳戶，請參閱 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在入口網站的搜尋方塊中，輸入「虛擬網路」。 按一下搜尋結果中出現的「虛擬網路」。
3. 在 [虛擬網路] 刀鋒視窗中，按一下要在其中新增子網路的虛擬網路。
4. 在 [虛擬網路] 刀鋒視窗中，按一下 [子網路]。
5. 按一下 [+子網路]。
6. 在 [新增子網路] 刀鋒視窗中，輸入下列參數的值︰
    - **名稱**：此名稱必須是虛擬網路中的唯一名稱。
    - **位址範圍**：此範圍必須是虛擬網路位址空間內的唯一範圍。 此範圍不能與虛擬網路內的其他子網路位址範圍重疊。 位址空間必須以「無類別網域間路由選擇」(CIDR) 標記法來指定。 例如，在位址空間為 10.0.0.0/16 的虛擬網路中，您可以定義 10.0.0.0/24 的子網路位址空間。 您可以指定的最小範圍是 /29，此範圍可提供八個 IP 位址供子網路使用。 為了符合通訊協定的規定，Azure 會保留每個子網路中的第一個和最後一個位址。 Azure 還會保留三個位址供 Azure 服務使用。 因此，以 /29 位址範圍所定義的子網路會在子網路中產生三個可用的 IP 位址。 如果您打算將虛擬網路連接至 VPN 閘道，則必須建立一個閘道子網路。 深入了解[閘道子網路位址範圍的具體考量](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 若符合特定條件，您可以在子網路加入後變更其位址範圍。 若要了解如何變更子網路的位址範圍，請參閱本文的[變更子網路設定](#change-subnet)。
    - **網路安全性群組**：您可以選擇性地建立現有網路安全性群組與子網路的關聯，以控制子網路的輸入和輸出網路流量篩選。 網路安全性群組必須與虛擬網路位於相同的訂用帳戶和位置當中。 該群組也必須使用 Resource Manager 部署模型來建立。 若要深入了解如何建立網路安全性群組，請參閱[網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)。
    - **路由表**︰您可以選擇性地建立現有路由表和子網路的關聯，以控制路由傳送給其他網路的網路流量。 路由表必須與虛擬網路位於相同的訂用帳戶和位置當中。 該群組也必須使用 Resource Manager 部署模型來建立。 若要深入了解如何建立路由表，請參閱[使用者定義的路由](virtual-network-create-udr-arm-ps.md)。
    - **使用者**︰您可以使用內建角色或自有的自訂角色來控制子網路的存取。 若要深入了解如何指派角色和使用者以存取子網路，請參閱[使用角色指派來管理 Azure 資源的存取權](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access)。
7. 若要在所選虛擬網路中新增子網路，請按一下 [確定]。

**命令**

|工具|命令|
|---|---|
|Azure CLI|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>變更子網路設定

藉由管理子網路中的資源，您可以變更網路安全性群組、路由表，以及使用者的子網路存取權。 若要深入了解這些設定，請參閱[加入子網路](#create-subnet)的步驟 6。 如果您想要變更子網路的位址空間，則必須先刪除子網路中的所有資源。 不同資源的資源刪除步驟也各異。 若要了解如何刪除子網路中的資源，請閱讀您想要刪除之各個資源類型的適用文件。 若要變更子網路的位址範圍：

1. 請使用已指派 (至少) 網路參與者角色權限的帳戶來登入[入口網站](https://portal.azure.com)，以取得訂用帳戶。 若要深入了解如何將角色和權限指派給帳戶，請參閱 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在入口網站的搜尋方塊中，輸入「虛擬網路」。 按一下搜尋結果中出現的「虛擬網路」。
3. 在 [虛擬網路] 刀鋒視窗中，按一下要變更子網路位址範圍的虛擬網路。
4. 按一下要變更位址範圍的子網路。
5. 在 [子網路] 刀鋒視窗的 [位址範圍] 方塊中，輸入新的位址範圍。 此範圍必須是虛擬網路位址空間內的唯一範圍。 此範圍不能與虛擬網路內的其他子網路位址範圍重疊。 位址空間必須以 CIDR 標記法來指定。 例如，在位址空間為 10.0.0.0/16 的虛擬網路中，您可以定義 10.0.0.0/24 的子網路位址空間。 您可以指定的最小範圍是 /29，此範圍可提供八個 IP 位址供子網路使用。 為了符合通訊協定的規定，Azure 會保留每個子網路中的第一個和最後一個位址。 Azure 還會保留三個位址供 Azure 服務使用。 因此，使用 /29 位址範圍的子網路最終可用的 IP 位址只有三個。 如果您打算將虛擬網路連接至 VPN 閘道，則必須建立一個閘道子網路。 深入了解[閘道子網路位址範圍的具體考量](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 若符合特定條件，您可以在子網路建立好之後變更其位址範圍。 若要了解如何變更子網路的位址範圍，請參閱本文的[變更子網路設定](#change-subnet)。
6. 按一下 [儲存] 。

**命令**

|工具|命令|
|---|---|
|Azure CLI|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>刪除子網路

您只能刪除未包含任何資源的子網路。 如果子網路中含有資源，您必須先刪除其中的資源才能將它刪除。 不同資源的資源刪除步驟也各異。 若要了解如何刪除子網路中的資源，請閱讀您想要刪除之各個資源類型的適用文件。 若要刪除子網路：

1. 請使用已指派 (至少) 網路參與者角色權限的帳戶來登入[入口網站](https://portal.azure.com)，以取得訂用帳戶。 若要深入了解如何將角色和權限指派給帳戶，請參閱 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在入口網站的搜尋方塊中，輸入「虛擬網路」。 按一下搜尋結果中出現的「虛擬網路」。
3. 在 [虛擬網路] 刀鋒視窗中，按一下要刪除其子網路的虛擬網路。
4. 在 [虛擬網路] 刀鋒視窗中，按一下 [設定] 下方的 [子網路]。
5. 在 子網路 刀鋒視窗所示的子網路清單中，以滑鼠右鍵按一下要刪除的子網路，按一下 刪除，然後按一下是，即可刪除子網路。

**命令**

|工具|命令|
|---|---|
|Azure CLI|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>接續步驟

若要在子網路中建立虛擬機器，請參閱[在子網路中建立虛擬網路和部署 VM](virtual-network-get-started-vnet-subnet.md#create-vms)。
