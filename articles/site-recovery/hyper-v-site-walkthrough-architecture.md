---
title: "檢閱使用 Azure Site Recovery 將 Hyper-V (不含 System Center VMM) 複寫至 Azure 的架構 | Microsoft Docs"
description: "本文提供使用 Azure Site Recovery 服務將內部部署 Hyper-V VM (不含 VMM) 複寫至 Azure 時所用元件和架構的概觀。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: d57cbc5b205cfb020070d567097f3bb648ce5300
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-azure"></a>步驟 1：檢閱 Hyper-V 複寫至 Azure 的架構


本文說明使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署 Hyper-V 虛擬機器 (不是由系統中心 VMM 管理) 複寫至 Azure 時的相關元件和程序。

如有任何意見，請張貼於這篇文章下方或 [Azure 復原服務論壇 (英文)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 中。



## <a name="architectural-components"></a>架構元件

將 Hyper-V VM 複寫至 Azure (不含 VMM) 時，會涉及許多元件。

**元件** | <bpt id="p1">**</bpt>Location<ept id="p1">**</ept> | **詳細資料**
--- | --- | ---
**Azure** | 在 Azure 中，您需要 Microsoft Azure 帳戶、Azure 儲存體帳戶和 Azure 網路。 | 所複寫的資料會儲存在儲存體帳戶中，而在從內部部署網站進行容錯移轉時，便會以複寫的資料建立 Azure VM。<br/><br/> Azure VM 在建立後會連線到 Azure 虛擬網路。
**Hyper-V** | Hyper-V 主機和叢集會蒐集到 Hyper-V 網站中。 Azure Site Recovery 提供者和復原服務代理程式會安裝在每部 Hyper-V 主機上。 | 此提供者會透過網際網路與 Site Recovery 協調複寫作業。 復原服務代理程式會處理資料複寫。<br/><br/> 來自提供者和代理程式的通訊都是安全且加密的。 Azure 儲存體中的複寫的資料也會加密。
**Hyper-V VM** | 您必須有一或多部在 Hyper-V 主機伺服器上執行的 VM。 | 不需要在 VM 上明確安裝任何項目。

了解[支援矩陣](site-recovery-support-matrix-to-azure.md)中每個元件的部署必要條件和需求。

**圖 1：Hyper-V 網站至 Azure 的複寫**

![元件](./media/hyper-v-site-walkthrough-architecture/arch-onprem-azure-hypervsite.png)


## <a name="replication-process"></a>複寫程序

**圖 2：將 Hyper-V 複寫至 Azure 的複寫和復原程序**

![工作流程](./media/hyper-v-site-walkthrough-architecture/arch-hyperv-azure-workflow.png)

### <a name="enable-protection"></a>啟用保護。

1. 您在 Azure 入口網站或內部部署針對 Hyper-V VM 啟用保護之後，**啟用保護**隨即啟動。
2. 作業會檢查符合必要條件的機器，然後叫用 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) 方法，以使用您進行的設定來設定複寫。
3. 作業會啟動初始複寫，方法是叫用 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 方法，以初始化完整的 VM 複寫，並且將 VM 的虛擬磁碟傳送至 Azure。
4. 您可以在 [作業] 索引標籤中監視作業。
 
### <a name="replicate-the-initial-data"></a>複寫初始資料

1. 系統會在初始複寫觸發時擷取 [Hyper-V VM 快照集](https://technet.microsoft.com/library/dd560637.aspx)。
2. 虛擬硬碟會逐一複寫，直到它們全部複製到 Azure 為止。 可能需要一些時間，取決於 VM 大小和網路頻寬。 若要將網路使用量最佳化，請參閱 [如何管理內部部署至 Azure 保護的網路頻寬使用量](https://support.microsoft.com/kb/3056159)。
3. 如果在初始複寫進行時發生磁碟變更，Hyper-V 複本複寫追蹤器會以 Hyper-V 複寫記錄檔 (.hrl) 的形式追蹤這些變更。 這些記錄檔位於與磁碟相同的資料夾中。 每個磁碟都有一個相關聯的.hrl 檔案，將會傳送至次要儲存體。
4. 當初始複寫正在進行時，快照和記錄檔會取用磁碟資源。
5. 初始複寫完成時，就會刪除 VM 快照集。 記錄中的差異磁碟變更會同步處理，並合併到父磁碟。


### <a name="finalize-protection"></a>完成保護

1. 初始複寫完成之後，**在虛擬機器上完成保護**作業會設定網路和其他複寫後設定，如此就能讓虛擬機器受到保護。
2. 如果您要複寫至 Azure，您可能需要調整虛擬機器的設定，使其準備好進行容錯移轉。 此時，您可以執行測試容錯移轉，以確認一切如預期般運作。

### <a name="replicate-the-delta"></a>複寫差異

1. 在初始複寫之後，會根據複寫設定，開始進行差異同步處理。
2. Hyper-V 複本複寫追蹤器會以 .hrl 檔案格式追蹤虛擬硬碟的變更。 每個設定用於複寫的磁碟都會有相關聯的 .hrl 檔案。 此記錄檔會在初始複寫完成後，傳送至客戶的儲存體帳戶。 當記錄傳輸至 Azure 時，將會在同一目錄內的另一個記錄檔中追蹤主要磁碟中的變更。
3. 在初始和差異複寫期間，您可以在 VM 檢視中監視 VM。 [深入了解](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines)。  

### <a name="synchronize-replication"></a>同步處理複寫

1. 如果差異複寫失敗且完整複寫因為頻寬或時間需要大量成本，就會將 VM 標示為重新同步處理。 例如，如果 .hrl 檔案達到磁碟大小的 50%，系統就會標示 VM 以便重新同步處理。
2.  重新同步處理會計算來源和目標虛擬機器的總和檢查碼，並只傳送差異資料部分，藉此將傳送的資料量降至最低。 重新同步處理會使用固定區塊的區塊處理演算法，將來源和目標檔案分成固定區塊。 系統會產生每個區塊的總和檢查碼並相互比較，以決定需要將來源中的哪些區塊套用至目標。
3. 重新同步處理完成之後，應會繼續進行正常的差異複寫。 根據預設，重新同步處理會排程在上班時間以外的時間自動執行，但是您可以手動重新同步處理虛擬機器。 例如，若發生網路中斷或其他中斷情形，您可以繼續重新同步處理。 若要這樣做，請在入口網站中選取 VM > [重新同步處理]。

    ![手動重新同步處理](./media/hyper-v-site-walkthrough-architecture/image4.png)


### <a name="retry-logic"></a>重試邏輯

如果發生複寫錯誤，會有內建的重試。 此邏輯可分為以下兩種類別：

**類別** | **詳細資料**
--- | ---
**無法復原的錯誤** | 不嘗試重試。 VM 狀態為**重大**，需要管理員介入處理。 這些錯誤的範例包括︰中斷 VHD 鏈結；複本 VM 的狀態無效；網路驗證錯誤︰授權錯誤；找不到 VM 錯誤 (適用於獨立 Hyper-V 伺服器)
**可復原的錯誤** | 在每個複寫間隔中進行重試，並採用指數倒退法，從第一次嘗試開始增加重試間隔 (1、2、4、8、10 分鐘)。 如果錯誤持續發生，會每隔 30 分鐘重試一次。 範例包括：網路錯誤；磁碟空間不足錯誤；記憶體不足情況 |



## <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

1. 您可以執行從內部部署 Hyper-V VM 至 Azure 的計劃性或非計劃性[容錯移轉](site-recovery-failover.md)。 如果您執行計劃性容錯移轉，則來源 VM 會關閉以確保不會遺失資料。
2. 您可以容錯移轉單一機器，或建立[復原計劃](site-recovery-create-recovery-plans.md)來協調多部機器的容錯移轉。
4. 執行容錯移轉之後，您應該就會在 Azure 中看到所建立的複本 VM。 如有必要，您可以對 VM 指派公用 IP 位址。
5. 然後，您要認可讓容錯移轉開始存取來自複本 Azure VM 的工作負載。
6. 當主要的內部部署網站恢復可用狀態時，您就可以[容錯回復](site-recovery-failback-from-azure-to-hyper-v.md)。 您可以啟動從 Azure 至主要網站的計劃性容錯移轉。 針對計劃性容錯移轉，您可以選取要容錯回復至相同 VM 或其他位置，並同步處理 Azure 和內部部署之間的變更，以確保不會遺失任何資料。 在內部部署中建立了 VM 後，您就可以認可容錯移轉。




## <a name="next-steps"></a>後續步驟

移至[步驟 2：檢閱部署先決條件](hyper-v-site-walkthrough-prerequisites.md)
