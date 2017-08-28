---
title: "將 Windows 伺服器或工作站備份至 Azure (傳統模型) | Microsoft Docs"
description: "將 Windows 伺服器或用戶端備份至 Azure 中的備份保存庫。 瀏覽使用 Azure 備份代理程式將檔案和資料夾放入備份保存庫來保護的基本概念。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "備份保存庫；備份 Windows Server；備份Windows；"
ms.assetid: 3b543bfd-8978-4f11-816a-0498fe14a8ba
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: markgal;trinadhk;
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: a8daa6a4655b72936b6299c0fa5b80459ffa5da3
ms.contentlocale: zh-tw
ms.lasthandoff: 08/12/2017

---
# <a name="back-up-a-windows-server-or-workstation-to-azure-using-the-classic-portal"></a>使用傳統入口網站將 Windows 伺服器或工作站備份至 Azure
> [!div class="op_single_selector"]
> * [傳統入口網站](backup-configure-vault-classic.md)
> * [Azure 入口網站](backup-configure-vault.md)
>
>

本文涵蓋準備環境和將 Windows 伺服器 (或工作站) 備份至 Azure 所需依循的程序。 它還涵蓋部署備份解決方案的考量。 如果您想要第一次嘗試 Azure 備份，這篇文章可快速引導您完成該程序。

Azure 建立和處理資源的部署模型有二種：資源管理員和傳統。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。

## <a name="before-you-start"></a>開始之前
若要將伺服器或用戶端備份至 Azure，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="create-a-backup-vault"></a>建立備份保存庫
若要從伺服器或用戶端備份檔案與資料夾，您必須在要儲存這些資料的地理區域中建立備份保存庫。

> [!IMPORTANT]
> 從 2017 年 3 月開始，您無法再使用傳統入口網站來建立備份保存庫。
>
> 您現在可以將備份保存庫升級至復原服務保存庫。 如需詳細資訊，請參閱[將備份保存庫升級至復原服務保存庫](backup-azure-upgrade-backup-to-recovery-services.md)文章。 Microsoft 鼓勵您將備份保存庫升級至復原服務保存庫。<br/> 在 **2017 年 10 月 15 日**之後，您就無法使用 PowerShell 建立備份保存庫。 <br/> **自 2017 年 11 月 1 日起**：
>- 任何其餘的備份保存庫都會自動升級至復原服務保存庫。
>- 您將無法在傳統入口網站中存取您的備份資料。 相反地，使用 Azure 入口網站來存取您在復原服務保存庫中的備份資料。
>


## <a name="download-the-vault-credential-file"></a>下載保存庫認證檔
內部部署機器必須先驗證備份保存庫，才能將資料備份至 Azure。 驗證是透過「保存庫認證」 來達成。 保存庫認證檔會透過安全通道，從傳統入口網站下載。 憑證私密金鑰不會保存在入口網站或服務中。

### <a name="to-download-the-vault-credential-file-to-a-local-machine"></a>將保存庫認證檔下載至本機電腦
1. 在左側導覽窗格中，按一下 [復原服務] ，然後選取您所建立的備份保存庫。

    ![IR 已完成](./media/backup-configure-vault-classic/rs-left-nav.png)
2. 在 [快速入門] 頁面上，按一下 [ **下載保存庫認證**]。

   傳統入口網站會使用保存庫名稱和目前日期的組合來產生保存庫認證。 保存庫認證檔僅在註冊工作流程期間使用，並於 48 小時後過期。

   保存庫認證檔可以從入口網站下載。
3. 按一下 [儲存]  將保存庫認證檔下載至本機帳戶的 [下載] 資料夾。 您也可以從 [儲存] 功能表選取 [另存新檔]，以指定保存庫認證檔的儲存位置。

   > [!NOTE]
   > 請確保保存庫認證檔儲存在可從您的電腦存取的位置。 如果是儲存在檔案共用或伺服器訊息區塊中，請確認您擁有存取權限。
   >
   >

## <a name="download-install-and-register-the-backup-agent"></a>下載、安裝和註冊備份代理程式
在您建立備份保存庫並下載保存庫認證檔之後，必須在您每一部 Windows 電腦上安裝代理程式。

### <a name="to-download-install-and-register-the-agent"></a>下載、安裝和註冊代理程式
1. 按一下 [復原服務] ，然後選取您要向伺服器註冊的備份保存庫。
2. 在 [快速啟動] 頁面上，按一下 [適用於 Windows Server 或 System Center Data Protection Manager 或 Windows 用戶端的代理程式] 代理程式。 然後按一下 [儲存] 。

    ![儲存代理程式](./media/backup-configure-vault-classic/agent.png)
3. 下載 MARSagentinstaller.exe 檔案之後，按一下 [執行] \(或從儲存的位置按兩下 **MARSAgentInstaller.exe**)。
4. 選擇代理程式所需的安裝資料夾和快取資料夾，然後按一下 [下一步] 。 您指定的快取位置必須至少包含等於備份資料大小 5% 的可用空間。
5. 您可以繼續透過預設 Proxy 設定連線網際網路。             若您使用 Proxy 伺服器連線網際網路，請在 [Proxy 組態] 頁面上選取 [使用自訂 Proxy 設定]  核取方塊，然後輸入 Proxy 伺服器詳細資料。 若您使用已驗證的 Proxy，請輸入使用者名稱和密碼詳細資料，然後按一下 [下一步] 。
6. 按一下 [安裝]  開始代理程式安裝。 備份代理程式會安裝 .NET Framework 4.5 和 Windows PowerShell (若尚未安裝) 來完成安裝。
7. 安裝代理程式之後，按一下 [繼續註冊]  以繼續工作流程。
8. 在 [保存庫識別] 頁面中，瀏覽並選取先前下載的保存庫認證檔。

    從入口網站下載保存庫認證檔之後，其有效期只有 48 小時。 如果您在此頁面上遇到錯誤 (例如「提供的保存庫認證檔已過期」)，請登入入口網站並再次下載保存庫認證檔。

    請確定保存庫認證檔位於可透過設定應用程式存取的位置。 如果您遇到存取權相關的錯誤，請將保存庫認證檔複製到同一部電腦中的暫存位置並重試作業。

    如果您遇到保存庫認證錯誤 (例如「提供的保存庫認證無效」)，表示檔案已經損毀或沒有和復原服務關聯的最新認證。 從入口網站下載新的保存庫認證檔後重試作業。 若使用者連續快速地按下 [下載保存庫認證]  選項數次，也可能會發生此錯誤。 在此情況下，只有最後一個保存庫認證檔有效。
9. 在 [加密設定] 頁面中，您可以產生複雜密碼或提供複雜密碼 (最少 16 個字元)。 請記得將複雜密碼存放在安全的位置。
10. 按一下 [完成] 。 [註冊伺服器精靈] 會向備份註冊伺服器。

    > [!WARNING]
    > 如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。 加密複雜密碼為您所擁有，Microsoft 並無法看到您所使用的複雜密碼。 請將檔案儲存在安全的位置，因為在復原作業期間將會需要該檔案。
    >
    >

11. 設定加密金鑰之後，讓 [啟動 Microsoft Azure 復原服務代理程式] 核取方塊保持已選取狀態，然後按一下 [關閉]。

## <a name="complete-the-initial-backup"></a>完成初始備份
初始備份包括兩項重要工作：

* 建立備份排程
* 第一次備份檔案和資料夾

在備份原則完成初始備份之後，它會建立在您需要復原資料時可使用的備份點。 備份原則會依據您定義的排程執行此作業。

### <a name="to-schedule-the-backup"></a>排程備份
1. 開啟 Microsoft Azure 備份代理程式。 (如果您在關閉註冊伺服器精靈時，讓 [啟動 Microsoft Azure 復原服務代理程式] 核取方塊保持已選取狀態，備份代理程式將會自動開啟。)您可以透過在您的電腦中搜尋 **Microsoft Azure 備份**來找出備份。

    ![啟動 Azure 備份代理程式](./media/backup-configure-vault-classic/snap-in-search.png)
2. 在備份代理程式中，按一下 [排程備份] 。

    ![Windows Server 備份排程](./media/backup-configure-vault-classic/schedule-backup-close.png)
3. 在排程備份精靈的 [開始使用] 頁面上，按 [下一步] 。
4. 在 [選取要備份的項目] 頁面上，按一下 [新增項目] 。
5. 選取您要備份的檔案和資料夾，然後按一下 [確定] 。
6. 按一下 [下一步] 。
7. 在 [指定備份排程] 頁面上，指定[備份排程]，然後按 [下一步]。

    您可以排程每日 (一天最多三次) 或每週備份。

    ![Windows Server 備份項目](./media/backup-configure-vault-classic/specify-backup-schedule-close.png)

   > [!NOTE]
   > 如需如何指定備份排程的相關詳細資訊，請參閱 [使用 Azure 備份來取代您的磁帶基礎結構](backup-azure-backup-cloud-as-tape.md)一文。
   >
   >

8. 在 [選取保留原則] 頁面上，選取 [保留原則] 做為備份複本。

    保留原則會指定備份將儲存的期間。 除了僅針對所有備份點指定「一般原則」之外，您可以指定在進行備份時根據不同的保留原則。 您可以修改每日、每週、每月和每年保留原則，以符合您的需求。
9. 在 [選擇初始備份類型] 頁面上，選擇初始備份類型。 讓 [自動透過網路] 選項保持已選取狀態，然後按 [下一步]。

    您可以透過網路自動備份，也可以離線備份。 這篇文章的其餘部分說明自動備份的程序。 如果您想要執行離線備份，請檢閱 [在 Azure Backup 中離線備份工作流程](backup-azure-backup-import-export.md) 一文以了解其他資訊。
10. 在 [確認] 頁面上檢閱資訊，然後按一下 [完成] 。
11. 當精靈建立好備份排程時，請按一下 [關閉] 。

### <a name="enable-network-throttling-optional"></a>啟用網路節流 (選擇性)
備份代理程式提供網路節流。 節流會控制資料傳輸期間的網路頻寬使用方式。 如果您需要在上班時間內備份資料，但不希望備份程序干擾其他網際網路流量，這樣的控制會很有幫助。 節流適用於備份和還原活動。

**啟用網路節流**

1. 在備份代理程式中，按一下 [變更屬性] 。

    ![變更屬性](./media/backup-configure-vault-classic/change-properties.png)
2. 在 [節流] 索引標籤上，選取 [啟用備份作業的網際網路頻寬使用節流功能] 核取方塊。

    ![網路節流](./media/backup-configure-vault-classic/throttling-dialog.png)
3. 在您啟用節流之後，請指定允許的頻寬進行 [工作時間] 和 [非工作時間] 期間的備份資料傳輸。

    頻寬值從每秒 512 KB (Kbps) 開始，並可高達每秒 1023 MB (Mbps)。 您也可以指定 [工作時間] 的開始和完成時間，以及一週中有哪幾天視為工作天。 指定之工作時間以外的時間則視為非工作時間。
4. 按一下 [確定] 。

### <a name="to-back-up-now"></a>立即備份
1. 在備份代理程式中，按一下 [立即備份]  ，以透過網路完成初始植入。

    ![Windows Server 立即備份](./media/backup-configure-vault-classic/backup-now.png)
2. 在 [確認] 頁面上，檢閱立即備份精靈將用於備份電腦的設定。 然後按一下 [備份] 。
3. 按一下 [關閉]  即可關閉精靈。 如果您在備份程序完成之前關閉精靈，精靈會繼續在背景中執行。

完成初始備份之後，備份主控台中會顯示 [作業已完成]  狀態。

![IR 已完成](./media/backup-configure-vault-classic/ircomplete.png)

## <a name="next-steps"></a>後續步驟
* 註冊 [免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

如需備份 VM 或其他工作負載的詳細資訊，請參閱︰

* [備份 IaaS VM](backup-azure-vms-prepare.md)
* [使用 Microsoft Azure 備份伺服器備份工作負載](backup-azure-microsoft-azure-backup.md)
* [使用 DPM 將工作負載備份到 Azure](backup-azure-dpm-introduction.md)

