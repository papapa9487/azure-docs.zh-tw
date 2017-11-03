---
title: "對路由進行疑難排解 - PowerShell | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 對 Azure Resource Manager 部署模型中的路由進行疑難排解。"
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bf7dc5e7-9399-460e-8e0d-8992dbed98a6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 29823708b2d26a383b76e371499859e57f470c6f
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="troubleshoot-routes-using-azure-powershell"></a>使用 Azure PowerShell 對路由進行疑難排解
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

### <a name="view-effective-routes-for-a-network-interface"></a>檢視網路介面的有效路由
若要查看套用到網路介面的彙總路由，請完成下列步驟︰

1. 啟動 Azure PowerShell 工作階段並登入 Azure。 如果您不熟悉 Azure PowerShell，請閱讀 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview) 文章。
2. 下列命令會傳回套用到資源群組 *RG1* 中名為 *VM1-NIC1* 之網路介面的所有路由。
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > 如果您不知道網路介面的名稱，請輸入下列命令來擷取資源群組中所有網路介面的名稱。*
   > 
   > 
   
       Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name
   
   下列輸出與每個路由套用到 NIC 所連線的子網路的輸出看起來很類似︰
   
       Name :
       State : Active
       AddressPrefix : {10.9.0.0/16}
       NextHopType : VNetLocal
       NextHopIpAddress : {}
   
       Name :
       State : Active
       AddressPrefix : {0.0.0.0/16}
       NextHopType : Internet
       NextHopIpAddress : {}
   
   請注意輸出的下列項目：
   
   * **Name**︰使用者定義路由除非明確指定，否則有效路由的名稱可能是空的。 
   * **State**︰表示有效路由的狀態。 可能的值為 "Active" 或 "Invalid"
   * **AddressPrefixes**︰以 CIDR 標記法指定有效路由的位址首碼。 
   * **nextHopType**︰表示指定路由的下一個躍點。 可能的值為 *VirtualAppliance*、*Internet*、*VNetLocal*、*VNetPeering* 或 *Null*。 UDR 中 *nextHopType* 的值為 **Null** 可能表示是無效路由。 例如，如果 **nextHopType** 是 *VirtualAppliance* ，但是網路虛擬設備 VM 不是處於已佈建/執行中狀態。 如果 **nextHopType** 是 *VPNGateway* ，但是指定 VNet 中沒有任何閘道已佈建/執行中，路由可能會變成無效。
   * **NextHopIpAddress**︰指定有效路由下一個躍點的 IP 位址。
   
   下列命令以更輕鬆檢視的表格方式傳回路由︰
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table
   
   下列輸出是先前所述案例所收到的一部分輸出：
   
       Name State AddressPrefix NextHopType NextHopIpAddress
       ---- ----- ------------- ----------- ----------------
       Active {10.9.0.0/16} VnetLocal {}
       Active {0.0.0.0/0} Internet {}
3. 上一個步驟的輸出沒有列出從 WestUS-VNet3 (首碼 10.9.0.0/16)** 到 WestUS-VNet1 (首碼 10.10.0.0/16) 的路由。 如下圖所示，有 *WestUS-VNet3 VNet* 的 VNet 對等互連連結處於 *Disconnected* 狀態。
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)
   
    對等互連的雙向連結已中斷，這說明為什麼 VM1 無法連線到 *WestUS-VNet3* VNet 中的 VM3。 為 *WestUS-VNet1* 和 *WestUS-VNet3* VNet 再次設定雙向對等互連連結。 正確建立 VNet 對等互連連結之後，傳回的輸出如下︰
   
        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
   
    一旦您判斷出問題，可以新增、移除或變更路由和路由表。 輸入下列命令可看到一份用來執行這些操作的命令︰
   
        Get-Help *-AzureRmRouteConfig

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
  * 網路安全性群組 (NSG) 規則可能會影響流量流程。 如需詳細資訊，請參閱 [為網路安全性群組疑難排解](virtual-network-nsg-troubleshoot-powershell.md) 文章。

