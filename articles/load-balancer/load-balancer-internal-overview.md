---
title: "Azure 內部負載平衡器概觀 | Microsoft Docs"
description: "Azure 中內部負載平衡器的運作方式，以及用於設定內部端點的情節。"
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
ms.openlocfilehash: 54e390dbdb07cb4c45c801b638099aa0dcc6db1a
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="overview-of-azure-internal-load-balancer"></a>Azure 內部負載平衡器的概觀

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure 內部負載平衡 (ILB) 只會將流量導向位於雲端服務內，或使用 VPN 來存取 Azure 基礎結構流量的資源。 在這一方面，ILB 與網際網路對向負載平衡器不同。 Azure 基礎結構會限制存取雲端服務或虛擬網路的負載平衡虛擬 IP (VIP) 位址。 VIP 位址與虛擬網路永不會直接公開至網際網路端點。 內部企業營運應用程式會在 Azure 中執行，並且可從 Azure 內或內部部署資源內存取。

## <a name="why-you-might-need-an-internal-load-balancer"></a>為何需要內部負載平衡器

內部負載平衡可在位於雲端服務或虛擬網路 (VM) (具有區域範圍) 中的虛擬機器之間提供負載平衡。 如需具有區域範圍之虛擬網路的相關資訊，請參閱 Azure 部落格中的[區域虛擬網路](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)。 已針對同質群組設定的現有虛擬網路無法使用 ILB。

ILB 可提供下列幾種負載平衡類型：

* 雲端服務內：從 VM 到位於相同雲端服務內的一組 VM 的負載平衡。 請參閱此<a href="#figure1">範例</a>。
* 虛擬網路內：從虛擬網路中的 VM 到位於虛擬網路的相同雲端服務內的一組 VM 的負載平衡。 請參閱此<a href="#figure2">範例</a>。
* 在跨單位部署的虛擬網路負載平衡中，從內部部署電腦至一組位於虛擬網路的相同雲端服務中的虛擬機器。 請參閱此<a href="#figure3">範例</a>。
* 多層應用程式：網際網路對向多層式應用程式 (其中的後端層並非網際網路對向) 的負載平衡。 後端層需要來自網際網路對向層的流量負載平衡。
* 針對企業營運應用程式：在 Azure 中代管的企業營運應用程式的負載平衡，而不額外負載平衡器硬體或軟體。 此情節包括流量已負載平衡之電腦集合中的內部部署伺服器。

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>網際網路對向多層式應用程式的負載平衡

Web 層具有網際網路用戶端的網際網路面向端點，而且是負載平衡集合的一部分。 ILB 會將來自 TCP 連接埠 443 (HTTPS) 的 Web 用戶端的連入流量分配給 Web 伺服器。

資料庫伺服器位於 Web 伺服器用於儲存的 ILB 端點之後。 ILB 端點為資料庫服務負載平衡的端點。 其流量會在 ILB 集合中的各資料庫伺服器上達到負載平衡。

下圖顯示相同雲端服務中網際網路面向的多層式應用程式的內部負載平衡。

<a name="figure1"></a>
![網際網路面向的多層式應用程式](./media/load-balancer-internal-overview/IC736321.png)

另一個情節適用於多層式應用程式。 負載平衡器會從對 ILB 使用服務的端點部署到不同的雲端服務。

使用相同虛擬網路的雲端服務可以存取 ILB 端點。 下圖顯示前端 Web 伺服器與資料庫後端位於不同的雲端服務。 前端伺服器使用相同虛擬網路內的 ILB 端點做為後端。

<a name="figure2"></a>
![位於不同雲端服務的前端伺服器](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>內部網路企業營運應用程式的負載平衡

來自內部部署網路上用戶端的流量會在使用 VPN 連線至 Azure 網路的企業營運伺服器集合間達到負載平衡。

用戶端電腦能透過端點對站台 VPN 存取來自 Azure VPN 服務的 IP 位址。 企業營運應用程式可以裝載在 ILB 端點背後。

<a name="figure3"></a>
![裝載在 ILB 端點背後的企業營運應用程式](./media/load-balancer-internal-overview/IC744148.png)

企業營運應用程式的另一個情節是有站台對站台 VPN 至 ILB 端點設定所在的虛擬網路。 內部部署網路流量路由傳送至 ILB 端點。

<a name="figure4"></a>
![內部部署網路流量路由傳送至 ILB 端點](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>限制

內部負載平衡器組態不支援 SNAT。 在本文中，SNAT 是指涉及連接埠偽裝來源網路位址轉譯的情節。 負載平衡器集區中的 VM 必須連接個別內部負載平衡器前端的 IP 位址。 流量經過負載平衡傳輸至起始流程的 VM 時，會發生連線失敗。 ILB 不支援這些情節。 必須改為使用 Proxy 樣式負載平衡器。

## <a name="next-steps"></a>後續步驟

* [Azure Resource Manager 的 Azure Load Balancer 支援](load-balancer-arm.md)
* [開始設定網際網路面向的負載平衡器](load-balancer-get-started-internet-arm-ps.md)
* [開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)
* [設定負載平衡器分配模式](load-balancer-distribution-mode.md)
* [設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
