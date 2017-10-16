---
title: "檢閱使用 Azure Site Recovery 將 Hyper-V 複寫至次要網站的架構 | Microsoft Docs"
description: "本文提供使用 Azure Site Recovery 將內部部署 Hyper-V VM 複寫至次要 System Center VMM 網站的架構概觀。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 07099161-4cc7-4f32-8eb9-2a71bbf0750b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: b78cd0d5a5395873afaddc8856004775f447e8ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-a-secondary-site"></a>步驟 1：檢閱將 Hyper-V 複寫至次要網站的架構

本文說明在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務將 System Center Virtual Machine Manager (VMM) 雲端中的內部部署 Hyper-V 虛擬機器 (VM) 複寫至次要 VMM 網站時所涉及的元件和程序。

如有任何意見，請張貼於這篇文章下方或 [Azure 復原服務論壇 (英文)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 中。



## <a name="architectural-components"></a>架構元件

以下是要將 Hyper-V VM 複寫到次要 VMM 網站的所需項目。

**元件** | <bpt id="p1">**</bpt>Location<ept id="p1">**</ept> | **詳細資料**
--- | --- | ---
**Azure** | Azure 中的訂用帳戶。 | 您可以在 Azure 訂用帳戶中建立復原服務保存庫，來協調和管理 VMM 位置之間的複寫。
**VMM 伺服器** | 您需要 VMM 主要和次要位置。 | 我們建議主要網站與次要網站中各要有一部 VMM 伺服器 
**Hyper-V 伺服器** |  在主要和次要 VMM 雲端中，有一或多部 Hyper-V 主機伺服器。 | 在主要和次要 Hyper-V 主機伺服器之間，使用 Kerberos 或憑證驗證透過 LAN 或 VPN 來複寫資料。  
**Hyper-V VM** | 在 Hyper-V 主機伺服器上。 | 來源主機伺服器應該至少有一個您想要複寫的 VM。

## <a name="replication-process"></a>複寫程序

1. 您可設定 Azure 帳戶、建立復原服務保存庫，並指定您要複寫的項目。
2. 您可設定來源和目標複寫設定，其中包括在 VMM 伺服器上安裝 Azure Site Recovery Provider，以及在每部 Hyper-V 主機上安裝 Microsoft Azure 復原服務代理程式。
3. 您可建立來源 VMM 雲端的複寫原則。 此原則會套用至位於雲端主機上的所有 VM。
4. 您可為每個 VMM 啟用複寫，而系統會根據您選擇的設定進行 VM 的初始複寫。
5. 初始複寫之後，就會開始複寫差異變更。 項目的追蹤變更會保存在 .hrl 檔案中。


![內部部署至內部部署](./media/vmm-to-vmm-walkthrough-architecture/arch-onprem-onprem.png)

## <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

1. 您可以在內部部署網站間執行計劃性或非計劃性的[容錯移轉](site-recovery-failover.md)。 如果您執行計劃性容錯移轉，則來源 VM 會關閉以確保不會遺失資料。
2. 您可以容錯移轉單一機器，或建立[復原計劃](site-recovery-create-recovery-plans.md)來協調多部機器的容錯移轉。
4. 如果您執行非計劃性容錯移轉到次要網站，則在容錯移轉之後，次要位置中的容錯移轉機器不會啟用保護或複寫。 如果您執行了計劃性容錯移轉，則在容錯移轉之後，次要位置中的容錯移轉機器會受到保護。
5. 然後，您要認可讓容錯移轉開始存取來自複本 VM 的工作負載。
6. 當主要網站恢復可用狀態時，您就可以起始從次要網站到主要網站的反向複寫作業。 反向複寫會讓虛擬機器進入受保護的狀態，但是次要資料中心仍是使用中位置。
7. 若要讓主要網站再次成為使用中位置，您需要起始從次要網站到主要網站的計劃性容錯移轉，然後再進行另一個反向複寫。



## <a name="next-steps"></a>後續步驟

移至[步驟 2：檢閱必要條件和限制](vmm-to-vmm-walkthrough-prerequisites.md)。
