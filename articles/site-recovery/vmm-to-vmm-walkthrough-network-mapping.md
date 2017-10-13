---
title: "對應網路以使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要站台 | Microsoft Docs"
description: "說明使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要 VMM 站台時如何對應網路。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 461b7c1c-ef61-4005-aeec-2324e727a3d0
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 606e2d3d0e31b9d80200105e812f9d7bbbcf939c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-map-networks-for-hyper-v-vm-replication-to-a-secondary-site"></a>步驟 7：對應網路以將 Hyper-V VM 複寫至次要站台


設定好將 Hyper-V 虛擬機器 (VM) 複寫至次要的 System Center Virtual Machine Manager (VMM) 站台時的[來源和目標設定](vmm-to-vmm-walkthrough-source-target.md)後，請依本文指示設定網路對應，以使用 [Azure Site Recovery](site-recovery-overview.md) 將 Hyper-V 虛擬機器 (VM) 複寫至次要站台。

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。


## <a name="before-you-start"></a>開始之前

- 開始之前，深入了解[網路對應](vmm-to-vmm-walkthrough-network.md#network-mapping-overview)。
- 確認 VMM 伺服器上的虛擬機器已連線到 VM 網路。

## <a name="configure-network-mapping"></a>設定網路對應

1. 在 [網路對應] > [網路對應]，按一下 [+網路對應]。
2. 在 [新增網路對應] 索引標籤中，選取來源和目標 VMM 伺服器。 隨即會擷取與 VMM 伺服器相關聯的 VM 網路。
3. 在 [來源網路] 中，從與主要 VMM 伺服器相關聯的 VM 網路清單中，選取您要使用的網路。
4. 在 [目標網路] 中，選取您要在次要 VMM 伺服器上使用的網路。 然後按一下 [確定] 。

    ![網路對應](./media/vmm-to-vmm-walkthrough-network-mapping/network-mapping2.png)

以下是網路對應開始時發生的事情︰

* 對應到來源 VM 網路的任何現有複本虛擬機器都將連線到目標 VM 網路。
* 連線到來源 VM 網路的新虛擬機器都會在複寫之後連線到目標對應的網路。
* 如果您修改與新網路的現有對應，複本虛擬機器將使用新設定進行連線。
* 如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在之子網路名稱相同，複本虛擬機器將會在容錯移轉之後連線到該目標子網路。 如果沒有目標子網路具有相符的名稱，虛擬機器將會連線到網路中的第一個子網路。



## <a name="next-steps"></a>後續步驟

移至[步驟 8：設定複寫原則](vmm-to-vmm-walkthrough-replication.md)。