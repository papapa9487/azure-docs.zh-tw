---
title: "檢閱使用 Azure Site Recovery 將實體伺服器複寫至 Azure 的架構 | Microsoft Docs"
description: "本文概述使用 Azure Site Recovery 服務將內部部署實體伺服器複寫至 Azure 時，所使用的元件和架構"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 09c9b136-35f5-465e-8d0f-f4c5d5d9f880
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 697fbcdb4c5d5a900deb980db7cca9cf2f9fa36d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/27/2017

---

# <a name="step-1-review-the-architecture-for-physical-server-replication-to-azure"></a>步驟 1：檢閱將實體伺服器複寫至 Azure 的架構

本文說明使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署 Windows/Linux 實體伺服器複寫至 Azure 時，所使用的元件和程序。

請在本文下方張貼意見，或在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提出問題。


## <a name="architectural-components"></a>架構元件

下表摘要說明您所需的元件。

**元件** | **需求** | **詳細資料**
--- | --- | ---
**Azure** | 您需要 Azure 帳戶、Azure 儲存體帳戶及 Azure 網路。 | 複寫的資料會儲存在儲存體帳戶中，而在發生容錯移轉時，便會以複寫的資料建立 Azure VM。 Azure VM 在建立後會連線到 Azure 虛擬網路。
**組態伺服器** | 執行所有內部部署 Site Recovery 元件的單一內部部署管理伺服器 (實體伺服器或 VMware VM)。 這些包括組態伺服器、處理伺服器、主要目標伺服器。 | 組態伺服器元件會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
 **處理序伺服器**  | 預設會安裝在組態伺服器上。 | 會做為複寫閘道器。 接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。<br/><br/> 處理伺服器也會處理將行動服務安裝至受保護機器的推入安裝。<br/><br/> 您可以新增額外的個別專用處理伺服器，以處理日益增加的複寫流量。
 **主要目標伺服器** | 預設會安裝在內部部署組態伺服器上。 | 在從 Azure 容錯回復期間，處理複寫資料。<br/><br/> 如果容錯回復的流量很高，您可以部署個別的主要目標伺服器來供容錯回復使用。
**複寫的伺服器** | 行動服務元件會安裝在您要複寫的每部 Windows/Linux 伺服器上。 您可以手動將它安裝在每部電腦上，或是從處理序伺服器進行推入安裝。
**容錯回復** | 若要進行容錯回復，必須要有 VMware 基礎結構。 您無法容錯回復到實體伺服器。


**圖 1：實體到 Azure 的元件**

![元件](./media/physical-walkthrough-architecture/arch-enhanced.png)

## <a name="replication-process"></a>複寫程序

1. 您需設定部署，包括內部部署和 Azure 元件。 在 Recovery Services 保存庫中，您需指定複寫來源和目標、設定組態伺服器、建立複寫原則、部署行動服務、啟用複寫，以及執行測試容錯移轉。
2.  機器會根據複寫原則進行複寫，並將資料的初始複本複寫到 Azure 儲存體。
4. 初始複寫完成之後，就會開始將差異變更複寫到 Azure。 機器的追蹤變更會保存在 .hrl 檔案中。
    - 複寫機器會在輸入連接埠 HTTPS 443 上與組態伺服器進行通訊，以管理複寫。
    - 複寫機器會透過輸入連接埠 HTTPS 9443 (可修改) 將複寫資料傳送到處理伺服器。
    - 組態伺服器會透過輸出連接埠 HTTPS 443 與 Azure 協調複寫管理。
    - 處理序伺服器會透過輸出連接埠 443，接收來源機器所傳來的資料、將其最佳化並加密，再將它傳送至 Azure 儲存體。
    - 如果您啟用多部 VM 一致性，則複寫群組中的機器會透過連接埠 20004 彼此通訊。 如果您將多部機器群組為幾個共用當機時保持一致復原點和應用程式一致復原點的複寫群組，當這些群組在進行容錯移轉時，便會使用多部 VM。 如果機器執行的是相同的工作負載，且需要保持一致，此功能就很實用。
5. 流量透過網際網路複寫到 Azure 儲存體的公用端點。 或者，您可以使用 Azure ExpressRoute [公用對等](../expressroute/expressroute-circuit-peerings.md#azure-public-peering)。 不支援從內部部署網站透過站台對站台 VPN 將流量複寫至 Azure。

**圖 2：實體到 Azure 複寫**

![增強](./media/physical-walkthrough-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

確認測試容錯移轉如預期般運作之後，您可以視需要執行至 Azure 的未計劃容錯移轉。 當您執行容錯移轉時，系統會從複寫的資料建立 Azure VM。 接著，當您的主要內部部署站台恢復可用時，您就可以容錯回復。 請注意：

- 不支援有計劃的容錯移轉。
- 您可以容錯移轉單一機器，或建立[復原方案](site-recovery-create-recovery-plans.md)，來同時容錯移轉多部機器。
- 您要認可讓容錯移轉開始存取來自複本 Azure VM 的工作負載。
- 當主要站台恢復可用時，您就可以將機器從次要站台複寫到主要站台。 接著，從次要站台執行非計劃性容錯移轉。 在認可此容錯移轉後，資料會回到內部部署網站，而您必須再次啟用複寫至 Azure 的功能。

容錯回復元件包括：

- **Azure 中的暫時處理伺服器**︰您必須設定 Azure VM 來作為處理伺服器，以處理來自 Azure 的複寫。 容錯回復完成後，您可以刪除此 VM。
- **VPN 連線**：您需要有從 Azure 網路到內部部署站台的 VPN 連線 (或 Azure ExpressRoute)。
- **個別的內部部署主要目標伺服器**︰內部部署主要目標伺服器 (預設會安裝在組態伺服器上) 會處理容錯回復。 如果您要容錯回復大量流量，您應該針對此目的設定個別的內部部署主要目標伺服器。
- **容錯回復原則**：您需要一個容錯回復原則。 此原則會在您建立複寫原則時自動建立。
- **VMware 基礎結構**：您必須容錯回復到內部部署 VMware VM。 這表示您需要內部部署 VMware 基礎結構，即使是將內部部署實體伺服器複寫到 Azure。

**圖 3：實體伺服器容錯回復**

![容錯回復](./media/physical-walkthrough-architecture/enhanced-failback.png)


## <a name="next-steps"></a>後續步驟

移至[步驟 2：確認必要條件和限制](physical-walkthrough-prerequisites.md)

