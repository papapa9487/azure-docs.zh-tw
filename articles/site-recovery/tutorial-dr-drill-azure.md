---
title: "深入了解如何使用 Azure Site Recovery 為內部部署電腦及 Azure 執行災害復原演練 | Microsoft 文件"
description: "深入了解如何使用 Azure Site Recovery 為內部部署電腦及 Azure 執行災害復原演練"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ddd17921-68f4-41c7-ba4c-b767d36f1733
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 15e4487217ec21bb33380422640cb19dfcbcee39
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>執行 Azure 的災害復原演練

此教學課程會示範如何使用測試容錯移轉執行災害復原演練，從內部部署電腦至 Azure。 此演練可驗證您複寫策略未遺失資料。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 為測試容錯移轉設定隔離網路
> * 準備在容錯移轉後連接到 Azure VM
> * 執行單一機器測試容錯移轉

這是本系列的第四個教學課程 本教學課程假設您已經完成了先前教學課程中的工作。

1. [準備 Azure](tutorial-prepare-azure.md)
2. [準備內部部署 VMware](tutorial-prepare-on-premises-vmware.md)
3. [設定災害復原](tutorial-vmware-to-azure.md)

## <a name="verify-vm-properties"></a>驗證 VM 屬性

執行測試容錯移轉之前，請先驗證 VM 屬性，並確定 VM 遵守 [Azure 需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

1. 在 [受保護的項目] 中，按一下 [複寫的項目] > [VM]。
2. 在 [複寫的項目] 窗格中，將會呈現 VM 資訊、健康情況狀態，以及最新可用復原點的摘要。 如需檢視詳細資訊，請按一下 [屬性]。
3. 在 [計算與網路] 中，您可以修改 Azure 的名稱、資源群組、目標大小、[可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)，及管理磁碟設定。
4. 您可以檢視及修改網路設定，包括在容錯移轉後 Azure VM 所在的網路/子網路，以及要指派給它的 IP 位址。
5. 在 [磁碟] 中，您可以看見 VM 上作業系統和資料磁碟的相關資訊。

## <a name="run-a-test-failover-for-a-single-vm"></a>執行單一 VM 測試容錯移轉

當您執行測試容錯移轉時，會發生下列情況：

1. 程式會進行必要條件檢查，以確保所有容錯移轉所需之條件都已準備就緒。
2. 容錯移轉會處理資料，以便建立 Azure VM。 若選取最新的復原點，則復原點會從資料中建立。
3. 使用先前步驟中處理的資料來建立 Azure VM。

執行測試容錯移轉，如下所示：

1. 在 [設定] > [複寫的項目] 中，按一下 [VM] > [+測試容錯移轉]。

2. 選取要用於容錯移轉的復原點：
    - **最近處理**：將 VM 容錯移轉到 Site Recovery 所處理的最新復原點。 隨即顯示時間戳記。 使用此選項時，無須花費時間處理資料，因此它會提供低 RTO (復原時間目標)。
    - **最近的應用程式一致**：此選項會將所有 VM 容錯移轉到最新的應用程式一致復原點。 隨即顯示時間戳記。
    - **自訂**：選取任何復原點。
3. 在 [測試容錯移轉] 中，選取 Azure VM 在容錯移轉之後要連接的目標 Azure 網路。
4. 按一下 [確定]  即可開始容錯移轉。 您可以按一下 VM 開啟其內容以追蹤進度。 或者，您也可以按一下保存庫名稱中的 **[測試容錯移轉]** 工作 > **[設定]** > **[工作]** >
   **[Site Recovery 工作]**。
5. 容錯移轉完成之後，複本 Azure VM 會出現在 Azure 入口網站> [虛擬機器] 中。 確認 VM 為適當的大小、其已連線到正確的網路，而且正在執行中。
6. 您現在應該能夠連線到 Azure 中複寫的 VM 了
7. 若要刪除測試容錯移轉期間建立的 Azure VM，請按一下復原計劃上的 [清除測試容錯移轉]。 在 [記事] 中，記錄並儲存關於測試容錯移轉的任何觀察。

在某些情況下，容錯移轉需要額外的處理，這會耗費約 8 到 10 分鐘的時間來完成。 您可能注意到下列項目的測試容錯移轉時間較久：VMware Linux 機器、未啟用 DHCP 服務的 VMware VM，以及沒有下列開機驅動程式的 VMware VM：storvsc、vmbus、storflt、intelide、atapi。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在內部部署 VMware VM 執行容錯移轉和容錯回復](tutorial-vmware-to-azure-failover-failback.md)。

