---
title: "Azure 虛擬網路 | Microsoft Docs"
description: "了解 Azure 虛擬網路概念和功能。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.openlocfilehash: dc6916bd25c5a020fdcef0707fe28a1e34fb0f88
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="azure-virtual-network"></a>Azure 虛擬網路

Microsoft Azure 虛擬網路服務可讓 Azure 資源與虛擬網路中的其他資源進行安全的通訊。 虛擬網路是您的網路在雲端中的身分。 虛擬網路是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 您可以將虛擬網路連接到其他虛擬網路，或連接到內部部署網路。 下圖顯示 Azure 虛擬網路服務的各項功能︰

![網路圖表](./media/virtual-networks-overview/virtual-network-overview.png)

若要深入了解下列 Azure 虛擬網路功能，請按一下各項功能︰
- **[隔離︰](#isolation)**虛擬網路會彼此隔離。 您可以為使用相同 CIDR (例如，10.0.0.0/0) 位址區塊的開發、測試和生產環境建立個別的虛擬網路。 相反地，您可以建立多個使用不同 CIDR 位址區塊的虛擬網路並將這些網路連接在一起。 您可以將虛擬網路分成多個子網路。 Azure 為部署在虛擬網路中的虛擬機器和 Azure 雲端服務角色執行個體，提供內部名稱解析。 您可以選擇將虛擬機器設定成使用自己的 DNS 伺服器，而不使用 Azure 內部名稱解析。
- **[網際網路通訊︰](#internet)**根據預設，虛擬網路中的所有 Azure 虛擬機器和雲端服務角色執行個體都可以存取網際網路。 您也可以視需要啟用特定資源的輸入存取。
- **[Azure 資源通訊：](#within-vnet)**可在同一個虛擬網路中部署雲端服務和虛擬機器之類的 Azure 資源。 如果資源位於不同的子網路，則可使用私人 IP 位址彼此通訊。 Azure 會提供子網路、VNet 和內部部署網路之間的預設路由，因此您不必設定及管理路由。 不過，如有需要，您可以自訂 Azure 的路由。
- **[虛擬網路連線：](#connect-vnets)**虛擬網路可以彼此連線，以便任何虛擬網路中的資源與其他任何虛擬網路中的資源進行通訊。
- **[內部部署連線：](#connect-on-premises)**虛擬網路可以私下連線到內部部署網路，也可透過網際網路使用站對站 VPN 連線。
- **[流量篩選︰](#filtering)**可以依照來源 IP 位址和連接埠、目的地 IP 位址和連接埠以及通訊協定的輸入和輸出，篩選虛擬機器和雲端服務角色執行個體網路流量。
- **[路由︰](#routing)**您可以設定您自己的路由，或透過網路閘道傳播 BGP 路由，選擇性地覆寫 Azure 的預設路由。

## <a name = "isolation"></a>網路隔離和分割

您可以在每個 Azure [訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和 Azure [區域](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)內實作多個虛擬網路。 每個虛擬網路都與其他虛擬網路隔離。 對於每個虛擬網路，您可以：
- 使用公用和私人 (RFC 1918) 位址指定自訂私人 IP 位址空間。 Azure 會從您指派的位址空間，將私人 IP 位址指派給虛擬網路中的資源。
- 將虛擬網路分成一或多個子網路，並將虛擬網路位址空間的一部分配置給每個子網路。
- 使用 Azure 提供的名稱解析，或指定自有的 DNS 伺服器以供虛擬網路中的資源使用。 若要深入了解虛擬網路中的名稱解析，請閱讀 [VM 和雲端服務的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)一文。

## <a name = "internet"></a>網際網路通訊
依預設，虛擬網路中的所有資源都能夠進行向網際網路的輸出通訊。 資源的私人 IP 位址會進行來源網路位址轉譯 (SNAT)，成為 Azure 基礎結構選取的公用 IP 位址。 若要深入了解輸出網際網路連線能力，請閱讀[了解 Azure 中的輸出連線](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address)一文。 若要避免輸出的網際網路連線，您可以實作自訂路由或流量篩選。

若要進行從網際網路通對 Azure 資源的輸入通訊，或進行對網際網路的輸出通訊 (未經 SNAT)，則必須指派公用 IP 位址給資源。 若要深入了解公用 IP 位址，請閱讀[公用 IP 位址](virtual-network-public-ip-address.md)一文。

## <a name="within-vnet"></a>Azure 資源之間的安全通訊

您可以在虛擬網路中部署虛擬機器。 虛擬機器可透過網路介面與虛擬網路中的其他資源進行通訊。 若要深入了解網路介面，請參閱[網路介面](virtual-network-network-interface.md)。

您也可以將一些其他類型的 Azure 資源部署到虛擬網路，例如 Azure 虛擬機器、Azure 雲端服務、Azure 應用程式服務環境和 Azure 虛擬機器擴展集。 如需將您可以部署到虛擬網路的 Azure 資源列出的完整清單，請參閱[Azure 服務的虛擬網路服務整合](virtual-network-for-azure-services.md)。

某些資源無法部署到虛擬網路，但可讓您限制僅有虛擬網路中的資源進行通訊。 若要深入了解如何限制資源的存取，請參閱[虛擬網路服務端點](virtual-network-service-endpoints-overview.md)。 

## <a name="connect-vnets"></a>連線虛擬網路

您可以讓虛擬網路彼此連線，以便虛擬網路中的資源能夠使用虛擬網路對等互連彼此通訊。 不同虛擬網路中的資源之間的通訊頻寬和延遲均會相同，就像是同一個虛擬網路中的資源一樣。 若要深入了解對等互連，請閱讀[虛擬網路對等互連](virtual-network-peering-overview.md)文章。

## <a name="connect-on-premises"></a>連線到內部部署網路

您可以使用下列選項的任意組合，將內部部署網路連線至虛擬網路︰
- **點對站虛擬私人網路 (VPN)：**在虛擬網路與網路中的一台電腦之間建立。 每台想要建立虛擬網路連線的電腦必須獨立設定其連線。 如果您剛開始使用 Azure，此連線類型就很適合您，也適用於開發人員，因為它幾乎不需要變更您現有的網路。 連線會使用 SSTP 通訊協定，透過網際網路提供電腦與虛擬網路之間的加密通訊。 點對站 VPN 的延遲無法預期，因為流量會周遊網際網路。
- **站對站 VPN：**建立於 VPN 裝置與虛擬網路中部署的 Azure VPN 閘道之間。 此連線類型可讓您授權的任何內部部署資源存取虛擬網路。 此連線是 IPSec/IKE VPN，可透過網際網路提供內部部署裝置與 Azure VPN 閘道之間的加密通訊。 站對站連線的延遲無法預期，因為流量會周遊網際網路。
- **Azure ExpressRoute：**透過 ExpressRoute 合作夥伴，建立於您的網路與 Azure 之間。 此連線是私人連線。 流量不會周遊網際網路。 ExpressRoute 連線的延遲無法預期，因為流量不會周遊網際網路。

若要深入了解所有先前的連線選項，請閱讀[連線拓撲圖](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams)一文。

## <a name="filtering"></a>篩選網路流量
您可以使用下列一個或兩個選項，篩選子網路之間的網路流量︰
- **網路安全性群組：**網路安全性群組可以包含多個輸入和輸出安全性規則，讓您依照來源和目的地 IP 位址、連接埠和通訊協定篩選流量。 您可以將網路安全性群組套用於虛擬機器中的每個網路介面。 您也可以將網路安全性群組套用於子網路的網路介面，或其他 Azure 資源。 若要深入了解網路安全性群組，請參閱[網路安全性群組](security-overview.md#network-security-groups)。
- **網路虛擬設備：**網路虛擬設備是執行軟體的虛擬機器，該軟體可執行網路功能 (例如防火牆)。 以檢視 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) 中可用網路虛擬設備的清單。 此外，也可取得提供 WAN 最佳化和其他網路流量功能的網路虛擬設備。 網路虛擬設備通常使用於使用者定義或 BGP 路由。 您也可以使用網路虛擬設備，篩選虛擬網路之間的流量。

## <a name="routing"></a>路由網路流量

根據預設，Azure 會建立路由表，讓連線至任何虛擬網路中任何子網路的資源彼此通訊。 您可以實作下列一個或兩個選項，覆寫 Azure 所建立的預設路由︰
- **使用者定義的路由︰**您可以建立自訂路由表，其中的路由可控制每個子網路的流量會路由傳送至的位置。 若要深入了解使用者定義的路由，請參閱[使用者定義的路由](virtual-networks-udr-overview.md#user-defined)。
- **BGP 路由︰**如果您使用 Azure VPN 閘道或 ExpressRoute 連線將虛擬網路連線至內部部署網路，則可將 BGP 路由傳播至虛擬網路。

## <a name="pricing"></a>價格

虛擬網路、子網路、路由表或網路安全性群組均免費。 輸出網際網路頻寬使用量、公用 IP 位址、虛擬網路對等互連、VPN 閘道和 ExpressRoute 都有各自的價格結構。 如需詳細資訊，請檢視[虛擬網路](https://azure.microsoft.com/pricing/details/virtual-network)、[VPN 閘道](https://azure.microsoft.com/pricing/details/vpn-gateway)和 [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) 價格頁面。

## <a name="faq"></a>常見問題集

若要檢閱 Azure 虛擬網路相關常見問題的解答，請參閱[虛擬網路常見問題集](virtual-networks-faq.md)一文。


## <a name="next-steps"></a>接續步驟

- 完成[建立第一個虛擬網路](virtual-network-get-started-vnet-subnet.md)中的步驟，以建立第一個虛擬網路，並將一些虛擬機器部署到該網路中。
- 完成[設定點對站連線](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步驟，以建立對虛擬網路的點對站連線。
- 深入了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
