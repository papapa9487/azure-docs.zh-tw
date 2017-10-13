---
title: "設定 VMM 和 Hyper-V 以使用 Azure Site Recovery 複寫至次要站台 | Microsoft Docs"
description: "說明如何設定 System Center VMM 伺服器和 Hyper-V 主機以複寫至次要 VMM 站台。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d0389e3b-3737-496c-bda6-77152264dd98
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 73bd1790c56ac52166f638de2e80c2a2cfb87e53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-4-set-up-vmm-and-hyper-v-for-hyper-v-vm-replication-to-a-secondary-site"></a>步驟 4：設定 VMM 和 Hyper-V 以將 Hyper-V VM 複寫至次要站台 

網路對應就緒後，請設定 System Center Virtual Machine Manager (VMM) 伺服器和 Hyper-V 主機，以使用 Azure 入口網站中的 [Azure Site Recovery](site-recovery-overview.md) 將 Hyper-V 虛擬機器 (VM) 複寫至次要站台。 

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。



## <a name="prepare-vmm-servers"></a>準備 VMM 伺服器 

準備部署：


1. 請確定 VMM 伺服器符合[支援需求](site-recovery-support-matrix-to-sec-site.md#on-premises-servers)和[部署必要條件](vmm-to-vmm-walkthrough-prerequisites.md)。
2. 請確定 VMM 伺服器已連線到網際網路，並可存取這些 URL。
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - 如果您有以 IP 位址為基礎的防火牆規則，請確定這些規則允許對 Azure 的通訊。
    - 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。
    - 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。
3. 請確定已備妥的 VMM 伺服器[網路對應](vmm-to-vmm-walkthrough-network.md#prepare-for-network-mapping)


## <a name="prepare-hyper-v-hostsclusters"></a>準備 Hyper-V 主機/叢集

1. 請確定 Hyper-V 主機/叢集符合[支援需求](site-recovery-support-matrix-to-sec-site.md#on-premises-servers)和[部署必要條件](vmm-to-vmm-walkthrough-prerequisites.md)。
2. 驗證 [Hyper-V VM](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions) 的需求
3. 驗證[網路](site-recovery-support-matrix-to-sec-site.md#network-configuration)和[儲存體](site-recovery-support-matrix-to-sec-site.md#storage)需求。
4. 請確定 Hyper-V 主機已連線到網際網路，並可存取這些 URL。
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - 如果您有以 IP 位址為基礎的防火牆規則，請確定這些規則允許對 Azure 的通訊。
    - 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。
    - 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。

## <a name="prepare-for-single-server-deployment"></a>準備進行單一伺服器部署


如本文所述，如果您只有單一 VMM 伺服器，可以將 VMM 雲端中 Hyper-V 主機上的 VM 複寫到 [Azure](hyper-v-site-walkthrough-overview.md) 或次要 VMM 雲端。 我們建議採用第一個選項，因為在雲端之間複寫並不順暢。

如果您不想要在雲端之間複寫，您可以使用單一的獨立 VMM 伺服器進行複寫，或以延伸 Windows 叢集中部署的單一 VMM 伺服器進行複寫

### <a name="replicate-with-a-standalone-vmm-server"></a>以獨立 VMM 伺服器進行複寫

在這個案例中，您會將單一 VMM 伺服器部署為主要站台中的虛擬機器，並使用 Site Recovery 與 Hyper-V 複本將此 VM 複寫到次要站台。

1. **在 Hyper-V VM 上設定 VMM**。 建議您將 VMM 使用的 SQL Server 執行個體共置在相同的 VM 上。 因為只需建立一個 VM，這樣就能節省時間。 如果您想要使用遠端 SQL Server 執行個體卻發生中斷，則必須先復原該執行個體後再復原 VMM。
2. **確定 VMM 伺服器至少已設定兩個雲端**。 其中一個雲端包含您想要複寫的 VM，另一個雲端做為次要位置。 包含要保護 VM 的雲端必須遵守 [必要條件](#prerequisites)。
3. 以本文所述的方式設定 Site Recovery。 在保存庫中建立並註冊 VMM 伺服器、設定複寫原則，然後啟用複寫。 來源和目標 VMM 會有相同的名稱。 指定透過網路執行初始複寫。
4. 當您設定網路對應時，要將主要雲端的 VM 網路對應到次要雲端的 VM 網路。
5. 使用 Hyper-V 管理員主控台，在包含 VMM VM 的 Hyper-V 主機上啟用 Hyper-V 複本，並啟用 VM 的複寫功能。 請不要將 VMM 虛擬機器加入受 Site Recovery 保護的雲端，以確保 Site Recovery 不會覆寫 Hyper-V 複本設定。
6. 如果您建立適用於容錯移轉的復原方案，便須使用同一部 VMM 伺服器做為來源與目標。
7. 在完全中斷運作的情況下，您進行容錯移轉和復原的方式如下︰

   1. 在次要網站的 Hyper-V 管理員主控台中，執行非計劃性容錯移轉，將主要 VMM VM 容錯移轉至次要網站。
   2. 確認 VMM VM 已啟動並執行，然後在保存庫中執行非計劃性容錯移轉，將 VM 從主要雲端容錯移轉至次要雲端。 認可容錯移轉，並視需要選取替代復原點。
   3. 在非計劃性的容錯移轉完成之後，將可透過主要站台再次存取所有資源。
   4. 當主要網站恢復運作時，請在次要網站的 Hyper-V 管理員主控台中，對 VMM VM 啟用反向複寫。 這會開始將 VM 從次要網站複寫至主要網站。
   5. 在次要網站的 Hyper-V 管理員主控台中，執行計劃性容錯移轉，將 VMM VM 容錯移轉至主要網站。 認可容錯移轉。 然後啟用反向複寫，以再次將 VMM VM 從主要網站複寫至次要網站。
   6. 在復原服務保存庫中，針對工作負載 VM 啟用反轉複寫方向，以開始將它們從次要站台複寫到主要站台。
   7. 在復原服務保存庫中，執行計劃的容錯移轉以將工作負載 VM 容錯移轉回主要站台。 認可容錯移轉以完成它。 然後啟用反轉複寫方向，以開始將工作負載 VM 從主要站台複寫到次要站台。

### <a name="replicate-with-a-stretched-vmm-cluster"></a>以延伸的 VMM 叢集複寫

您不需要將獨立的 VMM 伺服器部署為複寫至次要站台的 VM，而可將它部署為 Windows 容錯移轉叢集中的 VM，讓 VMM 發揮高可用性。 這可以提供工作負載彈性及硬體故障的防護。 若要使用 Site Recovery 來部署，應將 VMM VM 部署至跨地理不同位置之站台的延伸叢集中。 作法：

1. 在 Windows 容錯移轉叢集中的虛擬機器上安裝 VMM，然後選取在安裝期間以高度可用性執行伺服器的選項。
2. VMM 所使用的 SQL Server 執行個體應以 SQL Server AlwaysOn 可用性群組來複寫，這樣一來次要站台就會有資料庫的複本。
3. 請遵循本文中的指示建立保存庫、註冊伺服器及設定保護。 您必須在復原服務保存庫的叢集中註冊每部 VMM 伺服器。 若要這樣做，您必須在作用中的節點上安裝 Provider，並註冊 VMM 伺服器。 然後在其他節點上安裝提供者。
4. 如果發生中斷，VMM 伺服器及其對應的 SQL Server 資料庫會容錯移轉並可從次要站台存取。



## <a name="next-steps"></a>後續步驟

移至[步驟 5：設定保存庫](vmm-to-vmm-walkthrough-create-vault.md)。