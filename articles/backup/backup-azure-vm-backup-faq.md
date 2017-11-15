---
title: "Azure VM 備份常見問題集 | Microsoft Docs"
description: "有關以下常見問題的解答：Azure VM 備份的運作方式、限制，以及原則變更時會發生什麼情況"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "azure vm 備份, azure vm 還原, 備份原則"
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: 85d6ec20fb0447165c672ba267569994e3a96e45
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Azure VM 備份服務的相關問題
本文包含常見問題的解答，可協助您快速了解 Azure VM 備份元件。 在某些答案中，有具有完整資訊的文章連結。 您也可以在 [論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中張貼有關 Azure 備份服務的問題。

## <a name="configure-backup"></a>設定備份
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>復原服務保存庫是否支援傳統 VM 或以 Resource Manager 為基礎的 VM？ <br/>
復原服務保存庫支援兩種模式。  您可以將傳統 VM (建立於傳統入口網站中) 或 Resource Manager VM (建立於 Azure 入口網站中) 備份至復原服務保存庫。

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Azure VM 備份不支援哪些組態？
請瀏覽[支援的作業系統](backup-azure-arm-vms-prepare.md#supported-operating-system-for-backup)和 [VM 備份的限制](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)。

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>在設定備份精靈中為何看不到我的 VM？
在「設定備份精靈」中，Azure 備份只會列出這種 VM：
  * 尚未保護 - 移至 VM 刀鋒視窗，並從 [設定] 功能表檢查備份狀態，即可驗證 VM 的備份狀態。 進一步了解如何[檢查 VM 的備份狀態](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade)
  * 屬於與 VM 相同的區域

## <a name="backup"></a>備份
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>隨選備份作業是否會遵循與排定備份相同的保留排程？
否。 您應該指定隨選備份作業的保留範圍。 根據預設，若從入口網站觸發，會保留 30 天。 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>我在最近一些 VM 上啟用了 Azure 磁碟加密。 我的備份是否會繼續運作？
您需要提供 Azure 備份服務存取 Key Vault 的權限。 您可以使用 [PowerShell](backup-azure-vms-automation.md) 文件的「啟用備份」一節中所述的步驟，在 PowerShell 中提供這些權限。

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>我已將 VM 的磁碟移轉到受控磁碟。 我的備份是否會繼續運作？
是，備份會順暢運作，而且不需要重新設定備份。 

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>我的 VM 關閉了。 隨選或排程的備份工作會運作嗎？
是。 即使機器已經關閉，且備份工作和復原點標示為「當機時保持一致」。 如需詳細資訊，請參閱[這篇文章](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines)的＜資料一致性＞。

### <a name="can-i-cancel-an-in-progress-backup-job"></a>我是否可以取消進行中的備份作業？
是。 如果備份作業處於「正在建立快照」階段，您可以將它取消。 **如果正在從快照傳輸資料，則無法取消作業**。 

## <a name="restore"></a>還原
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>如何在還原磁碟與完整 VM 還原之間做決定？
將 Azure VM 完整還原視為快速建立選項。 還原 VM 選項會變更磁碟的名稱、這些磁碟使用的容器、公用 IP 位址和網路介面名稱。 在 VM 建立期間，需要這些變更來維持建立之資源的唯一性。 但變更不會將 VM 加入可用性設定組。 

使用還原磁碟：
  * 自訂從時間點組態建立的 VM，例如變更大小
  * 新增備份時不存在的組態 
  * 控制建立資源時的命名慣例
  * 將 VM 新增至可用性設定組
  * 任何其他只使用 PowerShell/宣告式範本定義即可達成的組態
  
### <a name="can-i-use-backups-of-unmanaged-disk-vm-to-restore-after-i-upgrade-my-disks-to-managed-disks"></a>我的磁碟在升級為受控磁碟後，是否可以使用非受控磁碟 VM 的備份來進行還原？
是，您可以使用在將磁碟從非受控磁碟移轉為受控磁碟之前所擷取的備份。 根據預設，還原 VM 作業將會使用非受控磁碟來建立 VM。 您可以使用還原磁碟功能來還原磁碟，並用這些磁碟在受控磁碟上r建立 VM。 

## <a name="manage-vm-backups"></a>管理 VM 備份
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>變更 VM 的備份原則時會發生什麼狀況？
在 VM 上套用新原則後，就會遵循新原則的排程和保留期。 如果延長保留期，會標示現有的復原點，以依據新的原則加以保留。 如果縮短保留期，則會標示現有的復原點，以便在下次清除作業中剪除然後刪除。 
