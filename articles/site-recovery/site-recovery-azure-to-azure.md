---
title: "對於災害復原需求，複寫 Azure 區域之間的 Azure VM：Azure 至 Azure |Microsoft Docs"
description: "摘要說明對於災害復原需求，使用 Azure Site Recovery 服務在 Azure 區域 (Azure 至 Azure) 之間複寫 Azure VM 所需的步驟。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: raynew
ms.openlocfilehash: 9ca33057f6030fdcc233f6053fdc392d62f8f9f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>使用 Azure Site Recovery 在區域之間椱寫 Azure VM

>[!NOTE]
>
> Azure 虛擬機器 (VM) 的 Azure Site Recovery 複寫目前為預覽狀態。

本文說明如何在 Azure 入口網站中使用 [Site Recovery](site-recovery-overview.md) 服務，在 Azure 區域之間複寫 Azure VM。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) (英文) 上張貼意見或問題。

## <a name="disaster-recovery-in-azure"></a>Azure 的災害復原

對於在 Azure VM 上執行的工作負載而言，內建的 Azure 基礎結構功用和功能提供強固且彈性的可用性策略。 不過，您經常需要自行規劃 Azure 區域之間的災害復原：

- 對於需要業務續航力和災害復原 (BCDR) 策略的特定應用程式和工作負載，您必須符合合規性指導方針。
- 您想要能夠根據您的商務決策保護和復原 Azure VM，而不只根據內建的 Azure 功能。
- 您需要根據商務與合規性需求測試容錯移轉和復原，而不影響生產環境。
- 您需要在發生災害時容錯移轉到復原區域，並順暢地容錯回復到原始的來源地區。

使用 Azure-Azure VM 複寫的 Site Recovery 可協助您執行所有這些工作。


## <a name="why-use-site-recovery"></a>為什麼要使用 Site Recovery？      

Site Recovery 提供在區域之間複寫 Azure VM 的簡易方式：

- **自動部署**。 不同於主動-主動複寫模型，次要地區不需要昂貴且複雜的基礎結構。 您啟用複寫時，Site Recovery 會根據來源地區設定，在目標區域中自動建立所需的資源。
- **控制區域**。 使用 Site Recovery，即可從同一洲的任何地區複寫到任何地區。 使用讀取權限異地備援儲存體 (僅以非同步方式在標準[配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)之間複寫) 比較此項。 讀取權限異地備援儲存體提供目標區域資料的唯讀存取權限。
- **自動化複寫**。 Site Recovery 提供自動化連續複寫。 按一下即可觸發容錯移轉和容錯回復。
- **RTO 和 RPO**。 Site Recovery 會利用連線區域的 Azure 網路基礎結構，維持相當低的 RTO 和 RPO。
- **測試**。 您可藉由隨選測試容錯移轉視需要執行災害復原演練，完全不影響生產工作負載或進行中的複寫。
- **復原計劃**。 您可以使用復原計劃，對於在多個 VM 上執行的整個應用程式協調容錯移轉和容錯回復。 復原計劃功能已與 Azure 自動化 Runbook 達到絕佳的一流整合。


## <a name="deployment-summary"></a>部署摘要

以下摘要說明設定在 Azure 區域之間執行 VM 複寫所需的步驟：

1. 建立復原服務保存庫。 保存庫包含組態設定，並會協調複寫。
2. 啟用 Azure VM 的複寫。
3. 執行測試容錯移轉，確定一切都沒問題。

>[!IMPORTANT]
>
> 您可以檢查 [Azure VM 複寫的支援矩陣](./site-recovery-support-matrix-azure-to-azure.md)。

>[!IMPORTANT]
>
> 如需如何對於 Azure VM 設定所需網路輸出連線能力進行 Site Recovery 複寫的資訊，請參閱[網路指引文件](./site-recovery-azure-to-azure-networking-guidance.md)。

### <a name="before-you-start"></a>開始之前

* 您的 Azure 使用者帳戶必須具有特定[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能啟用 Azure 虛擬機器的複寫功能。
* 必須啟用您的 Azure 訂用帳戶，才能在要做為災害復原區域的目標位置中建立 VM。 請連絡支援人員啟用所需的配額。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> 建議您在要 VM 複寫的位置中，建立復原服務保存庫。 例如，如果您的目標位置是美國中部、請在**美國中部**建立保存庫。

## <a name="enable-replication"></a>啟用複寫

在 [復原服務保存庫] 中，按一下保存庫名稱。 在保存庫中，按一下 [+ 複寫] 按鈕。

### <a name="step-1-configure-the-source"></a>步驟 1. 設定來源
1. 在 [來源] 中，選取 [Azure-PREVIEW]。
2. 在 [來源位置] 中，選取 VM 目前執行所在的來源 Azure 區域。
3. 選取 VM 的部署模型：[資源管理員] 或 [傳統]。
4. 對於資源管理員 VM 選取**來源資源群組**，或對於傳統 VM 選取**雲端服務**。

    ![設定來源](./media/site-recovery-azure-to-azure/source.png)

### <a name="step-2-select-virtual-machines"></a>步驟 2. 選取虛擬機器

* 選取要複寫的 VM，然後按一下確定。

    ![選取 VM](./media/site-recovery-azure-to-azure/vms.png)

### <a name="step-3-configure-settings"></a>步驟 3. 配置設定

1. 若要覆寫預設的目標設定，並指定您選擇的設定，請按一下 [自訂]。 如需詳細資訊，請參閱[自訂目標資源](site-recovery-replicate-azure-to-azure.md##customize-target-resources)。

    ![配置設定](./media/site-recovery-azure-to-azure/settings.png)


2. 根據預設，Site Recovery 會建立複寫原則，每隔 4 小時會製作應用程式一致快照，並將復原點保留 24 小時。 若要使用不同的設定建立原則，請按一下 [複寫原則] 旁的 [自訂]。

    ![自訂原則](./media/site-recovery-azure-to-azure/customize-policy.png)

3. 若要開始佈建目標資源，請按一下 [建立目標資源]。 佈建約需要一分鐘左右。 請勿在佈建期間關閉刀鋒視窗，否則需要從頭開始。

4. 若要觸發所選 VM 的複寫，請按一下 [啟用複寫]。

5. 您可以在 [設定]  >  [作業]  >  [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。

6. 在 [設定] > [複寫的項目] 中，您可以檢視 VM 的狀態和初始複寫進度。 按一下 VM 可向下鑽研其設定。

## <a name="run-a-test-failover"></a>執行測試容錯移轉

一切就緒後，執行測試容錯移轉，確定一切如預期般運作：

1. 若要容錯移轉單一機器，請在 [設定]  >  [複寫的項目] 中，按一下 VM [+測試容錯移轉] 圖示。

2. 若要容錯移轉復原方案，請在 [設定]  >  [復原方案] 中，以滑鼠右鍵按一下方案 [測試容錯移轉]。 若要建立復原方案，請[遵循這些指示](site-recovery-create-recovery-plans.md)。 

3. 在 [測試容錯移轉] 中，選取 Azure VM 在容錯移轉之後所要連線的目標 Azure 虛擬網路。

4. 若要開始容錯移轉，請按一下 [確定]。 若要追蹤進度，請按一下 VM 開啟其內容。 您也可以按一下保存庫名稱中的 [測試容錯移轉]作業 > [設定] > [作業] > [Site Recovery 作業]。

5. 容錯移轉完成之後，複本 Azure 電腦會出現在 Azure 入口網站> [虛擬機器] 中。 請確定 VM 為適當的大小、已連線到適當的網路，而且正在執行中。

6. 若要刪除測試容錯移轉期間建立的 VM，請按一下複寫的項目或復原計劃上的 [清除測試容錯移轉]。 在 [記事] 中，記錄並儲存關於測試容錯移轉的任何觀察。 

[深入了解](site-recovery-test-failover-to-azure.md)測試容錯移轉。


## <a name="next-steps"></a>後續步驟

測試部署後：

- [深入了解](site-recovery-failover.md)不同類型的容錯移轉及執行方法。
- 深入了解[使用復原計劃](site-recovery-create-recovery-plans.md)降低 RTO。
