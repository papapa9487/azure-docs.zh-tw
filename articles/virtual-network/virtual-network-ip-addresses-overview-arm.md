---
title: "在 Azure 中的 IP 位址類型 | Microsoft Docs"
description: "了解 Azure 中的公用和私人 IP 位址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2017
ms.author: jdial
ms.openlocfilehash: 95f2b57b2012df816c76a1b6ec55ca9f92e134a3
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Azure 中的 IP 位址類型及配置方法

您可以將 IP 位址指派給 Azure 資源，來與其他 Azure 資源、內部部署網路和網際網路進行通訊。 您可以在 Azure 中使用兩種類型的 IP 位址：

* **公用 IP 位址**：用於與網際網路通訊，包括 Azure 公眾對應服務。
* **私人 IP 位址**：用於 Azure 虛擬網路 (VNet) 內的通訊，而當您使用 VPN 閘道或 ExpressRoute 電路將網路擴充至 Azure 時，則使用於內部部署網路內的通訊。

> [!NOTE]
> Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。  本文涵蓋之內容包括使用 Resource Manager 部署模型，Microsoft 建議大部分的新部署使用此模型，而不是[傳統部署模型](virtual-network-ip-addresses-overview-classic.md)。
> 

如果您熟悉傳統部署模型，請參閱 [傳統與 Resource Manager 之間的 IP 定址差異](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments)。

## <a name="public-ip-addresses"></a>公用 IP 位址

Azure 資源可透過公用 IP 位址來與網際網路和 Azure 公眾對應服務 (例如 [Azure Redis 快取](https://azure.microsoft.com/services/cache)、[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs)、[SQL Database](../sql-database/sql-database-technical-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Azure 儲存體](../storage/common/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)) 進行通訊。

在 Azure 資源管理員中， [公用 IP](virtual-network-public-ip-address.md) 位址是有自己的屬性的資源。 可供公用 IP 位址資源與之建立關聯的部分資源如下：

* 虛擬機器網路介面
* 網際網路對應負載平衡器
* VPN 閘道
* 應用程式閘道

### <a name="ip-address-version"></a>IP 位址版本

公用 IP 位址是使用 IPv4 或 IPv6 位址所建立的。 公用的 IPv6 位址只能指派給網際網路對應負載平衡器。

### <a name="sku"></a>SKU

公用 IP 位址是使用下列其中一個 SKU 所建立的：

#### <a name="basic"></a>基本

在 SKU 推出之前所建立的公用 IP 位址全都是基本 SKU 的公用 IP 位址。 SKU 推出後，您則可以選擇指定要讓公用 IP 位址成為哪種 SKU。 基本 SKU 的位址有下列特性：

- 使用靜態或動態配置方法來指派。
- 會指派給任何可以指派公用 IP 位址的 Azure 資源，例如網路介面、VPN 閘道、應用程式閘道和網際網路對應負載平衡器。
- 可指派到特定區域。
- 無區域備援功能。 若要深入了解可用性區域，請參閱[可用性區域概觀](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

#### <a name="standard"></a>標準

標準 SKU 的公用 IP 位址有下列特性：

- 只會使用靜態配置方法來指派。
- 會指派給網路介面或標準的網際網路對應負載平衡器。 如需 Azure 負載平衡器 SKU 的詳細資訊，請參閱 [Azure 負載平衡器的標準 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 預設具有區域備援功能。 可以建立為區域型，並保證在特定可用性區域中。  若要深入了解可用性區域，請參閱[可用性區域概觀](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
 
> [!NOTE]
> 當您將標準 SKU 的公用 IP 位址指派給虛擬機器的網路介面時，您必須使用[網路安全性群組](security-overview.md#network-security-groups)明確地允許預定的流量。  在建立和關聯網路安全性群組並明確地允許所要流量前，與資源進行的通訊都會失敗。

標準 SKU 目前為預覽版本。 在建立標準 SKU 的公用 IP 位址之前，您必須先註冊預覽版本，並在受支援的位置建立該位址。 若要註冊預覽版本，請參閱[註冊標準 SKU 預覽版本](virtual-network-public-ip-address.md#register-for-the-standard-sku-preview)。 如需支援位置 (區域) 清單，請參閱[區域可用性](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region-availability)並監看 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network)頁面以取得其他區域支援。


### <a name="allocation-method"></a>配置方法

有兩種方法可將 IP 位址配置給公用 IP 位址資源：「動態」或「靜態」。 預設配置方法是「動態」 ，此方法 **不會** 在建立 IP 位址時進行配置。 相反地，公用 IP 位址會在您啟動 (或建立) 相關聯的資源 (例如 VM 或負載平衡器) 時進行配置。 此 IP 位址會在您停止 (或刪除) 資源時釋出 。 例如，在從資源 A 釋出後，您就能將該 IP 位址指派給不同的資源。 如果在資源 A 停止時將 IP 位址指派給不同的資源，當您重新啟動資源 A 時，系統會指派不同的 IP 位址。

若要確保相關聯資源的 IP 位址維持不變，您可以明確地將配置方法設定為「靜態」 。 系統會立即指派靜態 IP 位址。 只有在您刪除資源或將其配置方法變更為「動態」時，系統才會釋出該位址。

> [!NOTE]
> 即使將配置方法設定為「靜態」，您也無法指定已指派給公用 IP 位址資源的實際 IP 位址。 Azure 會從資源建立所在的 Azure 位置中可用的 IP 位址集區指派 IP 位址。
>

靜態公用 IP 位址通常用於下列案例：

* 當您必須更新防火牆規則才能與您的 Azure 資源進行通訊時。
* DNS 名稱解析，其中當 IP 位址發生變更時將需要更新 A 記錄。
* 您的 Azure 資源與其他使用 IP 位址型安全性模型的應用程式或服務進行通訊。
* 您使用已連結到 IP 位址的 SSL 憑證。

> [!NOTE]
> Azure 會從對每個 Azure 區域來說都是唯一的範圍來配置公用 IP 位址。 如需詳細資訊，請參閱 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。
>

### <a name="dns-hostname-resolution"></a>DNS 主機名稱解析
您可以指定公用 IP 資源的 DNS 網域名稱標籤，以建立 domainnamelabel.location.cloudapp.azure.com 與 Azure 受管理 DNS 伺服器中的公用 IP 位址的對應。 比方說，如果您建立公用 IP 資源並以 **contoso** 作為**美國西部** Azure 位置中的domainnamelabel，則完整網域名稱 (FQDN) **contoso.westus.cloudapp.azure.com** 會解析為資源的公用 IP 位址。 您可以使用此 FQDN 來建立自訂網域 CNAME 記錄，其指向 Azure 中的公用 IP 位址。

> [!IMPORTANT]
> 所建立的每個網域名稱標籤必須是 Azure 位置中唯一的。  
>

### <a name="virtual-machines"></a>虛擬機器

您可以藉由將公用 IP 位址指派給其**網路介面**，以建立其與 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器的關聯。 您可以將動態或靜態公用 IP 位址指派給虛擬機器。 深入了解如何[將 IP 位址指派給網路介面](virtual-network-network-interface-addresses.md)。

### <a name="internet-facing-load-balancers"></a>網際網路對應負載平衡器

您可以將使用任一 [SKU](#SKU) 所建立的公用 IP 位址指派給負載平衡器**前端**組態，以建立其與 [Azure Load Balancer](../load-balancer/load-balancer-overview.md) 的關聯。 此公用 IP 位址作為負載平衡的虛擬 IP 位址 (VIP)。 您可以將動態或靜態公用 IP 位址指派給負載平衡器前端。 您也可以將多個公用 IP 位址指派給一個負載平衡器前端，以實現 [多重 VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 案例 (例如具有多個 SSL 架構網站的多租用戶環境)。 如需 Azure 負載平衡器 SKU 的詳細資訊，請參閱 [Azure 負載平衡器的標準 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

### <a name="vpn-gateways"></a>VPN 閘道

[Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)會將 Azure 虛擬網路連線到其他 Azure 虛擬網路或內部部署網路。 系統會將公用 IP 位址指派給 VPN 閘道，以便它能與遠端網路通訊。 您只可以將「動態」公用 IP 位址指派給 VPN 閘道。

### <a name="application-gateways"></a>應用程式閘道

您可以將公用 IP 位址指派給閘道的 [前端](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)組態，以建立其與 Azure **應用程式閘道** 的關聯。 此公用 IP 位址可做為負載平衡的 VIP。 您只可以將「動態」公用 IP 位址指派給應用程式閘道前端組態。

### <a name="at-a-glance"></a>快速總覽
下表顯示特定的屬性，公用 IP 位址可透過它關聯到最上層資源，以及顯示可以使用的可能配置方法 (動態或靜態)。

| 最上層資源 | IP 位址關聯 | 動態 | 靜態 |
| --- | --- | --- | --- |
| 虛擬機器 |Linux |是 |是 |
| 網際網路對應負載平衡器 |前端組態 |是 |是 |
| VPN 閘道 |閘道 IP 組態 |是 |否 |
| 前端 |前端組態 |是 |否 |

## <a name="private-ip-addresses"></a>私人 IP 位址
私人 IP 位址可讓 Azure 資源透過 VPN 閘道或 ExpressRoute 電路，與 [虛擬網路](virtual-networks-overview.md) 中或內部部署網路中的其他資源進行通訊，而不必使用可網際網路連線的 IP 位址。

在 Azure Resource Manager 部署模型中，私人 IP 位址會與下列 Azure 資源類型相關聯。

* 虛擬機器網路介面
* 內部負載平衡器 (ILB)
* 應用程式閘道

### <a name="ip-address-version"></a>IP 位址版本

私人 IP 位址是使用 IPv4 或 IPv6 位址所建立的。 您只能使用動態配置方法來指派私人的 IPv6 位址。 您無法在虛擬網路上的私人 IPv6 位址之間進行通訊。 您可以透過網際網路對應負載平衡器，從網際網路對私人的 IPv6 位址進行輸入通訊。 如需詳細資訊，請參閱[建立配置有 IPv6 的網際網路對向負載平衡器](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

### <a name="allocation-method"></a>配置方法

私人 IP 位址是從資源所在虛擬網路子網路的位址範圍進行配置。 私人 IP 位址有兩種配置方法：

- **動態**：Azure 會保留每個子網路位址範圍內的前四個位址，並不會指派位址。 Azure 會將子網路位址範圍內的下一個可用位址指派給資源。 例如，如果子網路的位址範圍是 10.0.0.0/16，而且已指派位址 10.0.0.0.4-10.0.0.14 (已保留 0-.3)，Azure 會將 10.0.0.15 指派給資源。 動態是預設配置方法。 指派之後，只有在網路介面遭到刪除、指派給相同虛擬網路內的不同子網路，或配置方法變更為靜態，並指定不同的 IP 位址後，才會釋出動態 IP 位址。 根據預設，當您的配置方法從動態變更為靜態時，Azure 會將先前動態指派的位址指派為靜態位址。
- **靜態**：您選取並指派子網路位址範圍內的位址。 您指派的位址可以是子網路位址範圍內的任何位址，但該位址不是子網路位址範圍內的前四個位址之一，而且目前並未指派給子網路中的任何其他資源。 只有在刪除網路介面後，才會釋出靜態位址。 如果您將配置方法變更為靜態，Azure 會以動態方式將先前指派的靜態 IP 位址指派為動態位址 (即使此位址不是子網路位址範圍內的下一個可用位址)。 如果網路介面已指派給相同虛擬網路內的不同子網路，位址也會跟著變更，但若要將網路介面指派給不同的子網路，您必須先將配置方法從靜態變更為動態。 一旦您將網路介面指派給不同的子網路，您即可將配置方法變回靜態，並從新的子網路位址範圍中指派 IP 位址。

### <a name="virtual-machines"></a>虛擬機器

一或多個私人 IP 位址會指派給 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器的一或多個**網路介面**。 您可以將每個私人 IP 位址的配置方法指定為動態或靜態。

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>內部 DNS 主機名稱解析 (適用於虛擬機器)

除非明確設定自訂 DNS 伺服器，否則所有 Azure 虛擬機器預設都會設定 [Azure 受管理 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) 。 這些 DNS 伺服器會針對位於相同虛擬網路的虛擬機器提供內部名稱解析。

當您建立虛擬機器時，會將主機名稱與其私人 IP 位址的對應加入至 Azure 受管理 DNS 伺服器。 如果一部虛擬機器有多個網路介面，或一個網路介面有多個 IP 組態，則主機名稱會對應至主要網路介面之主要 IP 組態的私人 IP 位址。

使用 Azure 受管理 DNS 伺服器所設定的虛擬機器，能夠將相同虛擬網路內所有虛擬機器的主機名稱解析為其私人的 IP 位址。 若要解析已連線的虛擬網路中虛擬機器的主機名稱，您必須使用自訂 DNS 伺服器。

### <a name="internal-load-balancers-ilb--application-gateways"></a>內部負載平衡器 (ILB) 與應用程式閘道

您可以將私人 IP 位址指派給 [Azure 內部負載平衡器](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) 或 [Azure 應用程式閘道](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的**前端**組態。 此私人 IP 位址可作為內部端點，只能存取其虛擬網路內的資源與連線到虛擬網路的遠端網路。 您可以將動態或靜態私人 IP 位址指派給前端組態。

### <a name="at-a-glance"></a>快速總覽
下表顯示特定的屬性，私人 IP 位址可透過它關聯到最上層資源，以及顯示可以使用的可能配置方法 (動態或靜態)。

| 最上層資源 | IP 位址關聯 | 動態 | 靜態 |
| --- | --- | --- | --- |
| 虛擬機器 |Linux |是 |是 |
| 負載平衡器 |前端組態 |是 |是 |
| 前端 |前端組態 |是 |是 |

## <a name="limits"></a>限制
加諸於 IP 位址上的限制，如在 Azure 中的完整[網路限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)所示。 這些限制是針對每一區域和每一訂用帳戶。 您可以 [連絡支援人員](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ，以根據您的業務需求將預設上限調升到最高上限。

## <a name="pricing"></a>價格
公用 IP 位址可能需要少許費用。 若要深入了解 IP 位址的價格，請參閱 [IP 位址價格](https://azure.microsoft.com/pricing/details/ip-addresses)頁面。

## <a name="next-steps"></a>後續步驟
* [使用 Azure 入口網站部署使用靜態公用 IP 的 VM](virtual-network-deploy-static-pip-arm-portal.md)
* [使用範本部署使用靜態公用 IP 的 VM](virtual-network-deploy-static-pip-arm-template.md)
* [部署使用靜態私人 IP 位址的 VM](virtual-networks-static-private-ip-arm-pportal.md)
