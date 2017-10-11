---
title: "使用 Azure Site Recovery 將 VMM 雲端中的 Hyper-V VM 複寫至 Azure | Microsoft Docs"
description: "說明如何使用 Azure Site Recovery 服務將 VMM 雲端中的 Hyper-V VM 複寫至 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 89a2c4fc-7e03-4a86-a2c0-52831ccebc1a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: 96a817e43a830e836f2faa4603fc88ed9c0b1828
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="step-11-enable-replication-to-azure-for-hyper-v-vms-in-vmm-clouds"></a>步驟 11：啟用將 Hyper-V VM (位於 VMM 雲端中) 複寫至 Azure 的複寫功能

設定複寫原則後，請使用本文來啟用複寫功能，以使用 Azure 入口網站中的 [Azure Site Recovery](site-recovery-overview.md) 服務將 System Center Virtual Machine Manager (VMM) 雲端中管理的內部部署 Hyper-V 虛擬機器複寫至 Azure。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。


## <a name="before-you-start"></a>開始之前

請確定您的 Azure 帳戶有適當的[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)可建立 Azure VM。 [深入了解](../active-directory/role-based-access-built-in-roles.md) Azure 角色型存取控制。

## <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

依預設會複寫電腦上的所有磁碟。 您可以從複寫排除磁碟。 例如，您可能不想要複寫具有暫存資料的磁碟，或是每次機器或應用程式重新啟動時便重新整理的資料 (例如 pagefile.sys 或 SQL Server tempdb)。 [深入了解](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>複寫 VM

啟用 VM 複寫，如下所示︰  

1. 按一下 [步驟 2: 複寫應用程式]  >  [來源]。 第一次啟用複寫之後，請按一下保存庫中的 [+複寫]，以對其他機器啟用複寫。

    ![啟用複寫](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication1.png)
2. 在 [來源] 刀鋒視窗中，選取 VMM 伺服器和 Hyper-V 主機所在的雲端。 然後按一下 [確定] 。

    ![啟用複寫](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-source.png)
3. 在 [目標] 中，選取訂用帳戶、容錯移轉後的部署模型，以及您用於複寫資料的儲存體帳戶。

    ![啟用複寫](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-target.png)
4. 選取您要使用的儲存體帳戶。 如果您想使用與現有不同的儲存體帳戶，您可以[建立一個](#set-up-an-azure-storage-account)。 如果您將進階儲存體帳戶使用於複寫的資料，則必須選取其他標準儲存體帳戶來儲存複寫記錄，而這類記錄會擷取內部部署資料的進行中變更。若要使用 Resource Manager 模型建立儲存體帳戶，按一下 [新建]。 如果您想要使用傳統模型建立儲存體帳戶，請[在 Azure 入口網站中](../storage/common/storage-create-storage-account.md)執行該作業。 然後按一下 [確定] 。
5. 選取 Azure VM 在容錯移轉後啟動時所要建立的 Azure 網路和子網路。 選取 [立即設定選取的機器]，將網路設定套用至您選取要進行保護的所有機器。 選取 [稍後設定] 以選取每部機器的 Azure 網路。 如果您想使用與現有不同的網路，您可以[建立一個](#set-up-an-azure-network)。 若要使用 Resource Manager 模型來建立網路，請按一下 [新建]。 如果您想要使用傳統模型建立網路，請[在 Azure 入口網站中](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)執行該作業。 選取適用的子網路。 然後按一下 [確定] 。
6. 在 [虛擬機器] > [選取虛擬機器] 中，按一下並選取您要複寫的每部機器。 您只能選取可以啟用複寫的機器。 然後按一下 [確定] 。

    ![啟用複寫](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication5.png)

7. 在 [名稱]  > 中，為選取的 VM 選取作業系統，以及 OS 磁碟。

    - 確認 Azure VM 名稱 (目標名稱) 符合 [Azure 虛擬機器需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。   
    - 依預設會選取 VM 的所有磁碟以進行複寫，但您可以將磁碟清除以排除它們。

        - 您可能想要排除磁碟來減少複寫頻寬。 例如，您可能不想要複寫具有暫存資料的磁碟，或是每次電腦或應用程式重新啟動時便重新整理的資料 (例如 pagefile.sys 或 Microsoft SQL Server tempdb)。 您可以取消選取磁碟以將磁碟排除複寫。
        - 只有基本磁碟可以排除。 您無法排除作業系統磁碟。
        - 我們建議不要排除動態磁碟。 Site Recovery 無法識別客體 VM 內的虛擬硬碟為基本還是動態磁碟。 如果未排除所有的相依動態磁碟區磁碟，受保護的動態磁碟會在 VM 容錯移轉時顯示為失敗的磁碟，且該磁碟上的資料無法存取。
        - 啟用複寫後，您無法加入或移除複寫的磁碟。 如果您想要新增或排除磁碟，必須停用 VM 的保護，然後重新啟用它。
        - 您以手動方式在 Azure 中建立的磁碟將不會容錯回復。 例如，如果您容錯移轉三個磁碟，並直接在 Azure VM 中建立兩個磁碟，則只有那三個容錯移轉的磁碟會從 Azure 容錯回復至 Hyper-V。 您無法在容錯回復，或是從 Hyper-V 至 Azure 的反向複寫中包含手動建立的磁碟。
        - 如果您排除應用程式運作所需的磁碟，在容錯移轉至 Azure 之後，您必須在 Azure 中手動建立它，複寫的應用程式才能執行。 或者，您可以將 Azure 自動化整合至復原計畫，在電腦容錯移轉期間建立磁碟。

    按一下 [確定] 儲存變更。 您可以稍後再設定其他屬性。

    ![啟用複寫](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

8. 在 [複寫設定]  >  [進行複寫設定] 中，選取您要套用於受保護 VM 的複寫原則。 然後按一下 [確定] 。 您可以在 > [複寫原則] > 原則名稱 > [編輯設定] 中，修改複寫原則。 您套用的變更用於已在複寫的機器和新的機器。

   ![啟用複寫](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication7.png)

您可以在 [作業] >  [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。 執行 [完成保護] 作業之後，機器即準備好進行容錯移轉。



## <a name="next-steps"></a>後續步驟

移至[步驟 12：執行測試容錯移轉](vmm-to-azure-walkthrough-test-failover.md)
