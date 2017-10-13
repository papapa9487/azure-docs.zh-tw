---
title: "為網路安全性群組疑難排解 - PowerShell | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 對 Azure Resource Manager 部署模型中的網路安全性群組進行疑難排解。"
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 5edaf7197576ac1c0bd1fc6bed21fd65ed135106
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>使用 Azure PowerShell 為網路安全性群組疑難排解
> [!div class="op_single_selector"]
> * [Azure 入口網站](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

如果您在虛擬機器 (VM) 上設定網路安全性群組 (NSG) 卻遇到 VM 連線問題，本文提供診斷 NSG 功能的概觀以協助進一步進行疑難排解。

NSG 可讓您控制流入和流出虛擬機器 (VM) 的流量類型。 您可對 Azure 虛擬網路 (VNet) 中的子網路、網路介面 (NIC) 或兩者套用 NSG。 對 NIC 套用的有效規則是對 NIC 套用的 NSG，以及對 NIC 所連線到的子網路套用的 NSG 兩者中的規則彙總的結果。 這些 NSG 的規則有時候會互相衝突，影響 VM 的網路連線。  

您可以檢視 NSG 中對 VM NIC 套用的所有有效安全性規則。 本文說明如何在 Azure Resource Manager 部署模型中使用這些規則對 VM 連線問題進行疑難排解。 如果您不熟悉 VNet 與 NSG 的概念，請參閱[虛擬網路](virtual-networks-overview.md)和[網路安全性群組](virtual-networks-nsg.md)概觀文章。

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>使用有效安全性規則對 VM 流量流程進行疑難排解
下面的案例是常見的連線問題範例︰

一個名為 *VM1* 的 VM 位於 *WestUS-VNet1* VNet 內的 *Subnet1* 子網路。 嘗試使用 RDP over TCP 連接埠 3389 連線到 VM 時失敗。 在 NIC *VM1-NIC1* 和子網路 *Subnet1* 同時套用了 NSG。 與網路介面 *VM1-NIC1*相關聯的 NSG 中允許 TCP 連接埠 3389 的流量，不過 TCP Ping VM1 的連接埠 3389 失敗。

雖然此範例使用 TCP 連接埠 3389，但是可以使用下列的步驟判斷任何連接埠的輸入和輸出連線失敗。

## <a name="detailed-troubleshooting-steps"></a>詳細的疑難排解步驟
完成下列步驟對 VM 的 NSG 進行疑難排解：

1. 啟動 Azure PowerShell 工作階段並登入 Azure。 如果您不熟悉如何使用 Azure PowerShell，請閱讀 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview) 文章。
2. 輸入下列命令會傳回對資源群組 *RG1* 中名為 *VM1-NIC1* 的 NIC 套用的所有 NSG 規則：
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > 如果您不知道 NIC 的名稱，請輸入下列命令來擷取資源群組中所有 NIC 的名稱： 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    下列文字是針對 *VM1-NIC1* NIC 傳回的有效規則輸出範例：
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    請注意輸出中的下列資訊︰
   
   * 有兩個 **NetworkSecurityGroup** 區段︰一個與子網路 (*Subnet1*) 相關聯，一個與 NIC (*VM1-NIC1*) 相關聯。 在此範例中，兩個都已套用 NSG。
   * **Association** 顯示指定的 NSG 相關聯的資源 (子網路或 NIC)。 如果在移動 NSG 資源/解除 NSG 資源的關聯之後緊接著執行此命令，您可能需要等候幾秒鐘的時間，變更才會反映在命令輸出中。 
   * 前面加上 *defaultSecurityRules*的規則名稱︰建立 NSG 時，當中會建立幾個預設的安全性規則。 預設規則無法移除，但是可以較高優先順序的規則覆寫。
     閱讀 [NSG 概觀](virtual-networks-nsg.md#default-rules) 文章以深入了解 NSG 預設安全性規則。
   * **ExpandedAddressPrefix** 會展開 NSG 預設標籤的位址首碼。 標籤代表多個位址首碼。 對 VM 與特定位址首碼的連線進行疑難排解時，展開標籤很有用。 例如，如果有 VNET 對等互連，VIRTUAL_NETWORK 標籤會在前面的輸出中展開以顯示對等互連的 VNet 首碼。
     
     > [!NOTE]
     > 只有當 NSG 與子網路、NIC 或兩者關聯時，命令才會顯示有效規則。 一個 VM 可能有多個 NIC 分別套用不同的 NSG。 進行疑難排解時，請為每個 NIC 執行命令。
     > 
     > 
3. 為了方便篩選較大量的 NSG 規則，請輸入下列命令以進一步進行疑難排解︰ 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    下圖對格線檢視套用了一個 RDP 流量 (TCP 連接埠 3389) 的篩選︰
   
    ![規則清單](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. 您在格線檢視中會看到，RDP 有允許和拒絕兩種規則。 步驟 2 的輸出顯示 *DenyRDP* 規則是在對子網路套用的 NSG 中。 輸入規則會先處理對子網路套用的 NSG。 如果找到相符項目，則不會處理對網路介面套用的 NSG。 此時，子網路的 *DenyRDP* 規則會封鎖 RDP 到 VM (**VM1**)。
   
   > [!NOTE]
   > 一個 VM 可以附加多個 NIC。 每個 NIC 可以連線至不同的子網路。 因為上一個步驟中的命令是針對 NIC 執行，所以請務必確定指定的是遇到連線失敗的 NIC。 如果您不確定，可以針對每個附加至 VM 的 NIC 執行一次命令。
   > 
   > 
5. 若要使用 RDP 進入 VM1，請在 **Subnet1-NSG** NSG 中將 *Deny RDP (3389)* 規則變更為 *Allow RDP(3389)*。 開啟 VM 的 RDP 連線或使用 PsPing 工具，確認 TCP 連接埠 3389 已開啟。 您可以閱讀 [PsPing 下載頁面](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    或者您可以使用下列命令輸出中的資訊移除 NSG 中的規則︰
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>考量
對連線問題進行疑難排解時，請考量下列幾點︰

* 預設的 NSG 規則會封鎖來自網際網路的輸入存取，並只允許 VNet 輸入流量。 必須視需要明確地新增規則，才能允許來自網際網路的輸入存取。
* 如果沒有 NSG 安全性規則導致 VM 的網路連線失敗，則問題可能是來自於︰
  * VM 作業系統內執行的防火牆軟體
  * 為虛擬設備或內部部署流量設定的路由。 網際網路流量可以透過強制通道重新導向至內部部署。 使用此設定時，根據內部部署網路硬體處理此流量的方式，從網際網路可能無法 RDP/SSH 連線到您的 VM。 閱讀 [對路由進行疑難排解](virtual-network-routes-troubleshoot-powershell.md) 文章，以了解如何診斷可能會妨礙流量進出 VM 的路由問題。 
* 如果您有對等互連的 VNet，VIRTUAL_NETWORK 標籤預設會自動展開以包含對等互連的 VNet 的首碼。 您可以在 **ExpandedAddressPrefix** 清單中檢視這些首碼，對任何與 VNet 對等互連連線相關的問題進行疑難排解。 
* 只有在有 NSG 與 VM 的 NIC 和/或子網路關聯時，才會顯示有效安全性規則。 
* 如果沒有 NSG 與 NIC 或子網路相關聯，且您已對 VM 指派公用 IP 位址，則會開啟所有連接埠供輸入和輸出存取。 如果 VM 有公用 IP 位址，強烈建議對 NIC 或子網路套用 NSG。  

