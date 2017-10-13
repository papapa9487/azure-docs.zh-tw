---
title: "如何從 Azure 容錯回復至 VMware | Microsoft Docs"
description: "將虛擬機器容錯移轉到 Azure 之後，您可以起始將虛擬機器容錯回復到內部部署的作業。 了解如何容錯回復的步驟。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.openlocfilehash: 1ca34b262a51b694cb9541750588bbea139eeae1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>從 Azure 容錯回復至內部部署網站

本文說明如何將虛擬機器從 Azure 虛擬機器容錯回復到內部部署網站。 請遵循本文中的指示，將已使用[使用 Azure Site Recovery 將 VMWare 虛擬機器和實體伺服器複寫至 Azure](site-recovery-vmware-to-azure-classic.md) 教學課程從內部部署網站容錯移轉至 Azure 的 VMware 虛擬機器或 Windows/Linux 實體伺服器進行容錯回復。

> [!WARNING]
> 如果您已[完成移轉](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration)、將虛擬機器移至另一個資源群組，或刪除 Azure 虛擬機器，則您無法在之後執行容錯回復。 如果您停用虛擬機器的保護，便無法進行容錯回復。

> [!NOTE]
> 如果您已對 VMware 虛擬機器進行容錯移轉，便無法容錯回復到 Hyper-v 主機。

## <a name="overview-of-failback"></a>容錯回復的概觀
容錯回復的運作方式如下。 在容錯移轉至 Azure 之後，您可以透過幾個階段來容錯回復至內部部署網站：

1. [重新保護](site-recovery-how-to-reprotect.md) Azure 上的虛擬機器，讓它們開始複寫到內部部署網站中的 VMware 虛擬機器。 在此程序進行期間，您還需要︰
    1. 設定內部部署主要目標︰Windows 主要目標 (若為 Windows 虛擬機器) 和 [Linux 主要目標](site-recovery-how-to-install-linux-master-target.md) (若為 Linux 虛擬機器)。
    2. 設定[處理序伺服器](site-recovery-vmware-setup-azure-ps-resource-manager.md)。
    3. 起始[重新保護](site-recovery-how-to-reprotect.md)。 這會關閉內部部署虛擬機器，並使 Azure 虛擬機器的資料與內部部署磁碟同步。
5. 在 Azure 上的虛擬機器複寫至內部部署網站後，您可以起始從 Azure 到內部部署網站的容錯回復。

容錯回復資料之後，您必須重新保護所容錯回復到的內部部署虛擬機器，使其開始複寫至 Azure。

如需快速概觀，請觀看下列有關如何從 Azure 容錯移轉到內部部署網站的影片。
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="fail-back-to-the-original-or-alternate-location"></a>容錯回復到原始位置或替代位置

如果您已容錯移轉 VMware 虛擬機器，在它仍存在的前提下，您可以將它容錯回復到相同的來源內部部署虛擬機器。 在此案例中，系統只會將變更複寫回來。 此案例稱為原始位置復原。 如果內部部署虛擬機器不存在，則此案例是替代位置復原。

> [!NOTE]
> 您只可容錯回復至原始的 vCenter 和組態伺服器。 您無法部署新的組態伺服器和使用它進行容錯回復。 此外，您無法將新的 vCenter 新增至現有組態伺服器及容錯回復到新的 vCenter。

#### <a name="original-location-recovery"></a>原始位置復原

如果您要容錯回復到原始虛擬機器，則必須符合下列條件：
* 如果虛擬機器是由 vCenter 伺服器管理，主要目標的 ESX 主機應該可以存取虛擬機器資料存放區。
* 如果虛擬機器位於 ESX 主機上，但不是由 vCenter 管理，則虛擬機器的硬碟必須位於主要目標的主機可以存取的資料存放區中。
* 如果虛擬機器位於 ESX 主機上，但未使用 vCenter，則應該先完成主要目標之 ESX 主機的探索後，才能重新保護。 如果您要容錯回復實體伺服器，則也適用這一條件。
* 您可以容錯回復至虛擬存放區域網路 (vSAN) 或以原始裝置對應 (RDM) 為基礎的磁碟 (如果磁碟已存在並連線到內部部署虛擬機器)。

#### <a name="alternate-location-recovery"></a>替代位置復原
如果在重新保護虛擬機器之前，內部部署虛擬機器不存在，則此案例稱為替代位置復原。 重新保護工作流程會重新建立內部部署虛擬機器。 這也會導致下載完整資料。

* 當您容錯回復至替代位置時，虛擬機器會復原到主要目標伺服器部署所在的相同 ESX 主機上。 用於建立磁碟的資料存放區將會是重新保護虛擬機器時選取的相同資料存放區。
* 您只能容錯回復到虛擬機器檔案系統 (VMFS) 或 vSAN 資料存放區。 如果您有 RDM，重新保護與容錯回復將無法運作。
* 重新保護程序涉及一項大型的初始資料傳輸作業以及緊接著的變更作業。 之所以會有此程序，是因為虛擬機器未存在於內部部署環境。 系統需要將完整資料複寫回來。 此重新保護作業也需要比原始位置復原更長的時間。
* 您無法容錯回復到以 RDM 為基礎的磁碟。 只能在 VMFS/vSAN 資料存放區上建立新的虛擬機器磁碟 (VMDK)。

將實體機器容錯回復到 Azure 時，只能容錯回復為 VMware 虛擬機器 (亦稱為 P2A2V)。 此流程是屬於替代位置復原的範圍。

* Windows Server 2008 R2 SP1 實體伺服器如果受保護且容錯移轉至 Azure，則無法容錯回復。
* 請確定您至少探索一部主要目標伺服器以及需要容錯回復到的必要 ESX/ESXi 主機。

## <a name="have-you-completed-reprotection"></a>您已完成重新保護嗎？
繼續之前，請先完成重新保護步驟，使虛擬機器處於已複寫狀態，這樣才能起始容錯移轉回到內部部署網站。 如需詳細資訊，請參閱[如何從 Azure 移至內部部署來重新保護](site-recovery-how-to-reprotect.md)。

## <a name="prerequisites"></a>必要條件

> [!IMPORTANT]
> 在針對 Azure 的容錯移轉期間，可能會無法存取內部部署網站，因此設定伺服器可能會無法使用或關機。 在重新保護和容錯回復期間，內部部署設定伺服器應該會執行並處於連線正常狀態。

* 執行容錯回復時，組態伺服器必須為內部部署。 伺服器應該處於執行中狀態並連線至服務，因此其健康情況為「正常」。 在容錯回復期間，虛擬機器必須存在於組態伺服器資料庫中，否則容錯回復會失敗。 因此，請確定您有排定來定期備份伺服器。 發生災害時，您需要使用相同的 IP 位址來還原伺服器，以便容錯回復能夠運作。
* 觸發重新保護/容錯回復之前，主要目標伺服器不能有任何快照集。

## <a name="steps-to-fail-back"></a>容錯回復的步驟

> [!IMPORTANT]
> 起始容錯回復之前，請確定您已完成虛擬機器的重新保護。 虛擬機器應該處於受保護狀態，且其健康狀態應該是**良好**。 若要重新保護虛擬機器，請參閱[如何重新保護](site-recovery-how-to-reprotect.md)。

1. 在 [複製的項目] 頁面中，請選取虛擬機器，並以滑鼠右鍵按一下以選取 [非計劃性容錯移轉]。
2. 在 [確認容錯移轉] 中，確認容錯移轉方向 (以 Azure 為來源)，然後選取您想要用來進行容錯移轉的復原點 (最近的復原點或最近的應用程式一致復原點)。 應用程式一致復原點會在最近的復原點之後，並造成部分資料遺失。
3. 在容錯移轉期間，Site Recovery 會將 Azure 上的虛擬機器關閉。 在確認容錯回復已如預期完成後，您可以確認 Azure 上的虛擬機器是否已關閉。

### <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>我可以將虛擬機器容錯回復至哪個復原點？

在容錯回復期間，有兩個選項讓您容錯回復虛擬機器/復原方案。

如果您選取最近處理的時間點，所有虛擬機器會容錯移轉至其最新可用的時間點。 如果復原方案內有複寫群組，則複寫群組的每個虛擬機器會容錯移轉至其獨自的最新時間點。

虛擬機器擁有至少一個復原點之後，才能予以容錯回復。 復原方案的所有虛擬機器至少要有一個復原點，您才能容錯回復此復原方案。

> [!NOTE]
> 最新復原點是損毀一致復原點。

如果您選取應用程式一致復原點，單一虛擬機器容錯回復會復原至其最新可用的應用程式一致復原點。 如果復原方案有複寫群組，每個複寫群組會復原至其一般可用的復原點。
請注意，應用程式一致復原點的時間會落後，可能會遺失資料。

### <a name="what-happens-to-vmware-tools-post-failback"></a>VMware 工具在容錯回復後會發生什麼狀況？

容錯移轉至 Azure 期間，VMware 工具無法在 Azure 虛擬機器上執行。 萬一遇上 Windows 虛擬機器，ASR 會在容錯移轉期間停用 VMware 工具。 萬一遇上 Linux 虛擬機器，ASR 會在容錯移轉期間解除安裝 VMware 工具。

在 Windows 虛擬機器容錯回復期間，VMware 工具也會在容錯回復之後立即重新啟用。 同樣地，針對 Linux 虛擬機器，VMware 工具會在容錯回復期間重新安裝於電腦上。

## <a name="next-steps"></a>後續步驟

容錯回復完成之後，您必須認可虛擬機器，以確保系統會刪除 Azure 中的已復原虛擬機器。

### <a name="commit"></a>認可
需要認可，以便從 Azure 移除容錯移轉的虛擬機器。
請以滑鼠右鍵按一下受保護的項目，然後按一下認可。 此時會有作業移除 Azure 中已容錯移轉的虛擬機器。

### <a name="reprotect-from-on-premises-to-azure"></a>從內部部署移至 Azure 來重新保護

在認可完成後，虛擬機器會回到內部部署網站上，但不會受到保護。 若要再次開始複寫至 Azure，請執行下列動作：

1. 在 保存庫 > 設定 > 已複寫的項目 中，選取已容錯回復的虛擬機器，然後按一下重新保護。
2. 提供必須用來將資料傳送回 Azure 之處理伺服器的值。
3. 按一下 [確定] 以開始重新保護作業。

> [!NOTE]
> 在內部部署虛擬機器啟動之後，需要經過一些時間，代理程式才會註冊回到組態伺服器中 (最多 15 分鐘)。 在這段時間，重新保護會失敗，並傳回錯誤訊息以指出未安裝代理程式。 請等候幾分鐘，然後再次嘗試重新保護。

重新保護作業完成之後，虛擬機器會複寫回到 Azure，而且您可以執行容錯移轉。

## <a name="common-issues"></a>常見問題
請先確定 vCenter 處於已連線狀態，再進行容錯回復。 否則，將磁碟中斷連線並將它們重新連結至虛擬機器的作業會失敗。
