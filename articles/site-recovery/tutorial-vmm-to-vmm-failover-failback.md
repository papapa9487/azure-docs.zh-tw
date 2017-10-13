---
title: "使用 Site Recovery 將複寫的 Hyper-V VM 容錯移轉及容錯回復到次要資料中心 | Microsoft Docs"
description: "了解如何使用 Azure Site Recovery 將 Hyper-V VM 容錯移轉至次要內部部署網站，以及容錯回復至主要網站"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44a662fa-2e7a-4996-86df-fdd6d6f5dedf
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.openlocfilehash: 8f139070de99c4249207d048d445e86dd41e9060
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>將複寫的 Hyper-V VM 容錯移轉及容錯回至次要內部部署網站

[Azure Site Recovery](site-recovery-overview.md) 服務會管理並協調內部部署機器和 Azure 虛擬機器 (VM) 的複寫、容錯移轉和容錯回復。

本教學課程說明如何將 System Center Virtual Machine Manager (VMM) 雲端中管理的 Hyper-V VM 容錯移轉至次要 VMM 網站。 在容錯移轉之後，您可以容錯回復至可用的內部部署網站。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 將 Hyper-V VM 從主要 VMM 雲端容錯移轉至次要 VMM 雲端
> * 從次要網站放到主要網站重新保護，然後容錯回復
> * 選擇性再次開始從主要網站複寫至次要網站

## <a name="overview"></a>概觀

容錯移轉和容錯回復有三個階段：

1. **容錯移轉至次要網站**：將機器從主要網站容錯移轉至次要網站。
2. **從次要網站容錯回復**：將 VM 從次要網站複寫至主要網站，然後執行計劃性容錯移轉來容錯回復。
3. 在計劃性容錯移轉後，選擇性地再次開始從主要網站複寫至次要網站。


## <a name="fail-over-to-a-secondary-site"></a>容錯移轉至次要網站

### <a name="failover-prerequisites"></a>容錯移轉必要條件

確定您已完成[災害復原演練](tutorial-dr-drill-secondary.md)，檢查一切是否如預期般運作。


### <a name="run-a-failover-from-primary-to-secondary"></a>執行從主要網站到次要網站的容錯移轉

您可以對 Hyper-V VM 執行定期或計劃性容錯移轉。

- 對非預期的中斷情形使用定期容錯移轉。 當您執行此容錯移轉時，Site Recovery 會在次要網站中建立 VM，然後將它啟動。 您可針對特定復原點執行容錯移轉。 視您使用的復原點而言，可能會發生資料遺失。
- 規劃的容錯移轉可用於維護，或在預期的中斷期間使用。 此選項會提供資料零遺失。 觸發規劃的容錯移轉時，來源 VM 會關閉。 系統會同步處理未同步處理的資料，並觸發容錯移轉。 
- 
此程序說明如何執行定期容錯移轉。


1. 在 [設定] > [複寫的項目] 中，按一下 VM > [容錯移轉]。
2. 在 [容錯移轉] 中，選取容錯移轉的目標**復原點**。 您可以使用下列其中一個選項：
    - **最新** (預設值)：此選項會先處理所有傳送至 Site Recovery 的資料。 它會提供最低的 RPO (復原點目標)，因為在容錯移轉後建立的複本 VM 具有在觸發容錯移轉時複寫到 Site Recovery 的所有資料。
    - **最近處理**：此選項會將 VM 容錯移轉到 Site Recovery 所處理的最新復原點。 此選項提供低 RTO (復原時間目標)，因為無須花費時間處理未處理的資料。
    - **最近的應用程式一致**：此選項會將 VM 容錯移轉到 Site Recovery 所處理的最近應用程式一致復原點。 
3. 加密金鑰不適用於此案例。
4. 如果想在觸發容錯移轉之前，讓 Site Recovery 嘗試將來源 VM 關機，請選取 [先將機器關機再開始容錯移轉]。 Site Recovery 也會在觸發容錯移轉之前，嘗試同步處理尚未傳送至次要網站的內部部署資料。 請注意，即使關機失敗，仍會繼續容錯移轉。 您可以 [作業] 頁面上追蹤容錯移轉進度。
5. 您現在應該能夠在次要 VMM 雲端中看到此 VM。
6. 驗證 VM 之後，請 [認可] 容錯移轉。 這會刪除所有可用的復原點。

> [!WARNING]
> **不要取消正在進行的容錯移轉**：在啟動容錯移轉之前，已停止 VM 複寫。 如果您取消正在進行的容錯移轉，容錯移轉會停止，但 VM 不會再次複寫。  


## <a name="reprotect-and-fail-back-from-secondary-to-primary"></a>重新保護並從次要網站容錯回復到主要網站

### <a name="prerequisites-for-failback"></a>容錯回復的必要條件

若要完成容錯回復，請確定主要和次要 VMM 伺服器已連線至 Site Recovery。


### <a name="reprotect-and-fail-back"></a>重新保護和容錯回復

開始從次要網站複寫至主要網站，然後容錯回復至主要網站。 當 VM 重新在主要網站中執行之後，您可以再次將它們複寫至次要網站。  

1. 在 [設定] > [複寫的項目] 中，按一下 VM 並啟用 [反向複寫]。 VM 開始複寫回到主要網站。
2. 按一下 VM > [計劃性容錯移轉]。
3. 在 [確認計劃性容錯移轉] 中，確認容錯移轉方向 (從次要 VMM 雲端)，以及選取來源和目標位置。 
4. 在 [資料同步] 中，指定您要的同步處理方式：
    - **在容錯移轉前同步處理資料 (僅同步處理差異變更)** - 這個選項可讓 VM 停機時間縮到最短，因為不需關閉 VM 即可進行同步處理。 以下是其作用：
        - 建立複本 VM 的快照集，並將它複製到主要 Hyper-V 主機。 複本 VM 會持續執行。
        - 關閉複本 VM，如此一來，它就不會有任何新的變更。 最後一組差異變更會傳送到主動網站，而主動網站中的 VM 會啟動。
    - **僅在容錯移轉期間同步處理資料 (完整下載)** — 如果您已在次要網站中長時間執行，請使用這個選項。 這個選項比較快速，因為我們預期會有多項磁碟變更，而且不想花時間計算總和檢查碼。 此選項會執行磁碟下載。 當主要 VM 已遭刪除時，它也很有用。
5. 加密金鑰不適用於此案例。
6. 起始容錯移轉。 您可以在 [工作]  索引標籤上追蹤容錯移轉進度。
7. 如果您已選擇要在容錯移轉前同步處理資料，則在完成初始資料同步處理且您已經準備好關閉次要網站中的複本 VM 之後，按一下 [作業] > <計劃性容錯移轉作業名稱> [完成容錯移轉]。 這會關機次要 VM、將最近的變更傳送到主要網站，然後啟動主要 VM。
8. 在主要 VMM 雲端中，檢查 VM 是否可用。
9. 主要 VM 現在處於「認可擱置」狀態。 按一下 [認可] 以認可容錯移轉。
10. 如果您想要再次開始將主要 VM 複寫回到次要網站，請啟用 [反向複寫]。


> [!NOTE]
> 反向複寫只會複寫在複本 VM 關閉後發生的變更，而且只會傳送差異變更。

