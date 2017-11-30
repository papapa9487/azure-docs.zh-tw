---
title: "準備機器，在使用 Site Recovery 移轉至 Azure 後，設定 Azure 之間的災害復原 | Microsoft Docs"
description: "本文說明如何準備機器，以在使用 Azure Site Recovery 移轉至 Azure 後，設定 Azure 區域之間的災害復原。"
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: ponatara
ms.openlocfilehash: 2943d33744a29da7ffd1f1544fc81696195ecce1
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="replicate-azure-vms-to-another-region-after-migration-to-azure-by-using-azure-site-recovery"></a>使用 Azure Site Recovery 移轉至 Azure 後，將 Azure VM 複寫至另一個區域

>[!NOTE]
> Azure 虛擬機器 (VM) 的 Azure Site Recovery 複寫目前為預覽狀態。

## <a name="overview"></a>概觀

本文可協助您準備 Azure 虛擬機器，在利用 Azure Site Recovery 從內部部署環境將這些機器移轉到 Azure 後，進行兩個 Azure 區域之間的複寫。

## <a name="disaster-recovery-and-compliance"></a>災害復原和合規性
現在，越來越多企業將其工作負載移轉到 Azure。 隨著企業將任務關鍵性內部部署生產工作負載移轉到 Azure，必須對於這些工作負載設定災害復原，才能達到合規性並防範 Azure 區域的任何干擾。

## <a name="steps-for-preparing-migrated-machines-for-replication"></a>準備移轉後的機器以進行複寫的步驟
若要準備移轉後的機器以設定複寫到另一個 Azure 區域：

1. 完成移轉。
2. 視需要安裝 Azure 代理程式。
3. 移除行動服務。  
4. 重新啟動 VM。

這些步驟將於下列各節中詳細說明。

### <a name="step-1-migrate-workloads-running-on-hyper-v-vms-vmware-vms-and-physical-servers-to-run-on-azure-vms"></a>步驟 1：將在 Hyper-V VM、VMware VM 和實體伺服器上執行的工作負載，移轉到 Azure VM 上來執行

若要設定複寫，並將內部部署 Hyper-V、 VMware 和實體工作負載移轉至 Azure，請依照[使用 Azure Site Recovery 在 Azure 區域之間移轉 Azure IaaS 虛擬機器](site-recovery-migrate-azure-to-azure.md)文章中的步驟執行。 

移轉後，您不需要認可或刪除容錯移轉。 相反地，請為您要移轉的每一部機器選取 [完成移轉] 選項：
1. 在 [複寫的項目] 中，以滑鼠右鍵按一下 VM，然後按一下 [完成移轉]。 按一下 [確定] 完成步驟。 您可以在 [Site Recovery 作業] 中監視 [完成移轉] 作業，以在 VM 屬性中追蹤進度。
2. **完成移轉**動作會完成移轉程序、移除機器的複寫，並停止該機器的 Site Recovery 計費。

### <a name="step-2-install-the-azure-vm-agent-on-the-virtual-machine"></a>步驟 2：在虛擬機器中安裝 Azure VM 代理程式
Azure [VM 代理程式](../../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux)必須安裝在虛擬機器上，站台復原擴充功能才能運作，並協助保護 VM。

>[!IMPORTANT]
>從版本 9.7.0.0 開始，在 Windows 虛擬機器上，行動服務安裝程式也會安裝最新可用的 Azure VM 代理程式。 在移轉時，虛擬機器符合使用任何 VM 擴充功能的代理程式安裝必要條件，包括 Site Recovery 擴充功能。 移轉後的機器上安裝的行動服務是 9.6 或更早版本時，才需要手動安裝 Azure VM 代理程式。

下表提供安裝 VM 代理程式和驗證已安裝所需的詳細資訊：

| **作業** | **Windows** | **Linux** |
| --- | --- | --- |
| 安裝 VM 代理程式 |下載並安裝 [代理程式 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您需要有系統管理員權限，才能完成安裝。 |安裝最新的 [Linux 代理程式](../../virtual-machines/linux/agent-user-guide.md)。 您需要有系統管理員權限，才能完成安裝。 我們建議您從散發儲存機制安裝代理程式。 我們「不建議」您直接從 Github 安裝 Linux VM 代理程式。  |
| 驗證 VM 代理程式安裝 |1.瀏覽至 Azure VM 中的 C:\WindowsAzure\Packages 資料夾。 您應該會看見 WaAppAgent.exe 檔案。 <br>2.在該檔案上按一下滑鼠右鍵，前往 [屬性]，然後選取 [詳細資料] 索引標籤。[產品版本] 欄位應為 2.6.1198.718 或更高版本。 |N/A |


### <a name="step-3-remove-the-mobility-service-from-the-migrated-virtual-machine"></a>步驟 3：從移轉後的虛擬機器解除行動服務

如果您已移轉 Windows/Linux 上的內部部署 VMware 機器或實體伺服器，則需要從移轉後的虛擬機器手動移除/解除安裝行動服務。

>[!IMPORTANT]
>移轉至 Azure 的 Hyper-V VM 不需要進行此步驟。

#### <a name="uninstall-the-mobility-service-on-a-windows-server-vm"></a>將 Windows Server VM 上的行動服務解除安裝
您可以使用下列其中一種方法將 Windows Server 電腦上的行動服務解除安裝。

##### <a name="uninstall-by-using-the-windows-ui"></a>使用 Windows UI 來解除安裝
1. 在 [控制台] 中，選取 [程式]。
2. 選取 [Microsoft Azure Site Recovery Mobility Service/主要目標伺服器]，然後選取 [解除安裝]。

##### <a name="uninstall-at-a-command-prompt"></a>在命令提示字元中解除安裝
1. 以系統管理員身分開啟 [命令提示字元] 視窗。
2. 執行下列命令以將行動服務解除安裝：

   ```
   MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
   ```

#### <a name="uninstall-the-mobility-service-on-a-linux-computer"></a>將 Linux 電腦上的行動服務解除安裝
1. 在 Linux 伺服器上，以 **root** 使用者登入。
2. 在終端機中，移至 /user/local/ASR。
3. 執行下列命令以將行動服務解除安裝：

   ```
   uninstall.sh -Y
   ```

### <a name="step-4-restart-the-vm"></a>步驟 4：重新啟動 VM

解除安裝行動服務之後，先重新啟動 VM，再設定複寫到另一個 Azure 區域的作業。


## <a name="next-steps"></a>後續步驟
- [複寫 Azure 虛擬機器](../azure-to-azure-quickstart.md)來開始保護您的工作負載。
- 深入了解[複寫 Azure 虛擬機器的網路指引](../site-recovery-azure-to-azure-networking-guidance.md)。
