---
title: "使用傳統部署模型將資料從 Azure 還原到 Windows Server 或 Windows 用戶端 | Microsoft Docs"
description: "了解如何從 Windows Server 或 Windows 用戶端進行還原。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 85585dfc-c764-4e8c-8f0e-40b969640ac2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: saurse;trinadhk;markgal;
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 300b2b17b44e21ed446fd63d572a2461e2fc1343
ms.contentlocale: zh-tw
ms.lasthandoff: 08/12/2017

---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-the-classic-deployment-model"></a>使用傳統部署模型將檔案還原到 Windows Server 或 Windows 用戶端電腦
> [!div class="op_single_selector"]
> * [傳統入口網站](backup-azure-restore-windows-server-classic.md)
> * [Azure 入口網站](backup-azure-restore-windows-server.md)
>
>

本文說明如何從備份保存庫復原資料，並將資料還原到伺服器或電腦。 從 2017 年 3 月開始，您無法再於傳統入口網站中建立備份保存庫。

> [!IMPORTANT]
> 您現在可以將備份保存庫升級至復原服務保存庫。 如需詳細資訊，請參閱[將備份保存庫升級至復原服務保存庫](backup-azure-upgrade-backup-to-recovery-services.md)文章。 Microsoft 鼓勵您將備份保存庫升級至復原服務保存庫。<br/> 在 **2017 年 10 月 15 日**之後，您就無法使用 PowerShell 建立備份保存庫。 <br/> **自 2017 年 11 月 1 日起**：
>- 任何其餘的備份保存庫都會自動升級至復原服務保存庫。
>- 您將無法在傳統入口網站中存取您的備份資料。 相反地，使用 Azure 入口網站來存取您在復原服務保存庫中的備份資料。
>

若要還原資料，您可以使用 Microsoft Azure 復原服務 (MARS) 代理程式中的 [復原資料精靈]。 當您還原資料時，您可以︰

* 將資料還原到進行備份的相同電腦。
* 將資料還原至其他電腦。

2017 年 1 月，Microsoft 推出 MARS 代理程式預覽更新。 這項更新除了錯誤修正之外，還啟用了「立即還原」，允許您掛接可寫入的復原點快照來做為復原磁碟區。 您接著可以探索復磁碟區並將檔案複製至本機電腦，藉此選擇性地還原檔案。

> [!NOTE]
> 如果您要使用「立即還原」來還原資料，必須要有 [2017 年 1 月 Azure 備份更新](https://support.microsoft.com/en-us/help/3216528?preview)。 另外，您也必須在支援文章列出之地區設定的保存庫中，保護備份資料。 請參閱 [2017 年 1 月 Azure 備份更新](https://support.microsoft.com/en-us/help/3216528?preview)，了解支援「立即還原」的最新地區設定清單。 「立即還原」目前**無法**在所有地區設定中使用。
>

「立即還原」可用於 Azure 入口網站中的復原服務保存庫以及傳統入口網站中的備份保存庫。 如果您想要使用「立即還原」，請下載 MARS 更新，並依照提及「立即還原」部分中的程序進行操作。


## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>使用「立即還原」將資料還原至同一台電腦

如果您不小心刪除檔案，而您想要將它還原到相同的電腦 (備份進行處)，下列步驟可協助您復原資料。

1. 開啟 **Microsoft Azure 備份** 嵌入式管理單元。 如果您不知道快照安裝的位置，請在電腦或伺服器上搜尋 **Microsoft Azure 備份**。

    傳統型應用程式應該會出現在搜尋結果中。

2. 按一下 [復原資料] 以啟動精靈。

    ![復原資料](./media/backup-azure-restore-windows-server/recover.png)

3. 在 [開始使用] 窗格中，若要將資料還原至同一台伺服器或電腦，請選取 [這台伺服器 (`<server name>`)] 並按一下 [下一步]。

    ![選擇 [這台伺服器] 選項以將資料還原到同一台電腦](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. 在 [選取復原模式] 窗格上，選擇 [個別檔案與資料夾]，然後按一下 [下一步]。

    ![瀏覽檔案](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. 在 [選取磁碟區和日期] 窗格上，選取包含您要還原之檔案和/或資料夾的磁碟區。

    在日曆上，選取復原點。 您可以從任何時間的復原點還原。 **粗體**的日期表示至少有一個復原點可用。 一旦您選取一個日期，如果有多個復原點可用，您可以從 [時間] 下拉式功能表選擇特定的復原點。

    ![磁碟區和日期](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. 選擇要還原的復原點之後，按一下 [掛接]。

    Azure 備份會掛接本機復原點，並且使用它做為復原磁碟區。

7. 在 [瀏覽及復原檔案] 窗格上，按一下 [瀏覽] 以開啟 [Windows 檔案總管]，然後尋找所需的檔案和資料夾。

    ![修復選項](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. 在 [Windows 檔案總管] 中，複製要還原的檔案和/或資料夾，並將它們貼上至伺服器或電腦的任意本機位置。 您可以直接從復原磁碟區開啟或串流檔案，並確認是否復原正確的版本。

    ![複製掛接磁碟區的檔案和資料夾，並貼上至本機位置](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. 當您完成還原檔案和/或資料夾後，請在 [瀏覽及復原檔案] 窗格上，按一下 [卸載]。 按一下 [是] 以確認要卸載磁碟區。

    ![卸載磁碟區並確認](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > 如果您並未按一下 [卸載]，復原磁碟區會保持掛接六個小時 (從掛接後開始計算)。 當磁碟區處於掛接狀態時，不會執行任何備份作業。 當掛接磁碟區時，任何排定要執行的備份作業會在復原磁碟區卸載之後才執行。
    >


## <a name="recover-data-to-the-same-machine"></a>將資料還原到相同電腦
如果您不小心刪除檔案，而您想要將它還原到相同的電腦 (備份進行處)，下列步驟可協助您復原資料。

1. 開啟 **Microsoft Azure 備份** 嵌入式管理單元。
2. 按一下 [復原資料]  初始化工作流程。

    ![復原資料](./media/backup-azure-restore-windows-server-classic/recover.png)
3. 選取 **這台伺服器 (*yourmachinename*)** 選項，在同一部電腦上還原備份的檔案。

    ![相同電腦](./media/backup-azure-restore-windows-server-classic/samemachine.png)
4. 選擇 [瀏覽檔案] 或 [搜尋檔案]。

    如果您打算還原路徑已知的一個或多個檔案，請保留預設選項。 如果您不確定資料夾結構，但想要搜尋檔案，請挑選 [ **搜尋檔案** ] 選項。 為了達成本章節的目的，我們將使用預設選項繼續進行。

    ![瀏覽檔案](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)
5. 選取您要還原檔案所在的磁碟區。

    您可以從任何時間點還原。 日曆控制項中以 **粗體** 顯示的日期會指出還原點的可用性。 選取日期之後，您可以根據您的備份排程 (以及成功的備份作業)，從 [時間]  下拉式清單選取時間點。

    ![磁碟區和日期](./media/backup-azure-restore-windows-server-classic/volanddate.png)
6. 選取要復原的項目。 您可以複選想要還原的資料夾/檔案。

    ![選取檔案](./media/backup-azure-restore-windows-server-classic/selectfiles.png)
7. 指定復原參數。

    ![修復選項](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)

   * 您可以選擇還原至原始位置 (其中的檔案/資料夾可能會遭到覆寫) 或相同電腦中的其他位置。
   * 如果目標位置有您想要還原的檔案/資料夾存在，您可以建立複本 (相同檔案的兩個版本)、覆寫目標位置中的檔案，或略過修復目標位置已存在的檔案。
   * 強烈建議您針對正在復原檔案上的 ACL 保留預設還原選項。
8. 提供這些輸入之後，按一下 [下一步] 。 就會開始執行將檔案還原到這部電腦的復原工作流程。

## <a name="recover-to-an-alternate-machine"></a>還原至其他電腦
若您遺失整個伺服器，您仍然可從 Azure 備份將資料還原到其他電腦。 下列步驟說明工作流程。  

這些步驟中所使用的術語包含：

*  – 用來進行備份且目前無法使用的的原始電腦。
*  – 復原資料時的目標電腦。
* 「範例保存庫」–「來源電腦」和「目標電腦」註冊的備份保存庫。 <br/>

> [!NOTE]
> 從電腦進行的備份無法在執行舊版作業系統的電腦上進行還原。 例如，如果從 Windows 7 電腦進行備份，則可以在 Windows 8 或更新版電腦上進行還原。 不過若情況相反，便無法進行還原。
>
>

1. 在「目標電腦」上開啟 [Microsoft Azure 備份] 嵌入式管理單元。
2. 確定「目標電腦」和「來源電腦」均已註冊到相同的備份保存庫。
3. 按一下 [復原資料]  初始化工作流程。

    ![復原資料](./media/backup-azure-restore-windows-server-classic/recover.png)
4. 選取 [其他伺服器] 

    ![其他伺服器](./media/backup-azure-restore-windows-server-classic/anotherserver.png)
5. 提供與「範例保存庫」 相對應的保存庫認證檔。 如果保存庫認證檔無效 (或已過期)，請從 Azure 傳統入口網站中的「範例保存庫」  下載新的保存庫認證檔。 一旦提供保存庫認證檔，即會顯示保存庫認證檔的備份保存庫。
6. 在顯示電腦的清單中選取 [來源電腦]  。

    ![電腦清單](./media/backup-azure-restore-windows-server-classic/machinelist.png)
7. 選取 [搜尋檔案] 或 [瀏覽檔案] 選項。 為了達成本章節的目的，我們將使用 [搜尋檔案]  選項。

    ![Search](./media/backup-azure-restore-windows-server-classic/search.png)
8. 在下一個畫面中選取磁碟區和日期。 搜尋您想要還原的資料夾/檔案名稱。

    ![搜尋項目](./media/backup-azure-restore-windows-server-classic/searchitems.png)
9. 選取需要還原之檔案的位置。

    ![還原位置](./media/backup-azure-restore-windows-server-classic/restorelocation.png)
10. 提供「來源電腦」註冊至「範例保存庫」期間所提供的加密複雜密碼。

    ![加密](./media/backup-azure-restore-windows-server-classic/encryption.png)
11. 一旦提供輸入，則按一下 [復原] ，將會觸發還原備份檔案到所提供目的地的程序。

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>使用「立即還原」將資料還原至其他電腦
若您遺失整個伺服器，您仍然可從 Azure 備份將資料還原到其他電腦。 下列步驟說明工作流程。

這些步驟中所使用的術語包含：

*  – 用來進行備份且目前無法使用的的原始電腦。
*  – 復原資料時的目標電腦。
* 「範例保存庫」–「來源電腦」和「目標電腦」註冊的復原服務保存庫。 <br/>

> [!NOTE]
> 備份無法還原到執行舊版作業系統的目標電腦。 例如，從 Windows 7 電腦建立的備份可以還原至 Windows 8 或更新版本的電腦。 從 Windows 8 電腦建立的備份無法還原至 Windows 7 電腦。
>
>

1. 在「目標電腦」上開啟 [Microsoft Azure 備份] 嵌入式管理單元。

2. 確定 [目標電腦] 和 [來源電腦] 已註冊到同一個復原服務保存庫。

3. 按一下 [復原資料] 以開啟 [復原資料精靈]。

    ![復原資料](./media/backup-azure-restore-windows-server/recover.png)

4. 在 [開始使用] 窗格上，選取 [其他伺服器]

    ![其他伺服器](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. 提供與「範例保存庫」相對應的保存庫認證檔，然後按一下 [下一步]。

    如果保存庫認證檔無效 (或已過期)，請從 Azure 入口網站中的「範例保存庫」下載新的保存庫認證檔。 一旦您提供有效的保存庫認證檔之後，就會顯示對應的備份保存庫名稱。

6. 在 [選取備份伺服器] 窗格上，從顯示的電腦清單選取 [來源電腦]，並提供複雜密碼。 然後按 [下一步] 。

    ![電腦清單](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. 在 [選取復原模式] 窗格上，選取 [個別檔案和資料夾] 並按一下 [下一步]。

    ![搜尋](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. 在 [選取磁碟區和日期] 窗格上，選取包含您要還原之檔案和/或資料夾的磁碟區。

    在日曆上，選取復原點。 您可以從任何時間的復原點還原。 **粗體**的日期表示至少有一個復原點可用。 一旦您選取一個日期，如果有多個復原點可用，您可以從 [時間] 下拉式功能表選擇特定的復原點。

    ![搜尋項目](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. 按一下 [掛接] 以掛接本機的復原點，做為 [目標電腦] 的復原磁碟區。

10. 在 [瀏覽及復原檔案] 窗格上，按一下 [瀏覽] 以開啟 [Windows 檔案總管]，然後尋找所需的檔案和資料夾。

    ![加密](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. 在 [Windows 檔案總管] 中，從復原磁碟區複製檔案和/或資料夾，然後貼上至 [目標電腦] 位置。 您可以直接從復原磁碟區開啟或串流檔案，並確認是否復原正確的版本。

    ![加密](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. 當您完成還原檔案和/或資料夾後，請在 [瀏覽及復原檔案] 窗格上，按一下 [卸載]。 按一下 [是] 以確認要卸載磁碟區。

    ![加密](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > 如果您並未按一下 [卸載]，復原磁碟區會保持掛接六個小時 (從掛接後開始計算)。 當磁碟區處於掛接狀態時，不會執行任何備份作業。 當掛接磁碟區時，任何排定要執行的備份作業會在復原磁碟區卸載之後才執行。
    >


## <a name="next-steps"></a>後續步驟
* [Azure 備份常見問題集](backup-azure-backup-faq.md)
* 造訪 [Azure 備份論壇](http://go.microsoft.com/fwlink/p/?LinkId=290933)(英文)。

## <a name="learn-more"></a>詳細資訊
* [Azure 備份概觀](http://go.microsoft.com/fwlink/p/?LinkId=222425)
* [備份 Azure 虛擬機器](backup-azure-vms-introduction.md)
* [備份 Microsoft 工作負載](backup-azure-dpm-introduction.md)

