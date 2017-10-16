---
title: "使用 DPM 將工作負載備份至 Azure 入口網站 | Microsoft Docs"
description: "使用 Azure 備份服務來備份 DPM 伺服器的簡介"
services: backup
documentationcenter: 
author: adigan
manager: nkolli
editor: 
keywords: "System Center Data Protection Manager, Data Protection Manager, DPM 備份"
ms.assetid: c8c322cf-f5eb-422c-a34c-04a4801bfec7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: adigan;giridham;jimpark;markgal;trinadhk
ms.openlocfilehash: 3422c8d57bdd786ce5d1a41fbb4c12cc4efffddd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>準備使用 DPM 將工作負載備份到 Azure
> [!div class="op_single_selector"]
> * [Azure 備份伺服器](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure 備份伺服器 (傳統)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (傳統)](backup-azure-dpm-introduction-classic.md)
>
>

本文簡介如何使用 Microsoft Azure 備份來保護 System Center Data Protection Manager (DPM) 伺服器和工作負載。 在閱讀本文後，您將了解：

* Azure DPM 伺服器備份的運作方式
* 使備份流暢的必要條件
* 遇到的一般錯誤以及如何排除
* 支援的案例

> [!NOTE]
> Azure 有兩種用來建立和使用資源的部署模型： [Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文提供的資訊和程序可供還原使用 Resource Manager 模型部署的 VM。
>
>

System Center DPM 會備份檔案和應用程式資料。 備份到 DPM 的資料可以儲存在磁帶、磁碟上，或使用 Microsoft Azure 備份來備份到 Azure。 DPM 與 Azure 備份的互動方式如下：

* **DPM 部署為實體伺服器或內部部署虛擬機器** — 如果 DPM 部署為實體伺服器或內部部署 Hyper-V 虛擬機器，除了備份到磁碟和磁帶上，您還可以將資料備份到復原服務保存庫。
* **DPM 部署為 Azure 虛擬機器** — 從 System Center 2012 R2 Update 3 開始，DPM 可以部署為 Azure 虛擬機器。 如果 DPM 部署為 Azure 虛擬機器，您可以將資料備份到連接至 DPM Azure 虛擬機器的 Azure 磁碟，或者您也可以將資料備份到復原服務保存庫以卸載資料儲存體。

## <a name="why-backup-from-dpm-to-azure"></a>為什麼要從 DPM 備份到 Azure？
使用 Azure 備份來備份 DPM 伺服器的商業利益包括：

* 在內部部署 DPM 部署中，您可以使用 Azure 來替代長期的磁帶部署。
* 在 Azure 的 DPM 部署中，Azure 備份可讓您卸載 Azure 磁碟中的儲存體，並在復原服務保存庫中儲存較舊的資料，而將較新的資料儲存在磁碟上以進行擴充。

## <a name="prerequisites"></a>必要條件
如下所示讓 Azure 備份做好備份 DPM 資料的準備：

1. **建立復原服務保存庫** — 在 Azure 入口網站中建立保存庫。
2. **下載保存庫認證** — 下載用來向復原服務保存庫註冊 DPM 伺服器的認證。
3. **安裝 Azure 備份代理程式** — 從 Azure 備份，在每一部 DPM 伺服器上安裝代理程式。
4. **註冊伺服器** — 向 [復原服務保存庫] 註冊 DPM 伺服器。

### <a name="1-create-a-recovery-services-vault"></a>1.建立復原服務保存庫。
若要建立復原服務保存庫：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表上按一下 [瀏覽]，然後在資源清單中輸入**復原服務**。 當您開始輸入時，清單將會根據您輸入的文字進行篩選。 按一下 [復原服務保存庫] 。

    ![建立復原服務保存庫的步驟 1](./media/backup-azure-dpm-introduction/open-recovery-services-vault.png)

    隨即會顯示 [復原服務保存庫] 清單。
3. 在 [復原服務保存庫] 功能表上，按一下 [新增]。

    ![建立復原服務保存庫的步驟 2](./media/backup-azure-dpm-introduction/rs-vault-menu.png)

    [復原服務保存庫] 刀鋒視窗隨即開啟，並提示您提供 [名稱]、[訂用帳戶]、[資源群組] 和 [位置]。

    ![建立復原服務保存庫的步驟 5](./media/backup-azure-dpm-introduction/rs-vault-attributes.png)
4. 在 [名稱] 中，輸入易記名稱來識別保存庫。 必須是 Azure 訂用帳戶中唯一的名稱。 輸入包含 2 到 50 個字元的名稱。 該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。
5. 按一下 [訂用帳戶]  以查看可用的訂用帳戶清單。 如果您不確定要使用哪個訂用帳戶，請使用預設 (或建議) 的訂用帳戶。 只有在您的組織帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。
6. 按一下 [資源群組] 以查看可用的資源群組清單，或按一下 [新增] 以建立新的資源群組。 如需資源群組的完整資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)
7. 按一下 [位置]  以選取保存庫的地理區域。
8. 按一下 [建立] 。 要等復原服務保存庫建立好，可能需要一些時間。 請監視入口網站右上方區域中的狀態通知。
   保存庫一旦建立好，就會在入口網站中開啟。

### <a name="set-storage-replication"></a>設定儲存體複寫
儲存體複寫選項有異地備援儲存體和本地備援儲存體可供您選擇。 根據預設，保存庫具有異地備援儲存體。 如果這是您的主要備份，請讓選項繼續設定為異地備援儲存體。 如果您想要更便宜但不持久的選項，請選擇本地備援儲存體。 在 [Azure 儲存體複寫概觀](../storage/common/storage-redundancy.md)中，深入了解[異地備援](../storage/common/storage-redundancy.md#geo-redundant-storage)和[本地備援](../storage/common/storage-redundancy.md#locally-redundant-storage)儲存體選項。

若要編輯儲存體複寫設定︰

1. 選取保存庫以開啟保存庫儀表板和 [設定] 刀鋒視窗。 如果 [設定] 刀鋒視窗未開啟，請按一下保存庫儀表板中的 [所有設定]。
2. 在 [設定] 刀鋒視窗上按一下 [備份基礎結構]  >  [備份設定]，開啟 [備份設定] 刀鋒視窗。 在 [備份設定]  刀鋒視窗上，選擇保存庫的儲存體複寫選項。

    ![備份保存庫的清單](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    選擇好保存庫的儲存體選項後，就可以開始建立 VM 與保存庫的關聯。 若要開始關聯，請探索及註冊 Azure 虛擬機器。

### <a name="2-download-vault-credentials"></a>2.下載保存庫認證
保存庫認證檔是入口網站針對每個備份保存庫所產生的憑證。 入口網站接著會將公開金鑰上傳至「存取控制服務」(ACS)。 憑證的私密金鑰可供使用者作為工作流程的一部分，屬於電腦註冊工作流程中的指定輸入。 這會對電腦進行驗證，以便將備份資料傳送至 Azure 備份服務中的已識別保存庫。

保存庫認證僅在註冊工作流程期間使用。 使用者應自行負責，並確保保存庫認證檔不會遭到破解。 保存庫認證檔落入任何惡意使用者的手中，則可用來針對相同的保存庫註冊其他電腦。 不過，當使用屬於客戶的複雜密碼來加密備份資料時，現有的備份資料不會遭到洩漏。 若要減輕這個問題，保存庫認證設在 48 小時後過期。 您可以下載復原服務的保存庫認證，次數不受限制，但僅最新的保存庫認證檔可在註冊工作流程期間提供使用。

保存庫認證檔會透過安全通道，從 Azure 入口網站下載。 Azure 備份服務不會知道憑證的私密金鑰，且私密金鑰不會保存在入口網站或服務中。 使用下列步驟將保存庫認證檔下載至本機電腦。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 開啟要註冊 DPM 電腦的復原服務保存庫。
3. 根據預設，[設定] 刀鋒視窗隨即會開啟。 如果該刀鋒視窗未開啟，請在保存庫儀表板上按一下 [設定]  ，以開啟 [設定] 刀鋒視窗。 在 [設定] 刀鋒視窗中，按一下 [屬性] 。

    ![開啟 [保存庫] 刀鋒視窗](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
4. 在 [屬性] 頁面中，按一下 [備份認證] 下方的 [下載]。 入口網站會產生可供下載的保存庫認證檔。

    ![下載](./media/backup-azure-dpm-introduction/vault-credentials.png)

入口網站會使用保存庫名稱和目前日期的組合來產生保存庫認證。 按一下 [ **儲存** ] 將保存庫認證下載至本機帳戶的下載資料夾，或從 [儲存] 功能表中選取 [另存新檔]，以指定保存庫認證的位置。 產生檔案需要一分鐘的時間。

### <a name="note"></a>注意
* 請確定保存庫認證檔儲存在可從電腦存取的位置。 如果它儲存在檔案共用/SMB，請檢查存取權限。
* 保存庫認證檔僅在註冊工作流程期間使用。
* 保存庫認證檔會在 48 小時後過期，並且可以從入口網站下載。

### <a name="3-install-backup-agent"></a>3.安裝備份代理程式
建立 Azure 備份保存庫之後，您的每部 Windows 電腦 (Windows Server、Windows 用戶端、System Center Data Protection Manager 伺服器，或 Azure 備份伺服器電腦) 上應該安裝代理程式，以便讓您將資料和應用程式備份至 Azure。

1. 開啟要註冊 DPM 電腦的復原服務保存庫。
2. 根據預設，[設定] 刀鋒視窗隨即會開啟。 如果該刀鋒視窗未開啟，請按一下 [設定]  以開啟 [設定] 刀鋒視窗。 在 [設定] 刀鋒視窗中，按一下 [屬性] 。

    ![開啟 [保存庫] 刀鋒視窗](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. 在 [設定] 頁面中，按一下 [Azure 備份代理程式] 下方的 [下載]。

    ![下載](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   代理程式下載完成後，按兩下 MARSAgentInstaller.exe 來啟動 Azure 備份代理程式的安裝。 選擇安裝資料夾和代理程式所需要的暫存資料夾。 指定的快取位置必須至少包含備份資料大小 5%的可用空間。
4. 若您使用 Proxy 伺服器連接至網際網路，請在 [ **Proxy 組態** ] 畫面上，輸入 Proxy 伺服器詳細資料。 若您使用已驗證的 Proxy，請在此畫面中輸入使用者名稱和密碼的詳細資料。
5. Azure 備份代理程式會安裝 .NET Framework 4.5 和 Windows PowerShell (若尚未安裝) 來完成安裝。
6. 安裝代理程式之後， 請 **關閉** 視窗。

   ![關閉](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)
7. 若要向保存庫**註冊 DPM 伺服器**，請在 [管理] 索引標籤中，按一下 [線上]。 接著，選取 [註冊] 。 隨即會開啟 [註冊設定] 精靈。
8. 若您使用 Proxy 伺服器連接至網際網路，請在 [ **Proxy 組態** ] 畫面上，輸入 Proxy 伺服器詳細資料。 若您使用已驗證的 Proxy，請在此畫面中輸入使用者名稱和密碼的詳細資料。

    ![Proxy 組態](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. 在保存庫認證畫面中，瀏覽並選取先前已下載的保存庫認證檔。

    ![保存庫認證](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    保存庫認證檔僅於 48 小時內有效 (從入口網站下載後起算)。 如果您在此畫面中遇到任何錯誤 (例如，「提供的保存庫認證檔已過期」)，請登入 Azure 入口網站，並再次下載保存庫認證檔。

    請確定保存庫認證檔位於可透過設定應用程式存取的位置。 如果您遇到存取權相關的錯誤，請將保存庫認證檔複製至此電腦中的暫存位置並重試作業。

    如果您遇到無效的保存庫認證錯誤 (例如，「提供的保存庫認證無效」)，檔案可能損毀或沒有復原服務所關聯的最新認證。 從入口網站下載新的保存庫認證檔後重試作業。 若使用者在 Azure 入口網站中連續快速地按下 [ **下載保存庫認證** ] 選項，則通常會看見此錯誤。 在此情況下，僅第二個保存庫認證檔為有效。
10. 若要控制工作期間以及非工作期間的網路頻寬使用，您可以在 [節流設定]  畫面中，設定頻寬使用限制，並定義工作與非工作時間。

    ![節流設定](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)
11. 在 [復原資料夾設定]  畫面上，瀏覽將暫存從 Azure 所下載檔案的資料夾。

    ![復原資料夾設定](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)
12. 在 [ **加密設定** ] 畫面中，您可以產生複雜密碼或提供複雜密碼 (最少 16 個字元)。 請記得將複雜密碼存放在安全的位置。

    ![加密](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > 如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。 加密複雜密碼為使用者所有，Microsoft 將不會看到使用者所使用的複雜密碼。 請將檔案儲存在安全的位置，在復原作業期間需要該檔案。
    >
    >
13. 一旦您按一下 [註冊]  按鈕，電腦即成功註冊至保存庫，且現在您已準備好開始備份至 Microsoft Azure。
14. 當使用 Data Protection Manager 時，您可以修改註冊工作流程期間所指定的設定，方法是選取 [管理] 索引標籤下的 [線上]，接著按一下 [設定] 選項。

## <a name="requirements-and-limitations"></a>需求 (和限制)
* DPM 可以做為實體伺服器或 System Center 2012 SP1 或 System Center 2012 R2 上安裝的 HYPER-V 虛擬機器來執行。 它也可以做為 System Center 2012 R2 (至少含 DPM 2012 R2 更新彙總套件 3) 上執行的 Azure 虛擬機器，或 System Center 2012 R2 (至少含更新彙總套件 5) 上執行之 VMWare 中的 Windows 虛擬機器來執行。
* 如果您使用 System Center 2012 SP1 來執行 DPM，則應該安裝 System Center Data Protection Manager SP1 的更新彙總套件 2。 要有此軟體才能安裝 Azure 備份代理程式。
* DPM 伺服器應該已安裝 Windows PowerShell 和 .Net Framework 4.5。
* DPM 可以將大部分的工作負載備份至 Azure 備份。 如需所支援項目的完整清單，請參閱下面的 Azure 備份支援項目。
* 使用 [複製到磁帶] 選項無法復原 Azure 備份中儲存的資料。
* 您需要已啟用 Azure 備份功能的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 請閱讀 [Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。
* 要使用 Azure 備份，就必須在您想要備份的伺服器上安裝 Azure 備份代理程式。 每個伺服器必須至少具有所要備份之資料大小的 5 % 做為本機可用儲存空間。 例如，備份 100GB 的資料時，在臨時位置中至少需要 5 GB 的可用空間。
* 資料會儲存在 Azure 保存庫儲存體中。 可以備份至 Azure 備份保存庫的資料數量沒有限制，但是資料來源 (例如虛擬機器或資料庫) 的大小不應超過 54400 GB。

下列檔案類型可支援備份至 Azure：

* 加密 (僅限完整備份)
* 壓縮 (支援增量備份)
* 疏鬆 (支援增量備份)
* 壓縮和疏鬆 (視為疏鬆來處理)

下列為不支援的類型：

* 不支援區分大小寫的檔案系統上的伺服器。
* 硬式連結 (略過)
* 重新分析點 (略過)
* 加密和壓縮 (略過)
* 加密和疏鬆 (略過)
* 壓縮資料流
* 疏鬆資料流

> [!NOTE]
> 從 System Center 2012 DPM SP1 開始，您可以使用 Microsoft Azure 備份將受到 DPM 保護的工作負載備份至 Azure。
>
>
