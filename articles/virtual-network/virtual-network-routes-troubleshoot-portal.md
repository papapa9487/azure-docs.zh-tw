---
title: "對路由進行疑難排解 - 入口網站 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站對 Azure Resource Manager 部署模型中的路由進行疑難排解。"
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bdd8b6dc-02fb-4057-bb23-8289caa9de89
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: f76693dfcaf5076372e4c4d5f28678c05eff85ed
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="troubleshoot-routes-using-the-azure-portal"></a>使用 Azure 入口網站對路由進行疑難排解
> [!div class="op_single_selector"]
> * [Azure 入口網站](virtual-network-routes-troubleshoot-portal.md)
> * [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
>
>

如果您和 Azure 虛擬機器 (VM) 之間遇到網路連線問題，路由可能會影響 VM 的流量流程。 本文提供協助診斷路由功能的概觀以進一步進行疑難排解。

路由表與子網路相關聯，在該子網路中的所有網路介面 (NIC) 上都有效。 下列類型的路由可以套用到每個網路介面︰

* **系統路由︰** 在 Azure 虛擬網路 (VNet) 中建立的每個子網路預設都有系統路由表，允許本機 VNet 流量、透過 VPN 閘道的內部部署流量以及網際網路流量。 對等互連的 VNet 也有系統路由。
* **BGP 路由︰** 透過 ExpressRoute 或站台對站台 VPN 連線傳播到網路介面。 閱讀 [BGP 與 VPN 閘道](../vpn-gateway/vpn-gateway-bgp-overview.md)和 [ExpressRoute 概觀](../expressroute/expressroute-introduction.md)文章深入了解 BGP 路由。
* **使用者定義路由 (UDR)：** 如果您使用網路虛擬設備或透過站台對站台 VPN 強制將流量導向內部部署網路，您可能會有與子網路路由表相關聯的使用者定義路由 (UDR)。 如果您不熟悉 UDR，請閱讀 [使用者定義路由](virtual-networks-udr-overview.md#user-defined) 文章。

因為有各種路由可套用至網路介面，所以可能會很難判斷哪些彙總路由是有效的。 為了協助對 VM 網路連線進行疑難排解，您可以檢視 Azure Resource Manager 部署模型中某個網路介面的所有有效路由。

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>使用有效路由對 VM 流量流程進行疑難排解
本文使用下列案例做為範例，說明如何對網路介面的有效路由進行疑難排解︰

某個連接到 VNet (*VNet1*，首碼︰10.9.0.0/16) 的 VM (*VM1*) 無法連線到新對等互連的 VNet (*VNet3*，首碼 10.10.0.0/16) 中的 VM (VM3)。 連線到 VM 的 VM1-NIC1 網路介面沒有套用 UDR 或 BGP 路由，只有套用系統路由。

本文說明如何使用 Azure Resource Manager 部署模型中的有效路由功能判斷連線失敗的原因。
雖然此範例只使用系統路由，但是可以使用相同的步驟判斷透過任何路由類型的輸入和輸出連線失敗。

> [!NOTE]
> 如果您的 VM 附加了多個 NIC，請檢查每個 NIC 的有效路由以診斷和 VM 之間的網路連線問題。
>
>

### <a name="view-effective-routes-for-a-virtual-machine"></a>檢視虛擬機器的有效路由
若要查看套用到 VM 的彙總路由，請完成下列步驟︰

1. 登入 Azure 入口網站，位址是 https://portal.azure.com。
2. 按一下 [更多服務]，然後在出現的清單中按一下 [虛擬機器]。
3. 從出現的清單中選取要進行疑難排解的 VM，此時會出現一個有選項的 VM 刀鋒視窗。
4. 按一下 [診斷並解決問題]，然後選取常見的問題。 例如選取了 [我無法連接到我的 Windows VM]  。

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)
5. 在問題下方會出現步驟，如下圖所示︰

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    按一下建議步驟清單中的 [有效路由]  。
6. [有效路由]  刀鋒視窗隨即會出現，如下圖所示︰

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    如果您的 VM 只有一個 NIC，預設會選取它。 如果您有多個 NIC，請選取您想要檢視有效路由的 NIC。

   > [!NOTE]
   > 如果與 NIC 相關聯的 VM 不是處於執行中狀態，就不會顯示有效路由。 入口網站中只會顯示前 200 個有效路由。 如需完整的清單，請按一下 [下載] 。 您可以從下載的 .csv 檔案進一步篩選結果。
   >
   >

    請注意輸出的下列項目：

   * **Source**︰表示路由的類型。 系統路由會顯示為 *Default*，UDR 會顯示為 *User*，閘道路由 (靜態或 BGP) 則會顯示為 *VPNGateway*。
   * **State**︰表示有效路由的狀態。 可能的值為 *Active* 或 *Invalid*。
   * **AddressPrefixes**︰以 CIDR 標記法指定有效路由的位址首碼。
   * **nextHopType**︰表示指定路由的下一個躍點。 可能的值為 *VirtualAppliance*、*Internet*、*VNetLocal*、*VNetPeering* 或 *Null*。 UDR 中 *nextHopType* 的值為 **Null** 可能表示是無效路由。 例如，如果 **nextHopType** 是 *VirtualAppliance* ，但是網路虛擬設備 VM 不是處於已佈建/執行中狀態。 如果 **nextHopType** 是 *VPNGateway* ，但是指定 VNet 中沒有任何閘道已佈建/執行中，路由可能會變成無效。
7. 上一個步驟的圖片中沒有列出從 *WestUS-VNet1* (首碼 10.9.0.0/16) 到 *WestUS-VNET3* VNet (首碼 10.10.0.0/16) 的路由。 在下圖中，對等互連連結處於 *Disconnected* 狀態︰

    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    對等互連的雙向連結已中斷，這說明為什麼 VM1 無法連線到 *WestUS-VNet3* VNet 中的 VM3。
8. 下圖顯示建立雙向對等互連連結之後的路由︰

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

如需更多強制通道和路由評估的疑難排解情況，請閱讀本文的 [考量](virtual-network-routes-troubleshoot-portal.md#considerations) 一節。

### <a name="view-effective-routes-for-a-network-interface"></a>檢視網路介面的有效路由
如果網路流量流程受到特定網路介面 (NIC) 影響，您可以直接在 NIC 上檢視有效路由的完整清單。 若要查看套用到 NIC 的彙總路由，請完成下列步驟︰

1. 登入 Azure 入口網站，位址是 https://portal.azure.com。
2. 按一下 [更多服務]，然後按一下 [網路介面]
3. 搜尋清單找出 NIC 的名稱，或從出現的清單中選取。 在此範例中選取了 [VM1-NIC1]  。
4. 在 [網路介面] 刀鋒視窗中選取 [有效路由]，如下圖所示︰

       ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    [範圍]  預設為選取的網路介面。

      ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)

### <a name="view-effective-routes-for-a-route-table"></a>檢視路由表的有效路由
在路由表中修改使用者定義路由 (UDR) 時，可以檢閱在特定 VM 上新增路由的影響。 路由表可以與任意數目的子網路產生關聯。 您現在可以檢視套用指定路由表之所有 NIC 的所有有效路由，而不必從指定路由表刀鋒視窗切換內容。

在此範例中，路由表 (*UDRouteTable*) 中指定了 UDR (*UDRoute*)。 此路由會透過網路虛擬設備 (NVA) 將 *WestUS-VNet1* VNet 中來自 *Subnet1* 的所有網際網路流量傳入同一個 VNet 的 *Subnet2*。 路由如下圖所示：

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

若要查看路由表的彙總路由，請完成下列步驟︰

1. 登入 Azure 入口網站，位址是 https://portal.azure.com。
2. 按一下 [更多服務]，然後按一下 [路由表]
3. 搜尋清單找出您想要查看彙總路由的路由表，然後選取它。 在此範例中選取了 **UDRouteTable** 。 選取路由表的刀鋒視窗會隨即出現，如下圖所示︰

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)
4. 在 [路由表] 刀鋒視窗中選取 [有效路由]。 [範圍]  會設為您所選取的路由表。
5. 路由表可以套用到多個子網路。 從清單中選取您想要檢閱 [子網路]  。 在此範例中選取了 [Subnet1]  。
6. 選取 [網路介面] 。 此時會列出連線到選取子網路的所有 NIC。 在此範例中選取了 [VM1-NIC1]  。

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

   > [!NOTE]
   > 如果 NIC 沒有與執行中的 VM 相關聯，則不會顯示任何有效路由。
   >
   >

## <a name="considerations"></a>考量
檢閱傳回的路由清單時，請記住下列幾點事項︰

* 路由是根據 UDR、BGP 和系統路由之間的最長首碼比對 (LPM)。 如果有多個符合相同 LPM 的路由，則會根據其來源，以下列順序選取路由：

  * 使用者定義路由
  * BGP 路由
  * 系統 (預設) 路由

    如果有有效路由，您就只會看到根據所有可用路由符合 LPM 的有效路由。 透過示範如何針對指定 NIC 實際評估路由，這樣可以更方便對可能會影響 VM 連線的特定路由進行疑難排解。
* 如果您有 UDR，要將流量傳送到網路虛擬設備 (NVA)，且 *VirtualAppliance* 為 **nextHopType**，請確定接收流量的 NVA 已啟用 IP 轉送，否則封包會被捨棄。
* 如果已啟用強制通道，所有輸出的網際網路流量就會路由到內部部署。 使用此設定時，根據內部部署處理此流量的方式，從網際網路可能無法 RDP/SSH 到您的 VM。
  符合下列其中一個條件時，可以啟用強制通道︰
  * 使用站台對站台 VPN 時，將使用者定義路由 (UDR) 的 nextHopType 設定為 VPNGateway
  * 透過 BGP 公告預設路由
* 要讓 VNet 對等互連流量正確運作，對等互連的 VNet 的首碼範圍必須有 **nextHopType** *VNetPeering* 的系統路由。 如果沒有此類路由，且 VNet 對等互連連結看起來正常︰
  * 如果是新建立的對等互連連結，請等候幾秒鐘並重試。 有時候需要花比較長的時間才能將路由傳播到子網路中的所有網路介面。
  * 網路安全性群組 (NSG) 規則可能會影響流量流程。 如需詳細資訊，請參閱 [為網路安全性群組疑難排解](virtual-network-nsg-troubleshoot-portal.md) 文章。
