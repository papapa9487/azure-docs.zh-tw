---
title: "使用 Site Recovery 將複寫的 Hyper-V VM 容錯移轉及容錯回復至 Azure | Microsoft Docs"
description: "了解如何使用 Site Recovery，將 Hyper-V VM 容錯移轉至 Azure，然後容錯回復至內部部署網站"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72065d01-ed0f-430e-b117-a5885cecd1db
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/15/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e1cc21661450a983c25b24fe2a6228e26ceecec6
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>將 Hyper-V VM 容錯移轉及容錯回復至 Azure

[Azure Site Recovery](site-recovery-overview.md) 服務會管理並協調內部部署機器和 Azure 虛擬機器 (VM) 的複寫、容錯移轉和容錯回復。

本教學課程說明如何將 Hyper-V VM 容錯移轉至 Azure。 在容錯移轉之後，您可以容錯回復至可用的內部部署網站。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 將 Hyper-V VM 從內部部署容錯移轉至 Azure
> * 從 Azure 容錯回復至內部部署，並再次開始複寫至 Azure

## <a name="overview"></a>概觀
容錯移轉和容錯回復有兩個階段：

1. **容錯移轉至 Azure**：將機器從內部部署網站容錯移轉至 Azure。
2. **從 Azure 容錯回復至內部部署**：執行從 Azure 到內部部署的規劃性容錯移轉。 完成規劃的容錯移轉之後，您可以啟用反向複寫，再次開始從內部部署複寫至 Azure。 


## <a name="fail-over-to-azure"></a>容錯移轉至 Azure

### <a name="failover-prerequisites"></a>容錯移轉必要條件

若要完成容錯移轉：

- 確定您已完成[災害復原演練](tutorial-dr-drill-azure.md)，檢查一切是否如預期般運作。
- (選擇性) 容錯移轉之前準備連線至 Azure VM。
- 執行容錯移轉之前，請確認 VM 屬性。

#### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>準備在容錯移轉後連接到 Azure VM

如果您想要在容錯移轉後使用 RDP 連線至 Azure VM，請執行下列作業︰

##### <a name="azure-vms-running-windows"></a>執行 Windows 的 Azure VM

1. 若要透過網際網路存取 Azure VM，請在內部部署 VM 上啟用 RDP，再進行容錯移轉。 確定已針對 [公用] 設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆] > [允許的應用程式] 中已針對所有設定檔允許 RDP。
2. 若要透過站對站 VPN 存取，請在內部部署機器上啟用 RDP。 您應該在 [Windows 防火牆] -> [允許的應用程式與功能] 中，針對 [網域] 和 [私人] 網路允許 RDP。 確認作業系統的 SAN 原則已設為 [OnlineAll]。 [深入了解](https://support.microsoft.com/kb/3031135)。 觸發容錯移轉時，VM 上不應該有任何擱置的 Windows 更新。 如果有，在更新完成之前，您將無法登入虛擬機器。 
3. 在容錯移轉之後，於 Windows Azure VM 上，勾選 [開機診斷] 以檢視 VM 的螢幕擷取畫面。 如果您無法連線，請檢查 VM 是否正在執行，並檢閱這些[疑難排解祕訣](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。


##### <a name="azure-vms-running-linux"></a>執行 Linux 的 Azure VM

1. 在容錯移轉之前，於內部部署機器上，確認安全殼層服務已設定為在系統開機時自動啟動。 確認防火牆規則允許 SSH 連線。
2. 在容錯移轉之後，於 Azure VM 上，針對已容錯移轉之 VM 上的網路安全性群組規則及其所連線的 Azure 子網路，允許 SSH 連接埠的連入連線。  [新增 VM 的公用 IP 位址](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine)。 您可以勾選 [開機診斷] 以檢視 VM 的螢幕擷取畫面。


#### <a name="verify-vm-properties"></a>驗證 VM 屬性

驗證 VM 屬性，並確定 VM 符合 [Azure 需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

1. 在 [受保護的項目] 中，按一下 [複寫的項目] > VM。
2. 在 [複寫的項目] 窗格中，將會呈現 VM 資訊、健康情況狀態，以及最新可用復原點的摘要。 如需檢視詳細資訊，請按一下 [屬性]。
3. 在 [計算與網路] 中，您可以修改 Azure 的名稱、資源群組、目標大小、[可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)及[受管理磁碟設定](#managed-disk-considerations)。
4. 您可以檢視及修改網路設定，包括在容錯移轉後 Azure VM 所在的網路/子網路，以及要指派給它的 IP 位址。
5. 在 [磁碟] 中，您可以看見 VM 上作業系統和資料磁碟的相關資訊。


### <a name="run-a-failover-from-on-premises-to-azure"></a>執行從內部部署容錯移轉至 Azure

您可以對 Hyper-V VM 執行定期或規劃的容錯移轉

- 對非預期的中斷情形使用定期容錯移轉。 當您執行此容錯移轉時，Site Recovery 會建立並啟動 Azure VM。 您可針對特定復原點執行容錯移轉。 視您使用的復原點而言，可能會發生資料遺失。
- 規劃的容錯移轉可用於維護，或在預期的中斷期間使用。 此選項會提供資料零遺失。 觸發規劃的容錯移轉時，來源 VM 會關閉。 系統會同步處理未同步處理的資料，並觸發容錯移轉。

此程序說明如何執行定期容錯移轉。


1. 在 [設定] > [複寫的項目] 中，按一下 VM > [容錯移轉]。
2. 在 [容錯移轉] 中，選取容錯移轉的目標**復原點**。 您可以使用下列其中一個選項：
    - **最新** (預設值)：此選項會先處理所有傳送至 Site Recovery 的資料。 它會提供最低的 RPO (復原點目標)，因為在容錯移轉後建立的 Azure VM 具有在觸發容錯移轉時複寫到 Site Recovery 的所有資料。
    - **最近處理**：此選項會將 VM 容錯移轉到 Site Recovery 所處理的最新復原點。 此選項提供低 RTO (復原時間目標)，因為無須花費時間處理未處理的資料。
    - **最新應用程式一致**：此選項會將 VM 容錯移轉到 Site Recovery 所處理的最近應用程式一致復原點。 
    - **自訂**：指定任何復原點。
3. 如果您正在將 System Center VMM 雲端中的 Hyper-V VM 容錯移轉至 Azure，而且已針對雲端啟用資料加密，請在 [加密金鑰] 中，選取當您在 VMM 伺服器上設定提供者期間，啟用資料加密時所發行的憑證。
4. 如果想在觸發容錯移轉之前，讓 Site Recovery 嘗試將來源虛擬機器關機，請選取 [先將機器關機再開始容錯移轉]。 Site Recovery 也會在觸發容錯移轉之前，嘗試同步處理尚未傳送至 Azure 的內部部署資料。 請注意，即使關機失敗，仍會繼續容錯移轉。 您可以 [作業] 頁面上追蹤容錯移轉進度。
5. 如果您已準備好連線到 Azure VM，請進行連線以在容錯移轉之後加以驗證。
6. 驗證之後，請 [認可] 容錯移轉。 這會刪除所有可用的復原點。

> [!WARNING]
> **不要取消正在進行的容錯移轉**：在啟動容錯移轉之前，已停止 VM 複寫。 如果您取消正在進行的容錯移轉，容錯移轉會停止，但 VM 不會再次複寫。  


## <a name="fail-back-from-azure-to-on-premises"></a>從 Azure 容錯回復至內部部署

### <a name="prerequisites-for-failback"></a>容錯回復的必要條件

若要完成容錯回復，請確定主要網站 VMM 伺服器/Hyper-V 伺服器已連線至 Site Recovery。


### <a name="run-a-failback-and-reprotect-on-premises-vms"></a>執行容錯回復並重新保護內部部署 VM

從 Azure 容錯移轉至內部部署網站，並開始將 VM 從內部部署網站複寫至 Azure。

1. 在 [設定] > [複寫的項目] 中，按一下 VM > [規劃的容錯移轉]。
2. 在 [確認規劃的容錯移轉] 中，確認容錯移轉方向 (從 Azure)，並選取來源和目標位置。 
3. 在 [資料同步] 中，指定您要的同步處理方式：
    - **在容錯移轉前同步處理資料 (僅同步處理差異變更)** - 這個選項可讓 VM 停機時間縮到最短，因為不需關閉 VM 即可進行同步處理。 以下是其作用：
        1. 製作 Azure VM 的快照集，並複製到內部部署 Hyper-V 主機。 VM 會在 Azure 中保持執行。
        2. 關閉 Azure VM，如此一來，它就不會有任何新的變更。 最後一組差異變更會傳送到內部部署伺服器，並啟動內部部署 VM。
    - **僅在容錯移轉期間同步處理資料 (完整下載)** - 如果您已經在 Azure 上長時間執行，請使用這個選項。 這個選項比較快速，因為我們預期會有多項磁碟變更，而且不想花時間計算總和檢查碼。 此選項會執行磁碟下載。 當內部部署 VM 已刪除時，此選項也很有用。
4. 如果您正在將 System Center VMM 雲端中的 Hyper-V VM 容錯移轉至 Azure，而且已針對雲端啟用資料加密，請在 [加密金鑰] 中，選取當您在 VMM 伺服器上設定提供者期間，啟用資料加密時所發行的憑證。
5. 起始容錯移轉。 您可以在 [工作]  索引標籤上追蹤容錯移轉進度。
6. 如果您已選擇要在容錯移轉前同步處理資料，則在完成初始資料同步處理且您已經準備好關閉 Azure VM 之後，請按一下 [作業] > 規劃的容錯移轉作業名稱 > [完成容錯移轉]。 這會將 Azure VM 關機、將最新變更轉送至內部部署，然後啟動內部部署 VM。
7. 登入內部部署 VM 以檢查它如預期般可用。
8. 內部部署 VM 現在處於「認可擱置」狀態。 按一下 [認可] 以認可容錯移轉。 這會刪除 Azure VM 和它的磁碟，並準備內部部署 VM 來反轉複寫方向。
9. 若要開始將內部部署 VM 複寫至 Azure，請啟用 [反向複寫]。


> [!NOTE]
> 反轉複寫方向只會複寫自從 Azure VM 關閉以來發生的變更，而且只會傳送差異變更。


