---
title: "傳統 Azure 虛擬網路閘道 SKU | Microsoft Docs"
description: "舊式虛擬網路閘道 SKU。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 3b2126b1ecd1613950bbf311ae08fafd4af0d51f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>使用虛擬網路閘道 SKU (舊版 SKU)

此文章包含舊版虛擬網路閘道 SKU 的相關資訊。 舊版 SKU 仍然可以在針對 VPN 閘道建立的兩個部署模型中運作。 傳統 VPN 閘道仍繼續針對現有閘道以及新閘道使用舊版 SKU。 在建立新的資源管理員 VPN 閘道時，請使用新的閘道 SKU。 如需最新 SKU 的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。

## <a name="gwsku"></a>閘道 SKU

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>依 SKU 列出的估計彙總輸送量

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>依 SKU 和 VPN 類型列出的支援組態

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>調整閘道的大小 (變更閘道 SKU)

您可以調整相同 SKU 系列內的閘道 SKU 大小。 例如，如果您有標準 SKU，則可以調整為高效能 SKU。 您無法調整舊 SKU 與新 SKU 系列之間的 VPN 閘道大小。 例如，您不能從標準 SKU 變成 VpnGw2 SKU。 

若要調整傳統部署模型的閘道 SKU 大小，請使用下列命令：

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

若要調整資源管理員部署模型的閘道 SKU 大小，請使用下列命令：

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="migrate"></a>移轉到新的閘道 SKU

如果您使用的是資源管理員部署模型，則可以移轉到新的閘道 SKU。 如果您使用的是傳統部署模型，則無法移轉到新的 SKU，而必須改為繼續使用舊版 SKU。

[!INCLUDE [Migrate SKU](../../includes/vpn-gateway-migrate-legacy-sku-include.md)]

## <a name="next-steps"></a>後續步驟

如需新式閘道 SKU 的相關資訊，請參閱[閘道 SKU](vpn-gateway-about-vpngateways.md#gwsku)。

如需組態設定的詳細資訊，請參閱[關於 VPN 閘道組態設定](vpn-gateway-about-vpn-gateway-settings.md)。