---
title: "使用傳統部署模型管理 Azure 備份保存庫與 Azure 伺服器 | Microsoft Azure"
description: "使用本教學課程了解如何管理 Azure 備份保存庫與伺服器。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: f175eb12-0905-437f-91fd-eaee03ab6e81
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: markgal;
ms.openlocfilehash: 91451b2cdc42ed05ef7c1ba9c66ad5b4b45dd788
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-backup-vaults-and-servers-using-the-classic-deployment-model"></a>使用傳統部署模型管理 Azure 備份保存庫與伺服器
> [!div class="op_single_selector"]
> * [資源管理員](backup-azure-manage-windows-server.md)
> * [傳統](backup-azure-manage-windows-server-classic.md)
>
>

在本文中，您將了解透過 Azure 傳統入口網站和 Microsoft Azure 備份代理程式提供之備份管理工作的概觀。

> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。

> [!IMPORTANT]
> 您現在可以將備份保存庫升級至復原服務保存庫。 如需詳細資訊，請參閱[將備份保存庫升級至復原服務保存庫](backup-azure-upgrade-backup-to-recovery-services.md)文章。 Microsoft 鼓勵您將備份保存庫升級至復原服務保存庫。<br/> 在 2017 年 10 月 15 日之後，您就不能使用 PowerShell 建立備份保存庫。 **在 2017 年 11 月 1 日以前**：
>- 所有其餘的備份保存庫都會自動升級至復原服務保存庫。
>- 您將無法在傳統入口網站中存取您的備份資料。 相反地，使用 Azure 入口網站來存取您在復原服務保存庫中的備份資料。
>

## <a name="management-portal-tasks"></a>管理入口網站工作
1. 登入 [管理入口網站](https://manage.windowsazure.com)。
2. 按一下 [復原服務] ，然後按一下備份保存庫的名稱以檢視 [快速啟動] 頁面。

    ![復原服務](./media/backup-azure-manage-windows-server-classic/rs-left-nav.png)

藉由選取 [快速啟動] 頁面頂端的選項，您可以看到可用的管理工作。

![管理索引標籤](./media/backup-azure-manage-windows-server-classic/qs-page.png)

### <a name="dashboard"></a>儀表板
選取 [儀表板] 以查看伺服器的使用量概觀。 **使用量概觀**包括︰

* 已向雲端註冊的 Windows Server 數目
* 雲端中受保護的 Azure 虛擬機器數目
* Azure 中已使用的儲存體總計
* 最近的工作狀態

您可以在儀表板下方執行下列工作：

* **管理憑證** - 如果已使用憑證註冊伺服器，則可使用這個選項來更新憑證。 如果使用保存庫認證，則請勿使用 [管理憑證] 。
* **刪除** - 刪除目前的備份保存庫。 如果您有不再使用的備份保存庫，可將之刪除來釋出儲存空間。 **刪除** 才會啟用。

![備份儀表板工作](./media/backup-azure-manage-windows-server-classic/dashboard-tasks.png)

## <a name="registered-items"></a>已註冊的項目
選取 [已註冊的項目]  ，以檢視已註冊至此保存庫的伺服器名稱。

![已註冊的項目](./media/backup-azure-manage-windows-server-classic/registered-items.png)

**類型** 篩選預設為 Azure 虛擬機器。 若要檢視已註冊至此保存庫的伺服器名稱，可從下拉式功能表中選取 [Windows Server]  。

您可以在其中執行下列工作：

* **允許重新註冊** - 為伺服器選取此選項時，您可以使用內部部署 Microsoft Azure 備份代理程式中的**註冊精靈**，再次向備份保存庫註冊伺服器。 如果憑證中有錯誤，或必須重建伺服器，您可能需要重新註冊。
* **刪除** - 從備份保存庫中刪除伺服器。 所有與該伺服器相關聯的已儲存資料都將立即刪除。

    ![已註冊的項目工作](./media/backup-azure-manage-windows-server-classic/registered-items-tasks.png)

## <a name="protected-items"></a>受保護項目
選取 [受保護項目]  ，以檢視已從伺服器備份的項目。

![受保護項目](./media/backup-azure-manage-windows-server-classic/protected-items.png)

## <a name="configure"></a>設定
從 [設定]  索引標籤，您可以選取適當的儲存體備援選項。 選取儲存體備援選項的最佳時機，就在建立保存庫之後，以及任何電腦註冊至保存庫之前。

> [!WARNING]
> 一旦項目已註冊至保存庫，儲存體備援選項即遭到鎖定且無法修改。
>
>

![設定](./media/backup-azure-manage-windows-server-classic/configure.png)

如需 [儲存體備援](../storage/common/storage-redundancy.md)的詳細資訊，請參閱這篇文章。

## <a name="microsoft-azure-backup-agent-tasks"></a>Microsoft Azure 備份代理程式工作
### <a name="console"></a>主控台
開啟 **Microsoft Azure 備份代理程式** (透過在電腦中搜尋「Microsoft Azure 備份」即可找到)。

![備份代理程式](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

從可在備份代理程式主控台右側取得的 [動作]  ，您可以執行下列管理工作︰

* 註冊伺服器
* 排程備份
* 立即備份
* 變更屬性

![代理程式主控台動作](./media/backup-azure-manage-windows-server-classic/console-actions.png)

> [!NOTE]
> 若要 **復原資料**，請參閱 [將檔案還原到 Windows Server 或 Windows 用戶端電腦](backup-azure-restore-windows-server.md)。
>
>

### <a name="modify-an-existing-backup"></a>修改現有備份
1. 在 Microsoft Azure 備份代理程式中，按一下 [排程備份] 。

    ![Windows Server 備份排程](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
2. 在**排程備份精靈**中，讓 [變更備份項目或時間] 選項保留選取狀態，然後按 [下一步]。

    ![修改排定的備份](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
3. 如果您想要新增或變更項目，在 [選取要備份的項目] 畫面中按一下 [新增項目]。

    您也可以在這個精靈頁面中設定 [排除設定]  。 如果您想要排除檔案或檔案類型，請參閱新增 [排除設定](#exclusion-settings)的程序。
4. 選取要備份的檔案和資料夾，然後按一下 [確定] 。

    ![新增項目](./media/backup-azure-manage-windows-server-classic/add-items-modify.png)
5. 指定 [備份排程]，然後按 [下一步]。

    您可以排程每日 (一天最多 3 次) 或每週備份。

    ![指定備份排程](./media/backup-azure-manage-windows-server-classic/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > 這篇 [文章](backup-azure-backup-cloud-as-tape.md)中會詳細說明指定備份排程。
   >
   >
6. 選取備份複本的 [保留原則]，然後按 [下一步]。

    ![選取保留原則](./media/backup-azure-manage-windows-server-classic/select-retention-policy-modify.png)
7. 在 [確認] 畫面上檢閱資訊，然後按一下 [完成]。
8. 當精靈完成 [備份排程] 的建立之後，按一下 [關閉]。

    修改保護之後，您可以藉由移至 [工作]  索引標籤並確認變更反映於備份工作中，來確定可正確觸發備份。

### <a name="enable-network-throttling"></a>啟用網路節流
Azure 備份代理程式提供 [節流] 索引標籤，可讓您控制在資料傳輸期間使用網路頻寬的方式。 如果您需要在上班時間內備份資料，但不希望備份程序干擾其他網際網路流量，這樣的控制會很有幫助。 資料傳輸的節流適用於備份和還原活動。  

啟用節流︰

1. 在**備份代理程式**中，按一下 [變更屬性]。
2. 選取 [啟用備份作業的網際網路頻寬使用節流功能]  核取方塊。

    ![網路節流](./media/backup-azure-manage-windows-server-classic/throttling-dialog.png)
3. 一旦啟用節流之後，請指定允許在 [工作時間] 和 [非工作時間] 進行備份資料傳輸的頻寬。

    頻寬值從每秒 512 KB (Kbps) 開始，並可高達每秒 1023 MB (Mbps)。 您也可以指定 [工作時間] 的開始和完成時間，以及一週中有哪幾天視為工作天。 指定工作時間以外的時間會視為非工作時間。
4. 按一下 [確定] 。

## <a name="exclusion-settings"></a>排除設定
1. 開啟 **Microsoft Azure 備份代理程式** (透過在電腦中搜尋「Microsoft Azure 備份」即可找到)。

    ![開啟備份代理程式](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)
2. 在 Microsoft Azure 備份代理程式中，按一下 [排程備份] 。

    ![Windows Server 備份排程](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
3. 在排程備份精靈中，讓 [變更備份項目或時間] 選項保留選取狀態，然後按 [下一步]。

    ![修改排程](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
4. 按一下 [排除設定] 。

    ![選取要排除的項目](./media/backup-azure-manage-windows-server-classic/exclusion-settings.png)
5. 按一下 [新增排除] 。

    ![新增排除項目](./media/backup-azure-manage-windows-server-classic/add-exclusion.png)
6. 選取位置，然後按一下 [確定] 。

    ![選取要排除的位置](./media/backup-azure-manage-windows-server-classic/exclusion-location.png)
7. 在 [檔案類型]  欄位中新增副檔名。

    ![依檔案類型排除](./media/backup-azure-manage-windows-server-classic/exclude-file-type.png)

    新增 .mp3 副檔名

    ![檔案類型範例](./media/backup-azure-manage-windows-server-classic/exclude-mp3.png)

    若要新增其他副檔名，可按一下 [新增排除]  ，然後輸入另一個副檔名 (新增 .jpeg 副檔名)。

    ![另一個檔案類型範例](./media/backup-azure-manage-windows-server-classic/exclude-jpg.png)
8. 當您已新增所有副檔名之後，按一下 [確定] 。
9. 按 [下一步] 繼續執行排程備份精靈， 直到出現 [確認] 頁面，然後按一下 [完成]。

    ![排除確認](./media/backup-azure-manage-windows-server-classic/finish-exclusions.png)

## <a name="next-steps"></a>後續步驟
* [從 Azure 還原 Windows Server 或 Windows 用戶端](backup-azure-restore-windows-server.md)
* 若要深入了解 Azure 備份，請參閱 [Azure 備份概觀](backup-introduction-to-azure-backup.md)
* 瀏覽 [Azure 備份論壇](http://go.microsoft.com/fwlink/p/?LinkId=290933)
