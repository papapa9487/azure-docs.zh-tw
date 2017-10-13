---
title: "將 VMware VM 與實體伺服器複寫到 Azure (舊版傳統) | Microsoft Docs"
description: "描述在 Azure 入口網站中，如何在舊版部署中使用 Azure Site Recovery，將內部部署 VM 和 Windows/Linux 實體伺服器複寫至 Azure。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: f980fb52-8ec2-4dd9-85e2-8e52e449f1ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
ms.openlocfilehash: 325be23cffc9c728a8af6f92a0f3dce6d31da4ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>在傳統入口網站 (舊版) 中使用 Azure Site Recovery 將 VMware 虛擬機器和實體伺服器複寫至 Azure
> [!div class="op_single_selector"]
> * [Azure 入口網站](site-recovery-vmware-to-azure.md)
> * [傳統入口網站](site-recovery-vmware-to-azure-classic.md)
> * [傳統入口網站 (舊版)](site-recovery-vmware-to-azure-classic-legacy.md)
>
>

歡迎使用 Azure Site Recovery！ 本文針對舊版部署，描述在 Azure 入口網站中使用 Azure Site Recovery，將內部部署 VMware 虛擬機器或 Windows/Linux 實體伺服器複寫至 Azure。

## <a name="overview"></a>概觀
組織需要 BCDR 策略，以決定應用程式、工作負載和資料如何在規劃與未規劃停機期間維持運作，並儘速復原到正常運作的情況。 BCDR 策略應保護商務資料安全且可復原，並確保發生災害時工作負載仍持續可用。

Site Recovery 是一項 Azure 服務，可藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，協助您的 BCDR 策略。 當您的主要位置發生故障時，您容錯移轉至次要位置，讓應用程式和工作負載保持可用。 當它恢復正常作業時，容錯回復至您的主要位置。 如需深入了解，請參閱 [什麼是 Azure Site Recovery？](site-recovery-overview.md)

> [!WARNING]
> 本文包含 **舊版指示**。 請勿用於新的部署。 相反地，請[遵循這些指示](site-recovery-vmware-to-azure.md)在 Azure 入口網站中部署 Site Recovery，或[使用這些指示](site-recovery-vmware-to-azure-classic.md)在傳統入口網站中設定增強的部署。 如果您已經使用本文所述的方法部署，建議您移轉到傳統入口網站中增強的部署。
>
>

## <a name="migrate-to-the-enhanced-deployment"></a>移轉至增強部署
只有在您已使用本文中的指示部署 Site Recovery 時，本節才有關。

若要移轉現有的部署，您必須：

1. 在內部部署網站部署新的 Site Recovery 元件。
2. 設定認證以自動探索新組態伺服器上的 VMware VM。
3. 使用新的組態伺服器探索 VMware 伺服器。
4. 使用新的組態伺服器建立新的保護群組。

開始之前：

* 建議您設計維護時段進行移轉。
* 只有在您具有舊版部署期間建立的現有保護群組時，[移轉機器]  選項才可用。
* 完成移轉步驟之後，可能需要 15 分鐘或更長的時間來重新整理認證，並探索及重新整理虛擬機器，以便您可以將它們加入至保護群組。 您可以手動重新整理而不用等待。

移轉如下所示：

1. 閱讀 [傳統入口網站中的增強部署](site-recovery-vmware-to-azure-classic.md)的相關資訊。 檢閱增強[架構](site-recovery-vmware-to-azure-classic.md)和[必要條件](site-recovery-vmware-to-azure-classic.md)。
2. 從您目前複寫的機器解除安裝行動服務。 當您將機器加入至新的保護群組時，機器上將安裝新版本的服務。
3. 下載[保存庫註冊金鑰](site-recovery-vmware-to-azure-classic.md)，並[執行整合安裝精靈](site-recovery-vmware-to-azure-classic.md)，以安裝組態伺服器、處理序伺服器和主要目標伺服器元件。 深入了解 [容量規劃](site-recovery-vmware-to-azure-classic.md)。
4. [設定認證](site-recovery-vmware-to-azure-classic.md) 供 Site Recovery 用來存取 VMware 伺服器，以自動探索 VMware VM。 閱讀 [需要的權限](site-recovery-vmware-to-azure-classic.md)的相關資訊。
5. 加入 [vCenter 伺服器或 vSphere 主機](site-recovery-vmware-to-azure-classic.md)。 可能需要 15 分鐘以上，伺服器才會出現在 Site Recovery 入口網站中。
6. 建立 [新的保護群組](site-recovery-vmware-to-azure-classic.md)。 可能需要 15 分鐘讓入口網站重新整理，以便探索和顯示虛擬機器。 如果您不想等待，可以反白顯示管理伺服器名稱 (不要按它) > [重新整理]。
7. 在新的保護群組底下按一下 [移轉機器] 。

    ![新增帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)
8. 在 [選取機器]  中選取您想要從中移轉的保護群組，以及您想要移轉的機器。

    ![新增帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)
9. 在 [設定目標設定]  中指定您是否要針對所有機器使用相同設定，然後選取處理序伺服器和 Azure 儲存體帳戶。 如果您沒有個別的處理序伺服器，則這是組態伺服器的 IP 位址。

    ![新增帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] 對於用於部署 Site Recovery 的儲存體帳戶，不支援跨相同訂用帳戶內的資源群組或跨訂用帳戶[移轉儲存體帳戶](../resource-group-move-resources.md)。

1. 在 [指定帳戶] 中，選取您為處理伺服器建立來存取機器以推送新行動服務版本的帳戶。

   ![新增帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)
2. Site Recovery 會將複寫的資料移轉至您所提供的 Azure 儲存體帳戶中。 您可以選擇性地重複使用您在舊版部署中使用的儲存體帳戶。
3. 在作業完成之後會自動同步處理虛擬機器。 同步處理完成之後，您可以從舊版保護群組中刪除虛擬機器。
4. 所有機器都移轉之後，您可以刪除舊版保護群組。
5. 請記得在同步處理完成之後指定機器的容錯移轉屬性和 Azure 網路設定。
6. 如果您有現有的復原方案，您可以使用 [移轉復原方案]  選項將它們移轉到增強型部署。 您應該只在已經移轉所有受保護的機器之後執行這項操作。

   ![新增帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

> [!NOTE]
> 完成移轉之後，請繼續閱讀 [增強的文章](site-recovery-vmware-to-azure-classic.md)。 此舊版文章的其餘部分將不再有關，您不需要再依照文章中說明的任何其他步驟**。
>
>

## <a name="what-do-i-need"></a>我需要什麼？
此圖表顯示部署元件。

![新增保存庫](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

以下是您需要的內容：

| **元件** | **部署** | **詳細資料** |
| --- | --- | --- |
| **組態伺服器** |與 Site Recovery 位於相同訂用帳戶中的 Azure 標準 A3 虛擬機器。 |組態伺服器可在 Azure 中的受保護機器、處理序伺服器與主要目標伺服器之間協調通訊。 它會在容錯移轉發生時在 Azure 中設定複寫並協調復原。 |
| **主要目標伺服器** |Azure 虛擬機器 - 以 Windows Server 2012 R2 資源庫映像為基礎的 Windows 伺服器 (用以保護 Windows 機器)，或以 OpenLogic CentOS 6.6 資源庫映像為基礎的 Linux 伺服器 (用以保護 Linux 機器)。<br/><br/> 有三種大小選項可供使用 - 標準 A4、標準 D14 和標準 DS4。<br/><br/> 此伺服器可與相同的 Azure 網路連接，以做為設定伺服器。<br/><br/> 您在 Site Recovery 入口網站中設定 |它會使用在您 Azure 儲存體帳戶中 Blob 儲存體上建立之連結的 VHD，從您的受保護機器接收和保留複寫的資料。<br/><br/> 特別針對設定工作負載的保護選取標準 DS4，該工作負載需要使用進階儲存體帳戶的一致高效能和低延遲。 |
| **處理序伺服器** |執行 Windows Server 2012 R2 的內部部署虛擬或實體伺服器<br/><br/> 建議您將它放置在與您要保護之機器相同的網路與 LAN 區段上，只要受保護的機器具有 L3 網路可見性，它就可以在不同的網路上運作。<br/><br/> 您會在 Site Recovery 入口網站中設定它，並向組態伺服器註冊。 |受保護的機器會傳送複寫資料給內部部署處理序伺服器。 它具有磁碟快取功能，可快取本身接收的複寫資料。 它會對該資料執行一些動作。<br/><br/> 在將資料傳送至主要目標伺服器之前，它會藉由快取、壓縮及加密資料，將資料最佳化。<br/><br/> 它會處理「行動服務」的推送安裝。<br/><br/> 它會執行 VMware 虛擬機器的自動探索。 |
| **內部部署機器** |內部部署 VMware 虛擬機器，或執行 Windows 或 Linux 的實體伺服器。 |您設定要套用至一或多部機器的複寫設定。 您可以將容錯移轉個別機器，或者，更常見的是您聚集到復原方案的多部機器。 |
| **行動服務** |安裝在您想要保護的每個虛擬機器或實體伺服器上<br/><br/> 可手動安裝，或當您為機器啟用複寫時，由處理伺服器自動推送並安裝。 |行動服務會在初始複寫期間將資料傳送到處理序伺服器。 當機器處於受保護的狀態後 (重新同步處理完成之後)，行動服務會擷取記憶體中的磁碟寫入，並傳送到處理序伺服器。 Windows 伺服器的應用程式一致性是利用 VSS 來達成。 |
| **Azure Site Recovery 保存庫** |您使用 Azure 訂用帳戶來建立 Site Recovery 保存庫，並在保存庫中註冊伺服器。 |保存庫可在您的內部部署網站與 Azure 之間協調資料複寫、容錯移轉及復原等作業。 |
| **複寫機制** |**透過網際網路** - 透過網際網路使用安全 SSL/TLS 通道，從受保護的內部部署伺服器到 Azure 進行通訊和資料複寫。 這是預設選項。<br/><br/> **VPN/ExpressRoute** - 透過 VPN 連線，在內部部署伺服器與 Azure 之間進行通訊和資料複寫。 您將必須在內部部署站台與 Azure 網路之間設定站對站 VPN 或 ExpressRoute 連接。<br/><br/> 您將選擇在 Site Recovery 部署期間想要如何複寫。 設定此機制之後，如果變更機制，一定會影響現有機器的複寫。 |這些選項都不需要在受保護機器上開啟任何輸入網路連接埠。 所有的網路通訊是從內部部署站台起始。 |

## <a name="capacity-planning"></a>容量規劃
您需要考量的主要方面︰

* **來源環境**—VMware 基礎結構、來源機器設定和需求。
* **元件伺服器**—處理序伺服器、設定伺服器和主要目標伺服器

### <a name="considerations-for-the-source-environment"></a>來源環境的考量
* **磁碟最大大小**—可以連接至虛擬機器的磁碟的目前大小上限為 1 TB。 因此，可以複寫的來源磁碟的最大大小也是限制為 1 TB。
* **每一來源的最大大小**—單一來源機器的最大大小是 31 TB (含 31 個磁碟)，以及對主要目標伺服器佈建一個 D14 執行個體。
* **每一主要目標伺服器的來源數目**—可以使用單一主要目標伺服器保護的多個來源機器。 不過，無法跨多個主要目標伺服器保護單一來源機器，因為在磁碟複寫時，會在 Azure Blob 儲存體上建立可反映磁碟大小的 VHD，並將其附加至主要目標伺服器做為資料磁碟。  
* **每一來源每日變更率上限**—考慮每一來源建議的變更率時，有三個需要考量的因素。 針對目標式考量，來源上每個作業的目標磁碟需要兩個 IOP。 這是因為目標磁碟上將會發生舊資料的讀取和新資料的寫入。
  * **處理序伺服器支援的每日變更率**—來源機器不能跨越多個處理序伺服器。 單一處理序伺服器可以支援多達 1 TB 的每日變更率。 因此 1 TB 是來源機器支援的每日資料變更率上限。
  * **目標磁碟支援的最大輸送量**—每一來源磁碟的最大變換不能超過 144 GB/天 (以 8K 寫入大小)。 請參閱主要目標小節中的資料表，以取得各種寫入大小的目標的輸送量和 IOP。 必須將此數字除以 2，因為每個來源 IOP 會在目標磁碟上產生 2 個 IOP。 設定進階儲存體帳戶的目標時，請參閱 [Azure 延展性和效能目標](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) 。
  * **儲存體帳戶支援的最大輸送量**—來源不能跨越多個儲存體帳戶。 假設某個儲存體帳戶每秒可接受最多 20,000 個要求，並且每個來源 IOP 會在主要目標伺服器上產生 2 個 IOP，建議您將來源的 IOP 數目保留為 10,000。 設定進階儲存體帳戶的來源時，請參閱 [Azure 延展性和效能目標](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) 。

### <a name="considerations-for-component-servers"></a>元件伺服器的考量
表 1 摘要說明設定和主要目標伺服器的虛擬機器大小。

| **元件** | **部署的 Azure 執行個體** | **核心** | **記憶體** | **最大磁碟** | **磁碟大小** |
| --- | --- | --- | --- | --- | --- |
| 組態伺服器 |標準 A3 |4 |7 GB |8 |1023 GB |
| 主要目標伺服器 |標準 A4 |8 |14 GB |16 |1023 GB |
| 標準 D14 |16 |112 GB |32 |1023 GB | |
| 標準 DS4 |8 |28 GB |16 |1023 GB | |

**表 1**

#### <a name="process-server-considerations"></a>處理序伺服器考量
通常處理序伺服器的大小取決於跨所有受保護的工作負載的每日變更率。

* 您需要足夠的計算才能執行內嵌壓縮和加密之類的工作。
* 處理序伺服器使用磁碟快取。 確定建議的快取空間和磁碟輸送量可供使用，以在加速網路瓶頸或中斷時促進儲存的資料變更。
* 確保有足夠的頻寬，使得處理序伺服器可以將資料上傳到主要目標伺服器，以提供持續的資料保護。

表 2 提供處理序伺服器指導方針的摘要。

| **資料變更率** | **CPU** | **記憶體** | **快取磁碟大小** | **快取磁碟輸送量** | **輸入/輸出頻寬** |
| --- | --- | --- | --- | --- | --- |
| < 300 GB |4 個 vCPU (2 個通訊端 * 雙核心 @ 2.5GHz) |4 GB |600 GB |每秒 7 至 10 MB |30 Mbps/21 Mbps |
| 300 至 600 GB |8 個 vCPU (2 個通訊端 * 四核心 @ 2.5GHz) |6 GB |600 GB |每秒 11 至 15 MB |60 Mbps/42 Mbps |
| 600 GB 至 1 TB |12 個 vCPU (2 個通訊端 * 六核心 @ 2.5GHz) |8 GB |600 GB |每秒 16 至 20 MB |100 Mbps/70 Mbps |
| > 1 TB |部署另一個處理序伺服器 | | | | |

**資料表 2**

其中：

* 輸入是下載頻寬 (來源與處理序伺服器之間的內部網路)。
* 輸出是上傳頻寬 (處理序伺服器與主要目標伺服器之間的網際網路)。 輸出數目假設平均 30% 的處理序伺服器壓縮。
* 針對快取磁碟，建議所有的處理序伺服器的個別作業系統磁碟最少有 128 GB。
* 針對快取磁碟輸送量，使用了下列儲存體進行基準測試：採用 RAID 10 配置的 8 個 10000 轉 RPM 的 SAS 磁碟機。

#### <a name="configuration-server-considerations"></a>設定伺服器考量
每個設定伺服器可以利用 3 到 4 個磁碟區支援多達 100 個來源機器。 如果部署較大，建議您部署另一個組態伺服器。 請參閱表 1 以取得設定伺服器的預設虛擬機器屬性。

#### <a name="master-target-server-and-storage-account-considerations"></a>主要目標伺服器與儲存體帳戶的考量
每個主要目標伺服器的儲存體包括作業系統磁碟、保留磁碟區和資料磁碟。 在 Site Recovery 入口網站中所定義的保留時段持續時間中，保留磁碟機會維護磁碟變更的日誌。  如需虛擬機器伺服器屬性的主要目標，請參閱表 1。 表 3 顯示如何使用 A4 的磁碟。

| **執行個體** | **作業系統磁碟** | **保留** | **資料磁碟** |
| --- | --- | --- | --- |
| **保留** |**資料磁碟** | | |
| 標準 A4 |1 個磁碟 (1 * 1023 GB) |1 個磁碟 (1 * 1023 GB) |15 個磁碟 (15 * 1023 GB) |
| 標準 D14 |1 個磁碟 (1 * 1023 GB) |1 個磁碟 (1 * 1023 GB) |31 個磁碟 (15 * 1023 GB) |
| 標準 DS4 |1 個磁碟 (1 * 1023 GB) |1 個磁碟 (1 * 1023 GB) |15 個磁碟 (15 * 1023 GB) |

**資料表 3**

主要目標伺服器的容量計劃仰賴於：

* Azure 儲存體效能和限制
  * 單一儲存體帳戶中標準層 VM 的高度使用的磁碟數目上限約為 40 (每一磁碟 20,000/500 IOPS)。 請參閱[標準儲存體帳戶的延展性目標](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)和[進階儲存體帳戶的延展性目標](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)。
* 每日變更率
* 保留磁碟區儲存體。

請注意：

* 一個來源不能跨越多個儲存體帳戶。 這會套用到屬於您設定保護時選取的儲存體帳戶的資料磁碟。 作業系統和保留磁碟通常屬於自動部署的儲存體帳戶。
* 需要的保留儲存體磁碟區取決於每日變更率和保留的天數。 每個主要目標伺服器需要的保留儲存體 = 每日來自來源的變換總計 * 保留天數。
* 每個主要目標伺服器只有一個保留磁碟區。 保留磁碟區會在連接到主要目標伺服器的磁碟之間共用。 例如：
  * 如果有一部具備 5 個磁碟的來源機器，並且在來源上的每個磁碟會產生 120 個 IOP (8K 大小)，則這可轉譯為每一磁碟 240 個 IOP (每一來源 IO 目標磁碟上 2 個作業)。 每一磁碟的 IOP 限制 500，在 Azure 內是 240 個 IOP。
  * 在保留磁碟區上，這會變成 120 * 5 = 600 個 IOP，而這可能會變成瓶頸。 在此案例中，加入更多磁碟到保留的磁碟區，並跨越以做為 RAID 等量磁碟區設定是不錯的策略。 因為 IOP 會分散到多個磁碟機，這將可改進效能。 要加入至保留磁碟區的磁碟機數目如下所示：
    * 來自來源環境的 IOP 總計 / 500
    * 每日來自來源環境的變換總計 (未壓縮) / 287 GB。 287 GB 是每日的目標磁碟支援的最大輸送量。 此度量會隨著寫入大小 8K 的因數而異，因為在此情況下，8K 是假設的寫入大小。 例如，如果寫入大小是 4K，則輸送量會是 287/2。 而且如果寫入大小為 16K，那麼輸送量會是 287*2。
* 儲存體帳戶所需的數目 = 來源 IOP 總計 / 10000。

## <a name="before-you-start"></a>開始之前
| **元件** | **需求** | **詳細資料** |
| --- | --- | --- |
| **Azure 帳戶** |您將需要 [Microsoft Azure](https://azure.microsoft.com/) 帳戶。 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 | |
| **Azure 儲存體** |您需要 Azure 儲存體帳戶來儲存複寫的資料<br/><br/> 帳戶應該是[標準異地備援儲存體帳戶](../storage/common/storage-redundancy.md#geo-redundant-storage)或[進階儲存體帳戶](../storage/common/storage-premium-storage.md)。<br/><br/> 此帳戶應與 Azure 站台復原服務位於相同的區域，且與相同的訂閱相關聯。 我們不支援使用 [新的 Azure 入口網站](../storage/common/storage-create-storage-account.md) 來跨資源群組移動所建立的儲存體帳戶。<br/><br/> 若要深入了解，請參閱 [Microsoft Azure 儲存體簡介](../storage/common/storage-introduction.md) | |
| **Azure 虛擬網路** |您需要一部 Azure 虛擬網路來部署組態伺服器與主要目標伺服器。 它應該與 Azure Site Recovery 保存庫位於相同的訂用帳戶和區域中。 如果您想要透過 ExpressRoute 或 VPN 連接來複寫資料，Azure 虛擬網路必須透過 ExpressRoute 連線或站對站 VPN 連接到內部部署網路。 | |
| **Azure 資源** |請確認您有足夠的 Azure 資源以部署所有元件。 如需深入了解，請參閱 [Azure 訂用帳戶限制](../azure-subscription-service-limits.md)。 | |
| **Azure 虛擬機器** |您想要保護的虛擬機器應該要符合 [Azure 必要條件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。<br/><br/> **磁碟計數**—一個受保護的伺服器最多可支援 31 個磁碟<br/><br/> **磁碟大小**—個別磁碟容量不可超過 1023 GB<br/><br/> **叢集**—不支援叢集伺服器<br/><br/> **開機**—不支援整合可延伸韌體介面 (UEFI)/可延伸韌體介面 (EFI) 開機<br/><br/> **磁碟區**—不支援 Bitlocker 加密的磁碟區<br/><br/> **伺服器名稱**—名稱應包含介於 1 到 63 個字元 (字母、數字和連字號)。 名稱必須以字母或數字開頭，並以字母或數字結尾。 機器受到保護之後，您可以修改 Azure 的名稱。 | |
| **組態伺服器** |將在您的訂用帳戶中，為組態伺服器建立以 Azure Site Recovery Windows Server 2012 R2 資源庫映像為基礎的標準 A3 虛擬機器。 它會建立為新的雲端服務中的第一個執行個體。 如果您選取 [公用網際網路] 作為組態伺服器的連線類型，系統將會使用保留的公用 IP 位址來建立雲端服務。<br/><br/> 安裝路徑僅限使用英文字元。 | |
| **主要目標伺服器** |Azure 虛擬機器，標準 A4、D14 或 DS4。<br/><br/> 安裝路徑僅限使用英文字元。 例如，執行 Linux 的主要目標伺服器的路徑應該為 **/usr/local/ASR**。 | |
| **處理序伺服器** |您可以在執行最新版更新之 Windows Server 2012 R2 的實體或虛擬機器上部署處理序伺服器。 在 C:/ 上安裝。<br/><br/> 建議您將此伺服器放在與您要保護的機器相同的網路與子網路上。<br/><br/> 在處理序伺服器上安裝 VMware vSphere CLI 5.5.0。 處理伺服器上必須要有 VMware vSphere CLI 元件，才能探索由 vCenter 伺服器管理的虛擬機器或在 ESXi 主機上執行的虛擬機器。<br/><br/> 安裝路徑僅限使用英文字元。<br/><br/> 不支援 ReFS 檔案系統。 | |
| **VMware** |VMware vCenter 伺服器，管理您的 VMware vSphere Hypervisor。 它應該執行 vCenter 5.1 或 5.5 版 (含最新更新)。<br/><br/> 一或多個 vSphere Hypervisor，包含您要保護的 VMware 虛擬機器。 Hypervisor 應該執行 ESX/ESXi 5.1 或 5.5 版 (含最新更新)。<br/><br/> VMware 虛擬機器應該安裝 VMware 工具且在執行中。 | |
| **Windows 機器** |執行 Windows 的受保護實體伺服器或 VMware 虛擬機器有一些需求。<br/><br/> 支援的 64 位元作業系統：**Windows Server 2012 R2**、**Windows Server 2012** 或 **Windows Server 2008 R2 (至少為 SP1)**。<br/><br/> 主機名稱、掛接點、裝置名稱、Windows 系統路徑 (例如：C:\Windows) 僅限使用英文。<br/><br/> 作業系統應該安裝在 C:\ 磁碟機上。<br/><br/> 僅支援基本磁碟。 不支援動態磁碟。<br/><br/> 受保護機器上的防火牆規則應該允許它們連線到 Azure 中的設定和主要目標伺服器。p><p>您必須提供系統管理員帳戶 (必須是 Windows 機器上的本機系統管理員)，以在 Windows 伺服器上推入安裝行動服務。 如果提供的帳戶是非網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。 若要執行此動作，請在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下加入值為 1 的 LocalAccountTokenFilterPolicy DWORD 登錄項目。 若要從 CLI 加入登錄項目，請開啟 cmd 或 powershell 並輸入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。 [深入了解](https://msdn.microsoft.com/library/aa826699.aspx)存取控制。<br/><br/> 容錯移轉之後，如果您想要使用遠端桌面連線到 Azure 中的 Windows 虛擬機器，請確認內部部署機器的遠端桌面已啟用。 如果您不透過 VPN 連線，防火牆規則應該允許透過網際網路的遠端桌面連線。 | |
| **Linux 機器** |支援的 64 位元作業系統：**Centos 6.4、6.5、6.6**；**Oracle Enterprise Linux 6.4、6.5 (執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3))**、**SUSE Linux Enterprise Server 11 SP3**。<br/><br/> 受保護機器上的防火牆規則應該允許它們連線到 Azure 中的設定和主要目標伺服器。<br/><br/> 受保護機器上的 /etc/hosts 檔案應該包含將本機主機名稱對應到所有 NIC 相關聯之 IP 位址的項目 <br/><br/> 如果您想要在容錯轉移之後使用安全殼層用戶端 (ssh) 連線到執行 Linux 的 Azure 虛擬機器，請確認受保護機器上的安全殼層服務已設為在系統開機時自動啟動，且防火牆規則允許 ssh 與其連線。<br/><br/> 主機名稱、掛接點、裝置名稱和 Linux 系統路徑和檔案名稱 (eg /etc/; /usr) 僅可使用英文。<br/><br/> 可以利用下列儲存體啟用內部部署機器的保護：<br>檔案系統：EXT3、ETX4、ReiserFS、XFS<br>多重路徑軟體裝置對應程式 (多重路徑)<br>磁碟區管理員：LVM2<br>不支援使用 HP CCISS 控制站儲存體的實體伺服器。 | |
| **第三方** |在這個案例中某些部署元件取決於第三方廠商軟體才能正常運作。 如需完整清單，請參閱 [第三方廠商軟體注意事項和資訊](#third-party) | |

### <a name="network-connectivity"></a>網路連線
有兩個選項可設定您的內部部署網站與部署您的基礎結構元件 (設定伺服器、主要目標伺服器) 所在的 Azure 虛擬網路之間的網路連線。 您必須決定要使用的網路連線選項，之後才能部署組態伺服器。 您必須在部署時選擇這個設定。 稍後無法變更。

**網際網路：**內部部署伺服器 (處理伺服器、受保護的機器) 與 Azure 基礎結構元件伺服器 (組態伺服器、主要目標伺服器) 之間的通訊和資料複寫會透過安全 SSL/TLS 連線，以從內部部署環境到組態伺服器及主要目標伺服器上的公用端點方向進行。 (唯一的例外狀況是 TCP 連接埠 9080 上處理序伺服器與主要目標伺服器之間的連線未加密。 在此連接上只有與用來設定複寫相關的複寫通訊協定相關的控制資訊會加以交換。)

![部署圖表網際網路](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**：內部部署伺服器 (處理伺服器、受保護的機器) 與 Azure 基礎結構元件伺服器 (組態伺服器、主要目標伺服器) 之間的通訊和資料複寫會透過 VPN 連線，在內部部署網路與部署了組態伺服器及主要目標伺服器的 Azure 虛擬網路之間進行。 確定您的內部部署網路透過 ExpressRoute 連線或站對站 VPN 連線連接到 Azure 虛擬網路。

![部署圖表 VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)

## <a name="step-1-create-a-vault"></a>步驟 1：建立保存庫
1. 登入 [管理入口網站](https://portal.azure.com)。
2. 展開 [資料服務]  >  [復原服務]，然後按一下 [Site Recovery 保存庫]。
3. 按一下 [新建]  >  [快速建立]。
4. 在 [ **名稱**] 中，輸入保存庫的易記識別名稱。
5. 在 [區域] 中，選取保存庫的地理區域。 若要查看支援的地區，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)
6. 按一下 [建立保存庫] 。

    ![新增保存庫](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

檢查狀態列，以確認是否順利建立保存庫。 保存庫在主要 [復原服務] 頁面上會列為 [使用中]。

## <a name="step-2-deploy-a-configuration-server"></a>步驟 2：部署設定伺服器
### <a name="configure-server-settings"></a>設定伺服器設定
1. 在 [復原服務]  頁面中，按一下保存庫以開啟 [快速啟動] 頁面。 您也可以使用圖示隨時開啟 [快速入門]。

    ![快速啟動圖示](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)
2. 在下拉式清單中，選取 [具有 VMware/實體伺服器的內部部署網站與 Azure 之間] 。
3. 在 [準備目標 (Azure) 資源] 按一下 [部署組態伺服器]。

    ![部署設定伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)
4. 在 [新的組態伺服器詳細資料]  中指定：

   * 設定伺服器的名稱和連接到它的認證。
   * 在 [網路連接類型] 下拉式清單中，選取 [公用網際網路] 或 [VPN]。 請注意，套用此設定後，就無法修改此設定。
   * 選取伺服器所在地方的 Azure 網路。 如果您使用 VPN，請確定 Azure 網路如預期般連線到內部部署網路。
   * 指定要指派給伺服器的內部 IP 位址與子網路。 請注意，任何子網路中的前四個 IP 位址是保留給內部 Azure 使用。 使用任何其他可用的 IP 位址。

     ![部署設定伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)
5. 當您按一下 [確定]  ，會在您的訂用帳戶中為組態伺服器建立以 Azure Site Recovery Windows Server 2012 R2 資源庫映像為基礎的標準 A3 虛擬機器。 它會建立為新的雲端服務中的第一個執行個體。 如果您選擇透過網際網路來連線，則會使用保留的公用 IP 位址建立雲端服務。 您可以在 [工作]  索引標籤中監視進度。

    ![監視進度](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)
6. 如果您是透過網際網路來連線，在部署組態伺服器之後，請記下 Azure 入口網站的 [虛擬機器] 頁面上指派給此伺服器的公用 IP 位址。 然後在 [端點] 索引標籤上注意對應至私人連接埠 443 的公用 HTTPS 連接埠。 稍後當您在主要目標與處理序伺服器上註冊此設定伺服器的時候，您會需要這個資訊。 這個設定伺服器利用這些端點進行部署：

   * HTTPS：公用連接埠是用於透過網際網路協調元件伺服器與 Azure 之間的通訊。 私用連接埠 443 是用來透過 VPN 協調元件伺服器與 Azure 之間的通訊。
   * 自訂：公用連接埠用於透過網際網路的容錯回復工具通訊。 私人連接埠 9443 是用於透過 VPN 的容錯回復工具通訊。
   * PowerShell：私人連接埠 5986
   * 遠端桌面：私人連接埠 3389

   ![VM 端點](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

   > [!WARNING]
   > 切勿刪除或變更設定伺服器部署期間建立的任何端點的公用或私用連接埠編號。
   >
   >

設定伺服器使用保留的 IP 位址部署在自動建立的 Azure 雲端服務。 需要此保留位址，才能確保雲端服務上的虛擬機器 (包括組態伺服器) 重新開機後，組態伺服器雲端服務 IP 位址仍是相同的。 當解除委任設定伺服器時，將必須手動將保留的公用 IP 位址取消保留，否貝它仍將維持保留。 每一訂用帳戶預設的保留公用 IP 位址數目限制為 20 個。 [深入了解](../virtual-network/virtual-networks-reserved-private-ip.md) 保留的 IP 位址。

### <a name="register-the-configuration-server-in-the-vault"></a>在保存庫中註冊設定伺服器
1. 在 [快速入門] 頁面中，按一下 [準備目標資源]  >  [下載註冊金鑰]。 金鑰檔案會隨即自動產生。 該金鑰在產生後會維持 5 天有效。 將它複製到設定伺服器。
2. 在 [虛擬機器]  中，從虛擬機器清單選取組態伺服器。 開啟 [儀表板] 索引標籤，然後按一下 [連接]。  下載的 RDP 檔案以使用遠端桌面登入組態伺服器。 如果您使用 VPN，請使用內部 IP 位址 (您部署組態伺服器時所指定的位址) 從內部部署網站進行遠端桌面連線。 當您第一次登入時，Azure Site Recovery 設定伺服器安裝精靈會自動執行。

    ![註冊](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)
3. 在 [協力廠商軟體安裝] 中，按一下 [我接受] 來下載並安裝 MySQL。

    ![MySQL 安裝](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)
4. 在 [MySQL 伺服器詳細資料]  中，建立認證來登入 MySQL 伺服器執行個體。

    ![MySQL 認證](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)
5. 在 [網際網路設定]  中，指定組態伺服器將連線到網際網路的方式。 請注意：

   * 如果您想要使用自訂 proxy，您應該在安裝提供者之前進行設定。
   * 當您按 [下一步]  時，將會執行測試來檢查 Proxy 連線。
   * 如果您使用自訂 Proxy，或者您的預設 Proxy 需要驗證，您必須輸入 Proxy 詳細資料，包含位址、連接埠和認證。
   * 下列 URL 應可透過 Proxy 存取：
     * *.hypervrecoverymanager.windowsazure.com
     * *.accesscontrol.windows.net
     * *.backup.windowsazure.com
     * *.blob.core.windows.net
     * *.store.core.windows.net
   * 如果您有以 IP 位址為基礎的防火牆規則，請確保規則均設定為允許組態伺服器可與 [Azure 資料中心 IP 範圍](https://msdn.microsoft.com/library/azure/dn175718.aspx) 和 HTTPS (443) 通訊協定中所述的 IP 位址通訊。 您必須具有打算使用以及美國西部之 Azure 區域的白名單 IP 範圍。

     ![Proxy 註冊](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)
6. 在 [提供者錯誤訊息當地語系化設定]  中指定顯示錯誤訊息的語言。

    ![錯誤訊息註冊](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)
7. 在 [Azure Site Recovery 註冊]  中瀏覽並選取複製到伺服器的金鑰檔。

    ![金鑰檔註冊](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)
8. 在精靈的完成頁面上選取這些選項：

   * 選取 [啟動帳戶管理對話方塊]  ，指定在完成精靈後應該開啟 [管理帳戶] 對話方塊。
   * 選取 [為 Cspsconfigtool 建立桌面圖示] 以在組態伺服器上加入桌面捷徑，讓您可以在任何時間開啟 [管理帳戶] 對話方塊，而不必重新執行精靈。

     ![完成註冊](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)
9. 按一下 [完成]  來完成精靈。 隨即產生複雜密碼。 將其複製到安全的位置。 您在設定伺服器上驗證與註冊處理序與主要目標伺服器時將會需要這個密碼。 這個密碼也可以用來確認設定伺服器通訊中的通道完整性。 您可以重新產生複雜密碼，但是屆時您也必須以新的複雜密碼再次註冊主要目標與處理序伺服器。

    ![複雜密碼](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

註冊完成後，組態伺服器會列在保存庫的 [組態伺服器]  頁面上。

### <a name="set-up-and-manage-accounts"></a>設定和管理帳戶
在部署期間，Site Recovery 會對下列動作要求認證：

* VMware 帳戶，讓 Site Recovery 可自動探索 vCenter 伺服器或 vSphere 主機上的 VM。
* 當您加入機器以進行保護時，如此 Site Recovery 即可以在機器上安裝行動服務。

註冊組態伺服器之後，您可以開啟 [管理帳戶]  對話方塊來新增及管理應該用於這些動作的帳戶。 有好幾種方法可執行這項操作：

* 針對設定伺服器 (cspsconfigtool) 安裝程式最後一頁上的對話方塊，開啟您選擇建立的捷徑。
* 開啟完成設定伺服器安裝程式上的對話方塊。

1. 在 [選取機器]  click 。 您也可以修改並刪除現有的帳戶。

    ![管理帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)
2. 在 [帳戶詳細資料]  中，指定要用於 Azure 的帳戶名稱和認證 (網域/使用者名稱)。

    ![管理帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>連線到設定伺服器
有兩種方式可以連線到設定伺服器：

* 透過 VPN 站對站或 ExpressRoute 連線
* 透過網際網路

請注意：

* 網際網路連線使用虛擬機器的端點搭配伺服器的公用虛擬 IP 位址。
* VPN 連線會使用伺服器的內部 IP 位址與端點私人連接埠。
* 它是一次性的決策，決定是否要從您的內部部署伺服器透過 VPN 連線或網際網路連接 (控制和複寫資料) 到在 Azure 中執行的各種元件伺服器 (設定伺服器、主要目標伺服器)。 您之後無法變更此設定。 如果您這樣做，將需要重新部署案例並重新保護您的機器。  

## <a name="step-3-deploy-the-master-target-server"></a>步驟 3：部署主要目標伺服器
1. 按一下 [準備目標 (Azure) 資源]  >  [部署主要目標伺服器]。
2. 指定主要目標伺服器的詳細資料和認證。 伺服器將部署在與組態伺服器相同的 Azure 網路中。 當您按一下以完成動作時，隨即會建立附帶 Windows 或 Linux 資源庫映像的 Azure 虛擬機器。

    ![目標伺服器設定](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

請注意，任何子網路中的前四個 IP 位址是保留給內部 Azure 使用。 指定任何其他可用的 IP 位址。

> [!NOTE]
> 針對需要一致的高 I/O 效能和低延遲的工作負載，在為其設定保護時，請選取 [標準 DS4]，以便使用 [進階儲存體帳戶](../storage/common/storage-premium-storage.md)來裝載需要大量 I/O 的工作負載。
>
>

1. 利用這些端點建立 Windows 主要目標伺服器 VM。 請注意，只有當您透過網際網路連線時，才會建立公用端點。

   * 自訂：處理序伺服器使用公用連接埠，透過網際網路傳送複寫資料。 處理序伺服器可使用私人連接埠 9443 透過 VPN 傳送複寫資料給主要目標伺服器。
   * 自訂1：處理序伺服器使用公用連接埠，透過網際網路傳送中繼資料。 處理序伺服器使用私用連接埠 9080，透過 VPN 將中繼資料傳送至主要目標伺服器。
   * PowerShell：私人連接埠 5986
   * 遠端桌面：私人連接埠 3389
2. 利用這些端點建立 Linux 主要目標伺服器 VM。 請注意，只有當您透過網際網路連線時，才會建立公用端點。

   * 自訂：處理序伺服器使用公用連接埠，透過網際網路傳送複寫資料。 處理序伺服器可使用私人連接埠 9443 透過 VPN 傳送複寫資料給主要目標伺服器。
   * 自訂1：處理序伺服器使用公用連接埠，透過網際網路傳送中繼資料。 處理序伺服器使用私用連接埠 9080，透過 VPN 將中繼資料傳送至主要目標伺服器
   * SSH：私人連接埠 22

     > [!WARNING]
     > 切勿刪除或變更主要目標伺服器部署期間建立的任何端點的公用或私用通訊埠編號。
     >
     >
3. 在 [虛擬機器]  中，等待虛擬機器啟動。

   * 如果它是 Windows 伺服器，請記下遠端桌面詳細資料。
   * 如果它是 Linux 伺服器，而且您透過 VPN 來連線，請記下虛擬機器的內部 IP 位址。 如果您透過網際網路連線，請注意公用 IP 位址。
4. 登入伺服器以完成安裝，並在設定伺服器上註冊該伺服器。
5. 如果您在執行 Windows：

   1. 啟動與虛擬機器的遠端桌面連線。 第一次登入指令碼會在 PowerShell 視窗中執行。 不要關閉它。 完成時，主機代理程式設定工具會自動開啟註冊伺服器。
   2. 在 [主機代理程式設定]  中，指定組態伺服器的內部 IP 位址與連接埠 443。 即使您並非透過 VPN 連線，您還是可以使用內部位址與私人連接埠 443，因為虛擬機器附加於和設定伺服器相同的 Azure 網路。 讓 [使用 HTTPS]  保持啟用狀態。 輸入您先前所記下的設定伺服器複雜密碼。 按一下 [確定]  以註冊伺服器。 您可以忽略 NAT 選項。 它們不會被使用。
   3. 如果您估計的保留磁碟機需求超過 1 TB，您可以使用虛擬磁碟和 [儲存空間](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)

   ![Windows 主要目標伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)
6. 如果您在執行 Linux：

   1. 在安裝主要目標伺服器之前，請確定您已安裝最新的 Linux Integration Services (LIS)。 您可以在 [這裡](https://www.microsoft.com/download/details.aspx?id=46842)找到最新版本的 LIS 以及安裝指示。 LIS 安裝之後重新啟動電腦。
   2. 在 [準備目標 (Azure) 資源] 中，按一下 [下載及安裝其他軟體 (僅適用於 Linux 主要目標伺服器)]。 將下載的 tar 檔案複製到使用 sftp 用戶端的虛擬機器。 或者，您也可以登入已部署的 Linux 主要目標伺服器，然後使用 *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* 來下載檔案。
   3. 使用安全殼層用戶端登入伺服器。 如果您已透過 VPN 連線到 Azure 網路，請使用內部 IP 位址。 否則請使用外部 IP 位址與 SSH 公用端點。
   4. 將檔案從 Gzip 安裝程式解壓縮，方法是執行：**tar –xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
      ![Linux 主要目標伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
   5. 請確認您在解壓縮 tar 檔案內容的目錄中。
   6. 使用命令 **echo *`<passphrase>`* >passphrase.txt** 將組態伺服器的複雜密碼複製到本機檔案
   7. 執行 “**sudo ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i *`<Configuration server internal IP address>`* -p 443 -s y -c https -P passphrase.txt**” 命令。

      ![註冊目標伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)
7. 等候幾分鐘 (10-15)，然後在頁面上，檢查主要目標伺服器在 [伺服器]  >  [組態伺服器] > [伺服器詳細資料] 索引標籤上是否列為已註冊。如果您在執行 Linux 而且伺服器並未註冊，請再次從 /usr/local/ASR/Vx/bin/hostconfigcli 執行主機設定工具。 您必須藉由執行 chmod 做為根使用者，以設定存取權限。

    ![確認目標伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

> [!NOTE]
> 註冊完成之後，最多需要 15 分鐘，入口網站才會列出主要目標伺服器。 若要立刻更新，請在 [組態伺服器]  on the  。
>
>

## <a name="step-4-deploy-the-on-premises-process-server"></a>步驟 4：部署內部部署處理序伺服器
開始之前，建議您在處理序伺服器上設定靜態 IP 位址，以保障在重新開機後可持續使用相同位址。

1. 按一下 [快速啟動] > [安裝處理序伺服器內部部署]  >  [下載與安裝處理序伺服器]。

    ![安裝處理序伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)
2. 將下載的 zip 檔案複製到您要安裝處理序伺服器的伺服器。 zip 檔案包含兩個安裝檔案：

   * Microsoft-ASR_CX_TP_8.4.0.0_Windows*
   * Microsoft-ASR_CX_8.4.0.0_Windows*
3. 解壓縮封存，並將安裝檔案複製到伺服器上的位置。
4. 執行 **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** 安裝檔案並遵循指示。 這樣可以安裝部署所需第三方廠商元件。
5. 然後執行 **Microsoft-ASR_CX_8.4.0.0_Windows***。
6. 在 [伺服器模式] 頁面上，選取 [處理序伺服器]。
7. 在 [環境詳細資料]  頁面執行下列動作：

    - 如果您想要保護 VMware 虛擬機器，請按一下  **是**
    - 如果您只想要保護實體伺服器，因此不需要在處理序伺服器上安裝 VMware vCLI。 按一下 [否]  並繼續。

1. 安裝 VMware vCLI 時，請注意下列事項：

   * **僅支援 VMware vSphere CLI 5.5.0**。 處理序伺服器無法與 vSphere CLI 的其他版本或更新搭配使用。
   * 從 [這裡](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
   * 如果您正好在您開始安裝處理序伺服器之前安裝 vSphere CLI，並且安裝程式未偵測到它，請等候最多五分鐘的時間，再重新嘗試安裝。 這可確保 vSphere CLI 偵測需要的所有環境變數已正確初始化。
2. 在 [處理序伺服器的 NIC 選取範圍]  中，選取處理序伺服器應該使用的網路介面卡。

   ![選取配接器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)
3. 在 [組態伺服器詳細資料] 中：

   * 針對 IP 位址和連接埠，如果您透過 VPN 連線，請指定設定伺服器的內部 IP 位址以及連接埠 443。 否則，請指定公用虛擬 IP 位址和對應的公用 HTTP 端點。
   * 輸入設定伺服器的複雜密碼。
   * 如果您想要在使用自動推入安裝服務時停用驗證，請清除 [驗證行動服務軟體簽章]  。 簽章驗證需要處理序伺服器的網際網路連線能力。
   * 按 [下一步] 。

   ![註冊設定伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)
4. 在 [選取安裝磁碟機]  中，選取快取磁碟機。 處理序伺服器需要至少有 600 GB 可用空間的快取磁碟機。 然後按一下 [安裝] 。

   ![註冊設定伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)
5. 請注意，您可能需要重新啟動伺服器才能完成安裝。 在 [選取機器] **Configuration Server** >  中，檢查已出現處理序伺服器，並在保存庫中成功註冊。

> [!NOTE]
> 註冊完成之後，最多需要 15 分鐘，設定伺服器下才會列出處理伺服器。 若要立即更新，請按一下設定伺服器頁面底部的重新整理按鈕，以重新整理設定伺服器
>
>

![驗證處理序伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

如果您在註冊處理序伺服器的時候未停用行動服務簽章驗證，您可以稍後再依下列說明停用簽章：

1. 以系統管理員身分登入處理序伺服器，並開啟 C:\pushinstallsvc\pushinstaller.conf 檔案進行編輯。 在 [PushInstaller.transport] 區段下加入下面這行：**SignatureVerificationChecks ="0"**。 儲存並關閉檔案。
2. 重新啟動 InMage PushInstall 服務。

## <a name="step-5-update-site-recovery-components"></a>步驟 5：更新 Site Recovery 元件
Site Recovery 元件會不定時更新。 當有新的更新可用時，您應該依下列順序安裝更新︰

1. 組態伺服器
2. 處理序伺服器
3. 主要目標伺服器
4. 容錯回復工具 (vContinuum)

### <a name="obtain-and-install-the-updates"></a>取得並安裝更新
1. 您可以從 Site Recovery [儀表板] 取得組態伺服器、處理伺服器及主要目標伺服器的更新。 針對 Linux 安裝，請從 Gzip 安裝程式解壓縮檔案，並執行命令 “sudo ./install” 來安裝更新。
2. [下載](http://go.microsoft.com/fwlink/?LinkID=533813) 容錯回復工具 (vContinuum) 的最新更新。
3. 如果是執行已安裝行動服務的虛擬機器或實體伺服器，您可以取得服務的更新，如下所示：

   * **選項 1**：下載更新：
     * [Windows Server (僅限 64 位元)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
     * [CentOS 6.4、6.5、6.6 (僅限 64 位元)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
     * [Oracle Enterprise Linux 6.4、6.5 (僅限 64 位元)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
     * [SUSE Linux Enterprise Server SP3 (僅限 64 位元)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
     * 在更新處理序伺服器之後，處理序伺服器上的 C:\pushinstallsvc\repository 資料夾中就有更新版本的行動服務可用。
   * **選項 2**：如果您有一部安裝了舊版行動服務的機器，則您可以從管理入口網站自動升級該機器上的行動服務。

     1. 請確定處理序伺服器已更新。
     2. 請確定受保護的機器符合自動推送行動服務的 [必要條件](#install-the-mobility-service-automatically) ，以便可以依預期方式進行更新。
     3. 選取保護群組、反白顯示受保護的機器，然後按一下 [更新行動服務] 。 只有在有更新版本的行動服務時，此按鈕才可用。

         ![選取 vCenter 伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

在 [選取帳戶] 中，指定用來更新受保護的伺服器上的行動服務的系統管理員帳戶。 按一下 [確定] 並等待觸發的工作完成。

## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>步驟 6：新增 vCenter 伺服器或 vSphere 主機
1. 按一下 [伺服器]  >  [組態伺服器] > 選取組態伺服器 > [新增 vCenter Server]，以新增 vCenter 伺服器或 vSphere 主機。

    ![選取 vCenter 伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)
2. 指定伺服器或主機的詳細資料，並選取將用來探索此伺服器或主機的處理序伺服器。

   * 如果 vCenter 伺服器不在預設連接埠 443 上執行，請指定 vCenter 伺服器執行所在的連接埠號碼。
   * 處理序伺服器必須與 vCenter 伺服器/vSphere 主機位於相同的網路上，而且應該安裝 VMware vSphere CLI 5.5.0。

     ![vCenter 伺服器設定](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)
3. 探索完成之後，vCenter 伺服器將會列在組態伺服器詳細資料之下。

    ![vCenter 伺服器設定](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)
4. 如果您使用非系統管理員帳戶來加入伺服器或主機，請確定帳戶具有下列權限：

   * vCenter 帳戶應該已啟用資料中心、資料存放區、資料夾、主機、網路、資源、儲存體檢視、虛擬機器和 vSphere 分散式切換權限。
   * vSphere 主機帳戶應該已啟用資料中心、資料存放區、資料夾、主機、網路、資源、虛擬機器和 vSphere 分散式交換器權限

## <a name="step-7-create-a-protection-group"></a>步驟 7：建立保護群組
1. 開啟 [受保護的項目]  >  [保護群組]  >  [建立保護群組]。

    ![建立保護群組](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)
2. 在 [指定保護群組設定]  頁面上，指定群組名稱，並且選取您要建立群組的設定伺服器。

    ![保護群組設定](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)
3. 在 [指定複寫設定]  頁面上，進行將用於群組中所有機器的複寫設定。

    ![保護群組複寫](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)
4. 設定：

   * **多部 VM 一致性**：如果您開啟這項功能，則會在保護群組中的機器之間，建立共用的應用程式一致復原點。 當保護群組中的所有機器都執行相同的工作負載時，這項設定就可以發揮最高的重要性。 所有機器都將復原到相同的資料點。 僅適用於 Windows 伺服器。
   * **RPO 臨界值**：當連續資料保護複寫 RPO 超過設定的 RPO 臨界值時，將會產生警示。
   * **復原點保留期**：指定保留週期。 受保護的機器可以復原到這個週期內的任意點。
   * **應用程式一致的快照頻率**：指定建立包含應用程式一致快照的復原點的頻率。

您可以監視保護群組，因為它們是建立在 [受保護項目]  頁面上。

## <a name="step-8-set-up-machines-you-want-to-protect"></a>步驟 8：設定您想要保護的電腦
您必須在您想要保護的虛擬機器和實體伺服器上安裝行動服務。 您可以使用兩種方式執行此動作：

* 從處理序伺服器自動推入並在每一部機器上安裝服務。
* 手動安裝服務。

### <a name="install-the-mobility-service-automatically"></a>自動安裝行動服務
當您將機器加入保護群組時，行動服務會自動由處理序伺服器推入並安裝於每部機器上。

**自動推入會在 Windows 伺服器上安裝行動服務：**

1. 如 [步驟 5：安裝最新的更新](#step-5-install-latest-updates)所述，安裝處理序伺服器的最新更新，並確定處理序伺服器可用。
2. 確保來源機器和處理序伺服器之間具有網路連線，且可從處理序伺服器存取來源機器。  
3. 設定 Windows 防火牆，允許**檔案及印表機共用**和 **Windows Management Instrumentation**。 在 Windows 防火牆設定中，選取 [允許應用程式或功能通過防火牆] 選項，並選取應用程式，如下圖所示。 針對隸屬於網域中的機器，您可以利用 GPO 設定防火牆原則。

    ![防火牆設定](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)
4. 用來執行推入安裝的帳戶必須在您要保護之機器的系統管理員群組中。 這些認證僅適用於推入行動服務的安裝，並且您會在將電腦加入保護群組時提供它們。
5. 如果提供的帳戶不是網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。 若要執行此動作，請在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下加入值為 1 的 LocalAccountTokenFilterPolicy DWORD 登錄項目。 若要從 CLI 加入登錄項目，請開啟 cmd 或 powershell 並輸入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。

**自動推入會在 Linux 伺服器上安裝行動服務：**

1. 如 [步驟 5：安裝最新的更新](#step-5-install-latest-updates)所述，安裝處理序伺服器的最新更新，並確定處理序伺服器可用。
2. 確保來源機器和處理序伺服器之間具有網路連線，且可從處理序伺服器存取來源機器。  
3. 確認帳戶是來源 Linux 伺服器上的根使用者。
4. 確保來源 Linux 伺服器上的 /etc/hosts 檔案包含將本機主機名稱對應到所有 NIC 相關聯之 IP 位址的項目。
5. 在您想要保護的電腦上安裝最新的 openssh、openssh 伺服器、openssl 套件。
6. 請確定 SSH 已啟用且正在連接埠 22 上執行。
7. 在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證，如下所示：

   * a) 以 root 的身分登入。
   * b) 在 /etc/ssh/sshd_config 檔案中，尋找以 **PasswordAuthentication** 開頭的那一行。
   * c) 取消註解該行，並將值從 “no” 變更為 “yes”。

       ![Linux 行動性](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)
   * d) 尋找以 Subsystem 開頭的行並取消註解該行。

       ![Linux 推播行動性](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    
8. 確定支援來源機器 Linux 變異。

### <a name="install-the-mobility-service-manually"></a>手動安裝行動服務
用來安裝行動服務使用的軟體套件位於處理序伺服器上的 C:\pushinstallsvc\repository 中。 登入處理序伺服器，並將適當的安裝套件複製到來源電腦，根據下表：

| 來源作業系統 | 處理序伺服器上的行動服務封裝 |
| --- | --- |
| Windows Server (僅限 64 位元) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe` |
| CentOS 6.4、6.5、6.6 (僅限 64 位元) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3 (僅限 64 位元) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz` |
| Oracle Enterprise Linux 6.4、6.5 (僅限 64 位元) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz` |

**若要在 Windows 伺服器上手動安裝行動服務**，請執行下列動作：

1. 從上表所列的處理序伺服器目錄路徑將 **Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** 套件複製到來源機器。
2. 在來源電腦上執行可執行檔來安裝行動服務。
3. 遵循安裝程式的指示。
4. 選取 [行動服務] 做為角色，然後按 [下一步]。

    ![安裝行動服務](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)
5. 將安裝目錄保留為預設安裝路徑，然後按一下 [安裝] 。
6. 在 [主機代理程式設定]  中，指定組態伺服器的 IP 位址與 HTTPS 連接埠。

   * 如果您是透過網際網路連線，請指定公用虛擬 IP 位址和公用 HTTPS 端點做為連接埠。
   * 如果您是透過 VPN 連接，請指定內部 IP 位址和連接埠 443。 讓 [使用 HTTPS]  保持勾選狀態。

     ![安裝行動服務](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)
7. 指定組態伺服器複雜密碼，然後按一下 [確定]  來向組態伺服器註冊行動服務。

**若要從命令列執行：**

1. 將複雜密碼從 CX 複製到伺服器上的 "C:\connection.passphrase" 檔案，並執行此命令。 在我們的範例中，CX 是 104.40.75.37 和 HTTPS 連接埠是 62519：

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**在 Linux 伺服器上手動安裝行動服務**：

1. 根據上表，將適當的 tar 封存檔從將處理序伺服器複製到來源機器。
2. 開啟殼層程式，並藉由執行 `tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. 從殼層輸入 *`echo <passphrase> >passphrase.txt`* ，在解壓縮 tar 封存檔內容的本機目錄中建立 passphrase.txt 檔案。
4. 輸入 *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*來安裝行動服務。
5. 指定 IP 位址和連接埠：

   * 如果您是透過網際網路連接到設定伺服器，請在 `<IP address>` 和 `<port>` 中指定組態伺服器的虛擬公用 IP 位址和公用 HTTPS 端點。
   * 如果您要透過 VPN 連線連接，請指定內部 IP 位址和 443。

**若要從命令列執行**：

1. 將複雜密碼從 CX 複製到伺服器上的 "passphrase.txt"檔案，並執行此命令。 在我們的範例中，CX 是 104.40.75.37 和 HTTPS 連接埠是 62519：

若要在實際執行伺服器上安裝：

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

若要在目標伺服器上安裝：

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

> [!NOTE]
> 當您加入機器到已在執行適當版本行動服務的保護群組時，那麼會略過推入安裝。
>
>

## <a name="step-9-enable-protection"></a>步驟 9：啟用保護
若要啟用保護，您會將虛擬機器和實體伺服器加入至保護群組。 開始之前，請注意：

* 系統會每隔 15 分鐘探索虛擬機器一次，而且在探索之後，需要最多 15 分鐘的時間，虛擬機器才會會出現在 Azure Site Recovery 中。
* 虛擬機器上的環境變更 (例如 VMware 工具安裝) 也可能需要最多 15 分鐘的時間，才能在 Site Recovery 中更新。
* 您可以在 [組態伺服器] 頁面上 vCenter 伺服器/ESXi 主機的 [上次連絡時間] 欄位中檢查上次探索的時間。
* 如果您已建立保護群組，並新增 vCenter Server 或 ESXi 主機之後，需要 15 分鐘，Azure Site Recovery 入口網站才會重新整理並且在 [將機器加入至保護群組]  對話方塊中列出虛擬機器。
* 如果您想要立即繼續將機器加入至保護群組，而不想等候排程的探索，請反白顯示設定伺服器 (不要按它)，然後按一下 [重新整理]  按鈕。
* 當您將虛擬機器或實體機器加入保護群組時，處理序伺服器會自動推入行動服務並在來源伺服器上安裝 (如果尚未安裝)。
* 若要讓自動推入機制運作，請確認已將受保護機器如上一個步驟所述進行設定。

如下所示加入機器：

1. 按一下 [受保護的項目]  >  [保護群組]  >  [機器]  >  [新增機器]。 做為最佳做法，我們建議保護群組應反映您的工作負載，這樣一來您才可以將執行特定應用程式的機器加入相同的群組。
2. 在 [選取虛擬機器] 中，如果您要保護實體伺服器，在 [加入實體機器] 精靈中，提供 IP 位址和易記名稱。 然後選取作業系統系列。

    ![加入 V-Center 伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)
3. 在 [選取虛擬機器]  中，如果您要保護 VMware 虛擬機器，請選取正在負責管理您的虛擬機器 (或其執行所在的 EXSi 主機) 的 vCenter 伺服器，然後選取機器。

    ![加入 V-Center 伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png)    
4. 在 [指定目標資源]  中，選取要用於複寫主要目標伺服器和儲存體，並選取設定是否應該用於所有工作負載。 設定工作負載的保護時選取 [進階儲存體帳戶](../storage/common/storage-premium-storage.md) ，該工作負載需要一致高 IO 效能和低延遲，以裝載需要大量 IO 的工作負載。 如果您希望針對您的工作負載磁碟使用進階儲存體帳戶，您必須使用 DS 系列的主要目標。 您無法搭配使用進階儲存體磁碟與非 DS 系列的主要目標。

   > [!NOTE]
   > 我們不支援使用 [新的 Azure 入口網站](../storage/common/storage-create-storage-account.md) 來跨資源群組移動所建立的儲存體帳戶。
   >
   >

    ![vCenter 伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)
5. 在 [指定帳戶]  中，選取您要於在受保護的機器上安裝行動服務的帳戶。 需要帳戶認證，才能自動安裝行動服務。 如果您無法選取帳戶，請確定您如步驟 2 中所述設定一個。 請注意，Azure 無法存取此帳戶。 對於 Windows 伺服器而言，帳戶應該具有來源伺服器上的系統管理員權限。 若為 Linux，帳戶必須是 root。

    ![Linux 認證](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)
6. 按一下核取記號，以完成將機器加入保護群組，並啟動每部機器的初始複寫。 您可以在 [工作]  頁面上監視狀態。

    ![加入 V-Center 伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)
7. 此外，您可以監視保護狀態，方法是按一下 [受保護的項目] > 保護群組名稱 > [虛擬機器]。 在初始複寫完成之後且機器在同步處理資料時，仍會顯示**受保護**狀態。

    ![虛擬機器作業](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)

### <a name="set-protected-machine-properties"></a>設定受保護機器屬性
1. 在機器具有 **受保護** 狀態之後，您可以設定其容錯移轉屬性。 在保護群組詳細資料中，選取機器並開啟 [設定]  索引標籤。
2. 在容錯移轉和 Azure 虛擬機器大小調整之後，您可以修改要提供給 Azure 中機器的名稱。 您也可以選取機器要在容錯移轉之後要連結的 Azure 網路。

   > [!NOTE]
   > 對於用於部署 Site Recovery 的網路，不支援跨相同訂用帳戶內的資源群組或跨訂用帳戶[移轉網路](../resource-group-move-resources.md)。
   >
   >

    ![設定虛擬機器屬性](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

請注意：

* Azure 機器的名稱必須符合 Azure 需求。
* 根據預設，Azure 中的複寫虛擬機器不會與 Azure 網路連線。 如果您要讓複寫的虛擬機器進行通訊，請務必為其設定相同的 Azure 網路。
* 如果您調整 VMware 虛擬機器或實體伺服器上磁碟區的大小，它會進入嚴重狀態。 如果您需要修改大小，請執行下列動作：

  * a) 變更大小設定。
  * b) 在 [虛擬機器] 索引標籤上，選取虛擬機器，然後按一下 [移除]。
  * c) 在 [移除虛擬機器] 中，選取 [停用保護 (用於復原切入和磁碟區調整大小)]。 此選項會停用保護，但會在 Azure 中保留復原點。

      ![設定虛擬機器屬性](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)
  * d) 重新啟用虛擬機器的保護。 當您重新啟用保護時，重新調整過大小的磁碟區資料會傳輸至 Azure。

## <a name="step-10-run-a-failover"></a>步驟 10：執行容錯移轉
目前您只能為受保護的 VMware 虛擬機器和實體伺服器執行非計劃的容錯移轉。 請注意：

* 起始容錯移轉之前，請確保設定和主要目標伺服器執行中且狀況良好。 否則容錯移轉將會失敗。
* 來源機器未隨著非計劃的容錯移轉關閉。 執行非計劃的容錯移轉會停止受保護伺服器的資料複寫。 您將必須從保護群組刪除機器並重新加入，然後才能在非計劃的容錯移轉完成之後，再次開始保護機器。
* 如果您想要容錯移轉而不遺失任何資料，請確定主要網站虛擬機器在您起始容錯移轉之前是關閉的。

1. 在 [復原方案] 頁面上，並新增復原方案。 指定計畫的詳細資料並選取 **Azure** 做為目標。

    ![設定復原計畫](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)
2. 在 [選取虛擬機器]  中，選取保護群組，然後在群組中選取要加入復原計畫的機器。 [閱讀更多](site-recovery-create-recovery-plans.md) 復原方案的相關資訊。

    ![新增虛擬機器](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)
3. 若需要，您可以自訂計畫以建立群組並決定機器在復原計劃中容錯移轉的順序。 您也可以加入進行手動動作和指令碼的提示。 復原到 Azure 時，可以使用 [Azure 自動化 Runbook](site-recovery-runbook-automation.md)加入指令碼。
4. 在 [復原方案] 頁面中，選取方案並按一下 [非計劃性容錯移轉]。
5. 在 [確認容錯移轉]  中，確認容錯移轉方向 (朝向 Azure)，然後選取容錯移轉的目標復原點。
6. 等候容錯移轉工作完成，然後確認容錯移轉如預期般運作，且複寫的虛擬機器在 Azure 中成功啟動。

## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>步驟 11：來自 Azure 機器的容錯移轉失敗
[進一步了解](site-recovery-failback-azure-to-vmware-classic-legacy.md) 有關如何在 Azure 中將執行失敗的機器還原到您的內部部署環境。

## <a name="manage-your-process-servers"></a>管理您的處理序伺服器
處理序伺服器會傳送複寫資料至 Azure 中的主要目標伺服器，並探索加入至 vCenter 伺服器的新 VMware 虛擬機器。 在下列情況下，您可能要變更在您的部署中的處理序伺服器：

* 如果目前的處理序伺服器關閉
* 如果您的復原點目標 (RPO) 上升到您的組織無法接受的程度。

必要時，您可以將內部部署 VMware 虛擬機器和實體伺服器的部分或所有複寫移到另一個處理序伺服器。 例如：

* **失敗**—如果處理序伺服器失敗或無法使用，您可以將受保護的機器複寫移至另一個處理序伺服器。 來源機器和複本機器的中繼資料將會移至新的處理序伺服器，並重新同步處理資料。 新的處理序伺服器將自動連接至 vCenter 伺服器來執行自動探索。 您可以在 Site Recovery 儀表板上監視處理序伺服器的狀態。
* **負載平衡以調整 RPO**—如要獲得改善的負載平衡，您可以在 Site Recovery 入口網站中選取不同的處理序伺服器，並將一或多部機器的複寫移到它，以手動進行負載平衡。 在此情況下，選取的來源和複本機器的中繼資料會移至新的處理序伺服器。 原始的處理序伺服器仍然連接至 vCenter 伺服器。

### <a name="monitor-the-process-server"></a>監視處理序伺服器
如果處理序伺服器處於嚴重狀態，Site Recovery 儀表板上將顯示狀態警告。 您可以按一下狀態以開啟 [事件] 索引標籤，然後向下鑽研至 [工作] 索引標籤上的特定工作。

### <a name="modify-the-process-server-used-for-replication"></a>修改用於複寫的處理序伺服器
1. 開啟 [伺服器]  >  [組態伺服器] > 選取組態伺服器 > [伺服器詳細資料]。
2. 按一下 [處理伺服器] > 您想要修改的伺服器旁邊的 [變更處理伺服器]。

    ![變更處理序伺服器 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)
3. 在 [選取機器]  >  中，選取您要使用的新伺服器，然後選取您要複寫到新伺服器的虛擬機器。 按一下伺服器名稱旁的資訊圖示，以查看可用空間和已用記憶體的詳細資料。 將每個選取的虛擬機器複寫到新的處理序伺服器所需的平均空間就會顯示，以協助您進行負載的決策。

    ![變更處理序伺服器 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)
4. 按一下核取記號以開始複寫到新處理序伺服器。 請注意，如果從重要的處理序伺服器中移除所有虛擬機器，儀表板中應該不會再顯示重大警告。

## <a name="third-party-software-notices-and-information"></a>第三方廠商軟體注意事項和資訊
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.
