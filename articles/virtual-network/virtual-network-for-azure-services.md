---
title: "Azure 服務的虛擬網路 | Microsoft Docs"
description: "了解將資源部署到擬網路的優點。 虛擬網路中的資源可以彼此通訊，並且可以內部部署資源，而不會產生周遊網際網路的流量。"
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
ms.date: 08/25/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 36ff77a4205132d1dcc3b743f11791fc5e98e9e7
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="virtual-network-integration-for-azure-services"></a>Azure 服務的虛擬網路整合

將 Azure 服務整合至 Azure 虛擬網路 (VNet) 中，可以讓服務私下存取部署在該 VNet 中的執行個體。

您可以使用下列選項，將 Azure 服務與虛擬網路整合：
- 將服務的專用執行個體直接部署至 VNet。 在虛擬網路和內部部署網路中，可以私下存取這些服務的專用執行個體。
- 透過服務端點將虛擬網路擴充至服務。 這可保障虛擬網路使用得到個別服務資源。
 
## <a name="deploy-azure-services-into-virtual-networks"></a>將 Azure 服務部署至虛擬網路

您可以使用公用 IP 位址，透過網際網路與大部分的 Azure 資源通訊。 若在[虛擬網路](virtual-networks-overview.md)中部署 Azure 服務，可以使用私人 IP 位址，私下與該服務的資源通訊。


![部署在虛擬網路中的服務](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

部署在虛擬網路中的服務有下列功能：

- 虛擬網路中的資源可以透過私人 IP 位址，私下互相通訊。 例如，在 VNet 中，直接在於虛擬機器上執行的 HDInsight 和 SQL Server 之間傳輸資料。
- 內部部署資源可以透過[站對站 VPN (VPN 閘道)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) 或 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，使用私人 IP 位址，存取虛擬網路中的資源。
- 虛擬網路則可以使用私人 IP 位址進行[對等互連](virtual-network-peering-overview.md)，來讓虛擬網路中的資源彼此通訊。
- VNet 中的服務執行個體由 Azure 服務全權管理，以監控執行個體的健康情況，及根據負載提供需要的規模。
- 服務執行個體已部署至客戶 VNet 中的專用子網路。 根據服務的指導，應透過子網路的網路安全性群組 (NSG) 開啟輸入和輸出存取。


### <a name="list-of-services-that-can-be-deployed-into-a-virtual-network"></a>可以部署至虛擬網路的服務清單

每個直接部署至虛擬網路的服務對於路由，以及必須允許進出子網路的流量有特定要求。 如需更多詳細資料，請參閱下列連結的網頁： 
 
- 虛擬機器：[Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service Fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [App Service 環境](../app-service-web/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API 管理](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [應用程式閘道 (內部)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Container Service 引擎](../container-service/container-service-intro.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：Azure Container Service 會建立預設的虛擬網路。 您可以建立自訂的虛擬網路，和 [Azure Container Service 引擎](https://github.com/Azure/acs-engine/tree/master/examples/vnet)搭配使用。
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：僅限虛擬網路 (傳統)
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)：僅限虛擬網路 (傳統)
- [雲端服務](https://msdn.microsoft.com/library/azure/jj156091)：僅限虛擬網路 (傳統)

您可以部署[內部的 Azure Load Balancer](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，來為上面清單中的許多資源進行負載平衡。 在某些情況下，該服務會在您建立資源時自動建立和部署負載平衡器。

## <a name="service-endpoints-for-azure-services"></a>Azure 服務的服務端點

某些 Azure 服務無法部署在虛擬網路中。 您可以選擇將部分的服務資源只開放給特定虛擬網路子網路存取，只要啟用虛擬網路服務端點即可。 深入了解[虛擬網路服務端點](virtual-network-service-endpoints-overview.md)。

服務端點目前支援以下服務： 
- Azure 儲存體：[保護虛擬網路的 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- Azure SQL Database：[保護虛擬網路的 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

## <a name="virtual-network-integration-across-multiple-azure-services"></a>跨多個 Azure 服務的虛擬網路整合

您可以將 Azure 服務部署到虛擬網路中的子網路，並保護該子網路的重要服務資源。 

舉例來說，您可以將 HDInsight 部署到您的虛擬網路，並保護 HDInsight 子網路的儲存體帳戶。






