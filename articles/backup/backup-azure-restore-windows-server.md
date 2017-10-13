---
title: "將 Azure 中的資料還原至 Windows Server 或 Windows 電腦 | Microsoft Docs"
description: "了解如何將儲存於 Azure 中的資料還原至 Windows Server 或 Windows 電腦。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 742f4b9e-c0ab-4eeb-8e22-ee29b83c22c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/16/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 231dd61f95267b3a504ed70e9b3a5abc470b69b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>使用 Resource Manager 部署模型將檔案還原到 Windows Server 或 Windows 用戶端電腦
> [!div class="op_single_selector"]
> * [Azure 入口網站](backup-azure-restore-windows-server.md)
> * [傳統入口網站](backup-azure-restore-windows-server-classic.md)
>
>

此文章說明如何從備份保存庫還原資料。 若要還原資料，您可以使用 Microsoft Azure 復原服務 (MARS) 代理程式中的 [復原資料精靈]。 當您還原資料時，您可以︰

* 將資料還原到進行備份的相同電腦。
* 將資料還原至其他電腦。

2017 年 1 月，Microsoft 推出 MARS 代理程式預覽更新。 這項更新除了錯誤修正之外，還啟用了「立即還原」，允許您掛接可寫入的復原點快照來做為復原磁碟區。 您接著可以探索復磁碟區並將檔案複製至本機電腦，藉此選擇性地還原檔案。

> [!NOTE]
> 如果您要使用「立即還原」來還原資料，必須要有 [2017 年 1 月 Azure 備份更新](https://support.microsoft.com/en-us/help/3216528?preview)。 另外，您也必須在支援文章列出之地區設定的保存庫中，保護備份資料。 請參閱 [2017 年 1 月 Azure 備份更新](https://support.microsoft.com/en-us/help/3216528?preview)，了解支援「立即還原」的最新地區設定清單。 「立即還原」目前**無法**在所有地區設定中使用。
>

「立即還原」可用於 Azure 入口網站中的復原服務保存庫以及傳統入口網站中的備份保存庫。 如果您想要使用「立即還原」，請下載 MARS 更新，並依照提及「立即還原」部分中的程序進行操作。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>使用「立即還原」將資料還原至同一台電腦

如果您不小心刪除檔案，而您想要將它還原到相同的電腦 (備份進行處)，下列步驟可協助您復原資料。

1. 開啟 **Microsoft Azure 備份** 嵌入式管理單元。 如果您不知道快照安裝的位置，請在電腦或伺服器上搜尋 **Microsoft Azure 備份**。

    傳統型應用程式應該會出現在搜尋結果中。

2. 按一下 [復原資料] 以啟動精靈。

    ![復原資料](./media/backup-azure-restore-windows-server/recover.png)

3. 在 [開始使用] 窗格中，若要將資料還原至同一台伺服器或電腦，請選取 [這台伺服器 (`<server name>`)] 並按一下 [下一步]。

    ![選擇 [這台伺服器] 選項以將資料還原到同一台電腦](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. 在 選取復原模式 窗格上，選擇 個別檔案與資料夾，然後按一下下一步。

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
    > 如果您並未按一下 [卸載]，復原磁碟區會保持掛接 6 個小時 (從掛接後開始計算)。 不過，如果是進行中的檔案複製，掛接時間可擴充至高達 24 小時。 當磁碟區處於掛接狀態時，不會執行任何備份作業。 當掛接磁碟區時，任何排定要執行的備份作業會在復原磁碟區卸載之後才執行。
    >


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
    > 如果您並未按一下 [卸載]，復原磁碟區會保持掛接 6 個小時 (從掛接後開始計算)。 不過，如果是進行中的檔案複製，掛接時間可擴充至高達 24 小時。 當磁碟區處於掛接狀態時，不會執行任何備份作業。 當掛接磁碟區時，任何排定要執行的備份作業會在復原磁碟區卸載之後才執行。
    >

## <a name="troubleshooting"></a>疑難排解
如果即使按一下 [掛接] 數分鐘之後，Azure 備份仍未成功掛接復原磁碟區，或者無法掛接復原磁碟區且有一或多個錯誤，請遵循以下步驟來開始一般復原。

1.  如果已執行數分鐘，請取消進行中的掛接程序。

2.  確認您使用最新版本的 Azure 備份代理程式。 若要了解 Azure 備份代理程式的版本資訊，按一下 Microsoft Azure 備份主控台之 [動作] 窗格中的 [關於 Microsoft Azure 復原服務代理程式]，並且確定**版本**號碼等於或高於[本文](https://go.microsoft.com/fwlink/?linkid=229525)中所述的版本。 您可以從[這裡](https://go.microsoft.com/fwLink/?LinkID=288905)下載最新版本

3.  移至 [裝置管理員]  ->  [儲存體控制器]，並確保您可以找出 **Microsoft iSCSI 啟動器**。 如果您可以找到它，請直接前往以下的步驟 7。 

4.  如果您無法如步驟 3 中所述，找到 Microsoft iSCSI 啟動器服務，請查看是否可以在名為 [不明裝置]、硬體識別碼為**ROOT\ISCSIPRT** 的 [裝置管理員]  ->  [儲存體控制器]項目。

5.  以滑鼠右鍵按一下 [不明裝置]，然後選取 [更新驅動程式軟體]。

6.  藉由選取 [自動搜尋更新的驅動程式軟體] 的選項，以更新驅動程式。 完成更新應該會將 [不明裝置] 變更為 [Microsoft iSCSI 啟動器]如下所示。 

    ![加密](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  移至 [工作管理員]  ->  [服務 (本機)]  ->  [Microsoft iSCSI 啟動器服務]。 

    ![加密](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  以滑鼠右鍵按一下服務，以重新啟動 Microsoft iSCSI 啟動器服務，按一下 [停止] 並且再次以滑鼠右鍵按一下，然後按一下 [啟動]。

9.  使用即時還原重試復原。 

如果復原仍然失敗，請重新啟動您的伺服器/用戶端。 如果不想要重新開機，或者即使在重新啟動伺服器之後復原仍然失敗，請嘗試從其他電腦復原，移至 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)連絡 Azure 支援並且提交支援要求。

## <a name="next-steps"></a>後續步驟
* 現在您已復原檔案和資料夾，接下來您可以 [管理您的備份](backup-azure-manage-windows-server.md)。
