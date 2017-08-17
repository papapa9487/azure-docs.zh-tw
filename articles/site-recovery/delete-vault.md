---
title: "刪除 Site Recovery 保存庫"
description: "瞭解如何根據 Site Recovery 案例刪除 Azure Site Recovery 保存庫。"
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: b95b9defa0a037f7d7d3ef36b99bc7c53c751050
ms.contentlocale: zh-tw
ms.lasthandoff: 07/21/2017

---
# <a name="delete-a-site-recovery-vault"></a>刪除 Site Recovery 保存庫
相依性會阻擋您刪除 Azure Site Recovery 保存庫。 您需要採取的動作隨 Site Recovery 案例而異：VMware 到 Azure、HYPER-V (附與不附 System Center Virtual Machine Manager) 到 Azure，以及 Azure 備份。 若要刪除 Azure 備份使用的保存庫，請參閱[刪除 Azure 中的備份保存庫](../backup/backup-azure-delete-vault.md)。

>[!Important]
>如果您要測試產品，且不在乎資料遺失問題，可使用強制刪除方法快速移除保存庫及其所有相依性。

> PowerShell 命令會刪除保存庫的所有內容，而且此步驟無法復原。

## <a name="use-powershell-to-force-delete-the-vault"></a>使用 PowerShell 強制刪除保存庫 

即使有受保護的項目也要刪除 Site Recovery 保存庫，請使用這些命令：

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault


## <a name="delete-a-site-recovery-vault"></a>刪除 Site Recovery 保存庫 
請依照針對您案例的建議步驟刪除保存庫。

### <a name="vmware-vms-to-azure"></a>VMware VM 至 Azure

1. 依照[停用 VMware 保護](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)中的步驟刪除所有受保護的 VM。

2. 依照[刪除複寫原則](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)。中的步驟刪除所有複寫原則。

3. 依照[刪除 vCenter](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery) 中的步驟刪除 vCenter 的參考。

4. 依照[解除委任的組態伺服器](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server)中的步驟刪除組態伺服器。

5. 刪除保存庫。


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>Hyper-V VM (附 Virtual Machine Manager) 到 Azure
1. 依照[停用 VMware VM 或實體伺服器的保護](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)中的步驟刪除所有受保護的 VM。

2. 依照[刪除複寫原則](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)。中的步驟刪除所有複寫原則。

3.  依照[取消登錄已連線的 VMM 伺服器](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server)中的步驟刪除 Virtual Machine Manager 伺服器的參考。

4.  刪除保存庫。

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Hyper-V VM (不附 Virtual Machine Manager) 到 Azure
1. 依照[停用 VMware VM 或實體伺服器的保護](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)中的步驟刪除所有受保護的 VM。

2. 依照[刪除複寫原則](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)。中的步驟刪除所有複寫原則。

3. 依照[取消登錄 HYPER-V 主機](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site)中的步驟來刪除 HYPER-V 伺服器的參考。

4. 刪除 Hyper-V 站台。

5. 刪除保存庫。

