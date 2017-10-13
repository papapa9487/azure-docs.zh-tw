---
title: "使用 Azure Site Recovery 將 VMM 雲端中的 Hyper-V VM 複寫至 Azure 時設定網路對應 | Microsoft Docs"
description: "說明使用 Azure Site Recovery 將 VMM 雲端中的 Hyper-V VM 複寫至 Azure 時，如何設定網路對應"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ed6f73d8baea5af0d2aa5f0ae885f305911ccc82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-9-configure-network-mapping-for-hyper-v-replication-with-vmm-to-azure"></a>步驟 9：針對複寫至 Azure 的 Hyper-V 複寫作業 (含 VMM) 設定網路對應

設定[來源和目標的複寫設定](vmm-to-azure-walkthrough-source-target.md)後，使用本文來設定網路對應，以對應內部部署 VMM VM 網路和 Azure 網路。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。

## <a name="before-you-start"></a>開始之前

- 深入了解[網路對應](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure)。
- [準備 VMM 以進行網路對應](vmm-to-azure-walkthrough-network.md#prepare-vmm-for-network-mapping)。 
- 確認 VMM 伺服器上的虛擬機器已連接到 VM 網路，以及您已建立至少一個 Azure 虛擬網路。 多個 VM 網路可對應至單一 Azure 網路。

## <a name="configure-mapping"></a>設定對應

設定對應，如下所示︰

1. 在 [Site Recovery 基礎結構]  >  [網路對應]  >  [網路對應]中，按一下 [+網路對應] 圖示。

    ![網路對應](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping1.png)
2. 在 [新增網路對應] 上選取來源 VMM 伺服器，並選 [Azure] 做為目標。
3. 在容錯移轉後確認訂用帳戶和部署模型。
4. 在 [來源網路] 中，從與 VMM 伺服器相關聯的清單中，選取您要對應的來源內部部署 VM 網路。
5. 在 [目標網路] 中，選取複本 Azure VM 建立後所在的 Azure 網路。 然後按一下 [確定] 。

    ![網路對應](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping2.png)

以下是網路對應開始時發生的事情︰

* 開始對應時，來源 VM 網路上的現有 VM 會連接到目標網路。 連接到來源 VM 網路的新 VM 會在發生複寫時連接到對應的 Azure 網路。
* 如果您修改現有的網路對應，則複本虛擬機器會使用新設定進行連線。
* 如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在之子網路名稱相同，複本虛擬機器會在容錯移轉之後連線到該目標子網路。
* 如果沒有目標子網路具有相符的名稱，虛擬機器會連線到網路中的第一個子網路。



## <a name="next-steps"></a>後續步驟

移至[步驟 10：建立複寫原則](vmm-to-azure-walkthrough-replication.md)
