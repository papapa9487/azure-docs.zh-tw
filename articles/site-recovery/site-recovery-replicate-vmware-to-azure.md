---
title: "複寫應用程式 (VMware 至 Azure) | Microsoft Docs"
description: "本文說明如何將在 VMware 上執行的虛擬機器設定複寫至 Azure。"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e0047a996c9bfd7d950b32f0871ddd7608924b42
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---


# <a name="replicate-applications-running-on-vmware-vms-to-azure"></a>將 VMware VM 上執行的應用程式複寫至 Azure



本文說明如何將在 VMware 上執行的虛擬機器設定複寫至 Azure。
## <a name="prerequisites"></a>必要條件

本文假設您已經

1.  [設定內部部署來源環境](site-recovery-set-up-vmware-to-azure.md)
2.  [在 Azure 中設定目標環境](site-recovery-prepare-target-vmware-to-azure.md)


## <a name="enable-replication"></a>啟用複寫
#### <a name="before-you-start"></a>開始之前
當您要複寫 VMware 虛擬機器時，請注意下列事項︰

* 您的 Azure 使用者帳戶必須具有特定[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能將新的虛擬機器複寫至 Azure。
* 系統會每隔 15 分鐘探索 VMware VM 一次。 在探索之後，可能需要 15 分鐘以上，VMware VM 才會出現在入口網站中。 同樣地，當您加入新的 vCenter 伺服器或 vSphere 主機時，探索可能需要 15 分鐘以上。
* 虛擬機器上的環境變更 (例如 VMware 工具安裝) 會花上 15 分鐘以上的時間，才會在入口網站更新。
* 您可以在 [組態伺服器] 刀鋒視窗上 vCenter 伺服器/vSphere 主機的 [上次連絡時間] 欄位中，查看 VMware VM 的上次探索時間。
* 若要新增要複寫的機器而不等候已排定的探索，請反白選取組態伺服器 (不要按它)，然後按一下 [重新整理] 按鈕。
* 當您啟用複寫時，如果機器已準備好，處理序伺服器會自動在其上安裝行動服務。


**立即啟用複寫，如下所示**︰

1. 按一下 [步驟 2: 複寫應用程式]  >  [來源]。 第一次啟用複寫之後，請按一下保存庫中的 [+複寫]，以對其他機器啟用複寫。
2. 在 [來源] 刀鋒視窗 > [來源] 中，選取組態伺服器。
3. 在 [機器類型] 中，選取 [虛擬機器] 或 [實體機器]。
4. 在 [vCenter/vSphere Hypervisor] 中，選取管理 vSphere 主機的 vCenter 伺服器，或選取主機。 如果您是複寫實體機器，則這個設定不相關。
5. 選取處理序伺服器。 如果您尚未建立任何額外的處理序伺服器，這將是組態伺服器的名稱。 然後按一下 [確定] 。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. 在 [目標] 中，選取您想要在其中建立容錯移轉虛擬機器的訂用帳戶和資源群組。 選擇您想要在 Azure (傳統或資源管理) 中，針對容錯移轉虛擬機器使用的部署模型。
7. 選取您要用來複寫資料的 Azure 儲存體帳戶。 請注意：

   * 您可以選取進階或標準儲存體帳戶。 如果選取進階帳戶，您需要針對進行中的複寫記錄檔，額外指定一個標準儲存體帳戶。 帳戶必須位於與復原服務保存庫相同的區域中。
   * 如果您想要使用和您所擁有之儲存體帳戶不同的儲存體帳戶，您可以使用資源管理員建立一個建立儲存體帳戶的預留位置連結，這將在＜開始使用＞中涵蓋。 若要使用 Resource Manager 建立儲存體帳戶，請按一下 [新建]。 如果您想要使用傳統模型建立儲存體帳戶，請 [在 Azure 入口網站中](../storage/common/storage-create-storage-account.md)執行該作業。

8. 選取 Azure VM 在容錯移轉後啟動時所要啟動的 Azure 網路和子網路。 此網路必須位於與復原服務保存庫相同的區域中。 選取 [立即設定選取的機器]，將網路設定套用至您選取要進行保護的所有機器。 選取 [稍後設定] 以選取每部機器的 Azure 網路。 如果您沒有網路，則必須[建立一個](#set-up-an-azure-network)。 若要使用 Resource Manager 建立網路，請按一下 [新建]。 如果您想要使用傳統模型建立網路，請[在 Azure 入口網站中](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)執行該作業。 選取適用的子網路。 然後按一下 [確定] 。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. 在 [虛擬機器] > [選取虛擬機器] 中，按一下並選取您要複寫的每部機器。 您只能選取可以啟用複寫的機器。 然後按一下 [確定] 。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. 在 [名稱]  > 中，選取處理序伺服器將用來在機器上自動安裝行動服務的帳戶。  
11. 依預設會複寫所有磁碟。 若要將磁碟從複寫中排除，按一下 [所有磁碟]，然後清除任何您不想要複寫的磁碟。  然後按一下 [確定] 。 您可以稍後再設定其他屬性。 [深入了解](site-recovery-exclude-disk.md)排除磁碟。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/enable-replication6.png)

12. 在 [複寫設定] > [設定複寫設定] 中，確認已選取正確的複寫原則。 您可以在 [設定]  >  [複寫原則] > 原則名稱 > [編輯設定] 中修改複寫原則設定。 套用到原則的變更將會套用到複寫和新的機器。
13. 如果您想要將機器聚集成一個複寫群組，請啟用 [多部 VM 一致性]  ，並指定群組的名稱。 然後按一下 [確定] 。 請注意：

    * 複寫群組中的機器會一起複寫，並且在容錯移轉時會有共用的損毀一致和應用程式一致的復原點。
    * 我們建議您將 VM 與實體伺服器一起收集，讓它們鏡像您的工作負載。 啟用多部 VM 一致性可能會影響工作負載的效能，應該只用於機器正在執行相同工作負載，且您需要一致性的情況。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/enable-replication7.png)
14. 按一下 [啟用複寫] 。 您可以在 [設定]  >  [作業]  >  [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。 執行 [完成保護]  作業之後，機器即準備好進行容錯移轉。

> [!NOTE]
> 如果機器已準備好進行推送安裝，則當啟用保護時，將會安裝行動服務元件。 在機器上安裝元件之後，保護工作會啟動並失敗。 在失敗之後，您需要手動重新啟動每一部機器。 重新啟動之後，保護作業再次開始，並且會發生初始複寫。
>
>

## <a name="view-and-manage-vm-properties"></a>檢視及管理 VM 屬性

建議您確認來源機器的屬性。 請記住，Azure VM 名稱應該符合 [Azure 虛擬機器需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

1. 按一下 [設定] > [複寫的項目]，然後選取機器。 [程式集]  刀鋒視窗會顯示機器設定與狀態的相關資訊。
2. 在 [屬性] 中，您可以檢視 VM 的複寫和容錯移轉資訊。
3. 在 [計算和網路] > [計算屬性] 中，您可以指定 Azure VM 名稱和目標大小。 視需要修改名稱以符合 Azure 需求。
    ![啟用複寫](./media/site-recovery-vmware-to-azure/VMProperties_AVSET.png)
 
4.  您可以選取電腦將成為其後置容錯移轉一部分的[資源群組](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)。 您可以在容錯移轉之前隨時變更這項設定。 容錯移轉後，如果將電腦移轉到不同的資源群組，則會中斷電腦的保護設定。
5. 如果您的電腦必須是其中一個後置容錯移轉，您可以選取[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)。 當選取可用性設定組時，請記住︰

    * 只將屬於指定之資源群組的可用性設定組列出  
    * 虛擬網路不同的電腦不可在相同的可用性設定組中
    * 只有相同大小的虛擬機器可在相同的可用性設定組中
5. 您也可以檢視和加入目標網路、子網路的相關資訊，以及將指派給 Azure VM 的 IP 位址。
6. 在 [磁碟] 中，您可以看見 VM 上將要複寫的作業系統和資料磁碟。

### <a name="network-adapters-and-ip-addressing"></a>網路介面卡和 IP 位址 

- 您可以設定目標 IP 位址。 如果您未提供地址，則容錯移轉的機器會使用 DHCP。 如果您設定的位址在容錯移轉時無法使用，則容錯移轉會失敗。 如果位址可用於測試容錯移轉網路，則相同的目標 IP 位址可用於測試容錯移轉。
- 網路介面卡的數目會視您指定給目標虛擬機器的大小而有所不同，如下所示：
    - 如果來源電腦上的網路介面卡數目小於或等於針對目標機器大小所允許的介面卡數目，則目標將具備與來源相同的介面卡數目。
    - 如果來源虛擬機器的介面卡數目超過針對目標大小所允許的數目，則將使用目標大小的最大值。
    - 例如，如果來源機器具有兩張網路介面卡，而目標機器大小支援四張，則目標機器將會有兩張介面卡。 如果來源機器具有兩張介面卡，但支援的目標大小僅支援一張，則目標機器將只會有一張介面卡。
    - 如果虛擬機器有多張網路介面卡，則全部會連接至相同的網路。
    - 如果虛擬機器具有多個網路介面卡，則清單中顯示的第一個會變成 Azure 虛擬機器中的*預設*網路介面卡。
   



## <a name="common-issues"></a>常見問題

* 各磁碟大小必須都小於 1 TB。
* OS 磁碟應該是基本磁碟而非動態磁碟
* 針對第 2 代/已啟用 UEFI 的虛擬機器，作業系統系列應為 Windows，而且開機磁碟應小於 300 GB

## <a name="next-steps"></a>後續步驟

保護完成之後，您可以嘗試[容錯移轉](site-recovery-failover.md)，以檢查應用程式是否會出現在 Azure 中。

如果您想要停用保護，請檢查如何[清除註冊與保護設定](site-recovery-manage-registration-and-protection.md)

