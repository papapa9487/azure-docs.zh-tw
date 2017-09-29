---
title: "使用 Azure Site Recovery 為您的次要內部部署網站執行災害復原演練 | Microsoft 文件"
description: "深入了解如何使用 Azure Site Recovery 為您的次要內部部署網站執行災害復原演練"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 445878e2-6682-49ba-914d-4c6824ab08a6
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8737c2a22bd729cfc15d5448e1ec0becef643fd5
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="run-a-disaster-recovery-drill-for-hyper-v-vms-to-your-secondary-on-premises-site"></a>為 Hyper-V VM 及您的次要內部部署網站執行災害復原演練

[Azure Site Recovery](site-recovery-overview.md) 服務可藉由管理及協調內部部署電腦與 Azure 虛擬機器 (VM) 的複寫、容錯移轉及容錯回復，為您的災害復原策略做出貢獻。

此教學會讓您了解如何為 Hyper-V VM 及您的次要內部部署網站執行災害復原演練。 Hyper-V VM 必須由 System Center Virtual Machine Manager (VMM) 私人雲端管理。 此演練會驗證您不遺失資料或不停機的複寫策略，並且不會影響您的生產環境。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 準備測試容錯移轉的必要條件
> * 執行單一機器測試容錯移轉


## <a name="prerequisites"></a>必要條件

- 您可以針對次要網站上的網路使用數個選項執行測試容錯移轉。 您可以在沒有網路、連線至現有網路，或讓 Site Recovery 自動建立測試網路的情況下執行測試容錯移轉。 
**若您想要使用現有的生產網路來執行測試容錯移轉**：
    - 您執行測試容錯移轉時，應關閉主要 VM。 否則，相同的網路中將同時有兩部 VM 以相同的身分識別執行。 
    - 如果您對測試 VM 進行變更，當您清除測試容錯移轉時，將會遺失這些變更。 變更不會複寫回主要 VM。
    - 測試生產網路會導致生產工作停滯。 請要求您的使用者不要在進行災害復原測試時使用相關應用程式。 
- 複本測試網路不需要符合用於測試容錯移轉的 VMM 邏輯網路類型。 但是，某些組合將無法運作。 例如，若複本使用 DHCP 和 VLAN 式隔離，您將無法使用 Windows 網路虛擬化作為測試容錯移轉網路，因為它需要 IP 位址集區。 
- 我們建議您不要針對測試容錯移轉使用您用來進行網路對應的網路。


## <a name="run-a-test-failover-for-a-vm"></a>執行 VM 的測試容錯移轉

1. 在 [複寫的項目] 中，按一下 [VM] > [測試容錯移轉]。
2. 在 [測試容錯移轉] 中，指定 VM 在測試容錯移轉後將如何連線到網路。 我們建議您針對此教學課程，讓 Site Recovery 自動建立測試網路。 [深入了解](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery)。
3. 按一下 [確定]  即可開始容錯移轉。 在 [作業] 索引標籤上追蹤進度。
4. 完成容錯移轉之後，請確認測試 VM 已成功啟動。
5. 當您完成之後，按一下 [清除測試容錯移轉]。 這將會刪除測試 VM，以及任何在測試容錯移轉期間建立的網路。
6. 在 [記事] 中，記錄並儲存關於測試容錯移轉的任何觀察。 


## <a name="next-steps"></a>後續步驟

[執行實際容錯移轉](tutorial-vmm-to-vmm-failover-failback.md)







