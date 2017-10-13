---
title: "使用 Azure Site Recovery 來執行 Azure VM 複寫的測試容錯移轉 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 服務來執行將 Azure VM 複寫至另一個 Azure 區域之測試容錯移轉所需的步驟。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: e15c1b0c-5d75-4fdf-acb0-e61def9e9339
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 8babb0d016729f318442af93596d206c38d91206
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-run-a-test-failover-for-azure-vm-replication"></a>步驟 6：執行 Azure VM 複寫的測試容錯移轉

在啟用 Azure 虛擬機器 (VM) 的複寫之後，請遵循本文中的步驟，使用 Azure 入口網站中的 [Azure Site Recovery](site-recovery-overview.md) 服務，執行從一個 Azure 區域測試容錯移轉到另一個 Azure 區域。

- 當您完成本文時，應該已使用測試容錯移轉確認至少一個 Azure VM 可以容錯移轉到次要的 Azure 地區。 
- 請在本文下方張貼意見，或在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提出問題

>[!NOTE]
>
> Azure VM 複寫目前為預覽狀態。


## <a name="before-you-start"></a>開始之前

- 在您執行測試容錯移轉之前，建議您確認 VM 屬性，並進行任何需要的變更。 您可以在 [複寫的項目] 中存取 VM 屬性。 [程式集]  刀鋒視窗會顯示機器設定與狀態的相關資訊。
- 建議您針對測試容錯移轉而非設定為實際執行容錯移轉的網路 (預設或自訂)，使用不同的 Azure VM 網路。
- 測試容錯移轉會將 Azure VM (和其儲存體) 容錯移轉到次要 Azure 地區。 它不會複寫任何相依的應用程式或資源。 如果已容錯移轉 VM 上執行的應用程式相依於其他資源 (例如 Active Directory 或 DNS)，若它們尚未在次要區域中提供使用，您也需要將這些加以複寫。 [深入了解](site-recovery-test-failover-to-azure.md#prepare-active-directory-and-dns)
- 如果您需要在從內部部署網站容錯移轉之後存取複寫的 VM，就需要[準備連線](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)到這些 VM。

## <a name="run-a-test-failover"></a>執行測試容錯移轉

1. 在 [設定] > [複寫的項目] 中，按一下 [VM] [+測試容錯移轉]圖示。 

2. 在 [測試容錯移轉] 中，選取要用於容錯移轉的復原點：

    - **最近處理**：將 VM 容錯移轉到 Site Recovery 服務所處理的最新復原點。 隨即顯示時間戳記。 使用此選項時，無須花費時間處理資料，因此它會提供低 RTO (復原時間目標)。
    - **最近的應用程式一致**：此選項會將所有 VM 容錯移轉到最新的應用程式一致復原點。 隨即顯示時間戳記。 
    - **自訂**：選取任何復原點。
 
3. 選取 Azure VM 在容錯移轉之後，次要地區所要連線的目標 Azure 虛擬網路。
4. 若要開始容錯移轉，請按一下 [確定]。 若要追蹤進度，請按一下 VM 開啟其內容。 或者，您也可以按一下保存庫名稱中的 [測試容錯移轉] 作業 > [設定] > [作業] > [Site Recovery 作業]。
5. 容錯移轉完成之後，複本 Azure VM 會出現在 Azure 入口網站> [虛擬機器] 中。 請確定 VM 為適當的大小、已連線到適當的網路，而且正在執行中。
6. 若要刪除測試容錯移轉期間建立的 VM，請按一下複寫的項目或復原計劃上的 [清除測試容錯移轉]。 在 [記事] 中，記錄並儲存關於測試容錯移轉的任何觀察。 

[深入了解](site-recovery-test-failover-to-azure.md)測試容錯移轉。

## <a name="next-steps"></a>後續步驟

測試容錯移轉之後，即完成本逐步解說。 現在，深入了解在生產環境中執行容錯移轉：

- [深入了解](site-recovery-failover.md)不同類型的容錯移轉及如何執行。
- 深入了解[使用復原計劃](site-recovery-create-recovery-plans.md)容錯移轉多個 VM。
- 深入了解[使用復原計劃](site-recovery-create-recovery-plans.md)。
- 深入了解容錯移轉之後[重新保護 Azure VM](site-recovery-how-to-reprotect.md)。

