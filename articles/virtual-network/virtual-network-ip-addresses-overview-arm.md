---
title: "在 Azure 中的 IP 位址類型 | Microsoft Docs"
description: "了解 Azure 中的公用和私人 IP 位址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 144f4ea213b8ed0a3530495e185f489155c474c9
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Azure 中的 IP 位址類型及配置方法
您可以將 IP 位址指派給 Azure 資源，來與其他 Azure 資源、內部部署網路和網際網路進行通訊。 您可以在 Azure 中使用兩種類型的 IP 位址：

* **公用 IP 位址**：用於與網際網路通訊，包括 Azure 公眾對應服務
* **私人 IP 位址**：用於 Azure 虛擬網路 (VNet) 內的通訊，而當您使用 VPN 閘道或 ExpressRoute 電路將網路擴充至 Azure 時，則使用於內部部署網路內的通訊。

> [!NOTE]
> Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../resource-manager-deployment-model.md)。  本文涵蓋之內容包括使用 Resource Manager 部署模型，Microsoft 建議大部分的新部署使用此模型，而不是[傳統部署模型](virtual-network-ip-addresses-overview-classic.md)。
> 

如果您熟悉傳統部署模型，請參閱 [傳統與 Resource Manager 之間的 IP 定址差異](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments)。

## <a name="public-ip-addresses"></a>公用 IP 位址
Azure 資源可透過公用 IP 位址來與網際網路和 Azure 公眾對應服務 (例如 [Azure Redis 快取](https://azure.microsoft.com/services/cache/)、[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)、[SQL Database](../sql-database/sql-database-technical-overview.md) 和 [Azure 儲存體](../storage/common/storage-introduction.md)) 進行通訊。

在 Azure 資源管理員中， [公用 IP](resource-groups-networking.md#public-ip-address) 位址是有自己的屬性的資源。 您可以將公用 IP 位址資源與下列任何資源建立關聯：

* 虛擬機器 (VM)
* 網際網路對應負載平衡器
* VPN 閘道
* 應用程式閘道

### <a name="allocation-method"></a>配置方法
有兩種方法可將 IP 位址配置給公用 IP 資源：「動態」或「靜態」。 預設配置方法是「動態」 ，此方法 **不會** 在建立 IP 位址時進行配置。 相反地，公用 IP 位址會在您啟動 (或建立) 相關聯的資源 (例如 VM 或負載平衡器) 時進行配置。 此 IP 位址會在您停止 (或刪除) 資源時釋出 。 這會導致 IP 位址在您停止並啟動資源時變更。

若要確保相關聯資源的 IP 位址維持不變，您可以明確地將配置方法設定為「靜態」 。 在此情況下會立即指派 IP 位址。 只有在您刪除資源或將其配置方法變更為「動態」 時，才會釋出 IP 位址。

> [!NOTE]
> 即使將配置方法設定為「靜態」，您也無法指定已指派給「公用 IP 資源」的實際 IP 位址。 相反地，它是從建立資源的 Azure 位置中可用的 IP 位址集區進行配置。
>

靜態公用 IP 位址通常用於下列案例：

* 使用者必須更新防火牆規則才能與您的 Azure 資源進行通訊。
* DNS 名稱解析，其中當 IP 位址發生變更時將需要更新 A 記錄。
* 您的 Azure 資源與其他使用 IP 位址型安全性模型的應用程式或服務進行通訊。
* 您使用已連結到 IP 位址的 SSL 憑證。

> [!NOTE]
> 將公用 IP 位址 (動態/靜態) 配置給 Azure 資源的 IP 範圍清單已發佈於 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。
>

### <a name="dns-hostname-resolution"></a>DNS 主機名稱解析
您可以指定公用 IP 資源的 DNS 網域名稱標籤，以建立 domainnamelabel.location.cloudapp.azure.com 與 Azure 受管理 DNS 伺服器中的公用 IP 位址的對應。 比方說，如果您建立公用 IP 資源並以 **contoso** 作為**美國西部** Azure 位置中的domainnamelabel，則完整網域名稱 (FQDN) **contoso.westus.cloudapp.azure.com** 會解析為資源的公用 IP 位址。 您可以使用此 FQDN 來建立自訂網域 CNAME 記錄，其指向 Azure 中的公用 IP 位址。

> [!IMPORTANT]
> 所建立的每個網域名稱標籤必須是 Azure 位置中唯一的。  
>

### <a name="virtual-machines"></a>虛擬機器
您可以藉由將公用 IP 位址指派給其**網路介面**，以建立其與 [Windows](../virtual-machines/windows/overview.md) 或 [Linux](../virtual-machines/virtual-machines-linux-about.md) VM 的關聯。 如果 VM 有多個網路介面，您可以只將它指派給「主要」網路介面。 您可以將動態或靜態公用 IP 位址指派給 VM。

### <a name="internet-facing-load-balancers"></a>網際網路對應負載平衡器
您可以將公用 IP 位址指派給負載平衡器 [前端](../load-balancer/load-balancer-overview.md)組態，以建立其與 **Azure Load Balancer** 的關聯。 此公用 IP 位址可做為負載平衡的虛擬 IP 位址 (VIP)。 您可以將動態或靜態公用 IP 位址指派給負載平衡器前端。 您也可以將多個公用 IP 位址指派給一個負載平衡器前端，以實現 [多重 VIP](../load-balancer/load-balancer-multivip.md) 案例 (例如具有多個 SSL 架構網站的多租用戶環境)。

### <a name="vpn-gateways"></a>VPN 閘道
[Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md) 用來將 Azure 虛擬網路 (VNet) 連接到其他 Azure Vnet 或內部部署網路。 您需要將公用 IP 位址指派給其 **IP 設定** ，以便啟用與遠端網路的通訊。 您目前只可以將 *動態* 公用 IP 位址指派給 VPN 閘道。

### <a name="application-gateways"></a>應用程式閘道
您可以將公用 IP 位址指派給閘道的 [前端](../application-gateway/application-gateway-introduction.md)組態，以建立其與 Azure **應用程式閘道** 的關聯。 此公用 IP 位址可做為負載平衡的 VIP。 您目前只可以將「動態」  公用 IP 位址指派給應用程式閘道前端組態。

### <a name="at-a-glance"></a>快速總覽
下表顯示特定的屬性，公用 IP 位址可透過它關聯到最上層資源，以及顯示可以使用的可能配置方法 (動態或靜態)。

| 最上層資源 | IP 位址關聯 | 動態 | 靜態 |
| --- | --- | --- | --- |
| 虛擬機器 |Linux |是 |是 |
| 負載平衡器 |前端組態 |是 |是 |
| VPN 閘道 |閘道 IP 組態 |是 |否 |
| 前端 |前端組態 |是 |否 |

## <a name="private-ip-addresses"></a>私人 IP 位址
私人 IP 位址可讓 Azure 資源透過 VPN 閘道或 ExpressRoute 電路，與 [虛擬網路](virtual-networks-overview.md) 中或內部部署網路中的其他資源進行通訊，而不必使用可網際網路連線的 IP 位址。

在 Azure Resource Manager 部署模型中，私人 IP 位址會與下列 Azure 資源類型相關聯。

* VM
* 內部負載平衡器 (ILB)
* 應用程式閘道

### <a name="allocation-method"></a>配置方法
私人 IP 位址是從附加資源的子網路位址範圍進行配置。 子網路本身的位址範圍是 VNet 位址範圍的一部分。

私人 IP 位址有兩種配置方法：「動態」或「靜態」。 預設配置方法是「動態」 ，此方法會從資源的子網路自動配置 IP 位址 (使用 DHCP)。 此 IP 位址可在您停止並啟動資源時變更。

您可以將配置方法設定為「靜態」  以確保 IP 位址維持不變。 在此情況下，您也需要提供屬於資源子網路的有效 IP 位址。

靜態私人 IP 位址通常用於：

* 做為網域控制站或 DNS 伺服器的 VM。
* 需要使用 IP 位址的防火牆規則的資源。
* 其他應用程式/資源透過 IP 位址存取的資源。

### <a name="virtual-machines"></a>虛擬機器
私人 IP 位址會指派給 [Windows](../virtual-machines/windows/overview.md) 或 [Linux](../virtual-machines/virtual-machines-linux-about.md) VM 的**網路介面**。 如果是多重網路介面 VM，每個介面都會取得指派的私人 IP 位址。 您可以將網路介面的配置方法指定為動態或靜態。

#### <a name="internal-dns-hostname-resolution-for-vms"></a>內部 DNS 主機名稱解析 (適用於 VM)
除非明確設定自訂 DNS 伺服器，否則所有 Azure VM 預設都會設定 [Azure 受管理 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) 。 這些 DNS 伺服器會針對位於相同 VNet 的 VM 提供內部名稱解析。

當您建立 VM 時，會將主機名稱與其私人 IP 位址的對應加入至 Azure 受管理 DNS 伺服器。 如果是多重網路介面 VM，主機名稱會對應至主要網路介面的私人 IP 位址。

使用 Azure 受管理 DNS 伺服器設定的 VM，能夠將其 VNet 內所有 VM 的主機名稱解析為其私人 IP 位址。

### <a name="internal-load-balancers-ilb--application-gateways"></a>內部負載平衡器 (ILB) 與應用程式閘道
您可以將私人 IP 位址指派給 [Azure 內部負載平衡器](../load-balancer/load-balancer-internal-overview.md) (ILB) 或 [Azure 應用程式閘道](../application-gateway/application-gateway-introduction.md)的**前端**組態。 此私人 IP 位址可做為內部端點，只能存取其虛擬網路 (VNet) 內的資源與連線到 VNet 的遠端網路。 您可以將動態或靜態私人 IP 位址指派給前端組態。

### <a name="at-a-glance"></a>快速總覽
下表顯示特定的屬性，私人 IP 位址可透過它關聯到最上層資源，以及顯示可以使用的可能配置方法 (動態或靜態)。

| 最上層資源 | IP 位址關聯 | 動態 | 靜態 |
| --- | --- | --- | --- |
| 虛擬機器 |Linux |是 |是 |
| 負載平衡器 |前端組態 |是 |是 |
| 前端 |前端組態 |是 |是 |

## <a name="limits"></a>限制
加諸於 IP 位址上的限制，如在 Azure 中的完整[網路限制](../azure-subscription-service-limits.md#networking-limits)所示。 這些限制是針對每一區域和每一訂用帳戶。 您可以 [連絡支援人員](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ，以根據您的業務需求將預設上限調升到最高上限。

## <a name="pricing"></a>價格
公用 IP 位址可能需要少許費用。 若要深入了解 IP 位址的價格，請參閱 [IP 位址價格](https://azure.microsoft.com/pricing/details/ip-addresses)頁面。

## <a name="next-steps"></a>後續步驟
* [使用 Azure 入口網站部署使用靜態公用 IP 的 VM](virtual-network-deploy-static-pip-arm-portal.md)
* [使用範本部署使用靜態公用 IP 的 VM](virtual-network-deploy-static-pip-arm-template.md)
* [部署使用靜態私人 IP 位址的 VM](virtual-networks-static-private-ip-arm-pportal.md)

