---
title: "使用 Azure Site Recovery 將 Hyper-V 複寫至次要站台時建立保存庫 | Microsoft Docs"
description: "說明如何在使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要的 System Center VMM 站台時，建立保存庫。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ff65dbfb-cb26-410e-ab48-76971625db08
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 28cfcf12b2e369f96664c163c0b6f2aa8a6ddcb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-create-a-vault-for-hyper-v-replication-to-a-secondary-site"></a>步驟 5：針對將 Hyper-V 複寫至次要站台的複寫作業建立保存庫

針對使用 [Azure Site Recovery](site-recovery-overview.md) 將 Hyper-V 複寫至次要站台的複寫作業，準備好內部部署 [System Center Virtual Machine Manager (VMM) 伺服器和 Hyper-V 主機/叢集](vmm-to-vmm-walkthrough-vmm-hyper-v.md)後，您可以建立復原服務保存庫，並選取複寫案例。

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。


## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>選擇保護目標

選取您要複寫的項目以及您要複寫到的位置。

1. 按一下 [Site Recovery] > [步驟 1: 準備基礎結構] > [保護目標]。
2. 選取 [到復原網站]，然後選取 [是，利用 Hyper-V]。
3. 選取 [是]，表示您使用 VMM 來管理 Hyper-V 主機。
4. 如果您有次要 VMM 伺服器，請選取 [是]。 如果您要在單一 VMM 伺服器上的雲端之間部署複寫，請按一下 [否] 。 然後按一下 [確定] 。

    ![選擇目標](./media/vmm-to-vmm-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>後續步驟

移至[步驟 6：設定複寫來源和目標](vmm-to-vmm-walkthrough-source-target.md)。