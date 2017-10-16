---
title: "使用 ExpressRoute 將 Azure Stack 連線至 Azure"
description: "如何使用 ExpressRoute 將 Azure Stack 中的虛擬網路連線至 Azure 中的虛擬網路。"
services: azure-stack
documentationcenter: 
author: victorar
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: aa6973939c6cfe0688f5781fdcea5d39670249df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>使用 ExpressRoute 將 Azure Stack 連線至 Azure

「適用於：Azure Stack 整合系統和 Azure Stack 開發套件」

支援兩種方法將 Azure Stack 中的虛擬網路連線至 Azure 中的虛擬網路：
   * **網站間**

     透過 IPsec (IKE v1 和 IKE v2) 的 VPN 連線。 此類型的連線需要 VPN 裝置或 RRAS。 如需詳細資訊，請參閱[使用 VPN 將 Azure Stack 連線至 Azure](azure-stack-connect-vpn.md)。
   * **ExpressRoute**

     從 Azure Stack 部署直接連線至 Azure。 ExpressRoute **不是**透過公用網際網路的 VPN 連線。 如需 Azure ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 概觀](../expressroute/expressroute-introduction.md)。

本文將示範如何使用 ExpressRoute 將 Azure Stack 連線至 Azure。
## <a name="requirements"></a>需求
使用 ExpressRoute 來連線 Azure Stack 和 Azure 時的特定需求如下：
* Azure 訂用帳戶，用於 Azure 中建立 ExpressRoute 線路和 VNet。
* 透過[連線提供者](../expressroute/expressroute-locations.md)佈建的 ExpressRoute 線路。
* 路由器，ExpressRoute 線路會連線至其 WAN 連接埠。
* 路由器的 LAN 端會連結至 Azure Stack 多組織用戶共享閘道。
* 路由器在其 LAN 介面和 Azure Stack 多組織用戶共享閘道之間，必須支援站對站 VPN 連線。
* 如果您的 Azure Stack 部署中新增多個租用戶，路由器必須能夠建立多個 VRF (虛擬路由和轉送)。

下圖顯示完成設定之後的概念性網路服務檢視：

![概念圖表](media/azure-stack-connect-expressroute/Conceptual.png)

**圖表 1**

下列架構圖表顯示多個租用戶如何從 Azure Stack 基礎結構，透過 ExpressRoute 路由器，連線至 Microsoft Edge 的 Azure：

![架構圖表](media/azure-stack-connect-expressroute/Architecture.png)

**圖表 2**

本文中顯示的範例使用相同的架構，以透過 ExpressRoute 私用對等互連來連線至 Azure。 在作法上是使用站對站 VPN 連線，從 Azure Stack 中的虛擬網路閘道連線至 ExpressRoute 路由器。 在本文中，下列步驟說明如何在兩個 VNet 之間建立端對端連線：從 Azure Stack 中的不同租用戶到它們在 Azure 中各自的 VNet。 您可以選擇新增更多個租用戶 VNet，並對每個租用戶重複同樣的步驟，或使用此範例而只部署單一租用戶 VNet。

## <a name="configure-azure-stack"></a>設定 Azure Stack
現在，請建立您需要的資源，以將 Azure Stack 環境設定為租用戶。 下列步驟說明您需要怎麼做。 以下指示說明如何使用 Azure Stack 入口網站來建立資源，但您也可以使用 PowerShell。

![網路資源步驟](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>開始之前
在開始設定之前，您需要：
* Azure Stack 部署。

   如需有關部署 Azure Stack 開發套件的資訊，請參閱 [Azure Stack 開發套件部署快速入門](azure-stack-deploy-overview.md)。
* Azure Stack 上可供使用者訂閱的供應項目。

  如需指示，請參閱[將虛擬機器提供給您的 Azure Stack 使用者](azure-stack-tutorial-tenant-vm.md)。

### <a name="create-network-resources-in-azure-stack"></a>在 Azure Stack 中建立網路資源

請使用下列程序在 Azure Stack 中建立每個租用戶所需的網路資源：

#### <a name="create-the-virtual-network-and-vm-subnet"></a>建立虛擬網路和 VM 子網路
1. 以使用者 (租用戶) 帳戶來登入使用者入口網站。

2. 在入口網站中，按一下 [新增]。

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. 從 Marketplace 功能表中選取 [網路]。

4. 按一下功能表上的 [虛擬網路]。

5. 根據下表，在適當欄位中輸入值：

   |欄位  |值  |
   |---------|---------|
   |名稱     |Tenant1VNet1         |
   |位址空間     |10.1.0.0/16|
   |子網路名稱     |Tenant1-Sub1|
   |子網路位址範圍     |10.1.1.0/24|

6. 您應會看到稍早建立的訂用帳戶填入 [訂用帳戶] 欄位中。

    a. 針對 [資源群組]，您可以建立資源群組，如果您已經有資源群組，請選取 [使用現有的]。

    b. 驗證預設位置。

    c. 按一下 [釘選到儀表板]。

    d. 按一下 [建立] 。



#### <a name="create-the-gateway-subnet"></a>建立閘道子網路
1. 從儀表板開啟您建立的虛擬網路資源 (Tenant1VNet1)。
2. 在 [設定] 區段上，選取 [子網路]。
3. 按一下 [閘道子網路]﹐將閘道子網路新增至虛擬網路。
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. 子網路名稱預設為 **GatewaySubnet**。
   閘道子網路很特別﹐必須具有此特定名稱，才能正常運作。
5. 在 [位址範圍] 欄位中，確認位址是 **10.1.0.0/24**。
6. 按一下 [確定] 以建立閘道子網路。

#### <a name="create-the-virtual-network-gateway"></a>建立虛擬網路閘道
1. 在 Azure Stack 使用者入口網站中，按一下 [新增]。
   
2. 從 Marketplace 功能表中選取 [網路]。
3. 從網路資源清單中選取 [虛擬網路閘道]。
4. 在 [名稱] 欄位中輸入 **GW1**。
5. 按一下 [虛擬網路] 項目以選擇虛擬網路。
   從清單中選取 [Tenant1VNet1]。
6. 按一下 [公用 IP 位址] 功能表項目。 當 [選擇公用 IP 位址] 區段開啟時﹐按一下 [新建]。
7. 在 [名稱] 欄位中，輸入 **GW1-PiP**﹐然後按一下 [確定]。
8. [VPN 類型] 預設應選取 [路由式]。
    保留此設定。
9. 確認 [訂用帳戶] 和 [位置] 均正確無誤。 想要的話，您可以將資源釘選到儀表板。 按一下 [建立] 。

#### <a name="create-the-local-network-gateway"></a>建立區域網路閘道

區域網路閘道資源的目的是指出 VPN 連線另一端的遠端閘道。 在此範例中，遠端是 ExpressRoute 路由器的 LAN 子介面。 對於此範例中的租用戶 1，遠端位址是 10.60.3.255，如圖 2 所示。

1. 登入 Azure Stack 實體機器。
2. 以您的使用者帳戶登入使用者入口網站，然後按一下新增。
3. 從 Marketplace 功能表中選取 [網路]。
4. 從資源清單中選取 [區域網路閘道]。
5. 在 [名稱] 欄位中輸入 **ER-Router-GW**。
6. 關於 [IP 位址] 欄位，請參閱圖 2。 對於租用戶 1，ExpressRoute 路由器的 LAN 子介面 IP 位址是 10.60.3.255。 根據您自己的環境，輸入路由器相對應介面的 IP 位址。
7. 在 [位址空間] 欄位中，輸入您想要連線至 Azure 中之 VNet 的位址空間。 關於此範例，請參閱圖 2。 對於租用戶 1，請注意，必要的子網路是 **192.168.2.0/24** (這是 Azure 中的中樞 VNet) 和 **10.100.0.0/16** (這是 Azure 中的輪輻 VNet)。 根據您自己的環境，輸入相對應的子網路。
   > [!IMPORTANT]
   > 對於 Azure Stack 閘道和 ExpressRoute 路由器之間的站對站 VPN 連線，這個範例假設您使用靜態路由。

8. 確認您的 [訂用帳戶]、[資源群組] 和 [位置] 都正確無誤，然後按一下 [建立]。

#### <a name="create-the-connection"></a>建立連線
1. 在 Azure Stack 使用者入口網站中，按一下 [新增]。
2. 從 Marketplace 功能表中選取 [網路]。
3. 從資源清單中選取 [連線]。
4. 在 [基本] 設定區段中，選擇 [站對站 (IPSec)] 作為 [連線類型]。
5. 選取 [訂用帳戶]、[資源群組] 和 [位置]﹐然後按一下 [確定]。
6. 在 [設定] 區段中，按一下 [虛擬網路閘道]，然後按一下 [GW1]。
7. 按一下 [區域網路閘道]，然後按一下 [ER 路由器 GW]。
8. 在 [連線名稱] 欄位中，輸入 **ConnectToAzure**。
9. 在 [共用金鑰 (PSK)] 欄位中輸入 **abc123**﹐然後按一下 [確定]。
10. 在 [摘要] 區段上，按一下 [確定]。

    建立連線之後，您就可以看到虛擬網路閘道所使用的公用 IP 位址。 若要在 Azure Stack 入口網站中找出位址，請瀏覽至您的虛擬網路閘道。 在 [概觀] 中，尋找 [公用 IP 位址]。 請記下這個位址。下一節會將它用作「內部 IP 位址」(如果適用於您的部署)。

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>建立虛擬機器
若要驗證透過 VPN 連線傳輸的資料，您需要虛擬機器在 Azure Stack Vnet 中傳送和接收資料。 現在，請建立虛擬機器，並將它放在您的虛擬網路中的 VM 子網路上。

1. 在 Azure Stack 使用者入口網站中，按一下 [新增]。
2. 從 Marketplace 功能表選取 [虛擬機器]。
3. 在虛擬機器映像清單中，選取 [Windows Server 2016 Datacenter 評估版] 映像，然後按一下 [建立]。
4. 在 [基本] 區段的 [名稱] 欄位中，輸入 **VM01**。
5. 輸入有效的使用者名稱和密碼。 建立 VM 之後﹐您將使用此帳戶來登入 VM。
6. 提供 訂用帳戶、資源群組 和 位置﹐然後按一下確定。
7. 在 大小 區段上，按一下此執行個體的虛擬機器大小，然後按一下選取。
8. 在 [設定] 區段上，您可以接受預設值。 但請確定選取的虛擬網路是 **Tenant1VNet1**，而子網路設定為 **10.1.1.0/24**。 按一下 [確定] 。
9. 檢閱 [摘要] 區段上的設定，然後按一下 [確定]。

針對您想要連線的每個租用戶 VNet，重複執行從**建立虛擬網路和 VM 子網路**到**建立虛擬機器**各節的步驟。

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>設定用於閘道周遊的 NAT 虛擬機器
> [!IMPORTANT]
> 本節僅適用於 Azure Stack 開發套件部署。 多重節點部署不需要 NAT。

Azure Stack 開發套件是獨立的，而且與部署實體主機的網路隔離。 因此，閘道所連線的「外部」VIP 網路不是在外部，而是隱藏在執行網路位址轉譯 (NAT) 的路由器背後。
 
路由器是 Windows Server 虛擬機器 (**AzS-BGPNAT01**)，在 Azure Stack 開發套件基礎結構中扮演「路由及遠端存取服務」(RRAS) 角色。 您必須在 AzS-BGPNAT01 虛擬機器上設定 NAT，允許以站對站 VPN 連線來連線兩端。

#### <a name="configure-the-nat"></a>設定 NAT

1. 以您的系統管理員帳戶登入 Azure Stack 實體機器。
2. 複製和編輯下列 PowerShell 指令碼，然後在提升權限的 Windows PowerShell ISE 中執行。 取代您的系統管理員密碼。 傳回的位址是您的「外部 BGPNAT 位址」。

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. 若要設定 NAT，請複製和編輯下列 PowerShell 指令碼，然後在提升權限的 Windows PowerShell ISE 中執行。 編輯指令碼來取代「外部 BGPNAT 位址」和「內部 IP 位址」(您先前在**建立連線**一節中記下的值)。

   在範例圖表中，「外部 BGPNAT 位址」是 10.10.0.62，「內部 IP 位址」是 192.168.102.1。

   ```
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>設定 Azure
既然您已完成 Azure Stack 設定，再來可以部署一些 Azure 資源。 下圖顯示 Azure 中的範例租用戶虛擬網路。 針對您在 Azure 中的 VNet，您可以使用任何名稱和定址配置。 不過，在 Azure 和 Azure Stack 中，VNet 的位址範圍必須是唯一的，而且不重疊。

![Azure Vnet](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**圖表 3**

您在 Azure 中部署的資源，類似於您在 Azure Stack 中部署的資源。 同樣地，您需要部署：
* 虛擬網路和子網路
* 閘道子網路
* 虛擬網路閘道
* 連線
* ExpressRoute 線路

範例 Azure 網路基礎結構的設定方式如下：
* 使用標準中樞 (192.168.2.0/24) 和輪輻 (10.100.0.0./16) VNet 模型。
* 工作負載部署在輪輻 Vnet 中，而 ExpressRoute 線路連線至中樞 VNet。
* 透過 VNet 對等互連功能來連結這兩個 VNet。

### <a name="configure-vnets"></a>設定 Vnet
1. 使用您的 Azure 認證登入 Azure 入口網站。
2. 使用 192.168.2.0/24 位址空間建立中樞 VNet。 使用 192.168.2.0/25 位址範圍建立子網路，並使用 192.168.2.128/27 位址範圍新增閘道子網路。
3. 使用 10.100.0.0/16 位址範圍建立輪輻 VNet 和子網路。


如需有關在 Azure 中建立虛擬網路的詳細資訊，請參閱[建立有多個子網路的虛擬網路](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。

### <a name="configure-an-expressroute-circuit"></a>設定 ExpressRoute 線路

1. 檢閱 [ExpressRoute 必要條件和檢查清單](../expressroute/expressroute-prerequisites.md)中的 ExpressRoute 必要條件。
2. 請依照[建立和修改 ExpressRoute 線路](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)中的步驟，使用您的 Azure 訂用帳戶建立 ExpressRoute 線路。
3. 將上一個步驟中的服務金鑰分享給主機服務提供者，以便在他們那一端佈建您的 ExpressRoute 線路。
4. 請依照[建立和修改 ExpressRoute 線路的對等互連](../expressroute/expressroute-howto-routing-portal-resource-manager.md)中的步驟，在 ExpressRoute 線路上設定私用對等互連。

### <a name="create-the-virtual-network-gateway"></a>建立虛擬網路閘道

* 請依照[使用 PowerShell 為 ExpressRoute 設定虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-resource-manager.md)中的步驟，在中樞 VNet 中為 ExpressRoute 建立虛擬網路閘道。

### <a name="create-the-connection"></a>建立連線

* 若要將 ExpressRoute 線路連結至中樞 VNet，請依照[將虛擬網路連線至 ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)中的步驟。

### <a name="peer-the-vnets"></a>將 Vnet 對等互連

* 依照[使用 Azure 入口網站建立虛擬網路對等互連](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md)中的步驟，將中樞和輪輻 VNet 對等互連。 設定 VNet 對等互連時，務必選取下列選項：
   * 從中樞至輪輻：**允許閘道傳輸**
   * 從輪輻至中樞：**使用遠端閘道**

### <a name="create-a-virtual-machine"></a>建立虛擬機器

* 將工作負載虛擬機器部署至輪輻 VNet。

對於您在 Azure 中想要透過各自 ExpressRoute 線路來連線的其他任何租用戶 VNet，重複這些步驟。

## <a name="configure-the-router"></a>設定路由器

您可以參考下列端對端基礎結構圖表，以指引您設定 ExpressRoute 路由器。 這個圖表顯示兩個租用戶 (租用戶 1 和租用戶 2)，有各自的 Express Route 線路。 每個都連結至各自在 ExpressRoute 路由器 LAN 和 WAN 端的 VRF (虛擬路由和轉送)，以確保兩個租用戶之間的端對端隔離。 隨著您進行範例設定，請記下路由器介面中使用的 IP 位址。

![端對端圖表](media/azure-stack-connect-expressroute/EndToEnd.png)

**圖表 4**

您可以使用任何支援 IKEv2 VPN 和 BGP 的路由器，以終止 Azure Stack 的站對站 VPN 連線。 相同的路由器用於透過 ExpressRoute 線路來連線至 Azure。 

以下是 Cisco ASR 1000 的範例設定，支援圖表 4 所示的網路基礎結構：

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>測試連線

建立站對站連線和 ExpressRoute 線路之後測試連線。 這項工作很簡單。  登入您在 Azure VNet 中建立的其中一個虛擬機器，然後偵測您在 Azure Stack 環境中建立的虛擬機器，反之亦然。 

為了確定是透過站對站和 ExpressRoute 連線來傳送流量，您必須在兩端都偵測虛擬機器的固定 IP (DIP) 位址，而不是虛擬機器的 VIP 位址。 因此，您必須找出並記下連線另一端的位址。

### <a name="allow-icmp-in-through-the-firewall"></a>允許 ICMP 通過防火牆
根據預設，Windows Server 2016 不允許 ICMP 封包中通過防火牆。 因此，針對您在測試中使用的每個虛擬機器，請在提升權限的 PowerShell 視窗中執行下列 Cmdlet：


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>偵測 Azure Stack 虛擬機器

1. 使用租用戶帳戶登入 Azure Stack 使用者入口網站。
2. 按一下左側導覽中的 [虛擬機器]。
3. 尋找並按一下您先前建立的虛擬機器。
4. 在虛擬機器的區段上，按一下 [連線]。
5. 開啟提高權限的 PowerShell 視窗，然後輸入 **ipconfig /all**。
6. 在輸出中尋找並記下 IPv4 位址。 從 Azure VNet 中的虛擬機器偵測此位址。 在範例環境中，此位址來自 10.1.1.x/24 子網路。 在您的環境中，此位址可能不同。 但應該會在您為租用戶 VNet 子網路建立的子網路內。


### <a name="view-data-transfer-statistics"></a>檢視資料轉送統計資料

如果想要知道有多少流量通過您的連線，您可以在 Azure Stack 使用者入口網站的 [連線] 區段上找到此資訊。 此資訊也很適合用來確認您剛傳送的 Ping，真的通過 VPN 和 ExpressRoute 連線。

1. 使用租用戶帳戶登入 Microsoft Azure Stack 使用者入口網站。
2. 瀏覽至已建立 VPN 閘道的資源群組，然後選取 [連線] 物件類型。
3. 按一下清單中的 [ConnectToAzure]連線。
4. 在 [連線] 區段上，您可以看到 [資料輸入] 和 [資料輸出] 的統計資料。您應該會看到一些非零值。

   ![資料輸入、資料輸出](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>後續步驟
[將應用程式部署到 Azure 和 Azure Stack](azure-stack-solution-pipeline.md)