---
title: "使用 Azure Site Recovery 將內部部署機器移轉至 Azure | Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 將內部部署機器移轉至 Azure。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.openlocfilehash: bbee77e669f49bdebb57121df8672a9253945b3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-on-premises-machines-to-azure"></a>將內部部署機器移轉至 Azure

[Azure Site Recovery](site-recovery-overview.md) 服務會管理並協調內部部署機器和 Azure 虛擬機器 (VM) 的複寫、容錯移轉和容錯回復。

本教學課程說明如何使用 Site Recovery 將內部部署 VM 和實體伺服器移轉至 Azure。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 設定部署的必要條件
> * 建立 Site Recovery 的復原服務保存庫
> * 部署內部部署管理伺服器
> * 設定複寫原則並啟用複寫
> * 執行災害復原演練，確定一切運作正常
> * 執行一次性容錯移轉至 Azure

## <a name="overview"></a>概觀

若要移轉機器，請啟用其複寫功能，再將它容錯移轉至 Azure。


## <a name="prerequisites"></a>必要條件

以下是您必須在此教學課程中執行的作業。

- [準備](tutorial-prepare-azure.md) Azure 資源，包括 Azure 訂用帳戶、Azure 虛擬網路和儲存體帳戶。
- [準備](tutorial-prepare-on-premises-vmware.md) VMware 內部部署 VMware 伺服器和 VM。
- 請注意，不支援並行虛擬驅動程式匯出的裝置。


## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>選取保護目標

選取您要複寫的項目以及您要複寫到的位置。
1. 按一下 [復原服務保存庫] > 保存庫。
2. 在 [資源功能表] 中，按一下 [Site Recovery] > [準備基礎結構] > [保護目標]。
3. 在 [保護目標] 中，選取：
    - **VMware**：選取 [至 Azure] > [es, with VMWare vSphere Hypervisor] \(是，使用 VMware vSphere Hypervisor)。
    - **實體機器**：選取 [至 Azure] > [未虛擬化/其他]。
    - **Hyper-V**：選取 [至 Azure] > [Yes, with Hyper-V] \(是，使用 Hyper-V)。


## <a name="set-up-the-source-environment"></a>設定來源環境

- [設定](tutorial-vmware-to-azure.md#set-up-the-source-environment) VMware VM 的來源環境。
- [設定](tutorial-physical-to-azure.md#set-up-the-source-environment)實體伺服器的來源環境。
- [設定](tutorial-hyper-v-to-azure.md#set-up-the-source-environment) Hyper-V VM 的來源環境。

## <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。

1. 按一下 [準備基礎結構] > [目標]，然後選取您要使用的 Azure 訂用帳戶。
2. 指定目標部署模型。
3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

## <a name="create-a-replication-policy"></a>建立複寫原則

- [設定 VMware VM 的複寫原則](tutorial-vmware-to-azure.md#create-a-replication-policy)。


## <a name="enable-replication"></a>啟用複寫

- [啟用 VMware VM 的複寫](tutorial-vmware-to-azure.md#enable-replication)。


## <a name="run-a-test-migration"></a>執行測試移轉

執行[測試容錯移轉](tutorial-dr-drill-azure.md)至 Azure，確定一切都沒問題。


## <a name="migrate-to-azure"></a>移轉至 Azure

針對您要移轉的機器執行容錯移轉。

1. 在 [設定] > [複寫的項目] 中，按一下機器 > [容錯移轉]。
2. 在 [容錯移轉] 中，選取要容錯移轉的目標**復原點**。 選取最新的復原點。
3. 加密金鑰設定不適用於此案例。
4. 如果想在觸發容錯移轉之前，讓 Site Recovery 嘗試將來源虛擬機器關機，請選取 [先將機器關機再開始容錯移轉]。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業] 頁面上追蹤容錯移轉進度。
5. 確認 Azure VM 如預期般出現在 Azure 中。
6. 在 [複寫的項目] 中，以滑鼠右鍵按一下 VM > [完成移轉]。 這會完成移轉程序、停止 VM 的複寫功能，並停止 VM 的 Site Recovery 計費。

    ![完成移轉](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **不要取消正在進行的容錯移轉**：在啟動容錯移轉之前，已停止 VM 複寫。 如果您取消正在進行的容錯移轉，容錯移轉會停止，但 VM 不會再次複寫。

在某些情況下，容錯移轉需要額外的處理，這會耗費約 8 到 10 分鐘的時間來完成。 您可能注意到下列項目的測試容錯移轉時間較久：實體伺服器、VMware Linux 機器、未啟用 DHCP 服務的 VMware VM，以及沒有下列開機驅動程式的 VMware VM：storvsc、vmbus、storflt、intelide、atapi。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移轉至 Azure 後，將 Azure VM 複寫至另一個區域](site-recovery-azure-to-azure-after-migration.md)
