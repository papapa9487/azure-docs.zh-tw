---
title: "從 VNET 連接到 Azure Data Lake Store | Microsoft Docs"
description: "從 Azure VNET 連接到 Azure Data Lake Store"
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9bd7a76ef644df3db72ce95a3ef89a04bec1381f
ms.contentlocale: zh-tw
ms.lasthandoff: 11/17/2016

---
# <a name="access-azure-data-lake-store-from-vms-within-an-azure-vnet"></a>從 Azure VNET 內的 VM 存取 Azure Data Lake Store
Azure Data Lake Store 使用公用網際網路 IP 位址執行的 PaaS 服務。 可以連線到公用網際網路的任何伺服器，通常也可以連接到 Azure Data Lake Store 端點。 根據預設，Azure VNET 中所有的 VM 可以存取網際網路，並因此可以存取 Azure Data Lake Store。 不過，也可以將 VNET 中的 VM 設定為無法存取網際網路。 對於這類 VM，也會限制對 Azure Data Lake Store 的存取。 封鎖 Azure VNET 中 VM 的公用網際網路存取，可以使用下列方法的任一個來完成。

* 藉由設定網路安全性群組 (NSG)
* 藉由設定使用者定義路由 (UDR)
* 在使用可封鎖網際網路存取的 ExpressRoute 時，透過 BGP (產業標準動態路由通訊協定) 交換路由

在本文中，您將學習如何從已使用上述三種方法之一被限制存取資源的 Azure VM 啟用對 Azure Data Lake Store 的存取。

## <a name="enabling-connectivity-to-azure-data-lake-store-from-vms-with-restricted-connectivity"></a>從具有受限制連線的 VM 啟用對 Azure Data Lake Store 的連線
若要從這類 VM 存取 Azure Data Lake Store ，您必須對其設定，以存取 Azure Data Lake Store 帳戶可供使用的 IP 位址。 您可以透過解析您的帳戶 (`<account>.azuredatalakestore.net`) 的 DNS 名稱來識別 Data Lake Store 帳戶的 IP 位址。 對此您可以使用 **nslookup** 之類的工具。 在您的電腦上開啟命令提示字元，並執行下列命令。

    nslookup mydatastore.azuredatalakestore.net

輸出結果類似下面。 **Address** 屬性的值是與您的 Data Lake Store 帳戶相關聯的 IP 位址。

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>使用 NSG 從受限制的 VM 啟用連線
使用 NSG 規則來封鎖對網際網路的存取時，您可以建立允許存取 Data Lake Store IP 位址的另一個 NSG。 NSG 規則的詳細資訊位於[什麼是網路安全性群組？](../virtual-network/virtual-networks-nsg.md)。 如需如何建立 NSG 的指示，請參閱[如何使用 Azure 入口網站管理 NSG](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)。

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>使用 UDR 或 ExpressRoute 從受限制的 VM 啟用連線
路由時會使用 UDR 或 BGP 交換路由來封鎖對網際網路的存取，必須設定特殊的路由，才能使這類子網路中的 VM 可以存取 Data Lake Store 端點。 如需詳細資訊，請參閱[什麼是使用者定義路由？](../virtual-network/virtual-networks-udr-overview.md)。 如需建立 UDR 的指示，請參閱[在 Resource Manager 中建立 UDR](../virtual-network/virtual-network-create-udr-arm-ps.md)。

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>使用 ExpressRoute 從受限制的 VM 啟用連線
設定 ExpressRoute 電路時，內部部署伺服器可以透過公用對等互連來存取 Data Lake Store。 如需針對公用互連設定 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](../expressroute/expressroute-faqs.md)。

## <a name="see-also"></a>另請參閱
* [Azure 資料湖儲存區概觀](data-lake-store-overview.md)
* [保護儲存在 Azure Data Lake Store 中的資料](data-lake-store-security-overview.md)


