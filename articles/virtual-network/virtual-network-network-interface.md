---
title: "建立、變更或刪除 Azure 網路介面 | Microsoft Docs"
description: "了解何謂網路介面，以及要如何建立網路介面、變更其設定和刪除網路介面。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 7dafb491cec908ffbb3683991919654f3d3eb452
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-network-interface"></a>建立、變更或刪除網路介面

了解如何建立網路介面、變更其設定和刪除網路介面。 網路介面可讓 Azure 虛擬機器與網際網路、Azure 以及內部部署資源進行通訊。 在使用 Azure 入口網站建立虛擬機器時，入口網站會以預設設定為您建立一個網路介面。 您可以改為選擇使用自訂設定建立網路介面，並在建立虛擬機器時新增一或多個網路介面。 您可能也想變更現有網路介面的預設網路介面設定。 本文說明如何以自訂設定建立網路介面、變更現有的設定 (例如，網路篩選 (網路安全性群組) 指派、子網路指派、DNS 伺服器設定和 IP 轉送)，以及刪除網路介面。

如果您需要新增、變更或移除網路介面的 IP 位址，請閱讀[管理 IP 位址](virtual-network-network-interface-addresses.md)一文。 如果您需要新增或移除虛擬機器的網路介面，請閱讀[新增或移除網路介面](virtual-network-network-interface-vm.md)一文。


## <a name="before-you-begin"></a>開始之前

在完成本文任一節的任何步驟之前，請先完成下列工作︰

- 檢閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，以了解網路介面的限制。
- 使用 Azure 帳戶來登入 Azure [入口網站](https://portal.azure.com)、Azure 命令列介面 (CLI) 或 Azure PowerShell。 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果您使用 PowerShell 命令來完成本文中的工作，請[安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure PowerShell commandlet。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 如果您使用 Azure 命令列介面 (CLI) 命令來完成本文中的工作，請[安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> --help`。 您可以不安裝 CLI 及其必要條件，而是改用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 若要使用 Cloud Shell，請按一下[入口網站](https://portal.azure.com)頂端的 Cloud Shell (**> _**) 按鈕。

## <a name="create-a-network-interface"></a>建立網路介面

在使用 Azure 入口網站建立虛擬機器時，入口網站會以預設設定為您建立一個網路介面。 如果您想要指定所有網路介面設定，可以使用自訂設定建立網路介面，並在建立虛擬機器時將該網路介面連接到虛擬機器 (使用 PowerShell 或 Azure CLI)。 您也可以建立網路介面，並將它新增至現有的虛擬機器 (使用 PowerShell 或 Azure CLI)。 若要深入了解如何使用現有的網路介面建立虛擬機器，或如何新增或移除現有虛擬機器的網路介面，請閱讀[新增或移除網路介面](virtual-network-network-interface-vm.md)一文。 在建立網路介面之前，用來建立網路介面的相同位置和訂用帳戶中必須已有既存的[虛擬網路](virtual-networks-create-vnet-arm-pportal.md)。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在顯示的 [網路介面] 刀鋒視窗中，按一下 [+ 新增]。
4. 在顯示的 [建立網路介面] 刀鋒視窗中，輸入或選取下列設定的值，然後按一下 [建立]：

    |設定|必要？|詳細資料|
    |---|---|---|
    |名稱|是|名稱必須是您選取的資源群組中唯一的名稱。 在經過一段時間後，您的 Azure 訂用帳戶中可能會有好幾個網路介面。 請閱讀[命名慣例](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)一文以了解有關建立命名慣例時的建議，以便您可以更輕鬆地管理好幾個網路介面。 建立網路介面之後，便無法變更名稱。|
    |虛擬網路|是|選取網路介面的虛擬網路。 您只能將網路介面指派給和網路介面的訂用帳戶和位置相同的虛擬網路。 網路介面建立後，即無法變更為它指派的虛擬網路。 新增網路介面的虛擬機器必須也位於和網路介面相同的位置和訂用帳戶。|
    |子網路|是|選取您所選虛擬網路內的子網路。 在網路介面建立後，您可以變更為它指派的子網路。|
    |私人 IP 位址指派|是| 在此設定中，您選擇的是 IPv4 位址指派方法。 選擇下列任一指派方法︰**動態︰**若選取此選項，Azure 會自動指派您所選子網路位址空間中的可用位址。 當網路介面所在的虛擬機器進入已停止 (已解除配置) 狀態後重新加以啟動時，Azure 可能會對此網路介面指派不同的位址。 如果虛擬機器未曾處於已停止 (已解除配置) 狀態後又重新啟動，位址會維持不變。 **靜態︰**若選取此選項，您必須手動指派所選子網路位址空間中的可用 IP 位址。 直到您變更靜態位址或刪除網路介面，靜態位址才會變更。 您可以在建立網路介面後變更指派方法。 Azure DHCP 伺服器會將此位址指派給虛擬機器作業系統內的網路介面。|
    |網路安全性群組|否| 保持設為 [無]、選取現有的[網路安全性群組](virtual-networks-nsg.md)或[建立網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)。 網路安全性群組可讓您篩選進出網路介面的網路流量。 您可以將網路安全性群組套用至網路介面或不套用。 您也可以將網路安全性群組套用至網路介面的目的地子網路或不套用。 當網路安全性群組套用至網路介面和網路介面的目的地子網路後，有時會發生非預期結果。 若要為套用至網路介面和子網路的網路安全性群組進行疑難排解，請閱讀[針對網路安全性群組進行疑難排解](virtual-network-nsg-troubleshoot-portal.md#nsg)一文。|
    |訂用帳戶|是|選取其中一個 Azure [訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)。 連接網路介面的虛擬機器和虛擬機器連線的虛擬網路，必須存在於相同的訂用帳戶。|
    |私人 IP 位址 (IPv6)|否| 如果您選取此核取方塊，除了指派給網路介面的 IPv4 位址以外，IPv6 位址也會指派給網路介面。 如需使用 IPv6 搭配網路介面的重要資訊，請參閱本文的 [IPv6](#IPv6) 一節。 您無法選取 IPv6 位址的指派方法。 如果您選擇要指派 IPv6 位址，會使用動態方法指派。
    |IPv6 名稱 (只有在核取 [私人 IP 位址 (IPv6)] 核取方塊時才會顯示) |是，如果核取 [私人 IP 位址 (IPv6)] 核取方塊。| 這個名稱會指派給網路介面的次要 IP 組態。 深入了解本文[檢視網路介面設定](#view-network-interface-settings)一節中的 IP 組態。|
    |資源群組|是|選取現有的[資源群組](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)，或建立一個群組。 網路介面可以位於與其連結的虛擬機器或虛擬機器所連線的虛擬網路相同或不同的資源群組中。|
    |位置|是|連接網路介面的虛擬機器和虛擬機器連線的虛擬網路，必須存在於相同的[地區](https://azure.microsoft.com/regions) (亦稱為區域)。|

當您建立網路介面時，入口網站並未提供將公用 IP 位址指派給網路介面的選項，然而在使用入口網站建立虛擬機器時，入口網站會建立公用 IP 位址，並將它指派給網路介面。 若要了解如何在建立網路介面之後對其新增公用 IP 位址，請閱讀[管理 IP 位址](virtual-network-network-interface-addresses.md)一文。 如果您想要建立具有公用 IP 位址的網路介面，就必須使用 CLI 或 PowerShell 來建立網路介面。

>[!Note]
> 在網路介面連接至虛擬機器和虛擬機器第一次啟動後，Azure 才會指派 MAC 位址給網路介面。 您無法指定 Azure 指派給網路介面的 MAC 位址。 在網路介面遭到刪除或指派給主要網路介面之主要 IP 組態的私人 IP 位址遭到變更之前，MAC 位址會保持指派給網路介面。 若要深入了解 IP 位址和 IP 組態，請閱讀[管理 IP 位址](virtual-network-network-interface-addresses.md)一文。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-network-interface-settings"></a>檢視網路介面設定

您可以在網路介面建立後變更其大部分的設定。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在出現的 [網路介面] 刀鋒視窗中，按一下您要檢視或變更設定的網路介面。
4. 下列設定會列在針對您所選網路介面顯示的刀鋒視窗中︰
    - **概觀：**提供網路介面相關資訊︰例如，為它指派的 IP 位址、為網路介面指派的虛擬網路/子網路，以及網路介面連接的虛擬機器 (如有連接)。 下圖顯示名為 **mywebserver256** 之網路介面的概觀設定：![網路介面概觀](./media/virtual-network-network-interface/nic-overview.png) 您可以按一下**資源群組**或**訂用帳戶名稱**旁邊的 ([變更])，以將網路介面移至不同的資源群組或訂用帳戶。 如果您移動網路介面，則必須移動和網路介面相關的所有資源。 例如，如果網路介面連接至虛擬機器，您也必須移動虛擬機器和其他虛擬機器相關資源。 若要移動網路介面，請閱讀[將資源移至新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal)一文。 該文會列出必要條件，以及如何使用 Azure 入口網站、PowerShell 和 Azure CLI 來移動資源。
    - **IP 組態︰**此處列出指派給 IP 組態的公用與私人 IPv4 和 IPv6 位址。 如果指派給 IP 組態的是 IPv6 位址，則不會顯示位址。 在[設定 Azure 網路介面的 IP 位址](virtual-network-network-interface-addresses.md)一文中深入了解 IP 組態和如何新增和移除 IP 位址。 IP 轉送和子網路指派也會在這一節設定。 若要深入了解這些設定，請閱讀本文的[啟用或停用 IP 轉送](#enable-or-disable-ip-forwarding)和[變更子網路指派](#change-subnet-assignment)章節。
    - **DNS 伺服器︰**您可以指定由 Azure DHCP 伺服器指派給網路介面的 DNS 伺服器。 網路介面可以繼承為它指派之虛擬網路中的設定，或可擁有覆寫此設定的自訂設定。 若要修改所顯示的內容，請完成本文之[變更 DNS 伺服器](#change-dns-servers)一節中的步驟。
    - **網路安全性群組 (NSG)：**顯示與網路介面相關聯的 NSG (如果有的話)。 NSG 包含輸入和輸出規則，可篩選網路介面的網路流量。 如果 NSG 與網路介面相關聯，則會顯示相關聯 NSG 的名稱。 若要修改顯示的內容，請完成[管理網路安全性群組關聯](virtual-network-manage-nsg-arm-portal.md#manage-associations)一文中的步驟。
    - **屬性︰**顯示有關網路介面的重要設定，包括其 MAC 位址 (如果網路介面未連結至虛擬機器，則位址會空白) 以及其所在的訂用帳戶。
    - **有效安全性規則︰**如果網路介面已連結至執行中的虛擬機器，而且 NSG 與網路介面、為它指派的子網路或兩者相關聯，則會列出安全性規則。 若要深入了解所顯示的內容，請閱讀[針對網路安全性群組進行疑難排解](virtual-network-nsg-troubleshoot-portal.md#nsg)一文。 若要深入了解 NSG，請閱讀[網路安全性群組](virtual-networks-nsg.md)一文。
    - **有效路由︰**如果網路介面已連結至執行中的虛擬機器，則會列出路由。 路由是下列各項的組合：Azure 預設路由、任何使用者定義的路由 (UDR)，以及為網路介面指派之子網路可能存在的任何 BGP 路由。 若要深入了解所顯示的內容，請閱讀[針對路由進行疑難排解](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface)一文。 若要深入了解 Azure 預設值和 UDR，請閱讀[使用者定義的路由](virtual-networks-udr-overview.md)一文。
    - **一般 Azure Resource Manager 設定：**若要深入了解一般 Azure Resource Manager 設定，請閱讀[活動記錄](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)、[存取控制 (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)、[標籤](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)、[鎖定](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[自動化指令碼](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)文章。

**命令**

如果指派給網路介面的是 IPv6 位址，PowerShell 輸出會傳回已指派位址的事實，但不會傳回指派的位址。 同樣地，CLI 在其輸出中會傳回已指派位址的事實，但位址則為 *null*。

|工具|命令|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) 用以檢視訂用帳戶中的網路介面；[az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) 用以檢視網路介面的設定|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) 用以檢視訂用帳戶中的網路介面；或檢視網路介面的設定|

## <a name="change-dns-servers"></a>變更 DNS 伺服器

DNS 伺服器是由 Azure DHCP 伺服器指派給虛擬機器作業系統內的網路介面。 指派的 DNS 伺服器就是網路介面的任何 DNS 伺服器設定。 若要深入了解網路介面的名稱解析設定，請參閱[虛擬機器的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)。 網路介面可以從虛擬網路繼承設定，或使用它自己的唯一設定來覆寫虛擬網路的設定。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在出現的 [網路介面] 刀鋒視窗中，按一下您要檢視或變更設定的網路介面。
4. 在您所選網路介面的刀鋒視窗中，按一下 [設定] 底下的 [DNS 伺服器]。
5. 按一下：
    - **繼承自虛擬網路 (預設值)**︰選擇此選項，可繼承為網路介面指派的虛擬網路的 DNS 伺服器設定。 在虛擬網路層級，可定義自訂 DNS 伺服器或 Azure 提供的 DNS 伺服器。 Azure 提供的 DNS 伺服器可為指派給相同虛擬網路的資源解析其主機名稱。 對於指派給不同虛擬網路的資源，則必須使用 FQDN 來解析。
    - **自訂**︰您可以設定自己的 DNS 伺服器，以解析跨多個虛擬網路的名稱。 輸入您要做為 DNS 伺服器之伺服器的 IP 位址。 您指定的 DNS 伺服器位址只會指派給此網路介面，並會覆寫為此網路介面指派的虛擬網路的任何 DNS 設定。
6. 按一下 [儲存] 。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="enable-or-disable-ip-forwarding"></a>啟用或停用 IP 轉送

IP 轉送讓網路介面連接的虛擬機器能夠：
- 接收以下網路流量：其目的地不是指派給任何 IP 組態 (已指派給網路介面) 的其中一個 IP 位址。
- 傳送以下網路流量：其來源 IP 位址不同於指派給其中一個網路介面之 IP 組態的 IP 位址。

對於接收需轉送之流量的虛擬機器，其所連接的每個網路介面，都必須啟用此設定。 無論虛擬機器是連接多個網路介面或連接單一網路介面，都可以轉送流量。 IP 轉送雖然是 Azure 設定，但虛擬機器也必須執行能夠轉送流量的應用程式，例如防火牆、WAN 最佳化及負載平衡應用程式。 當虛擬機器執行網路應用程式時，虛擬機器通常稱為網路虛擬設備。 您可以檢視 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) 中可立即部署的網路虛擬設備清單。 IP 轉送通常使用於使用者定義的路由。 若要深入了解使用者定義的路由，請閱讀[使用者定義的路由](virtual-networks-udr-overview.md)文章。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在出現的 [網路介面] 刀鋒視窗中，按一下您要啟用或停用 IP 轉送的網路介面。
4. 在所選網路介面的刀鋒視窗中，按一下 [設定] 區段中的 [IP 組態]。
5. 按一下 [已啟用] 或 [已停用] \(預設設定) 來變更設定。
6. 按一下 [儲存] 。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet-assignment"></a>變更子網路指派

您可以變更為網路介面指派的子網路 (而非虛擬網路)。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在出現的 [網路介面] 刀鋒視窗中，按一下您要檢視或變更設定的網路介面。
4. 在所選網路介面的刀鋒視窗中，按一下 [設定] 底下的 [IP 組態]。 如果所列 IP 組態的私人 IP 位址旁邊有 **(靜態)** 字樣，您必須完成下列步驟以將 IP 位址的指派方法變更為動態。 您必須使用動態指派方法來指派所有的私人 IP 位址，以便變更網路介面的子網路指派。 如果您使用動態方法來指派位址，請繼續執行步驟 5。 如果您使用靜態指派方法指派了任何 IPv4 位址，請完成下列步驟來將指派方法變更為動態︰
    - 從 IP 組態清單中，按一下您要變更 IPv4 位址指派方法的 IP 組態。
    - 在為此 IP 組態顯示的刀鋒視窗中，按一下 [動態] 做為 [指派] 方法。 您無法使用靜態指派方法指派 IPv6 位址。
    - 按一下 [儲存] 。
5. 從 [子網路] 下拉式清單中選取要和網路介面連線的子網路。
6. 按一下 [儲存] 。 系統就會從新的子網路位址範圍指派新的動態位址。 在對新的子網路指派網路介面之後，您可以從新的子網路位址範圍指派靜態 IPv4 位址 (如果您做此選擇的話)。 若要深入了解如何為網路介面新增、變更和移除 IP 位址，請閱讀[管理 IP 位址](virtual-network-network-interface-addresses.md)一文。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-a-network-interface"></a>刪除網路介面

只要網路介面未連接至虛擬機器，您便可將它刪除。 如果網路介面已連接至虛擬機器，您必須先讓虛擬機器進入已停止 (已解除配置) 狀態，接著在網路介面與虛擬機器中斷連結後，才能將它刪除。 若要讓網路介面與虛擬機器中斷連結，請完成[新增或移除網路介面](virtual-network-network-interface-vm.md)一文之[讓網路介面與虛擬機器中斷連結](virtual-network-network-interface-vm.md#vm-remove-nic)一節中的步驟。 刪除虛擬機器會中斷連結所有已連接的網路介面，但不會刪除網路介面。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 以滑鼠右鍵按一下您要刪除的網路介面，然後按一下 [刪除]。
4. 按一下 [是] 以確認刪除網路介面。

當您刪除網路介面時，會釋出指派給它的所有 MAC 或 IP 位址。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>後續步驟
若要建立具有多個網路介面或 IP 位址的虛擬機器，請閱讀下列文章：

**命令**

|Task|工具|
|---|---|
|建立具有多個 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|建立具有多個 IPv4 位址的單一 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|建立具有私人 IPv6 位址的單一 NIC VM (在 Azure Load Balancer 後端)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure Resource Manager 範本](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
