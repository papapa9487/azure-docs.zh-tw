---
title: "設定 Azure 網路介面的 IP 位址 | Microsoft Docs"
description: "了解如何對網路介面新增、變更和移除私人與公用 IP 位址。"
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
ms.openlocfilehash: c309c7c25a3ed75e96dec8046934530e24890f38
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>新增、變更或移除 Azure 網路介面的 IP 位址

了解如何對網路介面新增、變更和移除公用與私人 IP 位址。 指派給網路介面的私人 IP 位址可讓虛擬機器與 Azure 虛擬網路及已連線網路中的其他資源進行通訊。 私人 IP 位址還可使用無法預測的 IP 位址，啟用到網際網路的輸出通訊。 指派給網路介面的[公用 IP 位址](virtual-network-public-ip-address.md)，可啟用從網際網路到虛擬機器的輸入通訊。 公用 IP 位址還可使用可預測的 IP 位址，啟用從虛擬機器到網際網路的輸出通訊。 如需詳細資訊，請參閱[了解 Azure 中的輸出連線](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 

如果您需要建立、變更或刪除網路介面，請閱讀[管理網路介面](virtual-network-network-interface.md)一文。 如果您需要新增或移除虛擬機器的網路介面，請閱讀[新增或移除網路介面](virtual-network-network-interface-vm.md)一文。 


## <a name="before-you-begin"></a>開始之前

在完成本文任一節的任何步驟之前，請先完成下列工作︰

- 檢閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，以了解公用和私人 IP 位址的限制。
- 使用 Azure 帳戶來登入 Azure [入口網站](https://portal.azure.com)、Azure 命令列介面 (CLI) 或 Azure PowerShell。 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果您使用 PowerShell 命令來完成本文中的工作，請[安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure PowerShell commandlet。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 如果您使用 Azure 命令列介面 (CLI) 命令來完成本文中的工作，請[安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> --help`。 您可以不安裝 CLI 及其必要條件，而是改用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 若要使用 Cloud Shell，請按一下[入口網站](https://portal.azure.com)頂端的 Cloud Shell (**> _**) 按鈕。

## <a name="add-ip-addresses"></a>新增 IP 位址

您可以將所需的多個[私人](#private)和[公用](#public) [IPv4](#ipv4) 位址新增至網路介面，但不得超過 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文所列的限制。 您無法使用入口網站將 IPv6 位址新增至現有網路介面 (不過，在建立網路介面時，可以使用入口網站將私人 IPv6 位址新增至網路介面)。 針對未連接至虛擬機器的現有網路介面，可以使用 PowerShell 或 CLI 來將私人 IPv6 位址新增到[次要 IP 組態](#secondary) (只要目前尚無次要 IP 組態)。 您無法使用任何工具將公用 IPv6 位址新增至網路介面。 如需使用 IPv6 位址的詳細資訊，請參閱 [IPv6](#ipv6)。 

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在出現的 [網路介面] 刀鋒視窗中，按一下您要新增 IPv4 位址的網路介面。
4. 在所選網路介面的刀鋒視窗中，按一下 [設定] 區段中的 [IP 組態]。
5. 在針對 IP 組態開啟的刀鋒視窗中按一下 [+ 新增]。
6. 指定下列各項，然後按一下 [確定]，以關閉 [新增 IP 組態] 刀鋒視窗：

    |設定|必要？|詳細資料|
    |---|---|---|
    |名稱|是|在網路介面中必須是唯一的|
    |類型|是|由於您要新增 IP 組態至現有的網路介面，而每個網路介面都必須有一個[主要](#primary) IP 組態，所以您只能選擇 [次要]。|
    |私人 IP 位址指派方法|是|[**動態**](#dynamic)：Azure 會為其中部署網路介面的子網路位址範圍指派下一個可用位址。 [**靜態**](#static)：您會為其中部署網路介面的子網路位址範圍指派未使用的位址。|
    |公用 IP 位址|否|**已停用：**IP 組態目前沒有相關聯的公用 IP 位址資源。 **已啟用：**選取現有的 IPv4 公用 IP 位址，或建立一個新的。 若要了解如何建立公用 IP 位址，請閱讀[公用 IP 位址](virtual-network-public-ip-address.md#create-a-public-ip-address)一文。|
7. 請完成[將多個 IP 位址指派給虛擬機器作業系統](virtual-network-multiple-ip-addresses-portal.md#os-config)一文中的指示，以手動方式將次要私人 IP 位址新增至虛擬機器作業系統。 有關手動將 IP 位址新增至虛擬機器作業系統之前的特殊考量，請參閱[私人](#private) IP 位址。 請勿將任何公用 IP 位址新增至虛擬機器作業系統。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-address-settings"></a>變更 IP 位址設定

您可能需要變更 IPv4 位址的指派方法、變更靜態 IPv4 位址，或變更指派給網路介面的公用 IP 位址。 如果您要變更與虛擬機器中次要網路介面相關聯之次要 IP 組態的私人 IPv4 位址 (深入了解[主要和次要網路介面](virtual-network-network-interface-vm.md#about))，請先讓虛擬機器進入停止 (已取消配置) 狀態，再完成下列步驟︰ 

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在出現的 [網路介面] 刀鋒視窗中，按一下您要檢視或變更 IP 位址設定的網路介面。
4. 在所選網路介面的刀鋒視窗中，按一下 [設定] 區段中的 [IP 組態]。
5. 在針對 IP 組態開啟的刀鋒視窗中，按一下清單中您要修改的 IP 位址。
6. 使用本文之[新增 IP 組態](#create-ip-config)一節中步驟 6 的設定資訊，視需要變更設定。 按一下 [儲存] 以關閉您所變更之 IP 組態的刀鋒視窗。

>[!NOTE]
>如果主要網路介面有多個 IP 組態，而且您變更主要 IP 組態的私人 IP 位址，則必須以手動方式將主要和次要 IP 位址重新指派給 Windows 內的網路介面 (對 Linux 而言並非必要)。 若要以手動方式將 IP 位址指派給作業系統內的網路介面，請閱讀[將多個 IP 位址指派給虛擬機器](virtual-network-multiple-ip-addresses-portal.md#os-config)一文。 有關手動將 IP 位址新增至虛擬機器作業系統之前的特殊考量，請參閱[私人](#private) IP 位址。 請勿將任何公用 IP 位址新增至虛擬機器作業系統。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-ip-addresses"></a>移除 IP 位址

您可以從網路介面移除[私人](#private)和[公用](#public) IP 位址，但一律必須對網路介面指派至少一個私人 IPv4 位址。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在出現的 [網路介面] 刀鋒視窗中，按一下您要從中移除 IP 位址的網路介面。
4. 在所選網路介面的刀鋒視窗中，按一下 [設定] 區段中的 [IP 組態]。
5. 以滑鼠右鍵按一下您想要刪除的[次要](#secondary) IP 組態 (您無法刪除[主要](#primary)組態)，按一下 [刪除]，然後按一下 [是] 以確認刪除。 如果組態有相關聯的公用 IP 位址資源，此資源會與 IP 組態解除關聯，但不會刪除資源。
6. 關閉 [IP 組態] 刀鋒視窗。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configurations"></a>IP 組態

[私人](#private)和 (選擇性) [公用](#public) IP 位址會指派給為網路介面指派的一或多個 IP 組態。 IP 組態有兩種：

### <a name="primary"></a>主要

每個網路介面都需指派一個主要 IP 組態。 主要 IP 組態：

- 可為它指派一個[私人](#private) [IPv4](#ipv4) 位址。 您無法將私人 [IPv6](#ipv6) 位址指派給主要 IP 組態。
- 也可為它指派一個[公用](#public) IPv4 位址。 您無法將公用 IPv6 位址指派給主要或次要 IP 組態。 不過，您可以將公用 IPv6 位址指派給 Azure Load Balancer，以負載平衡流量到虛擬機器的私人 IPv6 位址。 如需詳細資訊，請參閱 [IPv6 的詳細資訊和限制](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations)。

### <a name="secondary"></a>次要

除了主要 IP 組態以外，也可為網路介面指派多個次要 IP 組態或不指派。 次要 IP 組態：

- 必須為它指派私人 IPv4 或 IPv6 位址。 如果位址是 IPv6，網路介面只能有一個次要 IP 組態。 如果位址是 IPv4，可以為網路介面指派多個次要 IP 組態。 若要深入了解可為網路介面指派多少個私人和公用 IPv4 位址，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。  
- 如果私人 IP 位址是 IPv4，也可為它指派公用 IPv4 位址。 如果私人 IP 位址是 IPv6，則無法指派公用 IPv4 或 IPv6 位址給 IP 組態。 將多個 IP 位址指派給網路介面對有些案例很有幫助，例如︰
    - 在單一伺服器上，以不同 IP 位址和 SSL 憑證裝載多個網站或服務。
    - 做為網路虛擬設備 (例如防火牆或負載平衡器) 的虛擬機器。
    - 能夠將任何網路介面的任何私人 IPv4 位址新增到 Azure Load Balancer 後端集區。 在過去，只能將主要網路介面的主要 IPv4 位址新增到後端集區。 若要深入了解如何負載平衡多個 IPv4 組態，請參閱[負載平衡多個 IP 組態](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。 
    - 能夠負載平衡一個指派給網路介面的 IPv6 位址。 若要深入了解如何負載平衡到私人 IPv6 位址，請參閱[負載平衡 IPv6 位址](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。


## <a name="address-types"></a>位址類型

您可以指派下列類型的 IP 位址給 [IP 組態](#ip-configurations)：

### <a name="private"></a>私人

私人 [IPv4](#ipv4) 位址可讓虛擬機器與虛擬網路或其他已連線網路中的其他資源進行通訊。 虛擬機器無法使用私人 [IPv6](#ipv6) 位址進行輸入或輸出通訊。唯一的例外是， 虛擬機器可以使用 IPv6 位址與 Azure Load Balancer 通訊。 如需詳細資訊，請參閱 [IPv6 的詳細資訊和限制](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations)。 

根據預設，Azure DHCP 伺服器會將 Azure 網路介面[主要 IP 組態](#primary)的私人 IPv4 位址指派給虛擬機器作業系統內的網路介面。 除非必要，您永遠不應手動設定虛擬機器作業系統內的網路介面 IP 位址。 

> [!WARNING]
> 如果設為虛擬機器作業系統內之網路介面主要 IP 位址的 IPv4 位址，不同於連接至 Azure 中的虛擬機器之主要網路介面主要 IP 組態的私人 IPv4 位址，可能會中斷與該虛擬機器的連線。

在某些案例中，您必須手動設定虛擬機器作業系統內的網路介面 IP 位址。 例如，將多個 IP 位址新增至 Azure 虛擬機器時，您必須手動設定 Windows 作業系統的主要和次要 IP 位址。 針對 Linux 虛擬機器，您可能只需要手動設定次要 IP 位址。 如需詳細資訊，請參閱[將 IP 位址新增至 VM 作業系統](virtual-network-multiple-ip-addresses-portal.md#os-config)。 手動設定作業系統內的 IP 位址時，建議您一律使用靜態 (而非動態) 指派方法為網路介面的 IP 組態指派位址。 使用靜態方法指派位址，可確保位址在 Azure 內不會變更。 如果需要變更指派給 IP 組態的位址，建議您：

1. 若要確保虛擬機器會接收來自 Azure DHCP 伺服器的位址，請將作業系統變更回透過 DHCP 指派 IP 位址並重新啟動虛擬機器。
2. 停止 (解除配置) 虛擬機器。
3. 變更 Azure 內之 IP 組態的 IP 位址。
4. 啟動虛擬機器。
5. [手動設定](virtual-network-multiple-ip-addresses-portal.md#os-config)作業系統內的次要 IP 位址 (以及 Windows 內的主要 IP 位址)，以符合您在 Azure 中的設定。
 
若依上述步驟執行，指派給 Azure 內之網路介面的私人 IP 位址，將與虛擬機器作業系統內的私人 IP 位址相同。 若要記錄訂用帳戶內已為哪些虛擬機器手動設定作業系統內的 IP 位址，請考慮在虛擬機器加入 Azure[標記](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)。 例如，您可以使用「IP 位址指派：靜態」。 如此一來，就可以輕鬆了解在訂用帳戶內，已為哪些虛擬機器手動設定作業系統內的 IP 位址。

除了讓虛擬機器與同一虛擬網路或已連線虛擬網路內的其他資源通訊，私人 IP 位址也可讓虛擬機器與網際網路進行輸出通訊。 輸出連線是由 Azure 轉譯為不可預測的公用 IP 位址的來源網路位址。 若要深入了解 Azure 輸出網際網路連線能力，請閱讀[Azure 輸出網際網路連線能力](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。 您無法從網際網路對虛擬機器的私人 IP 位址進行輸入通訊。 如果您的輸出連線需要可預測的公用 IP 位址，請建立公用 IP 位址資源與網路介面的關聯。

### <a name="public"></a>公開

透過公用 IP 位址資源指派的公用 IP 位址，可從網際網路對虛擬機器進行輸入連線。 對網際網路的輸出連線使用的是可預測的 IP 位址。 如需詳細資訊，請參閱[了解 Azure 中的輸出連線](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 您可以 (但不需要) 對 IP 組態指派公用 IP 位址。 如果您不透過與公用 IP 位址資源建立關聯，來指派公用 IP 位址給虛擬機器，虛擬機器仍然可和網際網路進行輸出通訊。 在此情況下，私人 IP 位址是由 Azure 轉譯為不可預測的公用 IP 位址的來源網路位址。 若要深入了解公用 IP 位址資源，請參閱[公用 IP 位址資源](virtual-network-public-ip-address.md)。

您可以指派給網路介面的私人和公用 IP 位址數目有所限制。 如需詳細資訊，請閱讀 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。

> [!NOTE]
> Azure 會將虛擬機器的私人 IP 位址轉譯為公用 IP 位址。 因此，虛擬機器的作業系統不會察覺有任何公用 IP 位址指派給它，所以不需在作業系統內手動指派公用 IP 位址。

## <a name="assignment-methods"></a>指派方法

您可以使用下列其中一個指派方法來指派公用和私人 IP 位址︰

### <a name="dynamic"></a>動態

系統預設會指派動態的私人 IPv4 和 IPv6 (選擇性) 位址。 

- **僅限公用**：Azure 會從每個 Azure 區域專屬的範圍指派位址。 若要了解每個區域會被指派哪些範圍，請參閱 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 您可以在虛擬機器停止 (解除配置) 並重新啟動時變更位址。 無論使用任一種指派方法，都無法將公用 IPv6 位址指派給 IP 組態。
- **僅限私人**：Azure 會保留每個子網路位址範圍內的前四個位址，並不會指派位址。 Azure 會將子網路位址範圍內的下一個可用位址指派給資源。 例如，如果子網路的位址範圍是 10.0.0.0/16，而且已指派位址 10.0.0.0.4-10.0.0.14 (已保留 0-.3)，Azure 會將 10.0.0.15 指派給資源。 動態是預設配置方法。 指派之後，只有在網路介面遭到刪除、指派給相同虛擬網路內的不同子網路，或配置方法變更為靜態，並指定不同的 IP 位址後，才會釋出動態 IP 位址。 根據預設，當您的配置方法從動態變更為靜態時，Azure 會將先前動態指派的位址指派為靜態位址。 您只能使用動態指派方法指派私人 IPv6 位址。

### <a name="static"></a>靜態

您可以 (選擇性) 指派公用或私人靜態 IPv4 位址給 IP 組態。 您無法指派靜態公用或私人 IPv6 位址給 IP 組態。 若要深入了解 Azure 如何指派靜態的公用 IPv4 位址，請參閱[公用 IP 位址](virtual-network-public-ip-address.md)一文。

- **僅限公用**：Azure 會從每個 Azure 區域專屬的範圍指派位址。 若要了解每個區域會被指派哪些範圍，請參閱 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 位址不會變更，除非其指派目的地的公用 IP 位址資源遭到刪除，或指派方法變更為動態。 如果公用 IP 位址資源與 IP 設定相關聯，則變更指派方法前，必須先取消與 IP 設定的關聯。
- **僅限私人**：您選取並指派子網路位址範圍內的位址。 您指派的位址可以是子網路位址範圍內的任何位址，但該位址不是子網路位址範圍內的前四個位址之一，而且目前並未指派給子網路中的任何其他資源。 只有在刪除網路介面後，才會釋出靜態位址。 如果您將配置方法變更為靜態，Azure 會以動態方式將先前指派的靜態 IP 位址指派為動態位址 (即使此位址不是子網路位址範圍內的下一個可用位址)。 如果網路介面已指派給相同虛擬網路內的不同子網路，位址也會跟著變更，但若要將網路介面指派給不同的子網路，您必須先將配置方法從靜態變更為動態。 一旦您將網路介面指派給不同的子網路，您即可將配置方法變回靜態，並從新的子網路位址範圍中指派 IP 位址。

## <a name="ip-address-versions"></a>IP 位址版本

指派位址時，您可以指定下列版本：

### <a name="ipv4"></a>IPv4

每個網路介面都必須有一個[主要](#primary) IP 組態具有指派的[私人](#private) [IPv4](#ipv4) 位址。 您可以新增一或多個[次要](#secondary) IP 組態，其各擁有一個 IPv4 私人及 (選擇性) 一個 IPv4 [公用](#public) IP 位址。

### <a name="ipv6"></a>IPv6

您可以指派一個私人 [IPv6](#ipv6) 位址給網路介面的次要 IP 組態 (或不指派)。 網路介面目前不能有任何次要 IP 組態。 您無法使用入口網站新增具有 IPv6 位址的 IP 組態。 使用 PowerShell 或 CLI 將具有私人 IPv6 位址的 IP 組態新增至現有的網路介面。 網路介面無法附加至現有的 VM。

> [!NOTE]
> 雖然您可以使用入口網站建立具有私人 IPv6 位址的網路介面，但無法使用入口網站將現有的網路介面新增到新的或現有的虛擬機器。 使用 PowerShell 或 Azure CLI 2.0 來建立具有私人 IPv6 位址的網路介面，再於建立虛擬機器時連結該網路介面。 您無法將具有私人 IPv6 位址的網路介面連接至現有的虛擬機器。 您無法使用任何工具 (入口網站、CLI 或 PowerShell)，將私人 IPv6 位址新增至連接至虛擬機器的任一網路介面的 IP 組態。

您無法將公用 IPv6 位址指派給主要或次要 IP 組態。

## <a name="skus"></a>SKU

公用 IP 位址是透過基本或標準 SKU 建立而成。  如需 SKU 差異的詳細資訊，請參閱[管理公用 IP 位址](virtual-network-public-ip-address.md)。

> [!NOTE]
> 當您將標準 SKU 的公用 IP 位址指派給虛擬機器的網路介面時，必須使用[網路安全性群組](security-overview.md#network-security-groups)明確地允許預定的流量。 在建立和關聯網路安全性群組並明確地允許所要流量前，與資源進行的通訊都會失敗。

## <a name="next-steps"></a>後續步驟
若要建立具有不同 IP 組態的虛擬機器，請閱讀下列文章：

|Task|工具|
|---|---|
|建立具有多個 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|建立具有多個 IPv4 位址的單一 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|建立具有私人 IPv6 位址的單一 NIC VM (在 Azure Load Balancer 後端)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure Resource Manager 範本](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
