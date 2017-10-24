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
ms.openlocfilehash: 77bc7e068629c5dbf650b39844912b1b7a6155b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="delete-a-site-recovery-vault"></a>刪除 Site Recovery 保存庫
相依性會阻擋您刪除 Azure Site Recovery 保存庫。 您需要採取的動作隨 Site Recovery 案例而異：VMware 到 Azure、HYPER-V (附與不附 System Center Virtual Machine Manager) 到 Azure，以及 Azure 備份。 若要刪除 Azure 備份使用的保存庫，請參閱[刪除 Azure 中的備份保存庫](../backup/backup-azure-delete-vault.md)。



## <a name="delete-a-site-recovery-vault"></a>刪除 Site Recovery 保存庫 
請依照針對您案例的建議步驟刪除保存庫。

### <a name="vmware-vms-to-azure"></a>VMware VM 至 Azure

1. 依照[停用 VMware 保護](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)中的步驟刪除所有受保護的 VM。

2. 依照[刪除複寫原則](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)。中的步驟刪除所有複寫原則。

3. 依照[刪除 vCenter](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery) 中的步驟刪除 vCenter 的參考。

4. 依照[解除委任的組態伺服器](site-recovery-vmware-to-azure-manage-configuration-server.md##delete-or-unregister-a-configuration-server)中的步驟刪除組態伺服器。

5. 刪除保存庫。


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>Hyper-V VM (附 Virtual Machine Manager) 到 Azure
1. 依照[使用 System Center VMM 至 Azure 案例停用複寫至 Azure 之 HYPER-V 虛擬機器的保護](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)中的步驟，刪除所有受保護的 VM。

2. 瀏覽至您的 [保存庫] -> [Site Recovery 基礎結構] -> [System Center VMM] -> [複寫原則]，取消關聯並刪除所有複寫原則

3.  依照[取消登錄已連線的 VMM 伺服器](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server)中的步驟刪除 Virtual Machine Manager 伺服器的參考。

4.  刪除保存庫。

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Hyper-V VM (不附 Virtual Machine Manager) 到 Azure
1. 依照[停用 Hyper-V 虛擬機器 (Hyper-V 至 Azure) 的保護](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure)中的步驟，刪除所有受保護的 VM。

2. 瀏覽至您的 [保存庫] -> [Site Recovery 基礎結構] -> [Hyper-V 網站] -> [複寫原則]，取消關聯並刪除所有複寫原則

3. 依照[取消登錄 HYPER-V 主機](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site)中的步驟來刪除 HYPER-V 伺服器的參考。

4. 刪除 Hyper-V 站台。

5. 刪除保存庫。


## <a name="use-powershell-to-force-delete-the-vault"></a>使用 PowerShell 強制刪除保存庫 

> [!Important]
> 如果您要測試產品，且不在乎資料遺失問題，可使用強制刪除方法快速移除保存庫及其所有相依性。
> PowerShell 命令會刪除保存庫的所有內容，而且此步驟**無法復原**。

即使有受保護的項目也要刪除 Site Recovery 保存庫，請使用這些命令：

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault