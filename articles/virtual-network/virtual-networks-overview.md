---
title: "Azure 虛擬網路 | Microsoft Docs"
description: "了解 Azure 虛擬網路概念和功能。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: jdial
ms.openlocfilehash: 6d6afd2b9b956138ed400fbd6cabd3b480fde0f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-network"></a>Azure 虛擬網路

Azure 虛擬網路服務可讓 Azure 資源與虛擬網路 (VNet) 安全地彼此連接。 VNet 是您的網路在雲端中的身分。 VNet 是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 您也可以將 VNet 連線到內部部署網路。 下圖顯示 Azure 虛擬網路服務的各項功能︰

![網路圖表](./media/virtual-networks-overview/virtual-network-overview.png)

若要深入了解下列 Azure 虛擬網路功能，請按一下各項功能︰
- **[隔離︰](#isolation)**VNet 會彼此隔離。 您可以為使用相同 CIDR 位址區塊的開發、測試和生產環境建立個別的 VNet。 相反地，您可以建立多個使用不同 CIDR 位址區塊的 VNet 並將這些網路連接在一起。 您可以將 VNet 分成多個子網路。 Azure 會針對連線至 VNet 的 VM 和雲端服務角色執行個體提供內部名稱解析。 您可以選擇地將 VNet 設定成使用自己的 DNS 伺服器，而不是使用 Azure 內部名稱解析。
- **[網際網路連線能力︰](#internet)**根據預設，連線至 VNet 的所有 Azure 虛擬機器 (VM) 和雲端服務角色執行個體都可以存取網際網路。 您也可以視需要啟用特定資源的輸入存取。
- **[Azure 資源連線能力︰](#within-vnet)**Azure 資源 (例如雲端服務和 VM) 可以連線至相同的 VNet。 如果資源位於不同的子網路，則可使用私人 IP 位址彼此連接。 Azure 會提供子網路、VNet 和內部部署網路之間的預設路由，因此您不必設定及管理路由。
- **[VNet 連線能力︰](#connect-vnets)**VNet 可以彼此連線，讓連線至任何 VNet 的資源能夠與任何其他 VNet 上的任何資源進行通訊。
- **[內部部署連線能力︰](#connect-on-premises)**VNet 可以透過您的網路與 Azure 之間的私人網路連線，或透過經由網際網路的站對站 VPN 連線，連線到內部部署網路。
- **[流量篩選︰](#filtering)**可以依照來源 IP 位址和連接埠、目的地 IP 位址和連接埠以及通訊協定的輸入和輸出，篩選 VM 和雲端服務角色執行個體網路流量。
- **[路由︰](#routing)**您可以設定您自己的路由，或使用透過網路閘道的 BGP 路由，選擇性地覆寫 Azure 的預設路由。

## <a name = "isolation"></a>網路隔離和分割

您可以在每個 Azure [訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和 Azure [區域](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)內實作多個 VNet。 每個 VNet 會與其他 VNet 隔離。 對於每個 VNet，您可以︰
- 使用公用和私人 (RFC 1918) 位址指定自訂私人 IP 位址空間。 Azure 會從您指派的位址空間，將私人 IP 位址指派給連線至 VNet 的資源。
- 將 VNet 分成一或多個子網路，並將 VNet 位址空間的一部分配置給每個子網路。
- 使用 Azure 提供的名稱解析，或指定自有的 DNS 伺服器以供連線至 VNet 的資源使用。 若要深入了解 VNet 中的名稱解析，請閱讀 [VM 和雲端服務的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)一文。

## <a name = "internet"></a>連線至網際網路
根據預設，連線至 VNet 的所有資源都具有網際網路的輸出連線能力。 資源的私人 IP 位址會由 Azure 基礎結構進行來源網路位址轉譯 (SNAT) 成為公用 IP 位址。 若要深入了解輸出網際網路連線能力，請閱讀[了解 Azure 中的輸出連線](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address)一文。 您可以實作自訂路由和流量篩選，以變更預設連線能力。

若要進行從網際網路通對 Azure 資源的輸入通訊，或進行對網際網路的輸出通訊 (未經 SNAT)，則必須指派公用 IP 位址給資源。 若要深入了解公用 IP 位址，請閱讀[公用 IP 位址](virtual-network-public-ip-address.md)一文。

## <a name="within-vnet"></a>連線 Azure 資源
您可以將數個 Azure 資源連線至 VNet，例如虛擬機器 (VM)、雲端服務、App Service 環境及虛擬機器擴展集。 VM 會透過網路介面 (NIC) 連線至 VNet 內的子網路。 若要深入了解 NIC，請閱讀[網路介面](virtual-network-network-interface.md)一文。

## <a name="connect-vnets"></a>連線虛擬網路

您可以將 VNet 互相連線，讓連線至任一 VNet 的資源能夠跨越 VNet 彼此通訊。 您可以使用下列一個或兩個選項將 VNet 彼此連線：
- **對等互連︰**讓連線至相同 Azure 位置內不同 Azure VNet 的資源彼此通訊。 如果資源已連線到相同的 VNet，則跨越 VNet 的頻寬和延遲就一樣。 若要深入了解對等互連，請閱讀[虛擬網路對等互連](virtual-network-peering-overview.md)文章。
- **VNet 對 VNet 連線︰**讓連線至相同或不同 Azure 位置內不同 Azure VNet 的資源彼此通訊。 不同於對等互連，VNet 之間的頻寬有限，因為流量必須通過 Azure VPN 閘道。 若要深入了解如何透過 VNet 對 VNet 連線來連線 VNet，請閱讀[設定 VNet 對 VNet 連線](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。

## <a name="connect-on-premises"></a>連線到內部部署網路

您可以使用下列選項的任意組合，將內部部署網路連線至 VNet︰
- **點對站虛擬私人網路 (VPN)：**建立於一部連線到您網路的電腦與 VNet 之間。 如果您剛開始使用 Azure，此連線類型就很適合您，也適用於開發人員，因為它幾乎不需要變更您現有的網路。 連線會使用 SSTP 通訊協定，透過網際網路提供電腦與 VNet 之間的加密通訊。 點對站 VPN 的延遲無法預期，因為流量會周遊網際網路。
- **站對站 VPN：**建立於 VPN 裝置與 Azure VPN 閘道之間。 此連線類型可讓您授權的任何內部部署資源存取 VNet。 此連線是 IPSec/IKE VPN，可透過網際網路提供內部部署裝置與 Azure VPN 閘道之間的加密通訊。 站對站連線的延遲無法預期，因為流量會周遊網際網路。
- **Azure ExpressRoute：**透過 ExpressRoute 合作夥伴，建立於您的網路與 Azure 之間。 此連線是私人連線。 流量不會周遊網際網路。 ExpressRoute 連線的延遲無法預期，因為流量不會周遊網際網路。

若要深入了解所有先前的連線選項，請閱讀[連線拓撲圖](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams)一文。

## <a name="filtering"></a>篩選網路流量
您可以使用下列一個或兩個選項，篩選子網路之間的網路流量︰
- **網路安全性群組 (NSG)：**每個 NSG 可以包含多個輸入和輸出安全性規則，讓您依照來源和目的地 IP 位址、連接埠和通訊協定篩選流量。 您可以將 NSG 套用至 VM 中的每個 NIC。 您也可以將 NSG 套用至 NIC 或其他 Azure 資源所連線的子網路。 若要深入了解 NSG，請閱讀[網路安全性群組](virtual-networks-nsg.md)一文。
- **網路虛擬裝置 (NVA)：**NVA 是可執行以下軟體的 VM：執行防火牆等網路功能的軟體。 請檢視 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) 中可用的 NVA 清單。 此外，也可取得提供 WAN 最佳化和其他網路流量功能的 NVA。 NVA 通常使用於使用者定義或 BGP 路由。 您也可以使用 NVA 來篩選 VNet 之間的流量。

## <a name="routing"></a>路由網路流量

根據預設，Azure 會建立路由表，讓連線至任何 VNet 中任何子網路的資源彼此通訊。 您可以實作下列一個或兩個選項，覆寫 Azure 所建立的預設路由︰
- **使用者定義的路由︰**您可以建立自訂路由表，其中的路由可控制每個子網路的流量會路由傳送至的位置。 若要深入了解使用者定義的路由，請閱讀[使用者定義的路由](virtual-networks-udr-overview.md)文章。
- **BGP 路由︰**如果您使用 Azure VPN 閘道或 ExpressRoute 連線將 VNet 連線至內部部署網路，則可將 BGP 路由傳播至 VNet。

## <a name="pricing"></a>價格

虛擬網路、子網路、路由表或網路安全性群組均免費。 輸出網際網路頻寬使用量、公用 IP 位址、虛擬網路對等互連、VPN 閘道和 ExpressRoute 都有各自的價格結構。 如需詳細資訊，請檢視[虛擬網路](https://azure.microsoft.com/pricing/details/virtual-network)、[VPN 閘道](https://azure.microsoft.com/pricing/details/vpn-gateway)和 [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) 價格頁面。

## <a name="faq"></a>常見問題集

若要檢閱虛擬網路相關常見問題的解答，請參閱[虛擬網路常見問題集](virtual-networks-faq.md)文章。


## <a name="next-steps"></a>接續步驟

- 完成[建立第一個虛擬網路](virtual-network-get-started-vnet-subnet.md)一文中的步驟，以建立第一個 VNet，並將一些 VM 連線至該 VNet。
- 完成[設定點對站連線](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文中的步驟，以建立對 VNet 的點對站連線。
- 深入了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
