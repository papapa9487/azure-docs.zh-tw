---
title: "使用 Azure Site Recovery 設定內部部署 Hyper-V VM 至 Azure 的災害復原 | Microsoft Docs"
description: "了解如何使用 Azure Site Recovery 服務來設定內部部署 Hyper-V VM 至 Azure 的災害復原。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 45e9b4dc-20ca-4c14-bee3-cadb8d9b8b24
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 4d43fb03ce1c54a47315b8c3a5c83ec2082bcab9
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>設定 Hyper-V VM 至 Azure 的災害復原

[Azure Site Recovery](site-recovery-overview.md) 服務可藉由管理及協調內部部署電腦與 Azure 虛擬機器 (VM) 的複寫、容錯移轉及容錯回復，為您的災害復原策略做出貢獻。

本教學課程說明如何設定內部部署 Hyper-V VM 至 Azure 的災害復原。 無論 Hyper-V VM 是否在 System Center Virtual Machine Manager (VMM) 雲端中管理，本教學課程都適用。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 設定 Azure 與內部部署的必要條件
> * 建立 Site Recovery 的復原服務保存庫 
> * 設定來源和目標複寫環境 
> * 設定網路對應 (如果 Hyper-V 是由 System Center VMM 管理)
> * 建立複寫原則
> * 啟用 VM 複寫


## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

- 請確定您了解[情節架構和元件](concepts-hyper-v-to-azure-architecture.md)。
- 檢閱所有元件的[支援需求](site-recovery-support-matrix-to-azure.md)。
- 請確定您想要複寫的 VM 符合 [Azure VM 需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
- 若要設計容量規劃：
    - 使用 [Hyper-V Capacity Planner 工具](http://aka.ms/asr-capacity-planner-excel)算出變換率。
    - 使用 [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) 來分析您的來源環境、估計頻寬和目標需求。
- 準備 Azure。 您需要 Azure 訂用帳戶、Azure 虛擬網路及儲存體帳戶。
- 準備內部部署 Hyper-V 主機和 VMM 伺服器 (如果有關)。





### <a name="set-up-an-azure-account"></a>設定 Azure 帳戶

取得 [Microsoft Azure 帳戶](http://azure.microsoft.com/)。

- 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。
- 了解 [Site Recovery 價格](site-recovery-faq.md#pricing)，並取得[定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
- 找出 Site Recovery [支援的區域](https://azure.microsoft.com/pricing/details/site-recovery/)。

### <a name="verify-azure-account-permissions"></a>確認 Azure 帳戶權限

請確定您的 Azure 帳戶具有複寫 VM 所需的權限。

- 檢閱您將機器複寫至 Azure 所需的[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)
- 驗證並修改[角色型存取](../active-directory/role-based-access-control-configure.md)權限。 


### <a name="set-up-an-azure-network"></a>設定 Azure 網路

設定 [Azure 網路](../virtual-network/virtual-network-get-started-vnet-subnet.md)。

- 在容錯移轉之後建立的 Azure VM 會置於這個網路。
- 此網路應位於與復原服務保存庫相同的區域。


### <a name="set-up-an-azure-storage-account"></a>設定 Azure 儲存體帳戶

設定 [Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)。

- Site Recovery 會將內部部署機器複寫至 Azure 儲存體。 容錯移轉發生後，會從儲存體建立 Azure VM。
- 儲存體帳戶與復原服務保存庫必須位於相同的區域。
- 此儲存體帳戶可以是標準或[進階](../virtual-machines/windows/premium-storage.md)帳戶。
- 如果您設定進階帳戶，則需要一個額外的標準帳戶來記錄資料。

### <a name="prepare-hyper-v-hosts"></a>準備 Hyper-V 主機

1. 確認 Hyper-V 主機符合[支援需求](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)。
2. 請確定主機可以存取這些 URL：

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - 任何以 IP 位址為基礎的防火牆規則都應該允許對 Azure 的通訊。
    - 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。
    - 允許訂用帳戶的 Azure 區域和美國西部 (用於存取控制和身分識別管理) 使用 IP 位址範圍。

### <a name="prepare-vmm-servers"></a>準備 VMM 伺服器

如果 Hyper-V 主機由 VMM 管理，您需要準備內部部署 VMM 伺服器。 

- 請確認 VMM 伺服器符合[支援需求](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)。
- 請確定 VMM 伺服器至少具有一個雲端，以及一或多個主機群組。 執行 VM 的 Hyper-V 主機應該位於雲端。
- VMM 伺服器必須可透過網際網路存取下列 URL：

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - 任何以 IP 位址為基礎的防火牆規則都應該允許對 Azure 的通訊。
    - 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。
    - 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。
- 準備 VMM 伺服器以進行網路對應。


#### <a name="prepare-vmm-for-network-mapping"></a>準備 VMM 以進行網路對應

如果您使用 VMM，[網路對應](site-recovery-network-mapping.md)會在內部部署 VMM VM 網路與 Azure 虛擬網路之間對應。 對應可確保容錯移轉之後建立的 Azure VM 會連線到正確的網路。

準備 VMM 以進行網路對應，如下所示：

1. 請確定 Hyper-V 主機所在雲端有相關聯的 [VMM 邏輯網路](https://docs.microsoft.com/system-center/vmm/network-logical)。
2. 請確定您有 [VM 網路](https://docs.microsoft.com/system-center/vmm/network-virtual)連結至邏輯網路。
3. 將 VM 連線至 VM 網路。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-a-protection-goal"></a>選取保護目標

選取要複寫的內容及複寫目的地。

1. 在保存庫中，按一下 [Site Recovery] > [準備基礎結構] > [保護目標]。
2. 在 [保護目標] 中，選取 [至 Azure]> [是，利用 Hyper-V]。 
    - 如果 Hyper-V 主機不受 VMM 管理，請選取 [否] 以確認您未使用 VMM。
    - 如果主機是在 VMM 雲端中管理，請選取 [是]。

## <a name="set-up-the-source-environment"></a>設定來源環境

設定來源環境時，您需要安裝 Azure Site Recovery Provider 和 Azure 復原服務代理程式，並在保存庫中註冊內部部署伺服器。 

1. 在 [準備基礎結構] 中，按一下 [來源]。 
    - 如果您未使用 VMM，請按一下 [+Hyper-V 網站]，並指定網站名稱。 然後按一下 [+Hyper-V 伺服器]，並將主機或叢集新增至網站。
    - 如果您使用 VMM，請在 [準備來源] 中，按一下 [+ VMM] 以新增 VMM 伺服器。 在 [新增伺服器] 中，檢查 [System Center VMM 伺服器] 是否出現在 [伺服器類型] 中。
2. 視您的環境而定，下載提供者和代理程式元件。
    - 若只有 Hyper-V，請下載提供者安裝檔案。 請在每個 Hyper-V 主機上執行此檔案，以同時安裝提供者和代理程式。
        
        ![提供者 (不含 VMM)](./media/tutorial-hyper-v-to-azure/download-no-vmm.png)
    
    - 若為含有 VMM 的 Hyper-V，請分開下載提供者和代理程式。 在 VMM 伺服器上執行提供者安裝。 在每一個 Hyper-V 主機上執行代理程式安裝。
    
        ![提供者和代理程式 (含 VMM)](./media/tutorial-hyper-v-to-azure/download-vmm.png)
    
3. 下載保存庫註冊金鑰。 您執行提供者安裝程式時需要此金鑰。 該金鑰在產生後會維持 5 天有效。

### <a name="install-components"></a>安裝元件

安裝下表概述的元件。 

**元件** | **詳細資料** | **僅限 Hyper-V** | **Hyper-V (含 VMM)**
--- | --- | --- | ---
**Azure Site Recovery 提供者** | 協調複寫至 Azure | 在每個 Hyper-V 主機上安裝 | 在 VMM 伺服器上安裝
**復原服務代理程式** | 處理資料複寫 | 在每個 Hyper-V 主機上安裝 | 在 Hyper-V 主機上安裝


#### <a name="install-the-provider-on-hyper-v-without-vmm"></a>在不含 VMM 的 Hyper-V 上安裝提供者

在您新增至 Hyper-V 網站的每個 Hyper-V 主機上安裝提供者。 如果您安裝在 Hyper-V 叢集上，請在每個叢集節點上執行安裝程式。 安裝和註冊每個 Hyper-V 叢集節點，可確保 VM 即使跨節點移轉，都還是會受保護。

1. 在 [Microsoft Update] 中，您可以選擇進行更新，以便根據您的 Microsoft Update 原則安裝 Provider 更新。
2. 在 [安裝] 中接受或修改預設 Provider 安裝位置，然後按一下 [安裝]。
4. 在 [保存庫設定] 中，按一下 [瀏覽] 來選取您已下載的保存庫金鑰檔案。 指定 Azure Site Recovery 訂用帳戶、保存庫名稱，以及 Hyper-V 伺服器所屬的 Hyper-V 網站。
5. 在 [Proxy 設定] 中，指定在 VMM 伺服器或 Hyper-V 主機上執行的提供者如何透過網際網路連線至 Site Recovery。
    * 若是直接連線，請選取 [不使用 Proxy 伺服器直接連線到 Azure Site Recovery]。
    * 若使用 Proxy，請選取 [使用 Proxy 伺服器連線到 Azure Site Recovery]。 視需要指定 Proxy 位址、連接埠和認證。
6. 在保存庫中註冊伺服器之後，請按一下 [完成]。

Azure Site Recovery 會取出來自 Hyper-V 伺服器的中繼資料，而該伺服器會顯示在 [Site Recovery 基礎結構] > [Hyper-V 主機] 中。


#### <a name="install-the-recovery-services-agent-on-hyper-v-host-without-vmm"></a>在不含 VMM 的 Hyper-V 主機上安裝復原服務代理程式

如果您的部署中使用 VMM，請在每一個 Hyper-V 主機上執行復原服務代理程式安裝程式。

1. 在 [安裝精靈] > [檢查必要條件] 中，按 [下一步]。 將自動安裝任何缺少的必要元件。

    ![Prerequisites Recovery Services Agent](./media/tutorial-hyper-v-to-azure/hyperv-agent-prerequisites.png)
3. 在 [安裝設定] 中，接受或修改安裝位置和快取位置。 快取磁碟機至少需要 5 GB 的儲存體。 我們建議磁碟機要有 600 GB 或更多的可用空間。 然後按一下 [安裝] 。
4. 在 [安裝] 中，當安裝完成時，請按一下 [關閉] 以完成精靈。

##### <a name="set-up-internet-access-via-a-proxy"></a>設定透過 Proxy 存取網際網路

Hyper-V 主機上的復原服務代理程式需要透過網際網路存取 Azure，才能進行 VM 複寫。 如果您透過 Proxy 存取網際網路，請如下所示設定它︰

1. 在 Hyper-V 主機上開啟 Microsoft Azure 備份 MMC 嵌入式管理單元。 根據預設，Microsoft Azure 備份的捷徑位於桌面上或在 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 中。
2. 在嵌入式管理單元中，按一下 [變更屬性]。
3. 在 [Proxy 設定] 索引標籤上，指定 Proxy 資訊。

    ![註冊 MARS 代理程式](./media/tutorial-hyper-v-to-azure/mars-proxy.png)
4. 檢查代理程式可連線至[必要 URL](#prepare-hyper-v-hosts)。

#### <a name="install-the-provider-on-the-vmm-server-hyper-v-with-vmm"></a>在 VMM 伺服器上安裝提供者 (含 VMM 的 Hyper-V)

1. 在 [Microsoft Update] 中，您可以選擇進行更新，以便根據您的 Microsoft Update 原則安裝 Provider 更新。
2. 在 [安裝] 中接受或修改預設 Provider 安裝位置，然後按一下 [安裝]。 
3. 現在，請在保存庫中註冊 VMM 伺服器。 在 [保存庫設定] 中，按一下 [瀏覽] 來選取您已下載的保存庫金鑰檔案。 指定 Azure Site Recovery 訂用帳戶和保存庫名稱。

    ![伺服器註冊](./media/tutorial-hyper-v-to-azure/provider-vault-settings.png)

4. 在 [Proxy 設定] 中，指定在 VMM 伺服器或 Hyper-V 主機上執行的提供者如何透過網際網路連線至 Site Recovery。

    * 若是直接連線，請選取 [不使用 Proxy 伺服器直接連線到 Azure Site Recovery]。
    * 若使用 Proxy，請選取 [使用 Proxy 伺服器連線到 Azure Site Recovery]。 視需要指定 Proxy 位址、連接埠和認證。
    - 將會使用指定的 Proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。 設定 proxy 伺服器，讓此帳戶可以成功進行驗證。 您可以在 VMM 控主台 > [設定] > [安全性] > [執行身分帳戶]中修改 RunAs 帳戶設定。 重新啟動 VMM 服務以更新變更。
5. 在 [資料加密] 中，指定傳送至 Azure 的所有資料是否要加密。 如果您選取此選項，Site Recovery 會發行憑證。 稍後，您需要使用此憑證將資料解密。
6. 在 [VMM 伺服器] 中，指定易記名稱來識別保存庫中的 VMM 伺服器。 在叢集設定中，指定 VMM 叢集角色名稱。 在 [同步處理雲端中繼資料與保存庫] 中，選擇是否要同步處理 VMM 伺服器上所有雲端的中繼資料與保存庫。 這個動作只需要在每個伺服器上進行一次。 如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。

註冊完成之後，Azure Site Recovery 會取出來自伺服器的中繼資料，而該 VMM 伺服器會顯示在 [Site Recovery 基礎結構] 中。






## <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。 

1. 按一下 [準備基礎結構] > [目標]。
2. 選取容錯移轉之後將在其中建立 Azure VM 的訂用帳戶和資源群組。 選擇您在 Azure 中要用於 VM 的部署模型。

3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

## <a name="configure-network-mapping-with-vmm"></a>設定網路對應 (含 VMM)

如果您使用 VMM，請設定網路對應。

1. 在 [Site Recovery 基礎結構]  >  [網路對應]  >  [網路對應]中，按一下 [+網路對應] 圖示。
2. 在 [新增網路對應] 中，選取來源 VMM 伺服器。 選取 [Azure] 作為目標。
3. 在容錯移轉後確認訂用帳戶和部署模型。
4. 在 [來源網路] 中，選取來源內部部署 VM 網路。
5. 在 [目標網路] 中，選取 Azure 網路來放置容錯移轉之後建立的複本 Azure VM。 然後按一下 [確定] 。

    ![網路對應](./media/tutorial-hyper-v-to-azure/network-mapping-vmm.png)

## <a name="create-a-replication-policy"></a>建立複寫原則

1. 按一下 [準備基礎結構] > [複寫設定] > [+建立並產生關聯]。
2. 在 [建立及關聯原則] 中指定原則名稱。
3. 在 [複製頻率] 中，指定您要在初始複寫後複寫差異資料的頻率 (每隔 30 秒、5 或 15 分鐘)。

    > [!NOTE]
    >  複寫到進階儲存體時，不支援 30 秒的頻率。 限制取決於進階儲存體所支援之每 blob (100) 的快照集數目。 [深入了解](../virtual-machines/windows/premium-storage.md#snapshots-and-copy-blob)。

4. 在 [復原點保留] 中，指定每個復原點的保留時間 (以小時為單位)。 受保護的機器可以復原到週期內的任意點。
5. 在 [應用程式一致快照頻率] 中，指定建立包含應用程式一致快照之復原點的頻率 (1-12 小時)。 Hyper-V 使用兩種快照集：
    - **標準快照集**：提供整個虛擬機器的累加快照集。
    - **應用程式一致快照集**：建立 VM 內應用程式資料的時間點快照集。 磁碟區陰影複製服務 (VSS) 可確保在製作快照集時，應用程式處於一致狀態。 啟用應用程式一致快照集會影響來源 VM 上的應用程式效能。 設定的值應該小於您設定的其他復原點數目。
6. 在 [初始複寫開始時間] 中，指出開始初始複寫的時間。 複寫會消耗網際網路頻寬，建議排定在非尖峰時間進行。
7. 在 [加密儲存在 Azure 上的資料] 中，指定是否加密 Azure 儲存體中的待用資料。 然後按一下 [確定] 。

    ![複寫原則](./media/tutorial-hyper-v-to-azure/replication-policy.png)


建立的新原則會自動與 VMM 雲端或 Hyper-V 網站產生關聯。

## <a name="enable-replication"></a>啟用複寫


1. 按一下 [複寫應用程式] > [來源]。 
2. 在 [來源] 中，選取 Hyper-V 網站/VMM 伺服器/雲端。 然後按一下 [確定] 。
3. 在 [目標] 中，確認目標為 Azure、保存庫訂用帳戶，以及容錯移轉之後要在 Azure 中使用的模型。
4. 指定所複寫資料的儲存體帳戶，以及 Azure VM 在容錯移轉之後所在的 Azure 網路。
5. 在 [虛擬機器] > [選取] 中，選取您要複寫的 VM。 然後按一下 [確定] 。

 您可以在 [作業] > [Site Recovery 作業] 中，追蹤 [啟用保護] 動作的進度。 在**完成保護**作業完成後，即完成初始複寫，虛擬機器已可進行容錯移轉。

## <a name="next-steps"></a>後續步驟
[執行災害復原演練](tutorial-dr-drill-azure.md)
