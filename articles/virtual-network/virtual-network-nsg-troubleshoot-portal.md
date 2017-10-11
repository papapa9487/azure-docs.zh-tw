---
title: "為網路安全性群組疑難排解 - 入口網站 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站對 Azure Resource Manager 部署模型中的網路安全性群組進行疑難排解。"
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: f01d3b43a7953697a6b03e176dace33448d95cd9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>使用 Azure 入口網站為網路安全性群組疑難排解
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

### <a name="vm"></a>檢視虛擬機器的有效安全性規則
完成下列步驟對 VM 的 NSG 進行疑難排解：

您可以從 VM 本身檢視 NIC 上完整的有效安全性規則清單。 如果您有權限，也可以從有效規則的刀鋒視窗新增、修改和刪除 NIC 和子網路的 NSG 規則。

1. 登入 Azure 入口網站，位址是 https://portal.azure.com。
2. 按一下 [更多服務]，然後在出現的清單中按一下 [虛擬機器]。
3. 從出現的清單中選取要進行疑難排解的 VM，此時會出現一個有選項的 VM 刀鋒視窗。
4. 按一下 [診斷並解決問題]，然後選取常見的問題。 例如選取了 [我無法連接到我的 Windows VM]  。 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. 在問題下方會出現步驟，如下圖所示︰ 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    按一下建議步驟清單中的 [有效安全性群組規則]  。
6. [取得有效的安全性規則]  刀鋒視窗隨即會出現，如下圖所示︰
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    請注意圖片的下列各區段︰
   
   * **範圍︰** 設定為在步驟 3 選取的 VM *VM1*。
   * **網路介面︰** *VM1-NIC1* 。 一個 VM 可以有多個網路介面 (NIC)。 每個 NIC 只能有唯一的有效安全性規則。 進行疑難排解時，您可能需要檢視每個 NIC 的有效安全性規則。
   * **相關聯的 NSG：** 可以對 NIC 和 NIC 所連線到的子網路兩者套用 NSG。 在圖片中，已經對 NIC 和所連線的子網路兩者套用 NSG。 您可以按一下 NSG 名稱來直接修改 NSG 中的規則。
   * **VM1-nsg 索引標籤︰** 圖片中顯示的規則清單是對 NIC 套用的 NSG。 每當建立一個 NSG 時，Azure 就會建立幾個預設的規則。 您無法移除預設規則，但是可以使用較高優先順序的規則覆寫。 若要深入了解預設規則，請閱讀 [NSG 概觀](virtual-networks-nsg.md#default-rules) 文章。
   * **DESTINATION 欄︰** 欄中某些規則有文字，而有些規則有位址首碼。 文字是在安全性規則建立時套用的預設標籤名稱。 標籤是系統提供的識別項，代表多個首碼。 選取有標籤的規則，例如 *AllowInternetOutBound* 會在 [位址首碼] 刀鋒視窗中列出首碼。
   * **下載︰** 規則的清單可能很長。 您可以按一下 [下載]  並儲存檔案，下載規則的 .csv 檔案供離線分析。
   * **AllowRDP** 輸入規則︰此規則允許透過 RDP 連線到 VM。
7. 按一下 [Subnet1-NSG]  索引標籤可檢視對子網路套用的 NSG 有效規則，如下圖所示︰ 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
    請注意 *denyRDP* **輸入** 規則。 在網路介面套用規則之前，會先評估在子網路套用的輸入規則。 由於在子網路套用了拒絕規則，而不會評估在 NIC 的允許規則，連線到 TCP 3389 的要求失敗。 
   
    *denyRDP* 規則是 RDP 連線失敗的原因。 移除此規則應該可以解決問題。
   
   > [!NOTE]
   > 如果與 NIC 相關聯的 VM 不在執行中，或者尚未對 NIC 或子網路套用 NSG，則不會顯示任何規則。
   > 
   > 
8. 若要編輯 NSG 規則，請在 [相關聯的 NSG] 區段中按一下 [Subnet1-NSG]。
   這會開啟 [Subnet1-NSG]  刀鋒視窗。 您可以按一下 [輸入安全性規則] ，直接編輯規則。
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. 在 [Subnet1-NSG] 中移除 *denyRDP* 輸入規則並加入 *allowRDP* 規則之後，有效規則清單看起來會類似下圖︰
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    開啟 VM 的 RDP 連線或使用 PsPing 工具，確認 TCP 連接埠 3389 已開啟。 您可以閱讀 [PsPing 下載頁面](https://technet.microsoft.com/sysinternals/psping.aspx)進一步了解 PsPing。

### <a name="nic"></a>檢視網路介面的有效安全性規則
如果您 VM 的流量流程因特定 NIC 而受到影響，可以完成下列步驟從網路介面內容檢視 NIC 的有效規則完整清單︰

1. 登入 Azure 入口網站，位址是 https://portal.azure.com。
2. 按一下 [更多服務]，然後在出現的清單中按一下 [網路介面]。
3. 選取某個網路介面。 在下圖中選取了名為 *VM1-NIC1* 的 NIC。
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    請注意 [範圍]  已設為選取的網路介面。 若要深入了解所顯示的其他資訊，請閱讀本文＜對 VM 的 NSG 進行疑難排解＞  一節的步驟 6。
   
   > [!NOTE]
   > 如果從網路介面移除 NSG，子網路 NSG 仍然會對指定的 NIC 有效。 此時，輸出只會顯示子網路 NSG 的規則。 只有當 NIC 附加至 VM 時，規則才會出現。
   > 
   > 
4. 您可以直接編輯與 NIC 和子網路相關聯之 NSG 的規則。 若要了解如何進行，請閱讀＜檢視虛擬機器的有效安全性規則＞  一節的步驟 8。

## <a name="nsg"></a>檢視網路安全性群組 (NSG) 的有效安全性規則
修改 NSG 規則時，可以檢閱特定 VM 上新增規則的影響。 您可以檢視套用指定 NSG 之所有 NIC 的完整有效安全性規則清單，而不必從指定的 NSG 刀鋒視窗切換內容。 若要對 NSG 中的有效規則進行疑難排解，請完成下列步驟︰

1. 登入 Azure 入口網站，位址是 https://portal.azure.com。
2. 按一下 [更多服務]，然後在出現的清單中按一下 [網路安全性群組]。
3. 選取某個 NSG。 在下圖中選取了名為 VM1-nsg 的 NSG。
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    請注意上圖的下列各區段︰
   
   * **範圍︰** 設定為選取的 NSG。
   * **虛擬機器︰** 對子網路套用 NSG 時，會對連線到子網路的所有 VM 的所有網路介面套用。 此清單顯示套用此 NSG 的所有 VM。 您可以從清單中選取任何 VM。
     
     > [!NOTE]
     > 如果只對空白子網路套用 NSG，則不會列出 VM。 如果對與 VM 無關的 NIC 套用 NSG，也不會列出這些 NIC。 
     > 
     > 
   * **網路介面：**一個 VM 可以有多個網路介面。 您可以選取附加至所選 VM 的網路介面。
   * **AssociatedNSGs：**一個 NIC 在任何時候最多可以有兩個有效的 NSG，一個套用到 NIC，一個套用到子網路。 雖然範圍選取了 VM1-nsg，但如果 NIC 有有效的子網路 NSG，輸出會顯示兩個 NSG。
4. 您可以直接編輯與 NIC 或子網路相關聯之 NSG 的規則。 若要了解如何進行，請閱讀＜檢視虛擬機器的有效安全性規則＞  一節的步驟 8。

若要深入了解所顯示的其他資訊，請閱讀本文＜檢視虛擬機器的有效安全性規則＞  一節的步驟 6。

> [!NOTE]
> 雖然子網路和 NIC 各只能套用一個 NSG，但是 NSG 可以與多個 NIC 和多個子網路相關聯。
> 
> 

## <a name="considerations"></a>考量
對連線問題進行疑難排解時，請考量下列幾點︰

* 預設的 NSG 規則會封鎖來自網際網路的輸入存取，並只允許 VNet 輸入流量。 必須視需要明確地新增規則，才能允許來自網際網路的輸入存取。
* 如果沒有 NSG 安全性規則導致 VM 的網路連線失敗，則問題可能是來自於︰
  * VM 作業系統內執行的防火牆軟體
  * 為虛擬設備或內部部署流量設定的路由。 網際網路流量可以透過強制通道重新導向至內部部署。 使用此設定時，根據內部部署網路硬體處理此流量的方式，從網際網路可能無法 RDP/SSH 連線到您的 VM。 閱讀 [對路由進行疑難排解](virtual-network-routes-troubleshoot-powershell.md) 文章，以了解如何診斷可能會妨礙流量進出 VM 的路由問題。 
* 如果您有對等互連的 VNet，VIRTUAL_NETWORK 標籤預設會自動展開以包含對等互連的 VNet 的首碼。 您可以在 **ExpandedAddressPrefix** 清單中檢視這些首碼，對任何與 VNet 對等互連連線相關的問題進行疑難排解。 
* 只有在有 NSG 與 VM 的 NIC 和/或子網路關聯時，才會顯示有效安全性規則。 
* 如果沒有 NSG 與 NIC 或子網路相關聯，且您已對 VM 指派公用 IP 位址，則會開啟所有連接埠供輸入和輸出存取。 如果 VM 有公用 IP 位址，強烈建議對 NIC 或子網路套用 NSG。

