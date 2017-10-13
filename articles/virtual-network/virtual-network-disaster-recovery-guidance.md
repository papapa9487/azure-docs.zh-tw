---
title: "發生影響 Azure 虛擬網路的 Azure 服務中斷事件時該怎麼辦 | Microsoft Docs"
description: "了解發生影響 Azure 虛擬網路的 Azure 服務中斷事件時該怎麼辦"
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: 4e125406d2e798138c45e3fbbf61a610afab69fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network--business-continuity"></a>虛擬網路 – 商務持續性
## <a name="overview"></a>概觀
「虛擬網路」(VNet) 是您網路在雲端的邏輯呈現方式。 它可讓您定義自己的私人 IP 位址空間，並將網路分割成子網路。 VNet 可做為信任界限來裝載您的運算資源，例如「Azure 虛擬機器」和「雲端服務」(Web/背景工作角色)。 VNet 可允許其裝載的資源之間進行直接私人 IP 通訊。 「虛擬網路」也可以透過其中一個混合式選項 (例如「VPN 閘道」或 ExpressRoute) 連結到內部部署網路。

VNet 是在區域的範圍內建立。 您可以在兩個不同的區域中建立具有相同位址空間的 VNet (亦即美國東部和美國西部，但無法讓它們直接連接到彼此)。 

## <a name="business-continuity"></a>商務持續性
可能有數種不同的方式會讓您的應用程式中斷。 指定的區域可能會因天然災害而被完全切斷，或因多個裝置/服務失敗而導致部分損毀。 這每一種情況對 VNet 服務的影響各有不同。

**問︰整個區域發生中斷 (亦即，如果區域因天然災害而完全封閉) 時，該怎麼辦？該區域中所裝載的「虛擬網路」會發生什麼情況？**

答︰在服務中斷期間，受影響區域中的「虛擬網路」及資源都會保持無法供存取的狀態。

![簡單的虛擬網路圖表](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**問︰若要在不同的區域中重新建立相同的「虛擬網路」，該怎麼做？**

答︰「虛擬網路」(VNet) 是非常輕量型的資源。 您可以叫用 Azure API，在不同的區域中建立具有相同位址空間的 VNet。 若要重新建立受影響區域中所具有的相同環境，您必須進行 API 呼叫來重新部署您的「雲端服務」(Web/背景工作角色) 和您所擁有的「虛擬機器」。 如果您有內部部署連線 (例如在混合式部署中)，您也必須備妥「VPN 閘道」並連接到內部部署網路。

[這裡](virtual-networks-create-vnet-arm-pportal.md)提供建立 VNet 的相關指示。 

**問︰是否可以事先在另一個區域中重新建立指定區域中的 VNet 複本？**

答︰是，您可以事先在兩個不同的區域中，使用相同的私人 IP 位址空間和資源來建立兩個 VNet。 如果客戶在 VNet 中裝載網際網路面向服務，他們可能已設定「流量管理員」將流量異地路由傳送至使用中的區域。 不過，客戶無法將兩個具有相同位址空間的 VNet 連接到其內部部署網路，因為這會造成路由問題。 當發生災害而失去一個區域中的 VNet 時，客戶可以將位於可用區域中具有相符位址空間的另一個 VNet 連接到其內部部署網路。

[這裡](virtual-networks-create-vnet-arm-pportal.md)提供建立 VNet 的相關指示。

