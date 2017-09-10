---
title: "將 Windows 系統狀態備份至 Azure | Microsoft Docs"
description: "了解如何將 Windows Server 和/或 Windows 電腦的系統狀態備份至 Azure。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: carmonm
editor: 
keywords: "如何備份; 如何備份; 備份檔案和資料夾"
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: saurse;markgal
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6fbd96935f444d8b0c6d068ebd0d28e612f19816
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>在 Resource Manager 部署中備份 Windows 系統狀態
本文說明如何將 Windows Server 的系統狀態備份至 Azure。 本文中的教學課程是要讓您逐步了解基本概念。

如果您想要深入了解 Azure 備份，請閱讀此 [概觀](backup-introduction-to-azure-backup.md)。

如果您沒有 Azure 訂用帳戶，請建立 [免費帳戶](https://azure.microsoft.com/free/) ，以便存取任何 Azure 服務。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫
若要備份檔案與資料夾，您必須在要儲存這些資料的區域中建立復原服務保存庫。 您也需要決定儲存體的複寫方式。

### <a name="to-create-a-recovery-services-vault"></a>建立復原服務保存庫
1. 如果您尚未這麼做，請使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。
2. 在 [中樞] 功能表上按一下 [更多服務]，在資源清單中輸入**復原服務**，然後按一下 [復原服務保存庫]。

    ![建立復原服務保存庫的步驟 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    如果訂用帳戶中有復原服務保存庫，則會列出保存庫。
3. 在 [復原服務保存庫] 功能表上，按一下 [新增]。

    ![建立復原服務保存庫的步驟 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    [復原服務保存庫] 刀鋒視窗隨即開啟，並提示您提供 [名稱]、[訂用帳戶]、[資源群組] 和 [位置]。

    ![建立復原服務保存庫的步驟 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. 在 [名稱] 中，輸入易記名稱來識別保存庫。 必須是 Azure 訂用帳戶中唯一的名稱。 輸入包含 2 到 50 個字元的名稱。 該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。

5. 在 [訂用帳戶] 區段中，使用下拉式功能表來選擇 Azure 訂用帳戶。 如果您只使用一個訂用帳戶，該訂用帳戶會出現，您可以跳到下一個步驟。 如果您不確定要使用哪個訂用帳戶，請使用預設 (或建議) 的訂用帳戶。 只有在您的組織帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。

6. 在 [資源群組] 區段中︰

    * 如果您想建立新的資源群組，請選取 [新建]。
    或
    * 選取 [使用現有的]﹐然後按一下下拉式功能表，以查看可用的資源群組清單。

  如需資源群組的完整資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。

7. 按一下 [位置]  以選取保存庫的地理區域。 此選項會決定您的備份資料要傳送到哪個地理區域。

8. 按一下 [復原服務保存庫] 刀鋒視窗底部的 [建立]。

    建立復原服務保存庫可能需要一些時間。 請監視入口網站右上方區域中的狀態通知。 保存庫一旦建立好，就會出現在 [復原服務保存庫] 的清單中。 在數分鐘之後﹐如果您沒有看到您的保存庫，請按一下 [重新整理]。

    ![按一下 [重新整理] 按鈕。](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    一旦在復原服務保存庫清單中看到您的保存庫，您即可開始設定儲存體備援。

### <a name="set-storage-redundancy-for-the-vault"></a>設定保存庫的儲存體備援
當您建立復原服務保存庫時，務必以您想要的方式設定儲存體備援。

1. 從 [復原服務保存庫] 刀鋒視窗，按一下 [新增保存庫]。

    ![從復原服務保存庫清單中選取新的保存庫](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    當您選取保存庫時，[復原服務保存庫] 刀鋒視窗會縮小﹐而 [設定] 刀鋒視窗 (頂端有保存庫名稱) 和 [保存庫詳細資料] 刀鋒視窗隨即開啟。

    ![檢視新保存庫的儲存體組態](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. 在新保存庫的 [設定] 刀鋒視窗中，使用垂直滑桿捲動至 [管理] 區段，然後按一下 [備份基礎結構]。
    [備份基礎結構] 刀鋒視窗隨即開啟。
3. 在 [備份基礎結構] 刀鋒視窗中，按一下 [備份設定]開啟 [備份設定] 刀鋒視窗。

    ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. 為保存庫選擇適當的儲存體複寫選項。

    ![儲存體組態選項](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    根據預設，保存庫具有異地備援儲存體。 如果您使用 Azure 做為主要的備份儲存體端點，請繼續使用 [異地備援]。 如果您未使用 Azure 做為主要的備份儲存體端點，則選擇 [本地備援]，以減少 Azure 儲存體成本。 在此[儲存體備援概觀](../storage/common/storage-redundancy.md)中，深入了解[異地備援](../storage/common/storage-redundancy.md#geo-redundant-storage)和[本地備援](../storage/common/storage-redundancy.md#locally-redundant-storage)儲存體選項。

您已建立了保存庫，接著請設定它來備份 Windows 系統狀態。

## <a name="configure-the-vault"></a>設定保存庫
1. 在 [復原服務保存庫] 刀鋒視窗上 (針對剛建立的保存庫)，在 [開始使用] 區段中按一下 [備份]，然後在 [開始使用備份功能] 刀鋒視窗上，選取 [備份目標]。

    ![開啟備份目標刀鋒視窗](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    [備份目標] 刀鋒視窗隨即開啟。

    ![開啟備份目標刀鋒視窗](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. 從 [您的工作負載在何處執行?] 下拉式功能表中，選取 [內部部署]。

    因為您的 Windows Server 或 Windows 電腦是不在 Azure 中的實體電腦，所以您選擇 [內部部署]。

3. 從 [您要備份什麼?] 功能表中，選取 [系統狀態]，然後按一下 [確定]。

    ![設定檔案和資料夾](./media/backup-azure-system-state/backup-goal-system-state.png)

    按一下 [確定] 後，[備份目標] 旁會出現勾選記號，且 [準備基礎結構] 刀鋒視窗隨即開啟。

    ![已設定備份目標，接下來是準備基礎結構](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. 在 [準備基礎結構] 刀鋒視窗上，按 [下載 Windows Server 或 Windows Client 的代理程式]。

    ![準備基礎結構](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    如果您使用 Windows Server Essential，請選擇下載 Windows Server Essential 的代理程式。 快顯功能表會提示您執行或儲存 MARSAgentInstaller.exe。

    ![MARSAgentInstaller dialog](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. 在下載快顯功能表中，按一下 [儲存]。

    根據預設，**MARSagentinstaller.exe** 檔案會儲存至 [下載] 資料夾。 安裝程式完成時，您會看到快顯視窗，詢問您是否要執行安裝程式，或開啟資料夾。

    ![準備基礎結構](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    您還不需要安裝代理程式。 您可以在下載保存庫認證之後﹐安裝代理程式。

6. 在 [準備基礎結構] 刀鋒視窗上，按 [下載]。

    ![下載保存庫認證](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    保存庫認證會下載至「下載」資料夾。 保存庫認證下載完成之後，您會看到快顯視窗，詢問您是否要開啟或儲存認證。 按一下 [儲存] 。 如果您不小心按到 [開啟]，請讓嘗試開啟保存庫認證的對話方塊失敗。 您無法開啟保存庫認證。 請繼續進行下一個步驟。 保存庫認證位於 [下載] 資料夾中。   

    ![保存庫認證下載完成](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>安裝和註冊代理程式

> [!NOTE]
> 透過 Azure 入口網站啟用備份的功能尚未推出。 使用 Microsoft Azure 復原服務代理程式來備份 Windows Server 系統狀態。
>

1. 在 [下載] 資料夾 (或其他儲存位置) 中找到 **MARSagentinstaller.exe** 並對其按兩下。

    安裝程式在擷取、安裝和註冊復原服務代理程式時會提供一系列訊息。

    ![執行復原服務代理安裝程式的認證](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. 完成 Microsoft Azure 復原服務代理程式安裝精靈。 若要完成精靈，您需要︰

   * 選擇安裝和快取資料夾的位置。
   * 如果您使用 Proxy 伺服器來連線到網際網路，請提供您的 Proxy 伺服器資訊。
   * 如果您使用已驗證的 Proxy，請提供您的使用者名稱和密碼詳細資料。
   * 提供下載的保存庫認證
   * 將加密複雜密碼存放在安全的位置。

     > [!NOTE]
     > 如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。 將檔案存放在安全的位置。 必須有此檔案才能還原備份。
     >
     >

現已安裝代理程式，且已向保存庫註冊您的電腦。 您已準備好可以設定及排程備份。

## <a name="back-up-windows-server-system-state-preview"></a>備份 Windows Server 系統狀態 (預覽)
初始備份包括三項工作：

* 使用 Azure 備份代理程式來啟用系統狀態備份
* 排程備份
* 第一次備份檔案和資料夾

若要完成初始備份，請使用 Microsoft Azure 復原服務代理程式。

### <a name="to-enable-system-state-backup-using-the-azure-backup-agent"></a>使用 Azure 備份代理程式來啟用系統狀態備份

1. 在 PowerShell 工作階段中，執行下列命令來停止 Azure 備份引擎。

  ```
  PS C:\> Net stop obengine
  ```

2. 開啟 Windows 登錄。

  ```
  PS C:\> regedit.exe
  ```

3. 新增下列具有指定 DWord 值的登錄機碼。

  | 登錄路徑 | 登錄機碼 | DWord 值 |
  |---------------|--------------|-------------|
  | HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | TurnOffSSBFeature | 2 |

4. 在提高權限的命令提示字元中執行下列命令以重新啟動備份引擎。

  ```
  PS C:\> Net start obengine
  ```

### <a name="to-schedule-the-backup-job"></a>排程備份作業

1. 開啟 Microsoft Azure 復原服務代理程式。 您可以透過在您的電腦中搜尋 **Microsoft Azure 備份**來找出備份。

    ![啟動 Azure 復原服務代理程式](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. 在復原服務代理程式中，按一下 [排程備份] 。

    ![Windows Server 備份排程](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. 在排程備份精靈的 [開始使用] 頁面上，按 [下一步] 。

4. 在 [選取要備份的項目] 頁面上，按一下 [新增項目] 。

5. 選取 [系統狀態]，然後按一下 [確定]。

6. 按一下 [下一步] 。

7. 系統狀態的備份和保留排程會自動設定為在當地時間每週日下午 9:00 進行備份，保留期限則設定為 60 天。

   > [!NOTE]
   > 系統會自動設定系統狀態的備份和保留原則。 如果除了備份 Windows Server 系統狀態外，您還要備份檔案和資料夾，請從精靈指定只適用於檔案備份的備份和保留原則。 
   >

8. 在 [確認] 頁面上檢閱資訊，然後按一下 [完成] 。

9. 當精靈建立好備份排程時，請按一下 [關閉] 。

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>第一次備份 Windows Server 系統狀態

1. 確定 Windows Server 沒有需要重新啟動的擱置中更新。

2. 在復原服務代理程式中按一下 [立即備份]  ，以透過網路完成初始植入。

    ![立即備份 Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. 在 [確認] 頁面上，檢閱立即備份精靈將用於備份電腦的設定。 然後按一下 [備份] 。

4. 按一下 [關閉]  即可關閉精靈。 如果您在備份程序完成之前關閉精靈，精靈會繼續在背景中執行。

5. 如果除了備份 Windows Server 系統狀態外，您還要備份伺服器上的檔案和資料夾，「立即備份」精靈只會備份檔案。 若要執行臨時的系統狀態備份，請使用下列 PowerShell 命令：

    ```
    PS C:\> Start-OBSystemStateBackup
    ```

  完成初始備份之後，備份主控台中會顯示 [作業已完成]  狀態。

  ![IR 已完成](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="frequently-asked-questions"></a>常見問題集

下列問題與解答會提供補充資訊。

### <a name="what-is-the-staging-volume"></a>什麼是暫存磁碟區？

暫存磁碟區代表原生可用的 Windows Server Backup 用來暫存系統狀態備份的中繼位置。 Azure 備份代理程式接著會壓縮並加密此中繼備份，然後透過安全的 HTTPS 通訊協定將此備份傳送到所設定的復原服務保存庫。 **我們強烈建議您在非 Windows 作業系統的磁碟區中建立暫存磁碟區。如果您發現系統狀態備份有問題，則進行疑難排解的第一步就是檢查暫存磁碟區的位置。** 

### <a name="how-can-i-change-the-staging-volume-path-specified-in-the-azure-backup-agent"></a>如何變更 Azure 備份代理程式中指定的暫存磁碟區路徑？

暫存磁碟區預設位於快取資料夾。 

1. 若要變更這個位置，請使用下列命令 (在提升權限的命令提示字元中)：
  ```
  PS C:\> Net stop obengine
  ```

2. 然後，使用新暫存磁碟區資料夾的路徑來更新下列登錄項目。

  |登錄路徑|登錄機碼|值|
  |-------------|------------|-----|
  |HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | SSBStagingPath | 新的暫存磁碟區位置 |

暫存路徑有區分大小寫，因此其大小寫必須與伺服器上的暫存路徑完全相同。 

3. 在變更了暫存磁碟區路徑後，請重新啟動備份引擎：
  ```
  PS C:\> Net start obengine
  ```
4. 若要挑選變更後的路徑，請開啟 Microsoft Azure 復原服務代理程式，並觸發系統狀態的臨時備份。

### <a name="why-is-the-system-state-default-retention-set-to-60-days"></a>系統狀態的保留時間為何會預設為 60 天？

系統狀態備份的實用生命週期和 Windows Server Active Directory 角色的「標記存留期」設定相同。 標記存留期項目的預設值是 60 天。 這個值可於目錄服務 (NTDS) 組態物件上設定。

### <a name="how-do-i-change-the-default-backup-and-retention-policy-for-system-state"></a>如何變更系統狀態預設的備份和保留原則？

若要變更系統狀態預設的備份和保留原則：
1. 停止備份引擎。 從提高權限的命令提示字元中執行下列命令。

  ```
  PS C:\> Net stop obengine
  ```

2. 在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider 中新增或更新下列登錄機碼項目。

  |登錄名稱|說明|值|
  |-------------|-----------|-----|
  |SSBScheduleTime|用來設定備份的時間。 預設值為當地時間下午 9 點。|DWord：格式為 HHMM (十進位)，例如用 2130 代表當地時間下午 9:30|
  |SSBScheduleDays|用來設定系統狀態備份必須在指定時間執行的日子。 個別數字會指定星期幾。 0 代表星期日、1 代表星期一，依此類推。 預設的備份日是星期日。|DWord：要在星期幾執行備份 (十進位)，例如 1230 會排定在星期一、星期二、星期三與星期日進行備份。|
  |SSBRetentionDays|用來設定備份的保留天數。 預設值為 60。 允許的最大值為 180。|DWord：備份的保留天數 (十進位)。|

3. 使用下列命令來重新啟動備份引擎。
    ```
    PS C:\> Net start obengine
    ```

4. 開啟 Microsoft 復原服務代理程式。

5. 按一下 [排程備份]，然後按 [下一步] 直到您看到系統已反映您的變更。

6. 按一下 [完成] 來套用變更。


## <a name="questions"></a>有疑問嗎？
如果您有問題，或希望我們加入任何功能，請 [傳送意見反應給我們](http://aka.ms/azurebackup_feedback)。

## <a name="next-steps"></a>後續步驟
* 詳細了解如何 [備份 Windows 電腦](backup-configure-vault.md)。
* 現在您已備份好檔案和資料夾，接下來您可以 [管理您的保存庫和伺服器](backup-azure-manage-windows-server.md)。
* 如果您需要還原備份，請使用本文來 [還原檔案到 Windows 電腦](backup-azure-restore-windows-server.md)。

