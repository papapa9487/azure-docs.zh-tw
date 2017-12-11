---
title: "關於 Azure Stack 的 VPN 閘道 | Microsoft Docs"
description: "了解並設定您搭配 Azure Stack 使用的 VPN 閘道。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: ba9642d8c51f57623aded44b84d7127334806bc1
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2017
---
# <a name="about-vpn-gateway-for-azure-stack"></a>關於 Azure Stack 的 VPN 閘道
適用於：Azure Stack 整合系統和 Azure Stack 開發套件


您必須先為您的虛擬網路建立虛擬網路閘道，才能傳送 Azure 虛擬網路和內部部署網站之間的網路流量。

VPN 閘道是一種虛擬網路閘道，可透過公用連接傳送加密的流量。 您可以使用 VPN 閘道安全地傳送 Azure Stack 中的虛擬網路與 Azure 中的虛擬網路之間的流量，或虛擬網路與連線至 VPN 裝置的另一個網路之間的流量。

建立虛擬網路閘道時，您可指定想要建立的閘道類型。 Azure Stack 支援一種虛擬網路閘道：'Vpn' 類型。

每個虛擬網路可以有兩個虛擬網路閘道，但每種類型只能有一個。 視您選擇的設定而定，您可以對單一 VPN 閘道建立多個連線。 例如，多站台連線設定。

> [!NOTE]
> 在 Azure 中，您選擇之 VPN 閘道 SKU 的頻寬輸送量必須分配給連線到它的所有連線。  在 Azure Stack 中，VPN 閘道 SKU 的頻寬值會套用到連線到它的每個連線資源。     

> 例如，在 Azure 中，基本 VPN 閘道 SKU 可以容納大約 100 Mbps 的彙總輸送量。  如果您對該 VPN 閘道建立兩個連線，而且其中一個連線使用 50 Mbps 的頻寬，則 50 Mbps 可供另一個連線使用。   

> 在 Azure Stack 中，基本 VPN 閘道 SKU 的「每次」連線會配置到 100 Mbps 的輸送量。

## <a name="configuring-a-vpn-gateway"></a>設定 VPN 閘道
VPN 閘道連線需仰賴多個具有特定設定的資源。 大部分的資源可以分別進行設定，雖然在某些情況下必須以特定順序來設定。

### <a name="settings"></a>設定
您為每個資源選擇的設定，對於建立成功連線而言極為重要。 如需 VPN 閘道的個別資源和設定資訊，請參閱 [關於 Azure Stack 的 VPN 閘道設定](azure-stack-vpn-gateway-settings.md)。 您可以尋找資訊來協助您了解閘道類型、VPN 類型、連線類型、閘道子網路、區域網路閘道，以及其他可能的各種資源設定。

### <a name="deployment-tools"></a>部署工具
您可以使用設定工具 (例如 Azure 入口網站) 開始建立及設定資源。 您可以稍後再決定切換到另一個工具 (如 PowerShell) 來設定其他資源，或是在適用的時機修改現有資源。 您目前無法在 Azure 入口網站中進行每一項資源和資源設定。 文章中各連線拓撲的指示會指定何時需要特定組態工具。

## <a name="connection-topology-diagrams"></a>連線拓撲圖表
請務必知道 VPN 閘道連線有不同的組態可用。 您必須決定哪個組態最符合您的需求。 在下列各節中，您可以檢視有關下列 VPN 閘道連線的資訊和拓撲圖表︰下列各節包含的資料表可列出︰

- 可用的部署模型
- 可用的設定工具
- 直接帶您前往某篇文章的連結 (如果可用)

下列各節中的圖形和描述可協助您選取符合您需求的連線拓撲。 這些圖表顯示主要基準拓撲，但您也可以使用這些圖表做為指導方針來建置更複雜的組態。

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>站台對站台以及多站台 (IPsec/IKE VPN 通道)
### <a name="site-to-site"></a>網站間
網站間 (S2S) VPN 閘道連線是透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道建立的連線。 此類型的連線需要位於內部部署的 VPN 裝置，其具有指派的公用 IP 位址且不是位於 NAT 後方。 S2S 連線可以用於跨單位與混合式組態。    
![站對站 VPN 連線組態範例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>多網站
這類型的連線是站對站連線的變化。 您可以從虛擬網路閘道建立多個 VPN 連線，通常會連接至多個內部部署網站。 處理多重連線時，您必須使用路由式 VPN 類型 (也就是使用傳統 VNet 時的動態閘道)。 因為每個虛擬網路只能有一個 VPN 閘道，所以透過該閘道的所有連線會共用可用的頻寬。 這通常稱為「多網站」連線。   
![Azure VPN 閘道多網站連接範例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)



## <a name="gateway-skus"></a>閘道 SKU
建立 Azure Stack 的虛擬網路閘道時，您可指定想要使用的閘道 SKU。 支援下列 VPN 閘道 SKU：
- 基本
- 標準
- 高效能

當您選取較高的閘道 SKU 時 (諸如標準高於基本，高效能高於標準或基本) 則會將更多 CPU 和網路頻寬配置給閘道。 如此一來，閘道可以對虛擬網路支援更高的網路輸送量。

Azure Stack 不支援專門搭配 Express Route 使用的超效能閘道 SKU。

選取 SKU 時，請考慮下列：
- Azure Stack 不支援以原則為基礎的閘道。
- 基本 SKU 不支援邊界閘道協定 (BGP)。
- Azure Stack 不支援 ExpressRoute-VPN 閘道共存組態
- 僅可以在高效能 SKU 上設定主動-主動 S2S VPN 閘道連線。

## <a name="estimated-aggregate-throughput-by-sku"></a>依 SKU 列出的估計彙總輸送量
下表依照閘道 SKU 顯示閘道類型和預估的彙總輸送量。

|   | VPN 閘道輸送量 *(1)* |VPN 閘道最大 IPsec 通道 |
|-------|-------|-------|
|**基本 SKU** ***(2)***    | 100 Mbps  | 10    |
|**標準 SKU**       | 100 Mbps  | 10    |
|**高效能 SKU** | 200 Mbps    | 30    |
***(1)*** VPN 輸送量不是網際網路上跨單位連線的保證輸送量。 這是可能的最大輸送量測量。  
***(2)*** 基本 SKU 不支援 BGP。

## <a name="next-steps"></a>後續步驟
了解 Azure Stack 的 [VPN 閘道設定](azure-stack-vpn-gateway-settings.md)。
