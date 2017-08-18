---
title: "建立、變更或刪除 Azure 虛擬網路 | Microsoft Docs"
description: "了解如何在 Azure 中建立、變更或刪除虛擬網路。"
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
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 74aace2136136c25bc56327d38cfbab168265401
ms.contentlocale: zh-tw
ms.lasthandoff: 08/15/2017

---
# <a name="create-change-or-delete-a-virtual-network"></a>建立、變更或刪除虛擬網路

了解如何建立和刪除虛擬網路以及變更現有虛擬網路的設定，例如 DNS 伺服器和 IP 位址空間。

虛擬網路是您的網路在雲端中的身分。 虛擬網路是專屬於您訂用帳戶的 Azure 雲端。 對於您建立的每個虛擬網路，您可以：
- 選擇要指派的位址空間。 位址空間包含使用無類別網域間路由 (CIDR) 表示法所定義的一或多個位址範圍，例如 10.0.0.0/16。
- 選擇使用 Azure 所提供的 DNS 伺服器，或您自己的 DNS 伺服器。 系統會對連線至虛擬網路的所有資源指派此 DNS 伺服器，以解析虛擬網路中的名稱。
- 將虛擬網路分成多個子網路，讓每個子網路都有自己的位址範圍 (在虛擬網路的位址空間內)。 若要了解如何建立、變更和刪除子網路，請參閱[新增、變更或刪除子網路](virtual-network-manage-subnet.md)。

本文會說明如何使用 Azure Resource Manager 部署模型來建立、變更和刪除虛擬網路。

## <a name="before"></a>開始之前

在開始本文所述的工作之前，請先完成下列必要條件：

- 如果您不熟悉虛擬網路，建議您檢閱[建立您的第一個 Azure 虛擬網路](virtual-network-get-started-vnet-subnet.md)中的練習。 這個練習有助您更加熟悉虛擬網路。
- 若要深入了解虛擬網路的限制，請檢閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 使用您的 Azure 帳戶，登入 Azure 入口網站、Azure 命令列工具 (Azure CLI) 或 Azure PowerShell。 如果您沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果您打算使用 Azure PowerShell 命令來完成本文所述的工作，您必須先[安裝並設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure PowerShell Cmdlet。 若要取得範例中 PowerShell 命令的說明，請輸入 `get-help <command> -full`。
- 如果您打算使用 Azure CLI 命令來完成本文所述的工作，您必須先[安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure CLI。 若要取得 Azure CLI 命令的說明，請輸入`az <command> --help`。


## <a name="create-vnet"></a>建立虛擬網路

若要建立虛擬網路：

1. 請使用已指派訂用帳戶網路參與者角色權限 (至少) 的帳戶來登入[入口網站](https://portal.azure.com)。 若要深入了解如何將角色和權限指派給帳戶，請參閱 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 按一下 [新增]  >  [網路]  >  [虛擬網路]。
3. 在 [虛擬網路] 刀鋒視窗的 [選取部署模型] 方塊中，讓 [Resource Manager] 保持選取狀態，然後按一下 [建立]。
4. 在 [建立虛擬網路] 刀鋒視窗上，輸入或選取下列設定的值，然後按一下 [建立]：
    - **名稱**：您選取用來建立虛擬網路的[資源群組](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)名稱必須是唯一的。 虛擬網路建立後，就不能再變更其名稱。 您可以隨著時間建立多個虛擬網路。 如需命名建議，請參閱[命名慣例](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)。 下列命名慣例有協助於輕鬆管理多個虛擬網路。
    - **位址空間**︰以 CIDR 表示法來指定位址空間。 您可以定義公用或私人的位址空間 (RFC 1918)。 不論您定義的是公用或私人的位址空間，您都只能從虛擬網路、互連的虛擬網路，以及任何已連線到虛擬網路的內部部署網路來連線到位址空間。 您無法新增下列位址空間︰
        - 224.0.0.0/4 (多點傳送)
        - 255.255.255.255/32 (廣播)
        - 127.0.0.0/8 (回送)
        - 169.254.0.0/16 (連結-本機)
        - 168.63.129.16/32 (內部 DNS)

      雖然在建立虛擬網路時，只能定義一個位址空間，但您可以在虛擬網路建立後新增更多位址空間。 若要了解如何將位址空間新增至現有的虛擬網路，請參閱本文中的[新增或移除地址空間](#add-address-spaces)。

      >[!WARNING]
      >如果虛擬網路具有與另一個虛擬網路或內部部署網路重疊的位址空間，就無法連接線這兩個網路。 在您定義位址空間之前，請考慮未來是否想要將虛擬網路連線到其他虛擬網路或內部部署網路。
      >
      >

    - **子網路名稱**：子網路名稱在虛擬網路內必須是唯一的。 子網路建立後，就不能再變更子網路名稱。 當您在建立虛擬網路時，即使虛擬網路不一定要擁有任何子網路，但入口網站仍會要求您定義一個子網路。 當您在入口網站中建立虛擬網路時，您只能定義一個子網路。 建立虛擬網路之後，您可以將更多子網路新增至虛擬網路。 若要在虛擬網路中新增子網路，請參閱[建立、變更或刪除子網路](virtual-network-manage-subnet.md)中的[建立子網路](virtual-network-manage-subnet.md#create-subnet)。 您可以使用 Azure CLI 或 PowerShell，建立具有多個子網路的虛擬網路。

      >[!TIP]
      >有時候，系統管理員會建立不同的子網路來篩選或控制在子網路之間傳送的流量。 在定義子網路之前，請先考慮您想要如何篩選和路由傳送子網路之間的流量。 若要深入了解如何篩選子網路之間的流量，請參閱[網路安全性群組](virtual-networks-nsg.md)。 Azure 會自動路由傳送子網路之間的流量，但您可以覆寫 Azure 預設路由。 若要深入了解如何覆寫 Azure 預設子網路流量路由，請參閱[使用者定義的路由](virtual-networks-udr-overview.md)。
      >

    - **子網路位址範圍**︰此範圍必須位於您針對虛擬網路輸入的位址空間內。 您可以指定的最小範圍是 /29，此範圍可提供八個 IP 位址供子網路使用。 為了符合通訊協定的規定，Azure 會保留每個子網路中的第一個和最後一個位址。 Azure 還會保留三個位址供 Azure 服務使用。 因此，使用 /29 子網路位址範圍的虛擬網路只有三個可用的 IP 位址。 如果您打算將虛擬網路連線至 VPN 閘道，則必須建立一個閘道子網路。 深入了解[閘道子網路位址範圍的具體考量](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 若符合特定條件，您可以在子網路建立好之後變更其位址範圍。 若要了解如何變更子網路的位址範圍，請參閱[新增、變更或刪除子網路](virtual-network-manage-subnet.md)中的[變更子網路設定](#change-subnet)。
    - **訂用帳戶**︰選取[訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)。 您無法在多個 Azure 訂用帳戶中使用相同的虛擬網路。 不過，您可以將一個訂用帳戶中的虛擬網路連線至其他訂用帳戶中的虛擬網路。 若要連線不同訂用帳戶中的虛擬網路，請使用 Azure VPN 閘道或虛擬網路對等互連。 您連線到虛擬網路的任何 Azure 資源必須與虛擬網路位於相同的訂用帳戶中。
    - **資源群組**：選取現有[資源群組](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups)或建立新的資源群組。 您連線到虛擬網路的 Azure 資源，可以位於與虛擬網路相同或不同的資源群組中。
    - **位置**︰選取 Azure [位置](https://azure.microsoft.com/regions/) (也稱為區域)。 虛擬網路只能位於一個 Azure 位置。 不過，您可以使用 VPN 閘道，將某個位置的虛擬網路連線至其他位置的虛擬網路。 您連線到虛擬網路的任何 Azure 資源必須與虛擬網路位於相同的位置。

**命令**

|工具|命令|
|---|---|
|Azure CLI|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>檢視虛擬網路與設定

若要檢視虛擬網路和設定：

1. 請使用已指派訂用帳戶網路參與者角色權限 (至少) 的帳戶來登入[入口網站](https://portal.azure.com)。 若要深入了解如何將角色和權限指派給帳戶，請參閱 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在入口網站的搜尋方塊中，輸入「虛擬網路」。 按一下搜尋結果中出現的「虛擬網路」。
3. 在 [虛擬網路] 刀鋒視窗中，按一下您想要檢視設定的虛擬網路。
4. 您所選虛擬網路的刀鋒視窗上會列出下列設定︰
    - **概觀**︰提供虛擬網路的相關資訊，包括位址空間和 DNS 伺服器。 下列螢幕擷取畫面顯示名為 **MyVNet** 之虛擬網路的概觀設定：

        ![網路介面概觀](./media/virtual-network-manage-network/vnet-overview.png)

      在 [概觀] 刀鋒視窗上，您可以將虛擬網路移至不同的訂用帳戶或資源群組。 若要了解如何移動虛擬網路，請參閱[將資源移到不同的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 該文會列出必要條件，以及如何使用 Azure 入口網站、PowerShell 和 Azure CLI 來移動資源。 所有連線至虛擬網路的資源都必須隨著虛擬網路移動。
    - **位址空間**︰這裡會列出指派給虛擬網路的位址空間。 若要了解如何新增和移除位址空間，請完成本文的[新增或移除位址空間](#address-spaces)中的步驟。
    - **已連線的裝置**︰這裡會列出任何與虛擬網路連線的資源。 在上述螢幕擷取畫面中，有三個網路介面和一個負載平衡器連線到虛擬網路。 您建立並連線到虛擬網路的任何新資源都會在此列出。 如果您刪除已連線到虛擬網路的資源，清單中就不會再顯示該資源。
    - **子網路**︰虛擬網路中現存的子網路清單會在此顯示。 若要了解如何新增和移除子網路，請參閱[新增、變更或刪除子網路](virtual-network-manage-subnet.md)的[建立子網路](virtual-network-manage-subnet.md#create-subnet)和[刪除子網路](virtual-network-manage-subnet.md#delete-subnet)。
    - **DNS 伺服器**︰您可以指定由 Azure 的內部 DNS 伺服器或自訂 DNS 伺服器來為連線到虛擬網路的裝置提供名稱解析。 在使用 Azure 入口網站建立虛擬網路時，虛擬網路預設會使用 Azure 的 DNS 伺服器來解析名稱。 若要修改 DNS 伺服器，請完成本文的[新增、變更或移除 DNS 伺服器](#dns-servers)中的步驟。
    - **對等互連**︰如果訂用帳戶中已有對等互連，這裡便會列出。 您可以檢視現有對等互連的設定，也可以建立、變更或刪除對等互連。 若要深入了解對等互連，請參閱[虛擬網路對等互連](virtual-network-peering-overview.md)。
    - **屬性**︰顯示虛擬網路的相關設定，包括虛擬網路的資源識別碼及其所在的訂用帳戶。
    - **圖表**︰此圖表會以視覺方式來呈現所有連線到虛擬網路的裝置。 此圖表包含有關裝置的一些重要資訊。 若要在此檢視中管理裝置，請在圖表中按一下裝置。
    - **一般 Azure 設定**：若要深入了解 Azure 的一般設定，請參閱下列資訊：
        *   [活動記錄檔](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [存取控制 (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [標記](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [鎖定](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [自動化指令碼](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**命令**

|工具|命令|
|---|---|
|Azure CLI|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="add-address-spaces"></a>新增或移除位址空間

您可以新增和移除虛擬網路的位址空間。 位址空間必須以 CIDR 表示法來指定，而且同一個虛擬網路中的位址空間不能重疊。 您可以定義公用或私人的位址空間 (RFC 1918)。 不論您定義的是公用或私人的位址空間，您都只能從虛擬網路、互連的虛擬網路，以及任何已連線到虛擬網路的內部部署網路來連線到位址空間。 您無法新增下列位址空間︰

- 224.0.0.0/4 (多點傳送)
- 255.255.255.255/32 (廣播)
- 127.0.0.0/8 (回送)
- 169.254.0.0/16 (連結-本機)
- 168.63.129.16/32 (內部 DNS)

若要新增或移除位址空間：

1. 請使用已指派訂用帳戶網路參與者角色權限 (至少) 的帳戶來登入[入口網站](https://portal.azure.com)。 若要深入了解如何將角色和權限指派給帳戶，請參閱 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在入口網站的搜尋方塊中，輸入「虛擬網路」。 在搜尋結果中，選取「虛擬網路」。
3. 在 [虛擬網路] 刀鋒視窗上，按一下您要新增或移除位址空間的虛擬網路。
4. 在 [虛擬網路] 刀鋒視窗中，按一下 [設定] 之下的 [位址空間]。
5. 在位址空間的刀鋒視窗中，完成下列其中一個選項：
    - **新增位址空間**︰輸入新的位址空間。 此位址空間不能與虛擬網路已定義的現有位址空間重疊。
    - **移除地址空間**︰以滑鼠右鍵按一下位址空間，然後按一下 [移除]。 如果位址空間有現存的子網路，您就無法移除該位址空間。 若要移除位址空間，您必須先刪除位址空間中存在的任何子網路 (以及連線至子網路的任何資源)。
6. 按一下 [儲存] 。

**命令**

|工具|命令|
|---|---|
|Azure CLI|僅限 Resource Manager|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>新增、變更或移除 DNS 伺服器

所有連線至虛擬網路的 VM 會向您為虛擬網路指定的 DNS 伺服器註冊。 他們也可使用指定的 DNS 伺服器進行名稱解析。 VM 中的每個網路介面 (NIC) 都可以有自己的 DNS 伺服器設定。 如果 NIC 有自己的 DNS 伺服器設定，其設定就會覆寫虛擬網路的 DNS 伺服器設定。 若要深入了解 NIC DNS 設定，請參閱[網路介面工作和設定](virtual-network-network-interface.md#change-dns-servers)。 若要深入了解 Azure 雲端服務中 VM 和角色執行個體的名稱解析，請參閱 [VM 和角色執行個體的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)。 若要新增、變更或移除 DNS 伺服器：

1. 請使用已指派訂用帳戶網路參與者角色權限 (至少) 的帳戶來登入[入口網站](https://portal.azure.com)。 若要深入了解如何將角色和權限指派給帳戶，請參閱 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在入口網站的搜尋方塊中，輸入「虛擬網路」。 在搜尋結果中，選取「虛擬網路」。
3. 在 [虛擬網路] 刀鋒視窗上，按一下您想要變更 DNS 設定的虛擬網路。
4. 在 [虛擬網路] 刀鋒視窗上，按一下 [設定] 之下的 [DNS 伺服器]。
5. 在列出 DNS 伺服器的刀鋒視窗上，選取下列其中一個選項︰
    - **預設值 (由 Azure 提供)**︰所有資源名稱和私人 IP 位址都會自動向 Azure DNS 伺服器進行註冊。 您可以在連線到相同虛擬網路的任何資源之間解析名稱。 您無法使用此選項來跨虛擬網路解析名稱。 若要跨虛擬網路解析名稱，您必須使用自訂 DNS 伺服器。
    - **自訂**︰您可以為虛擬網路新增一或多部伺服器，數量可達 Azure 的限制。 若要深入了解 DNS 伺服器限制，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic)。 您有下列選擇：
        - **新增地址**︰將伺服器新增至虛擬網路 DNS 伺服器清單。 此選項也會向 Azure 註冊 DNS 伺服器。 如果您已向 Azure 註冊 DNS 伺服器，即可在清單中選取該 DNS 伺服器。
        - **移除位址**︰在您想要移除的伺服器旁，按一下 **X**。刪除伺服器只會從此虛擬網路清單中移除伺服器。 DNS 伺服器會在 Azure 中保持登錄狀態，以便您其他的虛擬網路使用。
        - **將 DNS 伺服器位址重新排序**︰請務必要確認，您為環境所列出的 DNS 伺服器順序是正確的。 DNS 伺服器清單會依其指定的順序來使用。 它們不會當作循環配置資源設定運作。 如果清單上的第一部 DNS 伺服器是可以連線的，用戶端就會使用該 DNS 伺服器，而不管該 DNS 伺服器是否有正常運作。 請移除所有列出的 DNS 伺服器，再以您想要的順序重新新增。
        - **變更位址**：將清單中的 DNS 伺服器醒目提示，然後輸入新名稱。
6. 按一下 [儲存] 。
7. 將連線到虛擬網路的 VM 重新啟動，讓這些 VM 獲得新的 DNS 伺服器設定。 這些 VM 會繼續使用其目前的 DNS 設定，直到您將其重新啟動。

**命令**

|工具|命令|
|---|---|
|Azure CLI|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>刪除虛擬網路

您只能刪除未連線任何資源的虛擬網路。 如果有資源連線至虛擬網路中的任何子網路，您必須先刪除虛擬網路中所有子網路的已連線資源。 不同資源的資源刪除步驟也各異。 若要了解如何刪除已連線至子網路的資源，請閱讀您想要刪除之各個資源類型的適用文件。 若要刪除虛擬網路：

1. 使用 (至少) 具備您訂用帳戶網路參與者角色權限的帳戶來登入[入口網站](https://portal.azure.com)。 若要深入了解如何將角色和權限指派給帳戶，請參閱 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在入口網站的搜尋方塊中，輸入「虛擬網路」。 按一下搜尋結果中出現的「虛擬網路」。
3. 在 [虛擬網路] 刀鋒視窗上，選取您要刪除的虛擬網路。
4. 在 [虛擬網路] 刀鋒視窗上，若要確認沒有任何裝置連線到虛擬網路，請在 [設定] 之下，按一下 [已連線的裝置]。 如果有已連線的裝置，您必須先將其刪除，才能刪除虛擬網路。 如果沒有已連線的裝置，請按一下 [概觀]。
5. 在刀鋒視窗頂端，按一下 [刪除] 圖示。
6. 若要確認刪除虛擬網路，請按一下 [是]。


**命令**

|工具|命令|
|---|---|
|Azure CLI|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>接續步驟

- 若要建立 VM 並將它連線到虛擬網路，請參閱[建立虛擬網路並連線 VM](virtual-network-get-started-vnet-subnet.md#create-vms)。
- 若要篩選虛擬網路之間的網路流量，請參閱[建立網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)。
- 若要讓兩個虛擬交換器對等互連，請參閱[建立虛擬網路對等互連](virtual-network-create-peering.md#portal)。
- 若要了解用於將虛擬網路連線到內部部署網路的選項，請參閱[關於 VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams)。

