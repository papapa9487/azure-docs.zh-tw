---
title: "從備份還原虛擬機器 | Microsoft Docs"
description: "了解如何從復原點還原 Azure 虛擬機器"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "還原備份；如何還原；復原點；"
ms.assetid: fed877b3-b496-49fb-99e4-653be7c23e3a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: trinadhk; jimpark;
ms.openlocfilehash: 5f6e5dd9d4fb96376762300856b594d772d84af8
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2017
---
# <a name="restore-virtual-machines-in-azure"></a>還原 Azure 中的虛擬機器
> [!div class="op_single_selector"]
> * [在 Azure 入口網站中還原 VM](backup-azure-arm-restore-vms.md)
> * [在傳統入口網站中還原 VM](backup-azure-restore-vms.md)
>
>

使用下列步驟，利用 Azure 備份保存庫中儲存的備份，將虛擬機器還原到新的 VM。

> [!IMPORTANT]
> 您現在可以將備份保存庫升級至復原服務保存庫。 如需詳細資訊，請參閱[將備份保存庫升級至復原服務保存庫](backup-azure-upgrade-backup-to-recovery-services.md)文章。 Microsoft 鼓勵您將備份保存庫升級至復原服務保存庫。 後續 <br/> 在 **2017 年 11 月 30 日**，您就無法使用 PowerShell 建立備份保存庫。 <br/> 在 **2017 年 11 月 30 日**以前：
>- 所有其餘的備份保存庫都會自動升級至復原服務保存庫。
>- 您將無法在傳統入口網站中存取您的備份資料。 相反地，使用 Azure 入口網站來存取您在復原服務保存庫中的備份資料。
>

## <a name="restore-workflow"></a>還原工作流程
### <a name="step-1-choose-an-item-to-restore"></a>步驟 1：選擇要還原的項目
1. 瀏覽至 [ **受保護項目** ] 索引標籤，然後選取您想要還原至新 VM 的虛擬機器。

    ![受保護項目](./media/backup-azure-restore-vms/protected-items.png)

    [受保護項目] 頁面中的 [復原點] 欄會告訴您虛擬機器的復原點數目。 [ **最新復原點** ] 欄位會告訴您虛擬機器可還原的最近備份時間。
2. 按一下 [還原] 以開啟 [還原項目] 精靈。

    ![還原項目](./media/backup-azure-restore-vms/restore-item.png)

### <a name="step-2-pick-a-recovery-point"></a>步驟 2：挑選復原點
1. 在 [ **選取復原點** ] 畫面中，您可以從最新的復原點或較舊的復原點進行還原。 精靈開啟時預設選取的選項是 [最新復原點] 。

    ![選取復原點](./media/backup-azure-restore-vms/select-recovery-point.png)
2. 若要挑選較舊的時間，請選擇下拉式清單中的 [選取日期] 選項，然後按一下**行事曆圖示**，在行事曆控制項中選取一個日期。 在控制項中，所有擁有復原點的日期會以淺灰色陰影填滿，並可供使用者選取。

    ![選取日期](./media/backup-azure-restore-vms/select-date.png)

    一旦您按一下行事曆控制項中的日期時，該日可用的復原點將會顯示在下方的復原點資料表中。 [ **時間** ] 欄位會指出擷取快照集的時間。 [ **類型** ] 欄位會顯示復原點的 [一致性](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) 。 資料表標頭會在括號裡顯示該日可用的復原點數目。

    ![復原點](./media/backup-azure-restore-vms/recovery-points.png)
3. 從 [復原點]  資料表中選取復原點，然後按一下 [下一步] 箭號以移至下一個畫面。

### <a name="step-3-specify-a-destination-location"></a>步驟 3：指定目的地位置
1. 在 [ **選取還原執行個體** ] 畫面中，指定要將虛擬機器還原至何處的詳細資料。

   * 指定虛擬機器名稱：在指定的雲端服務中，虛擬機器名稱應該是唯一的。 我們不支援覆寫現有的 VM。
   * 選取 VM 的雲端服務：這是建立 VM 的必要步驟。 您可以選擇使用現有的雲端服務，或建立新的雲端服務。

        挑選的雲端服務名稱應具有全域唯一性。 一般來說，雲端服務名稱會以 [cloudservice].cloudapp.net 的形式來與公眾對應 URL 產生相關。 如果名稱已在使用中，Azure 將不允許您建立新的雲端服務。 如果您選擇建立新的雲端服務，系統將會為該服務提供與虛擬機器相同的名稱 - 在此情況下，所挑選的 VM 名稱應該具備足以套用至相關雲端服務的唯一性。

        我們只會顯示與還原執行個體詳細資料中的任何同質群組不關聯的雲端服務和虛擬網路。 [深入了解](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。
2. 選取 VM 的儲存體帳戶：這是建立 VM 的必要步驟。 您可以選取與 Azure 備份保存庫位於相同區域的現有儲存體帳戶。 我們不支援區域備援或進階儲存體類型的儲存體帳戶。

    如果沒有支援組態的儲存體帳戶，請在啟動還原作業之前建立一個支援組態的儲存體帳戶。

    ![選取虛擬網路](./media/backup-azure-restore-vms/restore-sa.png)
3. 選取虛擬網路：在建立 VM 時應該已經選取虛擬機器的虛擬網路 (VNET)。 還原 UI 會顯示此訂用帳戶內所有可以使用的 VNET。 為已還原的 VM 選取 VNET 並非必要步驟 – 即使不套用 VNET，您仍然可透過網際網路連接到已還原的虛擬機器。

    如果選取的雲端服務與虛擬網路相關聯，則您無法變更虛擬網路。

    ![選取虛擬網路](./media/backup-azure-restore-vms/restore-cs-vnet.png)
4. 選取子網路：如果 VNET 有子網路，預設選取的選項為第一個子網路。 從下拉式清單選項中選擇您想要的子網路。 若需子網路的詳細資訊，請移至[入口網站首頁](https://manage.windowsazure.com/)中的 [網路] 擴充功能，然後移至 [虛擬網路]，並在選取虛擬網路後，向下切入至 [設定] 以查看子網路的詳細資料。

    ![選取子網路](./media/backup-azure-restore-vms/select-subnet.png)
5. 按一下精靈中的 [提交]  圖示以提交詳細資料和建立還原工作。

## <a name="track-the-restore-operation"></a>追蹤還原作業
一旦您輸入還原精靈中的所有資訊和提交，Azure 備份將會嘗試建立一項工作以追蹤還原作業。

![正在建立還原工作](./media/backup-azure-restore-vms/create-restore-job.png)

如果成功建立工作，您會看到快顯通知指出工作已建立。 您可以按一下 [檢視工作] 按鈕進入 [工作] 索引標籤，以取得更多詳細資料。

![已建立還原工作](./media/backup-azure-restore-vms/restore-job-created.png)

在還原作業完成時，系統會在 **工作**  索引標籤中將其標示為已完成。

![已完成還原工作](./media/backup-azure-restore-vms/restore-job-complete.png)

還原虛擬機器後，您可能需要重新安裝原始虛擬機器 (VM) 上現有的擴充功能，並為 Azure 入口網站中的虛擬機器 [修改端點](../virtual-machines/windows/classic/setup-endpoints.md) 。

## <a name="post-restore-steps"></a>還原後的步驟
如果您使用 cloud-init 型 Linux 散發套件 (例如 Ubuntu)，為求安全，還原後將會封鎖密碼。 請在還原的 VM 上使用 VMAccess 擴充功能 [重設密碼](../virtual-machines/linux/classic/reset-access.md)。 建議您在這些散發套件上使用 SSH 金鑰，以避免在還原後重設密碼。

## <a name="backup-for-restored-vms"></a>備份還原 VM
如果您已將 VM 還原至相同的雲端服務，並使用與原始備份 VM 相同的名稱，備份將會在還原後繼續進行。 如果您已將 VM 還原至不同的雲端服務，或為還原 VM 指定不同名稱，系統會將該 VM 視為新 VM，因此您需要為還原 VM 設定備份。

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>在 Azure 資料中心發生災害時還原 VM
如果 VM 執行的主要資料中心發生災害，而您已將備份保存庫設定為異地備援，Azure 備份可讓您將備份 VM 還原至配對的資料中心。 在這種情況下，您需要選取配對之資料中心內的儲存體帳戶，其餘的還原程序則保持不變。 Azure 備份會使用配對之地理區域的計算服務來建立還原虛擬機器。 深入了解 [Azure 資料中心復原](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-domain-controller-vms"></a>還原網域控制站 VM
備份網域控制站 (DC) 虛擬機器是 Azure 備份支援的案例。 不過，在還原程序進行期間請務必小心。 正確的還原程序取決於網域結構。 最簡單的情況是您在單一網域中擁有單一 DC。 而在生產環境的負載中，較常見的情況是您擁有單一網域且內含多個 DC，其中有些 DC 可能位於內部部署環境。 最後，您也可能擁有內含多個網域的樹系。

從 Active Directory 的觀點來看，Azure VM 就像是位於新式的受支援 Hypervisor 上的其他 VM。 與內部部署 Hypervisor 的主要差異是，Azure 沒有提供 VM 主控台。 在某些情況下，您必須使用主控台，例如使用裸機復原 (BMR) 類型的備份進行復原。 不過，從備份保存庫來還原 VM 可完整取代 BMR。 我們也提供了 Active Directory 還原模式 (DSRM)，因此，您可以進行所有的 Active Directory 復原案例。 如需更多背景資訊，請參閱[虛擬網域控制站的備份和還原考量](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers)以及[規劃 Active Directory 樹系復原](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx)。

### <a name="single-dc-in-a-single-domain"></a>單一網域中的單一 DC
VM 可以從 Azure 入口網站或使用 PowerShell 還原 (就像任何其他 VM)。

### <a name="multiple-dcs-in-a-single-domain"></a>單一網域中的多個 DC
如果您可以透過網路來連線到相同網域內的其他 DC，您就可以像 VM 一樣地還原 DC。 如果該 DC 是網域內剩餘的最後一個 DC，或者您是在隔離的網路中進行復原，則您必須遵循樹系的復原程序。

### <a name="multiple-domains-in-one-forest"></a>單一樹系中的多個網域
如果您可以透過網路來連線到相同網域內的其他 DC，您就可以像 VM 一樣地還原 DC。 不過，若是其他情況，則建議進行樹系復原。

<!--- WK: this following original supportability statement is incorrect, taking it out.
The challenge arises because DSRM mode is not present in Azure. So to restore such a VM, you cannot use the Azure portal. The only supported restore mechanism is disk-based restore using PowerShell.

> [!WARNING]
> For Domain Controller VMs in a multi-DC environment, do not use the Azure portal for restore! Only PowerShell based restore is supported
>
>

Read more about the [USN rollback problem](https://technet.microsoft.com/library/dd363553) and the strategies suggested to fix it.
--->

## <a name="restoring-vms-with-special-network-configurations"></a>還原具有特殊網路組態的 VM
Azure 備份支援備份虛擬機器的以下特殊網路組態。

* 負載平衡器底下的 VM (內部與外部)
* 具有多個保留的 IP 的 VM
* 具有多個 NIC 的 VM

這些組態可在還原組態時授權以下考量項目。

> [!TIP]
> 請使用 PowerShell 型還原流程重新建立 VM 後續還原的特殊網路組態。
>
>

### <a name="restoring-from-the-ui"></a>從 UI 還原：
從 UI 還原時，請 **一律選擇新的雲端服務**。 請注意，入口網站在還原流程中只接受強制參數，使用 UI 還原的 VM 將會失去它們擁有的特殊網路組態。 也就是說，還原後的 VM 將會是一般的 VM，不具有負載平衡器組態、多個 NIC 或多個保留的 IP。

### <a name="restoring-from-powershell"></a>從 PowerShell 還原：
PowerShell 能夠只從備份還原 VM 磁碟，而不建立虛擬機器。 在還原需要上述特殊網路組態的虛擬機器時，這很有用。

為了完整重新建立虛擬機器後續還原磁碟，請依照以下步驟執行：

1. 使用 [Azure 備份 PowerShell](backup-azure-vms-classic-automation.md#restore-an-azure-vm)
2. 使用 PowerShell Cmdlet 建立負載平衡器/多個 NIC/多個保留的 IP 所需的 VM 組態，並使用該組態建立具備想要之組態的 VM。

   * 使用 [內部負載平衡器 ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * 建立 VM 來連接至[網際網路面向負載平衡器](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * 建立具有 [多個 NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * 建立具有 [多個保留的 IP](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>後續步驟
* [錯誤疑難排解](backup-azure-vms-troubleshoot.md#restore)
* [管理虛擬機器](backup-azure-manage-vms.md)
