---
title: "將 Site Recovery 保存庫升級成 Azure 復原服務保存庫"
description: "了解如何將 Azure Site Recovery 保存庫升級成 Microsoft Azure 復原服務保存庫"
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
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>將 Site Recovery 保存庫升級成以 Azure Resource Manager 為基礎的復原服務保存庫

本文說明如何在不影響進行中複寫的情況下，將 Azure Site Recovery 保存庫升級成以 Azure Resource Manager 為基礎的復原服務保存庫。 如需 Azure Resource Manager 功能和優點的詳細資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。

## <a name="introduction"></a>簡介
復原服務保存庫是一項 Azure Resource Manager 資源，可用來在雲端上以原生方式管理備份與災害復原。 此為整合保存庫，可供您在新的 Azure 入口網站內使用，並可取代傳統的備份與 Site Recovery 保存庫。

復原服務保存庫提供了一系列的功能，包括：

* 支援 Azure Resource Manager：可以保護您的虛擬機器與實體機器，並將這些機器容錯移轉至 Azure Resource Manager 堆疊。

* 排除磁碟：如果您有一些暫存檔案或高變換資料，但不想要讓它們用盡所有頻寬，則可以將磁碟區從複寫中排除。 這項功能目前已在「VMware 到 Azure」與「Hyper-V 到 Azure」中啟用，而且也已經擴展到其他案例。

* 支援進階與本地備援儲存體：現在可以讓伺服器進入進階儲存體帳戶加以保護，該帳戶允許客戶以更高的每秒輸入/輸出作業數 (IOPS) 保護應用程式。 這項功能目前已在「VMware 到 Azure」中啟用。

* 簡化使用者入門體驗：增強的使用者入門體驗是為了讓使用者能夠輕鬆地設定災害復原而設計。

* 從相同保存庫管理備份與 Site Recovery：現在可以從同一個保存庫保護伺服器，以進行災害復原或進行備份，大幅減少您的管理負荷。

如需升級後之體驗與功能的詳細資訊，請參閱[儲存、備份與復原部落格](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/)。

## <a name="salient-features"></a>主要功能

* 不影響進行中的複寫：進行中的複寫在升級期間與升級後會繼續進行，完全不會中斷。

* 不必另外付費：不必另外付費即可獲得一整組更新後的功能。

* 不會遺失資料：由於此程序是升級而非移轉，現有複寫的復原點和設定在升級期間與升級後會維持不變。


## <a name="what-happens-during-the-vault-upgrade"></a>保存庫升級期間會發生什麼情況？

在升級期間，您將無法進行註冊新伺服器或為虛擬機器 (VM) 啟用複寫等作業。 涉及在保存庫中讀取或寫入資料的作業 (例如正在將受保護項目複寫到保存庫) 仍會繼續進行而不中斷。

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>升級之後對自動化和工具的變更
在將保存庫類型從傳統部署模型升級成 Resource Manager 部署模型時，請更新現有的自動化功能或工具，以確保它在移轉之後仍可繼續運作。

### <a name="prepare-your-environment-for-the-upgrade"></a>讓環境進行升級準備

* [安裝 PowerShell 或將它升級為第 5 版或更新版本](https://www.microsoft.com/download/details.aspx?id=50395)
* [安裝最新版的 Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases)
* [下載復原服務保存庫升級指令碼](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>必要條件
若要從 Site Recovery 保存庫升級成以 Azure Resource Manager 為基礎的復原服務保存庫，您必須符合下列需求：

* 最低代理程式版本：伺服器上安裝的 Azure Site Recovery Provider 版本必須為 5.1.1700.0 或更新版本。

* 支援的設定：您無法使用存放區域網路 (SAN) 或 SQL Server AlwaysOn 可用性群組來設定保存庫。 所有其他的設定則可支援。

    >[!NOTE]
    >升級之後，您就只能透過 PowerShell 來管理儲存體對應。

* 支援的部署案例：您的保存庫不應該是「VMware 到 Azure」舊有部署模型。 在繼續之前，請先改為增強部署模型。

* 由使用者啟動的作用中作業皆未涉及管理平面作業：由於在升級期間會限制對管理平面的存取，因此請先完成所有的管理平面動作，再觸發升級。 此程序不包含進行中的複寫。

## <a name="frequently-asked-questions"></a>常見問題集

**升級是否會影響進行中的複寫？**

否。 進行中的複寫在升級期間與升級後會繼續進行，不會中斷。

**站對站 VPN 和 IP 設定等網路設定會發生什麼狀況？**

升級不會影響網路設定。 Azure 對內部部署的所有連線都會保持不變。

**如果近期內不打算升級，我的保存庫會怎麼樣？**

自 2017 年 9 月起，舊 Azure 入口網站的 Site Recovery 保存庫支援將會淘汰。 我們強烈建議您使用升級功能來改用新的入口網站。

**對於我現有的工具來說，此移轉計劃有何意義？**  

將您的工具更新為 Resource Manager 部署模型，是您在升級計畫中必須說明的最重要變更之一。 復原服務保存庫是以 Resource Manager 部署模型作為基礎。 

**管理平面的停機時間會持續多久？**

升級通常需要約 15 到 30 分鐘，最多則可能需要一個小時。

**升級之後可以復原嗎？**

否。 將資源成功升級之後，即不支援復原。

**我是否可以驗證訂用帳戶或資源，以查看是否能夠將它們升級？**

是。 在平台支援的升級選項中，升級的第一個步驟就是驗證資源是否能夠升級。 如果驗證失敗，您會收到適當的錯誤訊息或警告。

**如何回報升級問題？**

如果您在升級期間發生任何失敗，請記下錯誤所列出的作業識別碼。 Microsoft 支援服務會主動解決問題。 您也可以連絡支援小組，並提供您的訂用帳戶識別碼、保存庫名稱，以及作業識別碼。 支援人員會想辦法盡快解決問題。 除非 Microsoft 明確指示，否則請勿重試作業。

## <a name="run-the-script"></a>執行指令碼

在 PowerShell 中執行下列命令：

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* SubscriptionID：與您要升級之保存庫相關聯的訂用帳戶識別碼。

* VaultName：您要升級之保存庫的名稱。

* Location：您要升級之保存庫的所在位置。

* ResourceType：Site Recovery 保存庫的 HyperVRecoveryManagerVault。

* TargetResourceGroupName：要放置升級後保存庫的資源群組。 TargetResourceGroupName 可為 Azure Resource Manager 中現有的資源群組，或是新的資源群組。 如果提供的 TargetResourceGroupName 不存在，則升級過程中會在與保存庫相同的位置建立一個。 如需詳細資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md#resource-groups)的＜資源群組＞一節。

    >[!NOTE]
    >資源群組的命名受限於某些條件約束。 若要防止保存庫升級失敗，請務必仔細遵守命名慣例。
    >
    >例如：
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc

或者，您也可以執行下列指令碼。 為必要參數輸入值。

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. PowerShell 指令碼會提示您輸入認證。 請輸入認證兩次，一次用於傳統部署模型帳戶，一次用於 Azure Resource Manager 帳戶。

2. 在輸入認證之後，指令碼會執行檢查，以判斷您的基礎結構設定是否符合先前所述的需求。

3. 必要條件經過檢查並確認之後，系統會提示您繼續進行保存庫升級。 升級流程會開始升級您的保存庫。 整個升級需要 15 到 30 分鐘才能完成。

4. 成功完成升級後，您便可以在新的 Azure 入口網站中存取升級後的保存庫。

## <a name="post-upgrade-vault-management"></a>升級後保存庫的管理

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>在復原服務保存庫中使用 Azure Site Recovery 來進行複寫

* 現在可在各個 Azure 區域之間保護您的 Azure 虛擬機器。 如需詳細資訊，請參閱[使用 Azure Site Recovery 在區域之間椱寫 Azure VM](site-recovery-azure-to-azure.md)。

* 如需將 VMware VM 複寫至 Azure 的詳細資訊，請參閱[使用 Site Recovery 將 VMWare VM 複寫至 Azure](vmware-walkthrough-overview.md)。

* 如需將 Hyper-V VM (不含 VMM) 複寫至 Azure 的詳細資訊，請參閱[將 Hyper-V 虛擬機器 (不含 VMM) 複寫至 Azure](hyper-v-site-walkthrough-overview.md)。

* 如需將 Hyper-V VM (含 VMM) 複寫至 Azure 的詳細資訊，請參閱[使用 Azure 入口網站中的 Site Recovery 將 Hyper-V 虛擬機器 (位於 VMM 雲端中) 複寫至 Azure](vmm-to-azure-walkthrough-overview.md)。

* 如需將 Hyper-V VM (含 VMM) 複寫至次要站台的詳細資訊，請參閱[使用 Azure 入口網站將位於 VMM 雲端中的 Hyper-V 虛擬機器複寫至次要 VMM 站台](site-recovery-vmm-to-vmm.md)。

* 如需將 VMware VM 複寫至次要站台的詳細資訊，請參閱[在傳統 Azure 入口網站中，將內部部署 VMware 虛擬機器或實體伺服器複寫到次要站台](site-recovery-vmware-to-vmware.md)。

### <a name="view-your-replicated-items"></a>檢視複寫的項目

下圖為復原服務保存庫儀表板頁面，其中顯示了保存庫的金鑰實體。 若要檢視保存庫中受保護實體的清單，請選取 [Site Recovery] > [複寫的項目]。


![複寫的項目](./media/upgrade-site-recovery-vaults/replicateditems.png)

下圖顯示用來檢視複寫項目的的工作流程和用來起始容錯移轉的**容錯移轉**命令。

![複寫的項目](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>檢視複寫設定

在 Site Recovery 保存庫中，每個保護群組皆有設定複製頻率、復原點保留期、應用程式一致的快照頻率，以及其他複寫設定。 在復原服務保存庫中，這些設定會設成複寫原則。 原則的名稱即為保護群組的名稱，也就是 primarycloud_Policy。

如需複寫原則的詳細資訊，請參閱[管理 VMware 至 Azure 的複寫原則](site-recovery-setup-replication-settings-vmware.md)。
