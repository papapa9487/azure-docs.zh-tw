---
title: "使用 Azure Site Recovery 設定實體內部部署伺服器至 Azure 的災害復原 | Microsoft Docs"
description: "了解如何使用 Azure Site Recovery 服務，以設定內部部署 Windows 和 Linux 伺服器至 Azure 的災害復原。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 805f6946-c6da-491f-980e-bf724bebdf0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2f9a5092d4dacce8aa1630830499635b3b4eb151
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>設定內部部署實體伺服器至 Azure 的災害復原

[Azure Site Recovery](site-recovery-overview.md) 服務可藉由管理及協調內部部署電腦與 Azure 虛擬機器 (VM) 的複寫、容錯移轉及容錯回復，為您的災害復原策略做出貢獻。

本教學課程說明如何設定內部部署實體 Windows 和 Linux 伺服器至 Azure 的災害復原。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 設定 Azure 與內部部署的必要條件
> * 建立 Site Recovery 的復原服務保存庫 
> * 設定來源和目標複寫環境
> * 建立複寫原則
> * 啟用伺服器的複寫

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

- 請確定您了解[情節架構和元件](concepts-physical-to-azure-architecture.md)。
- 檢閱所有元件的[支援需求](site-recovery-support-matrix-to-azure.md)。
- 請確定您想要複寫的伺服器符合 [Azure VM 需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
- 準備 Azure。 您需要 Azure 訂用帳戶、Azure 虛擬網路及儲存體帳戶。
- 準備帳戶以自動在您要複寫的每個伺服器上安裝行動服務。



### <a name="set-up-an-azure-account"></a>設定 Azure 帳戶

取得 [Microsoft Azure 帳戶](http://azure.microsoft.com/)。

- 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。
- 了解 [Site Recovery 價格](site-recovery-faq.md#pricing)，並取得[定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
- 找出 Site Recovery [支援的區域](https://azure.microsoft.com/pricing/details/site-recovery/)。

### <a name="verify-azure-account-permissions"></a>確認 Azure 帳戶權限

請確定您的 Azure 帳戶具有權限將 VM 複寫至 Azure。

- 檢閱您將機器複寫至 Azure 所需的[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。
- 驗證並修改[角色型存取](../active-directory/role-based-access-control-configure.md)權限。 



### <a name="set-up-an-azure-network"></a>設定 Azure 網路

設定 [Azure 網路](../virtual-network/virtual-network-get-started-vnet-subnet.md)。

- 在容錯移轉之後建立的 Azure VM 會置於這個網路。
- 此網路應位於與復原服務保存庫相同的區域


## <a name="set-up-an-azure-storage-account"></a>設定 Azure 儲存體帳戶

設定 [Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)。

- Site Recovery 會將內部部署機器複寫至 Azure 儲存體。 容錯移轉發生後，會從儲存體建立 Azure VM。
- 儲存體帳戶與復原服務保存庫必須位於相同的區域。
- 此儲存體帳戶可以是標準或[進階](../storage/common/storage-premium-storage.md)帳戶。
- 如果您設定的是進階帳戶，則也需要一個額外的標準帳戶來記錄資料。



### <a name="prepare-an-account-for-mobility-service-installation"></a>準備一個用來安裝行動服務的帳戶

行動服務必須安裝在您要複寫的每部伺服器上。 當您啟用伺服器的複寫時，Site Recovery 會自動安裝此服務。 若要自動安裝，您必須準備一個可供 Site Recovery 用來存取伺服器的帳戶。

- 您可以使用網域或本機帳戶
- 若是 Windows VM，如果您不使用網域帳戶，請停用本機電腦上的遠端使用者存取控制。 若要這樣做，請在登錄的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下，新增 DWORD 項目 **LocalAccountTokenFilterPolicy**，值為 1。
- 若要從 CLI 新增登錄項目以停用設定，請輸入：``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- 在 Linux 上，帳戶應該是來源 Linux 伺服器上的根使用者。


## <a name="create-a-vault"></a>建立保存庫

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>選取保護目標

選取要複寫的內容及複寫目的地。

1. 按一下 [復原服務保存庫] > 保存庫。
2. 在 [資源] 功能表中，按一下 [Site Recovery] > [準備基礎結構] > [保護目標]。
3. 在 [保護目標] 中，選取 [至 Azure] > [未虛擬化/其他]。

## <a name="set-up-the-source-environment"></a>設定來源環境

安裝設定伺服器、註冊在保存庫及探索 VM。

1. 按一下 [Site Recovery] > [準備基礎結構] > [來源]。
2. 如果您沒有設定伺服器，請按一下 [+設定伺服器]。
3. 在 [新增伺服器] 中，檢查 [設定伺服器] 是否出現在 [伺服器類型] 中。
4. 下載 Site Recovery 統一安裝的安裝檔案。
5. 下載保存庫註冊金鑰。 您會在執行統一安裝時用到此金鑰。 該金鑰在產生後會維持 5 天有效。

   ![設定來源](./media/tutorial-physical-to-azure/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>在保存庫中註冊設定伺服器

開始之前，請執行下列動作： 

- 在組態伺服器機器上，確定系統時鐘會與[時間伺服器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)同步。 應該相符。 如果快慢誤差 15 分鐘，安裝可能會失敗。
- 請確定主機可以存取這些 URL：       [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

- 以 IP 位址為基礎的防火牆規則都應該允許對 Azure 的通訊。
- 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。
- 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。

以本機系統管理員身分執行整合安裝程式，以安裝設定伺服器。 根據預設，處理序伺服器與主要目標伺服器也會安裝在設定伺服器上。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

註冊完成後，設定伺服器會顯示在保存庫的 [設定] > [伺服器] 頁面上。


## <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。

1. 按一下 [準備基礎結構] > [目標]，然後選取您要使用的 Azure 訂用帳戶。
2. 指定目標部署模型。
3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

   ![目標](./media/tutorial-physical-to-azure/network-storage.png)


## <a name="create-a-replication-policy"></a>建立複寫原則

1. 若要建立新的複寫原則，請按一下 [Site Recovery 基礎結構] > [複寫原則] > [+複寫原則]。
2. 在 [建立複寫原則]中，指定原則名稱。
3. 在 [RPO 閾值] 中，指定復原點目標 (RPO) 限制。 這個值指定資料復原點的建立頻率。 連續複寫超過此限制時會產生警示。
4. 在 [復原點保留] 中，指定每個復原點的保留週期長度 (以小時為單位)。 複寫的 VM 可以還原至一個週期內的任何時間點。 複寫至進階儲存體的電腦支援最長保留 24 小時，標準儲存體則是 72 小時。
5. 在 [應用程式一致快照頻率] 中，指定建立包含應用程式一致快照之復原點的頻率 (以分鐘為單位)。 按一下 [確定]  以建立原則。

    ![複寫原則](./media/tutorial-physical-to-azure/replication-policy.png)


此原則會自動與設定伺服器產生關聯。 依預設會自動建立容錯回復的比對原則。 例如，如果複寫原則是 **rep-policy**，則建立的容錯回復原則是 **rep-policy-failback**。 從 Azure 起始容錯回復時才會使用此原則。

## <a name="enable-replication"></a>啟用複寫

啟用每個伺服器的複寫。

- 啟用複寫時，Site Recovery 會安裝行動服務。
- 啟用伺服器的複寫時，可能需要 15 分鐘或更久的時間，變更才會生效並顯示在入口網站中。

1. 按一下 [複寫應用程式] > [來源]。
2. 在 [來源] 中，選取設定伺服器。
3. 在 [機器類型] 中，選取 [實體機器]。
4. 選取處理序伺服器 (設定伺服器)。 然後按一下 [確定] 。
5. 在 [目標] 中，選取您想要在容錯移轉後，在其中建立 Azure VM 的訂用帳戶和資源群組。 選擇您想要在 Azure (傳統或資源管理) 中使用的部署模型。
6. 選取您要用來複寫資料的 Azure 儲存體帳戶。 
7. 選取 Azure VM 在容錯移轉後啟動時所要建立的 Azure 網路和子網路。
8. 選取 [立即設定選取的機器]，將網路設定套用至您選取要進行保護的所有機器。 選取 [稍後設定] 以選取每部機器的 Azure 網路。 
9. 在 [實體機器] 中，按一下 [+實體機器]。 指定名稱和 IP 位址。 選擇您想要複寫之機器的作業系統。 需要經過幾分鐘才會發現並列出伺服器。 
10. 在 [名稱]  > 中，選取處理序伺服器將用來在機器上自動安裝行動服務的帳戶。
11. 在 [複寫設定] > [設定複寫設定] 中，確認已選取正確的複寫原則。 
12. 按一下 [啟用複寫] 。 您可以在 [設定]  >  [作業]  >  [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。 執行 [完成保護]  作業之後，機器即準備好進行容錯移轉。


若要監視您新增的伺服器，您可以在 [設定伺服器] > [上次連絡時間] 中查看上次探索伺服器的時間。 若要新增機器而不等候已排定的探索時間，請醒目提示設定伺服器 (不要按一下)，然後按一下 [重新整理]。

## <a name="next-steps"></a>後續步驟

[執行災害復原演練](tutorial-dr-drill-azure.md)

