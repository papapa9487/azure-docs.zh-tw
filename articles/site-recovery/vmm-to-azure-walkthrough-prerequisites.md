---
title: "使用 Azure Site Recovery 檢閱 Hyper-V 對 Azure 複寫的必要條件 (含 System Center VMM) | Microsoft Docs"
description: "描述使用 Azure Site Recovery 來設定將 VMM 雲端中的內部部署 Hyper-V VM 複寫、容錯移轉和復原至 Azure 的必要條件"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a1c30fd5-c979-473c-af44-4f725ad3e3ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.openlocfilehash: 47c178c66ec98fe5d333edd725b64465026e73ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-with-vmm-to-azure-replication"></a>步驟 2：檢閱 Hyper-V (含 VMM) 對 Azure 複寫的必要條件

在檢閱[情節架構](vmm-to-azure-walkthrough-architecture.md)之後，請閱讀本文以確定您了解部署必要條件。 

## <a name="prerequisites-and-limitations"></a>必要條件和限制

**需求** | **詳細資料**
--- | ---
**Azure 帳戶** | 您需要 [Microsoft Azure 帳戶](http://azure.microsoft.com/)。
**Azure 儲存體** | 您需要 Azure 儲存體帳戶來儲存複寫的資料。<br/><br/> 儲存體帳戶必須位於與 Azure 復原服務保存庫相同的區域中。<br/><br/>您可以使用[異地備援儲存體](../storage/common/storage-redundancy.md#geo-redundant-storage)或本地備援儲存體。 建議使用異地備援儲存體。 使用異地備援儲存體時，如果發生區域性停電或無法復原主要區域，就能夠恢復資料。<br/><br/> 您可以使用標準的 Azure 儲存體帳戶，或使用 Azure [進階儲存體](../storage/common/storage-premium-storage.md)。 進階儲存體可以裝載需要大量 I/O 的工作負載，且通常用於需要持續有高 I/O 效能和低延遲的 VM。 如果您使用進階儲存體來存放複寫的資料，您也需要標準儲存體帳戶。 標準儲存體帳戶可儲存複寫記錄，這些記錄會擷取內部部署資料不斷發生的變更。
**Azure 網路** | 您需要 [Azure 網路](../virtual-network/virtual-network-get-started-vnet-subnet.md)，供 Azure VM 在容錯移轉之後連線。 此 Azure 網路必須位於與復原服務保存庫相同的區域中。
**內部部署 VMM 伺服器** | 您需要一或多部執行 System Center 2012 R2 或更新版本的 VMM 伺服器。<br/><br/> 每一部 VMM 伺服器必須有一或多個私人雲端。 每一個雲端需要一或多個主機群組。<br/><br/> VMM 伺服器需要存取網際網路。
**內部部署 Hyper-V** | Hyper-V 主機伺服器必須至少執行 Windows Server 2012 R2 (已啟用 Hyper-V 角色) 或 Microsoft Hyper-V Server 2012 R2。 必須安裝最新的更新。<br/><br/> Hyper-V 伺服器必須位於 VMM 主機群組中 (位於 VMM 雲端)。<br/><br/> 主機必須具有您想要複寫的一或多個 VM。<br/><br/> Hyper-V 主機必須連線至網際網路，才能直接或透過 proxy 來複寫至 Azure。 Hyper-V 伺服器必須具有 [2961977](https://support.microsoft.com/kb/2961977) 文章所述的修正程式。
**內部部署 Hyper-V VM** | 您想要複寫的 VM 應該執行[支援的作業系統](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)，也要符合 [Azure 必要條件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。 啟用複寫之後，可以修改 VM 名稱。 




## <a name="next-steps"></a>後續步驟

移至[步驟 3：規劃容量](vmm-to-azure-walkthrough-capacity.md)
