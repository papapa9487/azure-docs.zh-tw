---
title: "建立、變更或刪除 Azure 公用 IP 位置 | Microsoft Docs"
description: "了解如何建立、變更或刪除公用 IP 位址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 5bffea350061231e1dc664b3abcbf79950a54402
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-public-ip-address"></a>建立、變更或刪除公用 IP 位址

了解公用 IP 位址，以及如何建立、變更和刪除公用 IP 位址。 公用 IP 位址是可加以設定的資源。 將公用 IP 位址指派給其他 Azure 資源，能夠：
- 對以下資源進行輸入網際網路連線：Azure 虛擬機器、Azure 虛擬機器擴展集、Azure VPN 閘道、應用程式閘道和網際網路面向的 Azure Load Balancer。 如果沒有指派的公用 IP 位址，Azure 資源無法從網際網路接收輸入的通訊。 某些 Azure 資源本質上就可透過公用 IP 位址存取，而其他資源必須有指派的公用 IP 位址，才可以從網際網路存取。
- 使用可預測 IP 位址對網際網路進行輸出連線。 例如，虛擬機器不需有指派的公用 IP 位址，即可對網際網路進行輸出通訊，但其位址是由 Azure 網路位址轉譯。 指派公用 IP 位址給資源，可讓您知道輸出連線所使用的 IP 位址。 雖然可預測，但位址可能根據選擇的指派方法而有所變更。 如需詳細資訊，請參閱＜[建立公用 IP 位址](#create-a-public-ip-address)＞。 若要深入了解 Azure 資源的輸出連線，請閱讀[了解輸出連線](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。

## <a name="before-you-begin"></a>開始之前

在完成本文任一節的任何步驟之前，請先完成下列工作︰

- 檢閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，以了解公用 IP 位址的限制。
- 使用 Azure 帳戶來登入 Azure [入口網站](https://portal.azure.com)、Azure 命令列介面 (CLI) 或 Azure PowerShell。 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果您使用 PowerShell 命令來完成本文中的工作，請[安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure PowerShell commandlet。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 如果您使用 Azure 命令列介面 (CLI) 命令來完成本文中的工作，請[安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> --help`。 您可以不安裝 CLI 及其必要條件，而是改用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 若要使用 Cloud Shell，請按一下[入口網站](https://portal.azure.com)頂端的 Cloud Shell (**> _**) 按鈕。

公用 IP 位址需要少許費用。 若要檢視價格，請閱讀 [IP 位址價格](https://azure.microsoft.com/pricing/details/ip-addresses)頁面。 

## <a name="create-a-public-ip-address"></a>建立公用 IP 位址

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「公用 ip 位址」。 當「公用 IP 位址」出現於搜尋結果時，按一下它。
3. 在顯示的 [公用 IP 位址] 刀鋒視窗中按一下 [+ 新增]。
4. 在顯示的 [建立公用 IP 位址] 刀鋒視窗中輸入或選取下列設定的值，然後按一下 [建立]：

    |設定|必要？|詳細資料|
    |---|---|---|
    |SKU|是|在 SKU 推出之前所建立的公用 IP 位址全都是**基本** SKU 的公用 IP 位址。  建立公用 IP 位址之後，即無法變更 SKU。 獨立虛擬機器、可用性設定組內的虛擬機器，或虛擬機器擴展集，可以使用基本或標準 SKU。  不允許混用可用性設定組或擴展集內虛擬機器之間的 SKU。 **基本** SKU：如果您要在支援可用性區域的區域中建立公用 IP 位址，**可用性區域**設定依預設會設為「無」。 您可以選擇選取可用性區域，以確保您公用 IP 位址的特定區域。 **標準** SKU：標準 SKU 公用 IP 可與虛擬機器或負載平衡器前端建立關聯。 如果您要在支援可用性區域的區域中建立公用 IP 位址，**可用性區域**設定依預設會設為「區域備援」。 如需可用性區域的詳細資訊，請參閱**可用性區域**設定。 如果您要將位址與標準負載平衡器建立關聯，則需要標準 SKU。 若要深入了解標準負載平衡器，請參閱 [Azure 負載平衡器標準 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 標準 SKU 目前為預覽版本。 在建立標準 SKU 的公用 IP 位址之前，您必須先完成[註冊標準 SKU 預覽版](#register-for-the-standard-sku-preview)中的步驟，並在受支援的位置 (區域) 建立公用 IP 位址。 如需支援位置的清單，請參閱[區域可用性](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region-availability)，並監看 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network)頁面以取得其他區域支援。 當您將標準 SKU 的公用 IP 位址指派給虛擬機器的網路介面時，必須使用[網路安全性群組](security-overview.md#network-security-groups)明確地允許預定的流量。 在建立和關聯網路安全性群組並明確地允許所要流量前，與資源進行的通訊都會失敗。|
    |名稱|是|名稱必須是您選取的資源群組中唯一的名稱。|
    |IP 版本|是| 選取 IPv4 或 IPv6。 公用的 IPv4 位址可指派給數個 Azure 資源，而 IPv6 公用 IP 位址只可指派給網際網路面向的負載平衡器。 負載平衡器可將 IPv6 的流量負載分散到 Azure 虛擬機器。 深入了解[將 IPv6 流量負載分散到虛擬機器](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 如果您選取**標準 SKU**，則無法選擇 *IPv6*。 使用**標準 SKU** 時，您只能建立 IPv4 位址。|
    |IP 位址指派|是|**動態︰**只有在公用 IP 位址與連接至虛擬機器的網路介面建立關聯，而且該虛擬機器第一次啟動之後，才會指派動態位址。 如果網路介面連接的虛擬機器已停止 (已解除配置)，則可變更動態位址。 如果虛擬機器已重新啟動或停止 (但未解除配置)，則位址維持不變。 **靜態︰**建立公用 IP 位址時會指派靜態位址。 即使虛擬機器處於已停止 (已解除配置) 狀態，靜態位址也不會變更。 只有在刪除網路介面後才會釋出位址。 您可以在建立網路介面後變更指派方法。 如果您選取 *IPv6* 作為 **IP 版本**，則指派方法為「動態」。 如果您為 **SKU** 選取*標準*，則指派方法為「靜態」。|
    |閒置逾時 (分鐘)|否|不需依賴用戶端傳送保持連線訊息，讓 TCP 或 HTTP 連線保持開啟的分鐘數。 如果您選取 IPv6 作為 **IP 版本**，則無法變更此值。 |
    |DNS 名稱標籤|否|在您建立名稱的 Azure 位置 (跨越所有訂用帳戶和所有位置) 中必須是唯一的。 Azure 公用 DNS 服務會自動登錄名稱和 IP 位址，以便您連線至具有此名稱的資源。 Azure 會將 *location.cloudapp.azure.com* (其中 location 是您選取的位置) 附加至您提供的名稱，以建立完整的 DNS 名稱。 如果您選擇兩個位址版本都建立，則會指派相同的 DNS 名稱給 IPv4 和 IPv6 位址。 Azure DNS 服務包含 IPV4 A 和 IPv6 AAAA 名稱記錄，並且會在查詢 DNS 名稱時回應這兩個記錄。 用戶端選擇要與哪一個位址 (IPv4 或 IPv6) 通訊。|
    |建立 IPv6 (或 IPv4) 位址|否| 顯示 IPv6 或 IPv4 取決於您選取的 **IP 版本**。 例如，如果您選取 **IPv4** 作為 **IP 版本**，則此處會顯示 **IPv6**。 如果您為 **SKU** 選取「標準」，則無法建立 IPv6 位址。
    |名稱 (僅在您核取 [建立 IPv6 (或 IPv4) 位址] 核取方塊時顯示)|是 (如果您選取 [建立 IPv6] \(或 IPv4) 核取方塊)。|該名稱必須與此清單中的第一個**名稱**不同。 如果您選擇同時建立 IPv4 和 IPv6 位址，則入口網站會建立兩個個別的公用 IP 位址資源，並各指派一個 IP 位址版本。|
    |IP 位址指派 (僅在您核取 [建立 IPv6 (或 IPv4) 位址] 核取方塊時顯示)|是 (如果您選取 [建立 IPv6] \(或 IPv4) 核取方塊)。|如果核取方塊顯示**建立 IPv4 位址**，表示您可以選擇指派方法。 如果核取方塊顯示**建立 IPv6 位址**，表示您無法選擇指派方法，因為指派方法必須為**動態**。|
    |訂用帳戶|是|所在的[訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)必須與您想要與公用 IP 位址建立關聯的資源相同。|
    |資源群組|是|所在的[資源群組](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)可以與您想要與公用 IP 位址建立關聯的資源相同或不同。|
    |位置|是|所在的[位置](https://azure.microsoft.com/regions) (也稱為區域) 必須與您想要與公用 IP 位址建立關聯的資源相同。|
    |可用性區域| 否 | 只有在您選取受支援的位置時，才會出現此設定。 如需受支援位置的清單，請參閱[可用性區域概觀](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 可用性區域目前為預覽版本。 選取區域或區域備援選項之前，您必須先完成[註冊可用性區域預覽版](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#get-started-with-the-availability-zones-preview)中的步驟。 如果您選取**基本** SKU，則會自動為您選取「無」。 如果您想要保證特定區域，可選取特定區域。 或選擇非區域備援。 如果您選取**標準** SKU：會自動為您選取區域備援，並針對區域失敗進行資料路徑復原。 如果您希望保證對區域失敗無法復原的特定區域，則可選取特定區域。
  

**命令**

雖然入口網站提供建立兩個公用 IP 位址資源 (一個 IPv4 和一個 IPv6) 的選項，但下列的 CLI 和 PowerShell 命令則是會以其中一個 IP 版本的位址建立一個資源。 如果您想要兩個公用 IP 位址資源 (每個 IP 版本各一個)，您必須執行該命令兩次，並針對公用 IP 位址資源指定不同名稱和版本。 

|工具|命令|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>檢視、變更公用 IP 位址的設定，或刪除公用 IP 位址

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「公用 ip 位址」。 當「公用 IP 位址」出現於搜尋結果時，按一下它。
3. 在顯示的 [公用 IP 位址] 刀鋒視窗中，按一下您要檢視、變更設定，或刪除的公用 IP 位址名稱。
4. 在針對公用 IP 位址顯示的刀鋒視窗中，根據您要檢視、刪除或變更公用 IP 位址，完成下列其中一個選項。
    - **檢視**：刀鋒視窗的 [概觀] 區段會顯示公用 IP 位址的主要設定，例如和該位址關聯的網路介面 (如果位址與網路介面關聯)。 入口網站不會顯示位址版本 (IPv4 或 IPv6)。 若要檢視版本資訊，請使用 PowerShell 或 CLI 命令來檢視公用 IP 位址。 如果 IP 位址版本是 IPv6 時，指派的位址不會顯示在入口網站、PowerShell 或 CLI 。 
    - **刪除**：若要刪除公用 IP 位址，請在刀鋒視窗的 [概觀] 區段中按一下 [刪除]。 如果位址目前與 IP 組態相關聯，則無法加以刪除。 如果位址目前與組態相關聯，請按一下 [解除關聯] 來解除位址與 IP 組態的關聯。
    - **變更**：按一下 [組態]。 使用[建立公用 IP 位址](#create-a-public-ip-address)一節中步驟 4 的資訊來變更設定。 若要將 IPv4 位址的指派從靜態變更為動態，您必須先解除公用 IPv4 位址與相關聯 IP 組態的關聯。 您可以接著將指派方法變更為動態，然後按一下 [關聯] 讓 IP 位址與相同 IP 組態、不同組態建立關聯，您也可以讓它解除關聯。 若要解除公用 IP 位址的關聯，請在 [概觀] 區段中，按一下 [解除關聯]。

>[!WARNING]
>當您將指派方法從靜態變更為動態時，您會遺失已指派給公用 IP 位址的 IP 位址。 雖然 Azure 公用 DNS 伺服器會維護靜態或動態位址與任何 DNS 名稱標籤 (如果您定義一個位置) 之間的對應，但是動態 IP 位址可能會在虛擬機器處於停止 (已解除配置) 狀態後啟動時變更。 若要防止位址變更，請指派靜態 IP 位址。

**命令**

|工具|命令|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#list) 可列出公用 IP 位址、[az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#show) 可顯示設定；[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) 可進行更新；[az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) 可進行刪除|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) 可擷取公用 IP 位址物件並檢視其設定、[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) 可更新設定；[Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) 可進行刪除|

## <a name="register-for-the-standard-sku-preview"></a>註冊標準 SKU 預覽版

> [!NOTE]
> 預覽版本的功能可能沒有與正式發行版本功能相同層級的可用性和可靠性。 不支援預覽功能、可能有受限的功能，以及可能無法在所有 Azure 位置提供使用。 

您必須先註冊預覽版，才可以建立標準 SKU 公用 IP 位址。 請完成下列步驟以註冊預覽版：

1. 安裝並設定 Azure [PowerShell](/powershell/azure/install-azurerm-ps)。
2. 執行 `Get-Module -ListAvailable AzureRM` 命令以查看您安裝之 AzureRM 模組的版本。 您必須安裝 4.4.0 版或更高版本。 如果您未安裝，可以從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureRM)安裝最新版本。
3. 使用 `login-azurermaccount` 命令登入 Azure。
4. 請輸入下列命令以註冊預覽版：
   
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

5. 藉由輸入下列命令，確認您已註冊預覽版︰

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

## <a name="next-steps"></a>後續步驟
請在建立下列 Azure 資源時，指派公用 IP 位址︰

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器
- [網際網路面向的 Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 應用程式閘道](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [使用 Azure VPN 閘道的站對站連線](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
