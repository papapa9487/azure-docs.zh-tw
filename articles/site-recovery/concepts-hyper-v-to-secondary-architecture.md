---
title: "檢閱使用 Azure Site Recovery 將 Hyper-V 複寫至次要網站的架構 | Microsoft Docs"
description: "本文提供使用 Azure Site Recovery 將內部部署 Hyper-V VM 複寫至次要 System Center VMM 網站的架構概觀。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 26475782-a21a-408a-b089-35382d7e010e
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: a7a493097a4eaacc2c8d8449906b4a57eb411827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>Hyper-V 複寫至次要網站

本文說明在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務將 System Center Virtual Machine Manager (VMM) 雲端中的內部部署 Hyper-V 虛擬機器 (VM) 複寫至次要 VMM 網站時所涉及的元件和程序。


## <a name="architectural-components"></a>架構元件

下表和圖形提供將 Hyper-V 複寫至次要網站時所用元件的高層級檢視。

**元件** | **需求** | **詳細資料**
--- | --- | ---
**Azure** | Azure 訂閱 | 您可以在 Azure 訂用帳戶中建立復原服務保存庫，來協調和管理 VMM 位置之間的複寫。
**VMM 伺服器** | 您需要 VMM 主要和次要位置。 | 我們建議主要網站與次要網站中各要有一部 VMM 伺服器。
**Hyper-V 伺服器** |  在主要和次要 VMM 雲端中，有一或多部 Hyper-V 主機伺服器。 | 在主要和次要 Hyper-V 主機伺服器之間，使用 Kerberos 或憑證驗證透過 LAN 或 VPN 來複寫資料。  
**Hyper-V VM** | 在 Hyper-V 主機伺服器上。 | 來源主機伺服器應該至少有一個您想要複寫的 VM。

**內部部署至內部部署架構**

![內部部署至內部部署](./media/concepts-hyper-v-to-secondary-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>複寫程序

1. 觸發初始複寫後，就會建立 [Hyper-V VM 快照集](https://technet.microsoft.com/library/dd560637.aspx)。
2. VM 上的虛擬硬碟會逐一複寫至次要位置。
3. 如果在進行初始複寫時發生磁碟變更， 
4. 初始複寫完成時，就會開始差異複寫。 Hyper-V 複本複寫追蹤器會以 Hyper-V 複寫記錄 (.hrl) 追蹤變更。 這些記錄檔位於與磁碟相同的資料夾中。 每個磁碟都有一個相關聯的 .hrl 檔案會傳送至次要位置。 當初始複寫正在進行時，快照和記錄檔會取用磁碟資源。
5. 記錄中的差異磁碟變更會同步處理，並合併到父磁碟。
6. 

## <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

1. 您可以容錯移轉單一機器，或建立復原方案來協調多部機器的容錯移轉。
2. 您可以在內部部署網站間執行計劃性或非計劃性的容錯移轉。 如果您執行計劃性容錯移轉，則來源 VM 會關閉以確保不會遺失資料。
    - 如果您執行非計劃性容錯移轉到次要網站，則在容錯移轉之後，不會保護次要位置中的容錯移轉機器。
    - 如果您執行了計劃性容錯移轉，則在容錯移轉之後，次要位置中的容錯移轉機器會受到保護。
3. 執行初始容錯移轉之後，您要認可讓它開始從複寫 VM 存取工作負載。

當主要位置恢復可用狀態時，您就可以容錯回復。

1. 您可起始反向複寫，開始從次要網站複寫到主要網站。 反向複寫會讓虛擬機器進入受保護的狀態，但是次要資料中心仍是使用中位置。
2. 若要讓主要網站再次成為使用中位置，您需要起始從次要網站到主要網站的計劃性容錯移轉，然後再進行另一個反向複寫。



## <a name="next-steps"></a>後續步驟

請檢閱「遵循教學課程」支援對照表，以啟用 VMM 雲端之間的 Hyper-V 複寫。
執行容錯移轉和容錯回復。
