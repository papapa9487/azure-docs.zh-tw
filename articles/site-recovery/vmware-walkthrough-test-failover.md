---
title: "執行將 VMware 複寫至 Azure 的測試容錯移轉 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 服務來執行將 VMware VM 複寫至 Azure 之測試容錯移轉所需的步驟。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a640e139-3a09-4ad8-8283-8fa92544f4c6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: f1a6df56a2bb0094d972d2e659057cc124156b88
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="step-12-run-a-test-failover-to-azure-for-vmware-vms"></a>步驟 12：執行從 VMware VM 至 Azure 的測試容錯移轉

本文說明如何在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務，執行從內部部署 VMware 虛擬機器至 Azure 的測試容錯移轉。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。


## <a name="before-you-start"></a>開始之前

在您執行測試容錯移轉之前，建議您確認 VM 屬性，並進行任何需要的變更。 您可以在 [複寫的項目] 中存取 VM 屬性。 [程式集]  刀鋒視窗會顯示機器設定與狀態的相關資訊。

## <a name="managed-disk-considerations"></a>受控磁碟的考量

[受控磁碟](../virtual-machines/windows/managed-disks-overview.md)可透過管理與 VM 磁碟關聯的儲存體帳戶，來簡化 Azure VM 的磁碟管理。 

- 當您為 VM 啟用保護時，VM 資料會複寫到儲存體帳戶。 只有在發生容錯移轉時，才會建立受控磁碟並將它們連結至 VM。
- 只有針對使用 Resource Manager 模型來部署的 VM，才能建立受控磁碟。  
- 在啟用此設定的情況下，只能選取「資源群組」中已啟用 [使用受控磁碟] 的可用性設定組。 具有受控磁碟的 VM 必須位於 [使用受控磁碟] 設定為 [是] 的可用性群組中。 如果沒有為 VM 啟用此設定，則只能選取「資源群組」中未啟用受控磁碟的可用性設定組。
- [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set)受控磁碟和可用性設定組。
- 如果您用於複寫的儲存體帳戶已使用「儲存體服務加密」進行加密，在容錯移轉期間便無法建立受控磁碟。 在此情況下，請不要啟用使用受控磁碟的功能，或是停用 VM 保護，然後再將它重新啟用，以使用未啟用加密的儲存體帳戶。 [深入了解](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption)。


## <a name="network-considerations"></a>網路考量

您可以為在容錯移轉後建立的 Azure VM 設定目標 IP 位址。

- 如果您未提供地址，則容錯移轉的機器會使用 DHCP。
- 如果您設定的位址在容錯移轉時無法使用，則容錯移轉會失敗。
- 如果相同的目標 IP 位址在測試容錯移轉網路中可用，則此位址可用於測試容錯移轉。
- 網路介面卡的數目會視您指定給目標虛擬機器的大小而有所不同：

     - 如果來源電腦上的網路介面卡數目等於或小於針對目標電腦大小所允許的介面卡數目，則目標將具備與來源相同的介面卡數目。
     - 如果來源虛擬機器的介面卡數目超過針對目標大小所允許的數目，則將使用目標大小的最大值。
     - 例如，如果來源機器具有兩張網路介面卡，而目標機器大小支援四張，則目標機器將會有兩張介面卡。 如果來源機器具有兩張介面卡，但支援的目標大小僅支援一張，則目標機器將只會有一張介面卡。     
   - 如果虛擬機器有多張網路介面卡，則全部會連接至相同的網路。
   - 如果虛擬機器具有多個網路介面卡，則清單中顯示的第一個會變成 Azure 虛擬機器中的*預設*網路介面卡。
 - [深入了解](vmware-walkthrough-network.md) IP 位址指定。



## <a name="view-and-modify-vm-settings"></a>檢視及修改 VM 設定

建議您在執行容錯移轉之前，先確認來源機器的屬性。

1. 在 [受保護的項目] 中，按一下 [複寫的項目]，然後按一下 VM。
2. 在 [複寫的項目] 窗格中，您可以看到 VM 資訊、健康情況狀態及最新可用復原點的摘要。 若要檢視其他詳細資料，請按一下 [屬性]。
3. 在 [計算與網路] 中，您可以：
    - 修改 Azure VM 名稱。 名稱必須符合 [Azure 需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
    - 指定容錯移轉後的 [資源群組]。
    - 指定 Azure VM 的目標大小
    - 選取[可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)。
    - 指定是否要使用[受控磁碟](#managed-disk-considerations)。 如果您想要將受控磁碟連結到您移轉至 Azure 的機器上，請選取 [是]。
    - 檢視或修改網路設定，包括在容錯移轉後 Azure VM 將位於的網路/子網路，以及要指派給它的 IP 位址。
4. 在 [磁碟] 中，您可以看見有關 VM 上作業系統和資料磁碟的資訊。

## <a name="run-a-test-failover"></a>執行測試容錯移轉

一切就緒後，執行測試容錯移轉，確定一切如預期般運作。

- 如果您想要在容錯移轉後使用 RDP 來連線到 Azure VM，請[準備連線](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。
 - 若要完整測試，您需要在測試環境中將 Active Directory 和 DNS 進行複製。 [深入了解](site-recovery-active-directory.md#test-failover-considerations)。
 - 如需有關測試容錯移轉的完整資訊，請參閱[這篇文章](site-recovery-test-failover-to-azure.md)。
- 在開始之前，請透過影片快速建立概念︰


>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


現在，請執行容錯移轉：

1. 若要容錯移轉單一機器，請在 [設定]  >  [複寫的項目] 中，按一下 VM > [+測試容錯移轉] 圖示。

    ![測試容錯移轉](./media/vmware-walkthrough-test-failover/test-failover.png)

2. 若要容錯移轉復原方案，請在 [設定]  >  [復原方案] 中，以滑鼠右鍵按一下方案 > [測試容錯移轉]。 若要建立復原方案，請[遵循這些指示](site-recovery-create-recovery-plans.md)。  

3. 在 [測試容錯移轉] 中，選取 Azure VM 在容錯移轉之後要連接的 Azure 網路。

4. 按一下 [確定]  即可開始容錯移轉。 您可以按一下 VM 以開啟其屬性，或在保存庫名稱 > [設定]  >  [作業]  >  [Site Recovery 作業] 中的 [測試容錯移轉] 作業上按一下，來追蹤進度。

5. 容錯移轉完成之後，您應該也會看到複本 Azure 機器出現在 Azure 入口網站 > [虛擬機器]中。 您應該確定 VM 為適當的大小、已連接到適當的網路，而且正在執行中。

6. 如果您已準備好在容錯移轉後進行連線，應該能夠連線到 Azure VM。

7. 完成之後，在復原方案上按一下 [清除測試容錯移轉]。 在 [記事] 中，記錄並儲存關於測試容錯移轉的任何觀察。 這將刪除在測試容錯移轉期間所建立的 VM。



## <a name="next-steps"></a>後續步驟

- [深入了解](site-recovery-failover.md)不同類型的容錯移轉及如何執行。
- 如果您要移轉電腦而不是複寫和容錯回復，請[深入了解](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers)。
- [深入了解容錯回復](site-recovery-failback-azure-to-vmware.md)，以便將 Azure VM 容錯回復和複寫回到主要內部部署網站。

