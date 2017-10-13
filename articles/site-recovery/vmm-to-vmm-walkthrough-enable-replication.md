---
title: "使用 Azure Site Recovery 將 Hyper-V 複寫至次要站台 | Microsoft Docs"
description: "說明如何使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要的 System Center VMM 站台。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d8782d14-9fef-4396-8912-ff945efc851b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 6673d192dbc18bfc955d9e7e3c55893512511ffb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-9-enable-replication-to-a-secondary-site-for-hyper-v-vms"></a>步驟 9：將 Hyper-V VM 複寫至次要站台


設定複寫原則後，請依本文指示使用 [Azure Site Recovery](site-recovery-overview.md) 將內部部署 Hyper-V 虛擬機器 (VM) 複寫至次要的 System Center Virtual Machine Manager (VMM) 站台。

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。



## <a name="select-vms-to-replicate"></a>選取要複寫的 VM

1. 按一下 [步驟 2: 複寫應用程式]  >  [來源]。 

    ![啟用複寫](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication1.png)

2. 在 [來源] 中，選取 VMM 伺服器和您要複寫的 Hyper-V 主機所在的雲端。 然後按一下 [確定] 。

    ![啟用複寫](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication2.png)
3. 在 [目標] 中，確認次要 VMM 伺服器和雲端。
4. 在 [虛擬機器] 中，從清單中選取您要保護的 VM。

    ![啟用虛擬機器保護](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication5.png)

您可以在 [作業] > [Site Recovery 作業] 中，追蹤 [啟用保護] 動作的進度。 在**完成保護**作業完成後，即完成初始複寫，虛擬機器已可進行容錯移轉。

請注意：

- 您也可以在 VMM 主控台中啟用虛擬機器的保護。 在虛擬機器屬性 > [Azure Site Recovery] 索引標籤中的工具列上按一下 [啟用保護]。
- 啟用複寫之後，您就可以在 [複寫的項目] 中檢視 VM 的屬性。 在 [基本資訊] 儀表板中，您可以看到關於 VM 的複寫原則及其狀態的資訊。 如需詳細資訊，請按一下 [屬性]  。

## <a name="onboard-existing-vms"></a>加入現有的 VM

如果 VMM 中目前已經有使用 Hyper-V 複本複寫的虛擬機器，您可以使用下列方式加入它們以提供 Azure Site Recovery 複寫：

1. 確認裝載現有 VM 的 Hyper-V 伺服器位於主要雲端，且裝載複本虛擬機器的 Hyper-V 伺服器位於次要雲端。
2. 確定已針對主要 VMM 雲端設定複寫原則。
3. 針對主要虛擬機器啟用複寫。 Azure Site Recovery 和 VMM 將確保會偵測到相同的複本主機和虛擬機器，且 Azure Site Recovery 會使用指定的設定，重複使用及重新建立複寫。


## <a name="next-steps"></a>後續步驟

移至[步驟 10：執行測試容錯移轉](vmm-to-vmm-walkthrough-test-failover.md)。
