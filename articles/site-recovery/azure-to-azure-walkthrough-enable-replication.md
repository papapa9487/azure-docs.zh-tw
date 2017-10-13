---
title: "使用 Azure Site Recovery 來啟用將 Azure VM 複寫至其他 Azure 區域的複寫功能 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 服務來啟用將 VMware VM 複寫至 Azure 的複寫功能時所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a309644f-d36b-4188-bba7-ad45a2d9bede
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 8/01/2017
ms.author: raynew
ms.openlocfilehash: 8691ff9dbdb79ef445482b355b00859072d539e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-enable-replication-for-azure-vms"></a>步驟 5：啟用 Azure VM 的複寫


設定[復原服務保存庫](azure-to-azure-walkthrough-vault.md)之後，使用本文來啟用虛擬機器 (VM) 的複寫，以透過 [Azure Site Recovery](site-recovery-overview.md) 複寫到另一個 Azure 區域。 若要啟用複寫，請設定來源和目標設定、驗證複寫原則，並選取您想要複寫的 VM。

- 當您完成本文時，Azure VM 應會複寫到次要 Azure 區域。
- 請在本文下方張貼意見，或在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提出問題

>[!NOTE]
>
> Azure VM 複寫目前為預覽狀態。


## <a name="select-the-source"></a>選取來源

1. 在復原服務保存庫中，按一下 [+複寫]。
2. 在 [來源] 中，選取 [Azure-PREVIEW]。
2. 在 [來源位置] 中，選取 VM 目前執行所在的來源 Azure 區域。
3. 選取 VM 的 **Azure 虛擬機器部署模型**：[Resource Manager] 或 [傳統]。
4. 對於 Resource Manager VM 選取**來源資源群組**，或對於傳統 VM 選取**雲端服務**。
5. 按一下 [確定]  來儲存設定。

    ![設定來源](./media/azure-to-azure-walkthrough-enable-replication/source.png)

## <a name="select-the-vms"></a>選取 VM

Site Recovery 會擷取與訂用帳戶和資源群組/雲端服務相關聯的 VM 清單。

1. 在 [虛擬機器] 中，選取您要複寫的 VM。
2. 按一下 [確定] 。

    ![選取 VM](./media/azure-to-azure-walkthrough-enable-replication/vms.png)


## <a name="configure-settings"></a>配置設定

Site Recovery 會佈建目標區域 (根據來源區域設定) 及複寫原則的預設設定：

   - **目標位置**：您想要用於災害復原的目標區域。 我們建議目標位置符合 Site Recovery 保存庫的位置。
   - **目標資源群組**：目標區域中的 Azure VM 在容錯移轉後所屬的資源群組。 根據預設，Site Recovery 會在目標區域中建立具有 "asr" 尾碼的新資源群組。
   - **目標虛擬網路**：目標區域中的 Azure VM 在容錯移轉後所在的網路。 根據預設，Site Recovery 會在目標區域中建立具有 "asr" 尾碼的新虛擬網路 (和子網路)。 此網路會對應至您的來源網路。 請注意，如果您需要在來源和目標位置保留相同的 IP 位址，請可以在 VM 的容錯移轉之後，指派特定的 IP 位址。
   - **快取儲存體帳戶**：Site Recovery 會使用來源區域中的儲存體帳戶。 來源 VM 上的變更會在複寫到目標位置之前，先傳送到此帳戶。
   - **目標儲存體帳戶**：根據預設，Site Recovery 會在目標區域中建立新的儲存體帳戶，以反映來源 VM 儲存體帳戶。
   -  **目標可用性設定組**：根據預設，Site Recovery 會在目標區域中建立具有 "asr" 尾碼的新可用性設定組。
   - **複寫原則名稱**：原則名稱。
   - **復原點保留**：根據預設，Site Recovery 會保留復原點 24 小時。 您可以設定介於 1 與 72 小時之間的值。
   - **應用程式一致性快照的頻率**：根據預設，Site Recovery 會每隔 4 小時製作一份應用程式一致性快照集。 您可以設定介於 1 與 12 小時之間的任何值。 資料會持續複寫：
    - 損毀一致性復原點會在建立時維持一致的資料寫入順序。 如果您的應用程式設計成從損毀復原，但沒有資料不一致的情形，這類型的復原點通常就已足夠。
    - 系統會每隔數分鐘產生損毀一致性復原點。 使用這些復原點來容錯移轉和復原您的 VM，可依照分鐘的順序提供復原點目標 (RPO)。
    - 應用程式一致性復原點 (除了寫入順序一致性以外) 可確保執行中的應用程式會完成所有作業，並將緩衝區排清到磁碟 (應用程式靜止)。 建議您對資料庫應用程式 (例如 SQL Server、Oracle 和 Exchange) 使用這些復原點。

    ![配置設定](./media/azure-to-azure-walkthrough-enable-replication/settings.png)


### <a name="modify-settings"></a>修改設定

如果您想要修改目標與複寫原則設定，請執行下列動作：

1. 若要檢視或修改目標設定，請按一下 [設定]。
2. 若要覆寫預設目標設定，請按一下 [自訂]。 您可以指定目標資源群組、虛擬網路、可用性設定組和目標儲存體帳戶。 只有在 VM 是來源範圍中某個設定組的一部分，您才能新增可用性設定組。

    ![配置設定](./media/azure-to-azure-walkthrough-enable-replication/customize-target.png)

3. 若要覆寫復原點和應用程式一致性快照集的複寫設定，請按一下[複寫原則] 旁邊的 [自訂]。

    ![配置設定](./media/azure-to-azure-walkthrough-enable-replication/customize-policy.png)

4. 若要開始佈建目標資源，請按一下 [建立目標資源]。 佈建約需要一分鐘左右。 請勿在佈建期間關閉刀鋒視窗，否則需要從頭開始。




## <a name="enable-replication"></a>啟用複寫

1. 在 [設定] 中，按一下 [啟用複寫]。 這可進行您所選 VM 的初始複寫。 初始複寫狀態可能需要一些時間才會重新整理。 按一下 [重新整理] 以取得最新狀態。

2. 您可以在 [設定]  >  [作業]  >  [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。

3. 在 [設定] > [複寫的項目] 中，您可以檢視 VM 的狀態和初始複寫進度。 按一下 VM 可向下鑽研其設定。



## <a name="next-steps"></a>後續步驟

移至[步驟 6：執行測試容錯移轉](azure-to-azure-walkthrough-test-failover.md)
