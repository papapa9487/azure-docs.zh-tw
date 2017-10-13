---
title: "針對使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要站台的複寫作業執行測試容錯移轉 | Microsoft Docs"
description: "說明如何針對使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要的 System Center VMM 站台的複寫作業，執行測試容錯移轉。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a3fd11ce-65a1-4308-8435-45cf954353ef
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 23d235d326273e7ec59feee6588a39f685401e52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-run-a-test-failover-for-hyper-v-replication-to-a-secondary-site"></a>步驟 10：針對將 Hyper-V 複寫至次要站台的複寫作業執行測試容錯移轉


使用 [Azure Site Recovery](site-recovery-overview.md) 啟用 Hyper-V 虛擬機器 (VM) 的複寫功能後，請使用本文執行測試容錯移轉。 測試容錯移轉會驗證該複寫是否正常運作，且不會影響您的生產環境。 


在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。


## <a name="before-you-start"></a>開始之前

- 當您觸發測試容錯移轉時，您可以指定測試複本 VM 將要連線的網路。 [深入了解](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery)網路選項。
- 我們建議您不要選擇您在網路對應期間選取的網路。
- 本文中的指示將說明如何針對單一 VM 進行容錯移轉。 如果您想要同時對多個 VM 進行容錯移轉，請閱讀＜[建立復原計劃](site-recovery-create-recovery-plans.md)＞。
- 閱讀＜[測試容錯移轉的限制](site-recovery-test-failover-vmm-to-vmm.md#things-to-note)＞。
- 測試容錯移轉期間提供給 VM 的 IP 位址，與計劃性或非計劃性容錯移轉時 VM 收到的 IP 位址相同 (假設該 IP 位址可用於測試容錯移轉網路)。 如果在測試容錯移轉網路中無法使用該 IP 位址，VM 會收到另一個可用於測試容錯移轉網路的 IP 位址。
- 如果您要在生產網路中執行測試容錯移轉，為了完整驗證端對端網路連線的機器，請注意：
    - 您執行測試容錯移轉時，應關閉主要 VM。 否則，相同的網路中將同時有兩部 VM 以相同的身分識別執行。 
    - 如果您對測試 VM 進行變更，當您清除測試容錯移轉時，將會遺失這些變更。 這些變更不會複寫回主要 VM。
    - 測試生產網路會導致生產工作停滯。 請要求使用者不要在進行災害復原測試時使用應用程式。  


## <a name="run-a-test-failover-for-a-vm"></a>執行 VM 的測試容錯移轉

1. 若要容錯移轉單一 VM，請在 [複寫的項目] 中，按一下 [VM] > [測試容錯移轉]。
2. 在 [測試容錯移轉] 中，指定 VM 在測試容錯移轉後將如何連線到網路。 
3. 按一下 [確定]  即可開始容錯移轉。 在 [作業] 索引標籤上追蹤進度。
5. 完成容錯移轉之後，請確認測試 VM 已成功啟動。
6. 完成後，按一下復原方案上的 [清除測試容錯移轉]。
7. 在 [記事] 中，記錄並儲存關於測試容錯移轉的任何觀察。 這個步驟會刪除在測試容錯移轉期間所建立的虛擬機器和網路。


## <a name="next-steps"></a>後續步驟

測試部署之後，接著可深入了解其他類型的[容錯移轉](site-recovery-failover.md)。
