---
title: "使用 Azure Site Recovery 將 Hyper-V VM 複寫至 Azure (不含 System Center VMM) | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 啟用將 Hyper-V VM 複寫至 Azure 的複寫功能時所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: bd31ef01-69f1-4591-a519-e42510a6e2f4
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: aabe99dbd375b80e4a87ca7a067927008672b4ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-enable-replication-for-hyper-v-vms-replicating-to-azure"></a>步驟 10：啟用將 Hyper-V VM 複寫至 Azure 的複寫功能


本文說明如何在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務，啟用將內部部署 Hyper-V 虛擬機器 (非 System Center VMM 所管理) 複寫至 Azure 的複寫功能。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。




## <a name="before-you-start"></a>開始之前

在開始之前，請確定您的 Azure 使用者帳戶具有必要的[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能將新的虛擬機器複寫至 Azure。

## <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

依預設會複寫電腦上的所有磁碟。 您可以從複寫排除磁碟。 例如，您可能不想要複寫具有暫存資料的磁碟，或是每次機器或應用程式重新啟動時便重新整理的資料 (例如 pagefile.sys 或 SQL Server tempdb)。 [深入了解](site-recovery-exclude-disk.md)


## <a name="replicate-vms"></a>複寫 VM

啟用 VM 複寫，如下所示︰          

1. 按一下 [複寫應用程式] > [來源]。 第一次設定複寫之後，您可以按一下 [+複寫]，以對其他電腦啟用複寫。

    ![啟用複寫](./media/hyper-v-site-walkthrough-enable-replication/enable-replication.png)
2. 在 [來源] 中，選取 Hyper-V 網站。 然後按一下 [確定] 。
3. 在 [目標] 中，選取保存庫訂用帳戶，以及您想要在容錯移轉後於 Azure 中使用的容錯移轉模型 (傳統或資源管理)。
4. 選取您要使用的儲存體帳戶。 如果您沒有想要使用的帳戶，您可以[建立一個](#set-up-an-azure-storage-account)。 然後按一下 [確定] 。
5. 選取 Azure VM 在容錯移轉後啟動時所要連線的 Azure 網路和子網路。

    - 若要將網路設定套用至您啟用要進行複寫的所有電腦，請選取 [立即設定選取的機器]。
    - 選取 [稍後設定] 以選取每部機器的 Azure 網路。
    - 如果您沒有想要使用的網路，您可以[建立一個](#set-up-an-azure-network)。 選取適用的子網路。 然後按一下 [確定] 。

   ![啟用複寫](./media/hyper-v-site-walkthrough-enable-replication/enable-replication11.png)

6. 在 [虛擬機器] > [選取虛擬機器] 中，按一下並選取您要複寫的每部機器。 您只能選取可以啟用複寫的機器。 然後按一下 [確定] 。

    ![啟用複寫](./media/hyper-v-site-walkthrough-enable-replication/enable-replication5-for-exclude-disk.png)

7. 在 [名稱]  > 中，為選取的 VM 選取作業系統，以及 OS 磁碟。
8. 確認 Azure VM 名稱 (目標名稱) 符合 [Azure 虛擬機器需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
9. 依預設會選取 VM 的所有磁碟以進行複寫。 清除磁碟以將它們排除。
10. 按一下 [確定] 儲存變更。 您可以稍後再設定其他屬性。

    ![啟用複寫](./media/hyper-v-site-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

11. 在 [複寫設定]  >  [進行複寫設定] 中，選取您要套用於受保護 VM 的複寫原則。 然後按一下 [確定] 。 您可以在 [複寫原則] > 原則名稱 > [編輯設定] 中，修改複寫原則。 您套用的變更將用於已在複寫的機器和新的機器。


   ![啟用複寫](./media/hyper-v-site-walkthrough-enable-replication/enable-replication7.png)

您可以在 [作業] >  [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。 執行 [完成保護]  作業之後，機器即準備好進行容錯移轉。


## <a name="next-steps"></a>後續步驟


移至[步驟 11：執行測試容錯移轉](hyper-v-site-walkthrough-test-failover.md)
