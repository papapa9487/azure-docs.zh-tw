---
title: "Azure 虛擬網路對等互連 | Microsoft Docs"
description: "了解 Azure 中的虛擬網路對等互連。"
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: 7d3e6a34b5851a5a35a530b18efc3db3e2249274
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="virtual-network-peering"></a>虛擬網路對等互連

虛擬網路對等互連可讓您完美地連線兩個 Azure [虛擬網路](virtual-networks-overview.md)。 經過對等互連後，所有虛擬網路就可以作為一個整體來進行連線。 在對等互連之虛擬網路中的虛擬機器之間的流量，會透過 Microsoft 骨幹基礎結構路由傳送，其原理就像在相同虛擬網路中的虛擬機器之間，流量只會透過「私人」IP 位址來路由傳送。 

使用虛擬網路對等互連的優點包括︰

* 對等互連虛擬網路之間的網路流量為私用。 虛擬網路之間的流量會保留在 Microsoft 骨幹網路上。 虛擬網路之間的通訊不需要公用網際網路、閘道或加密。
* 不同虛擬網路的資源之間具有低延遲、高頻寬連線。
* 一旦虛擬網路對等互連，某個虛擬網路中的資源與不同虛擬網路中資源通訊的能力。
* 跨 Azure 訂用帳戶、部署模型和跨 Azure 區域 (預覽) 傳輸資料的能力。
* 能夠將透過 Azure Resource Manager 所建立的虛擬網路對等互連，或將透過 Resource Manager 所建立的虛擬網路與透過傳統部署模型所建立的虛擬網路對等互連。 若要深入了解 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 建立對等互連時或建立對等互連之後，虛擬網路中的資源沒有停機時間。

## <a name="requirements-constraints"></a>需求和限制

* 在一般情況下，可以為相同地區中的虛擬網路建立對等互連。 美國中西部、加拿大中部，和美國西部 2 可以使用為不同地區中虛擬網路建立對等互連的預覽版功能。 在不同的區域中將虛擬網路對等互連之前，您必須先[註冊您的訂用帳戶](virtual-network-create-peering.md#register)以進行預覽。 如果您尚未完成預覽的註冊，嘗試在不同區域中的虛擬網路之間建立對等互連就會失敗。
    > [!WARNING]
    > 與公開上市版相比，跨區域建立之虛擬網路對等互連的可用性和可靠性較低。 虛擬網路對等互連的功能可能有限，而且可能只有部分 Azure 區域能進行對等互連。 如需此功能可用性和狀態的最新通知，請查看 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network) 頁面。

* 對等互連的虛擬網路必須有非重疊的 IP 位址空間。
* 一旦虛擬網路與另一個虛擬網路對等互連，便無法在虛擬網路位址空間中新增或刪除位址空間。 如果您需要將位址範圍新增至對等互連虛擬網路的位址空間，您必須移除對等互連、新增位址空間，然後再次新增對等互連。
* 虛擬網路對等互連是介於兩個虛擬網路之間。 沒有衍生跨對等項目的可轉移關聯性。 例如，如果 virtualNetworkA 與 virtualNetworkB 對等互連，而 virtualNetworkB 與 virtualNetworkC 對等互連，則 virtualNetworkA「不會」對等互連至 virtualNetworkC。
* 只要兩個訂用帳戶之間有一個具有權限的使用者 (請參閱[特定權限](create-peering-different-deployment-models-subscriptions.md#permissions)) 授權對等互連，您就可以對等互連存在於兩個不同訂用帳戶中的虛擬網路，而訂用帳戶會與相同的 Azure Active Directory 租用戶產生關聯。 對於與不同 Active Directory 租用戶相關聯的訂用帳戶，您可以使用 [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)與其中的虛擬網路連線。
* 如果兩個虛擬網路都是透過 Resource Manager 部署模型建立，或如果其中一個虛擬網路是透過 Resource Manager 部署模型建立，而另一個是透過傳統部署模型建立，即可對等互連 VNet。 然而，透過傳統部署模型建立的虛擬網路無法彼此對等互連。 您可以使用 [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)，將透過傳統部署模型建立的虛擬網路加以連線。
* 雖然對等互連虛擬網路中虛擬機器之間的通訊沒有其他頻寬限制，但仍然有基於虛擬機器大小的網路頻寬上限。 若要深入了解不同虛擬機器大小的網路頻寬上限，請閱讀 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器大小文章。

     ![基本虛擬網路對等互連](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>連線能力

虛擬網路對等互連後，任一虛擬網路中的資源可以直接與對等互連虛擬網路中的資源連線。

在相同區域的對等互連虛擬網路中，虛擬機器之間的網路延遲與單一虛擬網路中的網路延遲相同。 網路輸送量為依照虛擬機器大小，按比例允許的頻寬。 對等互連內的頻寬沒有其他額外限制。

對等互連之虛擬網路中的虛擬機器之間的流量，會透過 Microsoft 骨幹基礎結構直接路由傳送，而不會透過閘道或透過公用網際網路來傳送。

虛擬網路中的虛擬機器可存取相同區域之對等互連虛擬網路中的內部負載平衡器。 對於內部負載平衡器的支援不會延伸到全域對等互連的虛擬網路 (預覽)。 正式運作版的全域虛擬網路對等互連會支援內部負載平衡器。

如有需要，可以將網路安全性群組套用在任一個虛擬網路，以封鎖其他虛擬網路或子網路的存取權限。
設定虛擬網路對等互連時，您可以開啟或關閉虛擬網路之間的網路安全性群組規則。 如果您開啟對等互連的虛擬網路 (預設選項) 之間的完整連線，您可以將網路安全性群組套用至特定子網路或虛擬機器，以封鎖或拒絕特定的存取。 若要深入了解網路安全性群組，請參閱[網路安全性群組概觀](virtual-networks-nsg.md)。

## <a name="service-chaining"></a>服務鏈結

您可以設定使用者定義的路由，指向對等互連虛擬網路中當做「下一個躍點」IP 位址的虛擬機器，以啟用服務鏈結。 服務鏈結可讓您透過使用者定義的路由，將流量從一個虛擬網路導向對等互連虛擬網路中的虛擬設備。

您也可以有效地建立中樞和輪輻類型的環境，讓中樞得以裝載網路虛擬應用裝置等基礎結構元件。 所有輪輻虛擬網路可以接著與中樞虛擬網路對等互連。 流量可以通過在中樞虛擬網路中執行的網路虛擬設備。 簡單來說，虛擬網路 對等互連可讓使用者定義路由上的下一個躍點 IP 位址成為對等互連虛擬網路中虛擬機器的 IP 位址。 若要深入了解使用者定義的路由，請參閱[使用者定義的路由概觀](virtual-networks-udr-overview.md)。 若要了解如何建立中樞和輪輻網路拓撲，請參閱[中樞和輪輻網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)。

## <a name="gateways-and-on-premises-connectivity"></a>閘道及內部部署連線能力

不論每個虛擬網路是否與其他虛擬網路對等互連，它們仍可以擁有自己的閘道並使用它來連線至內部部署網路。 即使虛擬網路已對等互連，您也可以使用閘道來設定[虛擬網路對虛擬網路連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

當針對虛擬網路內部連線的兩個選項已設定時，虛擬網路之間的流量將會透過對等互連設定流動 (也就是透過 Azure 骨幹)。

當虛擬網路在相同區域內對等互連時，您也可以將對等互連虛擬網路中的閘道設定為內部部署網路的傳輸點。 在此情況下，使用遠端閘道的虛擬網路不能擁有專屬閘道。 虛擬網路只能擁有一個閘道。 閘道可以是本機或遠端閘道 (在對等互連的虛擬網路中)，如下圖所示：

![虛擬網路對等互連傳輸](./media/virtual-networks-peering-overview/figure04.png)

透過不同部署模型或不同區域所建立之虛擬網路之間的對等互連關聯性不支援閘道傳輸。 對等互連關聯性中的兩個虛擬網路都必須是透過 Resource Manager 所建立的且位於相同區域中，閘道傳輸才能運作。 全域對等互連的虛擬網路目前不支援閘道傳輸。

當共用單一 Azure ExpressRoute 連線的虛擬網路已對等互連時，它們之間的流量會經過對等互連關聯性 (也就是透過 Azure 骨幹網路)。 您依然可以在每個虛擬網路中使用本機閘道來連線內部部署線路。 此外，您也可以使用共用閘道並設定內部部署連線的傳輸。

## <a name="permissions"></a>權限

虛擬網路對等互連是需要權限的作業。 它是 VirtualNetworks 命名空間下的個別功能。 使用者可以取得特定權限以授權對等互連。 具有虛擬網路讀寫權限的使用者會自動繼承這些權限。

身為系統管理員或具有對等互連功能權限的使用者可以啟動對另一個虛擬網路的對等互連作業。 所需的權限層級至少必須是網路參與者。 如果另一端有對等互連的相符要求，而且其他需求也符合，就會建立對等互連。

例如，如果您要將名為 myVirtualNetworkA 和 myVirtualNetworkB 的虛擬網路對等互連，您的帳戶就必須獲派各個虛擬網路的下列最基本角色或權限：

|虛擬網路|部署模型|角色|權限|
|---|---|---|---|
|myVirtualNetworkA|Resource Manager|[網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |傳統|[傳統網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|myVirtualNetworkB|Resource Manager|[網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||傳統|[傳統網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>監視

將兩個透過 Resource Manager 所建立的虛擬網路對等互連時，您必須為對等互連中的每個虛擬網路設定對等互連。
您可以監視對等互連連線的狀態。 對等互連狀態是下列其中一種：

* **已起始**：當您從第一個虛擬網路對第二個虛擬網路建立對等互連時，對等互連狀態是 [已起始]。

* **已連線**：當您從第二個虛擬網路對第一個虛擬網路建立對等互連時，其對等互連狀態是 [已連線]。 如果您檢視第一個虛擬網路的對等互連狀態，您會看到其狀態從 [已起始] 變更為 [已連線]。 在兩個虛擬網路對等互連的對等互連狀態變為 [已連線] 之後，您才能成功建立對等互連。

* **已中斷連線**：如果您在連線建立後刪除了其中一個對等互連連結，您的對等互連狀態會是 [已中斷連線]。

## <a name="troubleshoot"></a>疑難排解

若要針對跨對等互連連線流動的流量進行疑難排解，您可以[檢查有效的路由](virtual-network-routes-troubleshoot-portal.md)。

您也可以使用網路監看員的[連線能力檢查](../network-watcher/network-watcher-connectivity-portal.md)，以針對對等互連虛擬網路中的虛擬機器連線能力進行疑難排解。 連線能力檢查可讓您查看流量是以何種方式直接從來源 VM 的網路介面路由傳送至目的地 VM 的網路介面。

## <a name="limits"></a>限制

單一虛擬網路允許的對等互連數目有其限制。 如需詳細資訊，請參閱 [Azure 網路限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="pricing"></a>價格

我們會針對使用虛擬網路對等互連連線的輸入和輸出流量收取少許費用。 如需詳細資訊，請參閱 [價格頁面](https://azure.microsoft.com/pricing/details/virtual-network)。

## <a name="next-steps"></a>接續步驟

* 完成虛擬網路對等互連教學課程。 虛擬網路對等互連是建立於虛擬網路之間，而這兩個虛擬網路是透過存在於相同或不同訂用帳戶中的相同或不同部署模型所建立。 完成下列其中一個案例的教學課程：

    |Azure 部署模型  | 訂用帳戶  |
    |---------|---------|
    |兩者皆使用 Resource Manager |[相同](virtual-network-create-peering.md)|
    | |[不同](create-peering-different-subscriptions.md)|
    |一個使用 Resource Manager、一個使用傳統部署模型     |[相同](create-peering-different-deployment-models.md)|
    | |[不同](create-peering-different-deployment-models-subscriptions.md)|

* 了解如何建立[中樞和輪輻網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)。
* 了解所有[虛擬網路對等互連設定，以及如何變更它們](virtual-network-manage-peering.md)
