---
title: "將 Site Recovery 保存庫升級成 Microsoft Azure 復原服務保存庫"
description: "了解如何將 Azure Site Recovery 保存庫升級成 Microsoft Azure 復原服務保存庫"
ddocumentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 523cab85b195d85007bd85c45dbe3645f7a00ab1
ms.contentlocale: zh-tw
ms.lasthandoff: 08/10/2017

---
# <a name="upgrade-site-recovery-vaults-to-azure-resource-manager-based-recovery-services-vaults"></a>將 Site Recovery 保存庫升級成以 Azure Resource Manager 為基礎的 Microsoft Azure 復原服務保存庫

本文說明如何在不影響進行中複寫的情況下，將「Site Recovery 保存庫」升級成以 Azure Resource Manager 為基礎的「Microsoft Azure 復原服務保存庫」。 您可以在[這裡](../azure-resource-manager/resource-group-overview.md)進一步了解 Azure Resource Manager 的功能和優點。

## <a name="introduction"></a>簡介
Microsoft Azure 復原服務保存庫屬於 Azure Resource Manager 資源，可在雲端上以原生方式管理您的備份與災害復原需求。 此為整合保存庫，可在新的 Azure 入口網站內使用，取代基本的備份與 Site Recovery 保存庫。

Microsoft Azure 復原服務保存庫開放一系列的功能，包括：

-   支援 Azure Resource Manager：可以保護您的虛擬機器與實體機器，並將這些機器容錯移轉至 Azure Resource Manager 堆疊。

-   排除磁碟：如果您有一些暫存檔案或高變換資料，但不想要它們用盡頻寬，則可以將磁碟區從複寫中排除。 這項功能目前已在「VMware 到 Azure」與「Hyper-V 到 Azure」中啟用，即將擴展到其他情節。

- 支援進階與本地備援儲存體 (LRS)：現在可以讓伺服器進入進階儲存體帳戶加以保護，該帳戶允許客戶以更高的 IOP 保護應用程式。 這項功能目前已在「VMware 到 Azure」中啟用。

-   簡化「使用者入門」體驗：增強的「使用者入門」體驗針對使用者量身打造，確保能輕而易舉地設定災害復原。

- 從相同保存庫管理備份與 Site Recovery：現在可以從同一個保存庫保護伺服器，以進行災害復原或進行備份，大幅減少您的管理負荷。

如需有關升級體驗與功能的詳細資料，請參閱此[部落格](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/)。

## <a name="salient-features"></a>主要功能

- **不影響進行中的複寫**：進行中的複寫在升級期間與升級後會繼續進行，完全不會中斷。

- **不需額外的花費**：無須額外費用即可體驗整組新功能

- **不會遺失資料**：由於是升級而非移轉，現有的複寫資訊 (復原點、複寫設定等) 在升級期間與升級後會維持不變。


## <a name="what-happens-during-the-upgrade"></a>升級期間會發生什麼情況？

升級期間不允許進行註冊新伺服器、啟用虛擬機器複寫等作業。 任何涉及從保存庫讀取或寫入資料的作業 (例如正在將受保護項目複寫到保存庫) 仍會繼續進行而不中斷。

## <a name="changes-to-your-automation-and-tooling-after-vault-upgrade"></a>保存庫升級後自動化與工具的變更
將保存庫類型從「傳統」部署模型升級成 Resource Manager 部署模型的過程中，您必須更新現有的自動化功能或工具，以確保它在移轉之後仍可繼續運作。

## <a name="preparing-your-environment-for-vault-upgrade"></a>升級保存庫的環境準備

1.  請使用此[連結](https://www.microsoft.com/download/details.aspx?id=50395)將 PowerShell 安裝/升級至第 5 版

2.  從[這裡](https://github.com/Azure/azure-powershell/releases)安裝最新的 Azure PowerShell MSI 版本

3.  [下載](https://aka.ms/vaultupgradescript)保存庫升級指令碼

## <a name="prerequisites-for-upgrade"></a>升級的必要條件
若要將保存庫從 Site Recovery 保存庫升級成以 Azure Resource Manager 為基礎的 Microsoft Azure 復原服務保存庫，必須符合下列必要條件。

- 最低代理程式版本：伺服器上安裝的 Azure Site Recovery Provider 版本至少必須為 5.1.1700.0，方能升級。

- 支援的設定：不應將保存庫設定成具有 SAN、SQL Always Availability 群組。 所有其他的設定則可支援。

>[!NOTE]
> 升級後只能透過 PowerShell 管理儲存體對應。

- 支援的部署情節：您的保存庫不應該在「VMware 到 Azure」舊有部署模型上。  必須先移至增強的部署模型，才能繼續進行。

- 由使用者啟動的作用中作業皆未涉及管理平面作業：由於在升級期間會限制對管理平面的存取，因此您必須先完成所有的管理平面動作，再觸發升級。 進行中的複寫則不包含在內。

## <a name="frequently-asked-questions"></a>常見問題集

- 升級是否會影響進行中的複寫？

  否。 進行中的複寫在升級期間與升級後會繼續進行，不會中斷。

- 站對站 VPN、IP 設定等網路設定會發生什麼狀況？

  升級不會影響網路設定，所有 Azure 到內部部署的連線都會維持不變。
- 如果近期內不打算升級，我的保存庫會怎麼樣？

  自 9 月起，舊 Azure 入口網站的 Site Recovery 保存庫支援將被取代。 因此強烈建議客戶使用升級功能移至新的入口網站。

- 對於我現有的工具來說，此移轉計劃有何意義？  

  將您的工具更新為 Microsoft Azure 復原服務保存庫作為基礎的 Resource Manager 部署模型，是您在升級計畫中必須說明的其中一項最重要的變更。

- 管理平面的停機時間會持續多久？

  升級需要 15-30 分鐘的時間。 最多可能需要一個小時。

- 升級之後可以復原嗎？

  否。 將資源成功升級之後，即不支援復原。

- 我是否可以驗證訂用帳戶或資源，以查看是否能夠將它們升級？

  是。 在平台支援的升級選項中，升級的第一個步驟就是驗證資源是否能夠升級。 如果必要條件驗證失敗，您會收到正確的錯誤訊息/警告。

- 如何回報升級問題？

  如果您在升級過程中遇到任何失敗，請記下錯誤訊息中所列的 OperationId。 Microsoft 支援服務會主動解決問題。 您也可以與支援小組聯絡，並提供您的訂用帳戶識別碼、保存庫名稱，以及作業識別碼。 我們將努力儘早將問題解決。 除非 Microsoft 明確指示，否則請勿重試作業。

## <a name="how-to-run-the-script"></a>如何執行指令碼？

請在 PowerShell 提示字元中執行下列命令：

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

- SubscriptionId：與升級中保存庫聯結的訂用帳戶識別碼
- VaultName：升級中保存庫的名稱
- Locati：升級中保存庫的位置
- ResourceType：Site Recovery 保存庫為 HyperVRecoveryManagerVault
- TargetResourceGroupName：要放置升級後保存庫的資源群組。 TargetResourceGroupName 可為 Azure Resource Manager 中現有的 ResourceGroup，或是新的 ResourceGroup。 如果提供的 TargetResourceGroupName 不存在，則升級過程中會在與保存庫相同的位置建立一個。 若要深入了解資源群組，請按一下[這裡](../azure-resource-manager/resource-group-overview.md#resource-groups)：

>[!NOTE]
>資源群組名稱有限制。 請沿用原來的名稱，否則可能會導致保存庫升級失敗。

範例：

    .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc


您也可以執行以下指令碼，系統會要求您輸入所有必要的參數。

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1.  PowerShell 指令碼會提示您輸入認證。 您必須輸入認證兩次︰一次為 ASM 帳戶，另一次為 Azure Resource Manager 帳戶。

2.  輸入您的認證後，指令碼便會執行必要條件檢查，以判斷您的基礎結構設定是否符合前文所述的必要條件。

3.  檢查必要條件後，系統會提示您進行確認，才能繼續升級保存庫。 確認後，升級流程會開始升級您的保存庫。 整個升級需要 15-30 分鐘才能完成。

4.  一旦成功完成升級，便可在新的 Azure 入口網站中存取升級後的保存庫。

## <a name="management-experience-post-upgrade"></a>升級後的管理體驗

### <a name="how-to-replicate-using-azure-site-recovery-in-the-recovery-services-vault"></a>如何在 Microsoft Azure 復原服務保存庫中使用 Azure Site Recovery 進行複寫

- 現在可在各個 Azure 區域之間保護您的 Azure 虛擬機器。 若要深入了解，請參閱[這裡](site-recovery-azure-to-azure.md)的文件。

- 若要深入了解如何將 VMware 虛擬機器複寫至 Azure，請參閱[這裡](vmware-walkthrough-overview.md)的文件。

- 若要深入了解如何將 Hyper-VM (無 VMM) 複寫至 Azure，請參閱[這裡](hyper-v-site-walkthrough-overview.md)的文件。

- 若要深入了解如何將 Hyper-VM (含 VMM) 複寫至 Azure，請參閱[這裡](vmm-to-azure-walkthrough-overview.md)的文件。

- 若要深入了解如何將 Hyper-VM (含 VMM) 複寫至次要站台，請參閱[這裡](site-recovery-vmm-to-vmm.md)的文件。

- 若要深入了解如何將 VMware 虛擬機器複寫至次要站台，請參閱[這裡](site-recovery-vmware-to-vmware.md)的文件。

### <a name="how-to-view-your-replicated-items"></a>如何檢視複寫的項目

以下畫面為 Microsoft Azure 復原服務保存庫儀表板頁面，顯示了保存庫的金鑰實體。 按一下 [站台復原]  ->  [複寫的項目] 可檢視保存庫中受保護實體的清單。


![複寫的項目](./media/upgrade-site-recovery-vaults/replicateditems.png)

以下畫面顯示的是檢視複寫項目的工作流程，以及如何啟動容錯移轉。

![複寫的項目](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="how-to-view-your-replication-settings"></a>如何檢視複寫設定

在 Site Recovery 保存庫中，每個保護群組皆有設定複寫設定 (複製頻率、復原點保留期、應用程式一致的快照頻率等)。 在 Microsoft Azure 復原服務保存庫中，這些設定會設成複寫原則。 原則的名稱即為保護群組的名稱或 ‘primarycloud_Policy’。

若要深入了解複寫原則，請參閱[這裡](site-recovery-setup-replication-settings-vmware.md)

