---
title: "使用 Azure 備份來備份和還原已加密的 VM"
description: "本文討論使用「Azure 磁碟加密」加密之 VM 的備份與還原經驗。"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: pajosh;markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 3c0c13aebbf325bc1f03a39a456c8eef1551c64a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="how-to-back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>如何使用 Azure 備份來備份及還原加密的虛擬機器
本文討論使用 Azure 備份來備份和還原虛擬機器的步驟。 它也提供有關支援的案例、必要條件的詳細資料，以及的錯誤案例的疑難排解步驟。

## <a name="supported-scenarios"></a>支援的案例

 * 只有 Resource Manager 部署的虛擬機器支援備份及還原加密的 VM。 傳統虛擬機器不提供支援。 <br>
 * 使用 Azure 磁碟加密的 Windows 和 Linux 虛擬機器均可提供支援，Azure 磁碟加密會使用業界標準的 Windows BitLocker 功能與 Linux 的 DM-Crypt 功能來提供磁碟加密。 <br>
 * 下表擷取僅適用於 BitLocker 加密金鑰 (BEK) 的受支援案例，以及金鑰加密金鑰 (KEK) 加密 VM。
 
 
   |  | BEK + KEK VM | BEK 僅 VM |
   | --- | --- | --- |
   | **未受控 VM**  | 是 | 是  |
   | **受控 VM**  | 是 | 否  |

## <a name="prerequisites"></a>必要條件
1. 虛擬機器已使用 [Azure 磁碟加密](../security/azure-security-disk-encryption.md)進行加密。 
2. 已建立復原服務保存庫，並使用[準備環境以便備份](backup-azure-arm-vms-prepare.md)一文所述的步驟設定儲存體複寫。
3. Azure 備份已獲得[存取金鑰保存庫 (內含已加密 VM 的金鑰、祕密) 的權限](#provide-permissions-to-azure-backup)。

## <a name="backup-encrypted-vm"></a>備份加密的 VM
使用下列步驟來設定備份目標、定義原則、設定項目和觸發備份。

### <a name="configure-backup"></a>設定備份
1. 如果您已開啟復原服務保存庫，請繼續下一個步驟。 如果您並未開啟復原服務保存庫，但位於 Azure 入口網站中，請在 [中樞] 功能表上按一下 [瀏覽] 。

   * 在資源清單中輸入 **復原服務**。
   * 當您開始輸入時，清單會根據您輸入的文字進行篩選。 當您看到 [復原服務保存庫] 時，請按一下它。

      ![建立復原服務保存庫的步驟 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     隨即會出現 [復原服務保存庫] 清單。 在 [復原服務保存庫] 清單中選取保存庫。

     選取的保存庫儀表板隨即開啟。
2. 從出現在保存庫下方的項目清單中，按一下 [備份] 以開始備份加密 VM。

      ![開啟 [備份] 刀鋒視窗](./media/backup-azure-vms-encryption/select-backup.png)
3. 做為選取備份目標的第一步，請按一下 [備份目標] 。

      ![開啟 [案例] 刀鋒視窗](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. 在選取備份目標的第一步中，在 [備份目標] 刀鋒視窗中，將 [工作負載的執行位置] 設定為 [Azure]，並將 [欲備份的項目] 設定為 [虛擬機器]，然後按一下 [確定]。

   然後就是選取後備原則的第二步。

   ![開啟 [案例] 刀鋒視窗](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. 在選取原則的第二步中，選取您要套用至保存庫的備份原則，然後按一下 [確定] 。

      ![選取備份原則](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    預設原則的詳細資料便會列在詳細資料中。 如果您想要建立原則，請在下拉式功能表中選取 [建立新的]  。 一旦您按下 [確定] ，備份原則便會與保存庫建立關聯。

    接下來選擇要與保存庫建立關聯的 VM。
6. 選擇要與指定的原則建立關聯的已加密虛擬機器，然後按一下 [確定]。

      ![選取加密的 VM](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. 此頁面會顯示與所選加密的 VM 相關聯的金鑰保存庫相關訊息。 備份服務需要金鑰保存庫中金鑰和密碼的唯讀存取權。 它會使用這些權限來備份金鑰和密碼，以及相關聯的 VM。 **您必須將金鑰保存庫的存取權限授與備份服務才能進行備份**。 您可以使用[下一節所述的步驟](#provide-permissions-to-azure-backup)來提供這些權限。

      ![加密的 VM 訊息](./media/backup-azure-vms-encryption/encrypted-vm-warning-message.png)

      現在您已定義保存庫的所有設定，接下來按一下頁面底部的 [啟用備份]。 [啟用備份] 可將原則部署到保存庫和 VM。
8. 下一個階段的準備作業是安裝 VM 代理程式，或確定 VM 代理程式已安裝。 若要執行相同的動作，請使用[準備環境以便備份](backup-azure-arm-vms-prepare.md)一文中所述的步驟。

### <a name="triggering-backup-job"></a>觸發備份作業
使用[將 Azure VM 備份至復原服務保存庫](backup-azure-arm-vms.md)一文中所述的步驟來觸發備份作業。

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>將啟用加密之已備份 VM 繼續備份  
如果您的 VM 已在復原服務保存庫中進行備份，並於稍後已啟用加密，您必須將備份服務的權限提供給金鑰保存庫，才可繼續進行備份。 您可以使用[下一節的步驟](#provide-permissions-to-azure-backup)或使用 [PowerShell 文件](backup-azure-vms-automation.md)的**啟用備份**一節中所述的 PowerShell 步驟，來提供這些權限。 

## <a name="provide-permissions-to-azure-backup"></a>對 Azure 備份提供權限
使用下列步驟來提供相關權限給 Azure 備份，以供其存取金鑰保存庫並執行已加密 VM 的備份：
1. 選取 [更多服務]，然後搜尋 [金鑰保存庫]。

    ![搜尋金鑰保存庫](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. 從金鑰保存庫清單中選取與已加密之 VM 相關聯的金鑰保存庫，以讓其進行備份。

     ![選取金鑰保存庫](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. 按一下 [存取原則] 然後 [新增]。

    ![新增存取原則](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. 按一下 [選取主體]，然後在搜尋列中輸入**備份管理服務**。 

    ![搜尋備份服務](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. 選取 [備份管理服務]，然後按一下 [選取] 按鈕。

    ![選取備份服務](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. 在 [從範本設定] 下拉式功能表中選取 [Azure 備份]。 該備份會在 [金鑰權限和祕密權限] 下拉式清單中預先填入必要的權限。 如果您是使用**僅 BE** 加密您的 VM，則必需要機密資料的權限，因此您必須移除金鑰權限的選項項目。

    ![選取 Azure 備份](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. 按一下 [確定] 。 請注意「備份管理服務」會新增到 [存取原則] 中。 

    ![備份服務存取原則](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. 按一下 [儲存] 。 這會對 Azure 備份提供必要權限。

    ![備份服務存取原則](./media/backup-azure-vms-encryption/save-access-policy.png)

順利提供權限後，您可以繼續為已加密的 VM 啟用備份。

## <a name="restore-encrypted-vm"></a>還原已加密的 VM
若要還原加密的 VM，請先使用[選擇 VM 還原組態](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration)的＜還原備份的磁碟＞一節中所述的步驟來還原磁碟。 之後，您可以使用下列其中一個選項：
* 使用[從還原的磁碟建立 VM](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 中所述的 PowerShell 步驟，從還原的磁碟建立完整的 VM。
* 或者，[使用產生作為還原磁碟一部分的範本](backup-azure-arm-restore-vms.md#use-templates-to-customize-restore-vm)，從還原的磁碟建立 VM。 只可將範本用於 2017 年 4 月 26 日之後建立的復原點。

## <a name="troubleshooting-errors"></a>錯誤疑難排解
| 作業 | 錯誤詳細資料 | 解決方案 |
| --- | --- | --- |
|備份 | Azure 備份服務沒有足夠的 Key Vault權限可備份加密的虛擬機器 | 虛擬機器應該使用「BitLocker 加密金鑰」和「金鑰加密金鑰」進行加密。 之後，應該啟用備份。  您應該使用[上一節所述步驟](#provide-permissions-to-azure-backup)或[使用 AzureRM.RecoveryServices.Backup Cmdlet 備份虛擬機器](backup-azure-vms-automation.md#back-up-azure-vms)的 PowerShell 文件中的**啟用保護**一節所提到的 PowerShell 步驟，為備份服務提供這些權限。 |  
| 備份 |驗證失敗，因為虛擬機器單獨以 BEK 進行加密。 只會對同時使用 BEK 和 KEK 加密的虛擬機器啟用備份。 |應該使用 BEK 和 KEK 來加密虛擬機器。 先解密 VM，再使用 BEK 和 KEK 將它加密。 使用 BEK 和 KEK 加密 VM 之後，啟用備份。 了解如何[解密及加密 VM](../security/azure-security-disk-encryption.md)  |
| 還原 |您無法還原這部已加密的 VM，因為與此 VM 相關聯的金鑰保存庫不存在。 |利用[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)，建立金鑰保存庫。 請參閱[使用 Azure 備份來還原金鑰保存庫金鑰和密碼](backup-azure-restore-key-secret.md)文章來還原金鑰和密碼 (如果不存在)。 |
| 還原 |您無法還原這部已加密的 VM，因為與此 VM 相關聯的金鑰和密碼不存在。 |請參閱[使用 Azure 備份來還原金鑰保存庫金鑰和密碼](backup-azure-restore-key-secret.md)文章來還原金鑰和密碼 (如果不存在)。 |
| 還原 |備份服務無權存取您訂用帳戶中的資源。 |如前所述，請先使用[選擇 VM 還原組態](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration)的＜還原備份的磁碟＞一節中所述的步驟來還原磁碟。 之後，使用 PowerShell 來[從還原的磁碟建立 VM](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。 |


