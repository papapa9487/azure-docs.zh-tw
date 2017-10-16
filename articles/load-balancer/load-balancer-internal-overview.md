---
title: "內部負載平衡器概觀 | Microsoft Docs"
description: "內部負載平衡器與其功能的概觀。負載平衡器如何作用於 Azure 和可能案例，以設定內部端點"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ec07c77119c2da408da21fbdc7877d0b43d16556
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="internal-load-balancer-overview"></a>內部負載平衡器概觀

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

不同於網際網路面向的負載平衡器，內部負載平衡器 (ILB) 只會將流量引導到雲端服務內部的資源，或使用 VPN 來存取 Azure 基礎結構。 基礎結構會限制對雲端服務或虛擬網路的負載平衡虛擬 IP 位址 (VIP) 的存取，因此絕對不會直接向網際網路端點公開這些 IP 位址。 這可讓內部企業營運 (LOB) 應用程式在 Azure 中執行，而且可從雲端內或從內部部署資源存取。

## <a name="why-you-may-need-an-internal-load-balancer"></a>為何需要內部負載平衡器

Azure 內部負載平衡 (ILB) 可在位於雲端服務或虛擬網路 (具有區域範圍) 中的虛擬機器之間提供負載平衡。 如需使用和設定具有區域範圍之虛擬網路的相關資訊，請參閱 Azure 部落格中的 [區域虛擬網路](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) 。 已針對同質群組設定的現有虛擬網路無法使用 ILB。

ILB 可提供下列幾種負載平衡類型：

* 在雲端服務中，從虛擬機器至一組位於相同雲端服務內的虛擬機器 (請參閱圖 1)。
* 在虛擬網路中，從虛擬網路中的虛擬機器至一組位於虛擬網路的相同雲端服務中的虛擬機器 (請參閱圖 2)。
* 在跨單位部署的虛擬網路中，從內部部署電腦至一組位於虛擬網路的相同雲端服務中的虛擬機器 (請參閱圖 3)。
* 網際網路對向、多層式應用程式，其中後端層並非網際網路面向，但要求來自網際網路面向層的流量達到負載平衡。
* 在不需要額外負載平衡器硬體或軟體的情況下，針對裝載於 Azure 中的 LOB 應用程式進行負載平衡。 包括流量已負載平衡之電腦集合中的內部部署伺服器。

## <a name="internet-facing-multi-tier-applications"></a>網際網路面向的多層式應用程式

Web 層具有網際網路用戶端的網際網路面向端點，而且是負載平衡集合的一部分。 負載平衡器會將來自 TCP 連接埠 443 (HTTPS) 的 Web 用戶端的連入流量分配給 Web 伺服器。

資料庫伺服器位於 Web 伺服器用於儲存的 ILB 端點之後。 這是資料庫服務負載平衡的端點，其流量會在 ILB 集合中的各資料庫伺服器上達到負載平衡。

下圖顯示相同雲端服務中網際網路面向的多層式應用程式。

![單一雲端服務的內部負載平衡](./media/load-balancer-internal-overview/IC736321.png)

圖 1 - 網際網路面向的多層式應用程式

多層式應用程式的另一個可能用途，是 ILB 部署到與取用 ILB 服務不同的雲端服務的情況。

使用相同虛擬網路的雲端服務會有 ILB 端點的存取權。 下圖顯示前端 Web 伺服器與資料庫後端位於不同的雲端服務，並使用相同虛擬網路內的 ILB 端點。

![雲端服務之間的內部負載平衡](./media/load-balancer-internal-overview/IC744147.png)

圖 2 - 位於不同雲端服務的前端伺服器

## <a name="intranet-line-of-business-applications"></a>內部網路企業營運應用程式

使用 Azure 網路的 VPN 連線，可讓 LOB 伺服器集合中來自內部部署網路上用戶端的流量達到負載平衡。

用戶端電腦將能透過端點對站台 VPN 存取來自 Azure VPN 的 IP 位址。 它能允許使用裝載於 ILB 端點後方的 LOB 應用程式。

![使用端點對站台 VPN 的內部負載平衡](./media/load-balancer-internal-overview/IC744148.png)

圖 3 - 裝載於 LB 端點後方的 LOB 應用程式

LOB 的另一個案例是有站台對站台 VPN 至 ILB 端點設定所在的虛擬網路。 這可讓內部部署網路流量路由傳送至 ILB 端點。

![使用站台對站台 VPN 的內部負載平衡](./media/load-balancer-internal-overview/IC744150.png)

圖 4 - 內部部署網路流量路由傳送至 ILB 端點

## <a name="limitations"></a>限制

內部負載平衡器組態不支援 SNAT。 在此文件的內容中，SNAT 是指連接埠偽裝來源網路位址轉譯。  這適用於負載平衡器集區的 VM 需要連接個別內部負載平衡器前端 IP 位址的情況。 內部負載平衡器不支援這種情況。 流量經過負載平衡傳輸至起始流程的 VM 時，會發生連線失敗。 對於這類情況，您必須使用 Proxy 形式負載平衡器。

## <a name="next-steps"></a>後續步驟

[Azure Resource Manager 的 Azure Load Balancer 支援](load-balancer-arm.md)

[開始設定網際網路面向的負載平衡器](load-balancer-get-started-internet-arm-ps.md)

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
