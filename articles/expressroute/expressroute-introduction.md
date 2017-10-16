---
title: "ExpressRoute 概觀：透過私人連線將內部部署網路延伸至 Azure | Microsoft Docs"
description: "此「ExpressRoute 技術概觀」說明 ExpressRoute 連線如何透過私人連線，將內部部署網路延伸至 Azure。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: fd95dcd5-df1d-41d6-85dd-e91d0091af05
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: cherylmc
ms.openlocfilehash: 7390462d79506e63989baadac2b2cee00eef325d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-overview"></a>ExpressRoute 概欟
Microsoft Azure ExpressRoute 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。 透過 ExpressRoute，您可以建立 Microsoft 雲端服務的連線，例如 Microsoft Azure、Office 365 和 Dynamics 365。

從任意點對任意點 (IP VPN) 網路、點對點乙太網路，或在共置設施上透過連線提供者的虛擬交叉連接，都可以進行連線。 ExpressRoute 連線不會經過公用網際網路。 相較於一般網際網路連線，這可讓 ExpressRoute 連線提供更可靠、更快速、延遲更短和更安全的連線。 如需如何使用 ExpressRoute 將您的網路連接至 Microsoft 的詳細資訊，請參閱 [ExpressRoute 連線模型](expressroute-connectivity-models.md)。

![](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>主要權益

* 內部部署網路與 Microsoft Cloud 之間透過連線提供者的第 3 層連線能力。 從任意點對任意點 (IP VPN) 網路、點對點乙太網路，或透過乙太網路交換經由虛擬交叉連接，都可以進行連線。
* 跨地理政治區域中的所有區域連接到 Microsoft 雲端服務。
* 透過 ExpressRoute Premium 附加元件從全球連線到所有區域的 Microsoft 服務。
* 網路與 Microsoft 之間透過業界標準通訊協定 (BGP) 的動態路由。
* 每個對等位置有內建的備援性，可靠性更高。
* 連線執行時間 [SLA](https://azure.microsoft.com/support/legal/sla/)。
* 商務用 Skype 的 QoS 支援。

如需詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

## <a name="features"></a>特性

### <a name="layer-3-connectivity"></a>第 3 層連線能力
Microsoft 採用業界標準動態路由通訊協定 (BGP)，在您的內部部署網路、Azure 中的執行個體和 Microsoft 公用位址之間交換路由。  我們會針對不同的流量設定檔，與您的網路建立多個 BGP 工作階段。 如需詳細資訊，請參閱 [ExpressRoute 線路和路由網域](expressroute-circuit-peerings.md) 一文。

### <a name="redundancy"></a>備援性
每個 ExpressRoute 線路有兩條連線，從連線提供者 / 您的網路邊緣連接到兩個 Microsoft Enterprise 邊緣路由器 (MSEE) 。 Microsoft 需要有來自連線提供者/您這端的雙重 BGP 連線 – 每個連線皆各自連線至每個 MSEE。 您可以選擇不要在您這端部署備援裝置 / 乙太網路線路。 不過，連線提供者會使用備援裝置，確保以備援方式將您的連線交給 Microsoft。 備援第 3 層連線組態是我們的 [SLA](https://azure.microsoft.com/support/legal/sla/) 生效的條件。

### <a name="connectivity-to-microsoft-cloud-services"></a>連線到 Microsoft 雲端服務
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

透過 ExpressRoute 連線可存取下列服務：

* Microsoft Azure 服務
* Microsoft Office 365 服務
* Microsoft Dynamics 365

您可以瀏覽 [ExpressRoute 常見問題集](expressroute-faqs.md) 頁面，取得透過 ExpressRoute 所支援的服務的詳細清單。

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>連線到地理政治區域內的所有區域
您可以在我們的其中一個 [對等位置](expressroute-locations.md) 中連接到 Microsoft，就能夠存取地理政治區域內的所有區域。 

例如，如果您在阿姆斯特丹透過 ExpressRoute 連線至 Microsoft，您就能夠存取在北歐和西歐託管的所有 Microsoft 雲端服務。 如需地理政治地區、相關聯的 Microsoft 雲端區域和對應的 ExpressRoute 對等位置的概觀，請參閱 [ExpressRoute 合作夥伴和對等位置](expressroute-locations.md)一文。

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>使用 ExpressRoute Premium 附加元件從全球連線
您可以啟用 ExpressRoute Premium 附加功能，將連線能力延伸到跨越地理政治的界限。 例如，如果您在阿姆斯特丹透過 ExpressRoute 連接到 Microsoft，您就能夠存取全球所有區域裝載的所有 Microsoft 雲端服務 (不包括國家雲端)。 就像存取北歐和西歐區域一樣，您也可以存取部署在南美洲或澳大利亞的服務。

### <a name="rich-connectivity-partner-ecosystem"></a>豐富的連線合作夥伴生態系統
ExpressRoute 的連線提供者和 SI 合作夥伴生態系統持續成長茁壯。 如需最新資訊，請參閱 [ExpressRoute 提供者和位置](expressroute-locations.md) 一文。

### <a name="connectivity-to-national-clouds"></a>連線到國家雲端
Microsoft 為特殊的地理政治地區和客戶群提供隔離的雲端環境。 如需國家雲端和提供者的清單，請參閱 [ExpressRoute 提供者和位置](expressroute-locations.md) 頁面。

### <a name="bandwidth-options"></a>頻寬選項
您可以購買各種頻寬的 ExpressRoute 線路。 支援的頻寬如下所列。 請務必洽詢您的連線提供者，以判斷他們支援的頻寬清單。

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>動態調整頻寬
您可以在不中斷連線的情況下﹐增加 ExpressRoute 線路頻寬 (盡最大努力)。 

### <a name="flexible-billing-models"></a>彈性的計費模型
您可以挑選最適合的計費模型。 請從下列計費模型中選擇。 如需詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

* **無限制資料**。 ExpressRoute 線路按月計費，所有輸入和輸出資料傳輸已免費包括在內。 
* **已計量資料**。 ExpressRoute 線路按月計費。 所有輸入資料傳輸都免費。 輸出資料傳輸依每 GB 資料傳輸計費。 資料傳輸費率依區域而不同。
* **ExpressRoute Premium 附加元件**。 ExpressRoute Premium 是 ExpressRoute 線路上的附加元件。 ExpressRoute Premium 附加元件提供下列功能： 
  * Azure 公用和 Azure 私用對等的路由限制從 4,000 個路由提高到 10,000 個路由。
  * 從全球連線到服務。 任何區域中建立的 ExpressRoute 線路 (不包括國家雲端) 能夠存取全球其他任何區域的資源。 例如，透過在美國矽谷佈建的 ExpressRoute 線路，可存取在西歐建立的虛擬網路。
  * 每個 ExpressRoute 線路的 VNet 連結數目已從原本 10 個放寬限制，視線路的頻寬而定。

## <a name="faq"></a>常見問題集

如需有關 ExpressRoute 的常見問題，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [ExpressRoute 連線模型](expressroute-connectivity-models.md)。
* 了解 ExpressRoute 連線和路由網域。 請參閱 [ExpressRoute 線路和路由網域](expressroute-circuit-peerings.md)。
* 尋找服務提供者。 請參閱 [ExpressRoute 合作夥伴和對等位置](expressroute-locations.md)。
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。
* 請參閱[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的需求。
* 設定 ExpressRoute 連線。
  * [建立 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md)
  * [設定 ExpressRoute 線路的對等互連](expressroute-howto-routing-portal-resource-manager.md)
  * [將虛擬網路連線到 ExpressRoute 線路](expressroute-howto-linkvnet-portal-resource-manager.md)
* 深入了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md)。
