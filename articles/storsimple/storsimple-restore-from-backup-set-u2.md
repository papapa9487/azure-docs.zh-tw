---
title: "從備份還原 StorSimple 磁碟區 | Microsoft Docs"
description: "說明如何使用 StorSimple Manager 的 [備份類別目錄] 頁面，從備份組還原 StorSimple 磁碟區。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6f289c39-96c7-4d57-b68a-4bc2e99aef9d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/22/2017
ms.author: alkohli
ms.openlocfilehash: 99b76e3bc2939c65654cbf606fda6f8a45e0c44b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>從備份組還原 StorSimple 磁碟區 (Update 2)
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>概觀
[備份類別目錄]  頁面會顯示在產生手動或自動備份時建立的所有備份組。 使用此頁面，列出和管理備份、從備份集還原或複製磁碟區。

 ![備份類別目錄頁面](./media/storsimple-restore-from-backup-set-u2/restore.png)

本教學課程說明如何使用 [備份類別目錄]  頁面，從備份組還原您的裝置。

您可以從本機或雲端備份還原磁碟區。 在任一情況下，當資料在背景下載時，還原作業會立即讓磁碟區連線。 

## <a name="before-you-restore"></a>還原之前
在您起始還原作業之前，應該注意下列事項：

* **必須使磁碟區離線** - 起始還原作業之前，先讓主機和裝置上的磁碟區同時離線。 雖然還原作業會自動使裝置上的磁碟區連線，但您必須手動讓主機上的裝置連線。 在裝置上的磁碟區上線之後，您就可以讓主機上的磁碟區上線。 (不需要等到還原作業完成。)如需程序，請前往[使磁碟區離線](storsimple-manage-volumes-u2.md#take-a-volume-offline)。
* **還原後的磁碟區類型** - 根據快照集的類型還原已刪除的磁碟區。 已固定在本機的磁碟區會還原為固定在本機的磁碟區，而已分層的磁碟區會還原為階層式磁碟區。
  
    針對現有的磁碟區，磁碟機目前的使用類型會覆寫快照集中所還原的類型。 例如，如果您從原本磁碟區類型為階層式，而現在磁碟區類型為固定在本機 (因為轉換作業的緣故) 時所採取的快照集還原磁碟區，則磁碟區會還原成固定在本機的磁碟區。 同樣地，如果現有的固定在本機的磁碟區已擴充，且後續從過去磁碟區仍較小時所取得的舊快照集還原，則還原的磁碟區會保留目前擴充的大小。
  
    當磁碟區正在還原時，您無法從階層式磁碟區轉換成固定在本機的磁碟區，反之亦然。 請等候還原作業完成，您就能將磁碟區轉換為其他類型。 如需轉換磁碟區的詳細資訊，請移至 [變更磁碟區類型](storsimple-manage-volumes-u2.md#change-the-volume-type)。 
* **磁碟區大小會反映在還原的磁碟區中** - 如果您要還原已刪除的固定在本機的磁碟區，這是很重要的考量 (因為要完整佈建固定在本機的磁碟區)。 請確定您有足夠的空間，然後再嘗試還原之前已刪除的固定在本機的磁碟區。 
* **正在還原時，您無法擴充磁碟區** – 等候還原作業完成，才能嘗試擴充磁碟區。 如需有關擴充磁碟區的資訊，請移至 [修改磁碟區](storsimple-manage-volumes-u2.md#modify-a-volume)。
* **還原本機磁碟區時，您可以執行備份** – 如需程序，請移至 [使用 StorSimple Manager 服務來管理備份原則](storsimple-manage-backup-policies.md)。
* **可以取消還原作業** - 如果您取消還原作業，則磁碟區會回復到起始還原之前的狀態。 如需程序，請移至 [取消工作](storsimple-manage-jobs-u2.md#cancel-a-job)。

## <a name="how-does-restore-work"></a>還原的運作方式
如果是執行 Update 4 或更新版本的裝置，就會實作熱度圖式還原。 當存取資料的主機要求到達裝置時，即會追蹤這些要求並建立熱度圖。 高要求率會產生具有更高熱度的資料區塊，而較低的要求率會轉譯為熱度較低的區塊。 您必須存取資料至少兩次，才能將其標示為「熱」。 已修改的檔案也會標示為「熱」。 一旦您起始還原之後，接著會根據熱度圖進行資料的主動式水化。 如果版本早於 Update 4，就只會在還原期間根據存取下載資料。 

熱度圖式追蹤只會針對階層式磁碟區啟用，而不支援固定在本機的磁碟區。 將磁碟區複製到另一個裝置時，也不支援熱度圖式還原。 如果有就地還原且裝置上目前有要還原之磁碟區的本機快照集，則我們不會解除凍結 (因為資料已經可在本機使用)。 根據預設，當您還原時，會起始解除凍結作業，以根據熱度圖主動解除凍結資料。 在 Update 4 中，Windows PowerShell Cmdlet 可用來查詢執行中的解除凍結作業、取消解除凍結作業，以及取得解除凍結作業的狀態。

* `Get-HcsRehydrationJob` -此 Cmdlet 會取得解除凍結作業的狀態。 單一解除凍結作業會針對某一個磁碟區來觸發。
* `Set-HcsRehydrationJob` - 此 Cmdlet 可讓您在解除凍結正在進行時暫停、停止、繼續執行解除凍結作業。 

如需解除凍結 Cmdlet 的詳細資訊，請移至[適用於 StorSimple 的 Windows PowerShell Cmdlet 參考](https://technet.microsoft.com/library/dn688168.aspx)。

透過自動解除凍結功能，通常可預期較高的暫時性讀取效能。 改進的實際範圍取決於各種因素，例如存取模式、資料變換及資料類型。 若要取消解除凍結作業，您可以使用 PowerShell Cmdlet。 如果您希望針對所有未來的還原作業永久停用解除凍結作業，請連絡 Microsoft 支援服務。

## <a name="how-to-use-the-backup-catalog"></a>如何使用備份類別目錄
[備份類別目錄]  頁面提供查詢，可協助您縮小備份組選取範圍。 您可以篩選根據下列參數擷取的備份組：

* **裝置** - 建立備份組所在的裝置。
* **備份原則**或**磁碟區** – 與此備份組相關聯的備份原則或磁碟區。
* **起****迄** – 建立備份組的日期和時間範圍。

接著會根據下列屬性，將篩選的備份組列表顯示：

* **名稱** - 與備份組相關聯的備份原則或磁碟區的名稱。
* **大小** - 備份組的實際大小。
* **建立日期** - 建立備份的日期和時間。 
* **類型** - 備份組可以是本機快照集或雲端快照集。 本機快照集是儲存在本機裝置上所有磁碟區資料的備份。 雲端快照集是指位於雲端之磁碟區資料的備份。 本機快照可提供更快速的存取，而雲端快照是選擇來進行資料復原。
* **啟動者** - 備份可根據排程自動初始，或由您手動初始。 (您可以使用備份原則來排程備份。 或者，可以使用 [取得備份] 選項來取得互動式備份。)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>如何從備份還原您的 StorSimple 磁碟區
您可以使用 [備份類別目錄]  頁面，從特定的備份還原 StorSimple 磁碟區。 不過，請記住，還原磁碟區會將磁碟區的狀態還原為其在取得備份時的狀態。 在備份作業之後新增的所有資料都會遺失。

> [!WARNING]
> 從備份還原會從備份取代現有的磁碟區。 這可能會造成在取得備份之後寫入的所有資料遺失。
> 
> 

### <a name="to-restore-your-volume"></a>還原您的磁碟區
1. 在 StorSimple Manager 服務頁面上，按一下  [備份類別目錄]  索引標籤。
   
    ![備份類別目錄](./media/storsimple-restore-from-backup-set-u2/restore.png)
2. 選取備份組，如下所示：
   
   1. 選取適當的裝置。
   2. 在下拉式清單中，針對要選取的備份選擇磁碟區或備份原則。
   3. 指定時間範圍。
   4. 按一下核取圖示  ![核取圖示](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) 以執行此查詢。
      
      與選取的磁碟區或備份原則相關聯的備份應該會出現在備份組清單中。
3. 展開備份組以檢視相關聯的磁碟區。 您必須先在主機和裝置上將這些磁碟區離線，才能還原它們。 存取 [磁碟區容器] 頁面上的磁碟區，然後依照 [讓磁碟區離線](storsimple-manage-volumes-u2.md#take-a-volume-offline) 中的指示來讓它們離線。
   
   > [!IMPORTANT]
   > 確定您已先讓主機上的磁碟區離線，然後再讓裝置上的磁碟區離線。 如果您並未讓主機上的磁碟區離線，可能會導致資料損毀。
   > 
   > 
4. 瀏覽回到 [備份類別目錄]  索引標籤，並選取備份組。
5. 按一下頁面底部的 [還原]  。
6. 系統會提示您進行確認。 檢閱還原資訊，然後選取 [確認] 核取方塊。
   
    ![確認電子郵件](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)
7. 按一下核取圖示 ![核取圖示](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) 還原作業開始。 您可以藉由存取 [作業] 頁面來檢視作業。 
8. 還原完成之後，您可以確認磁碟區的內容已由備份的磁碟區所取代。

![提供的影片](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **提供的影片**

若要觀看影片示範如何使用 StorSimple 的複製和還原功能，將已刪除的檔案復原，請按一下 [這裡](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)。

## <a name="if-the-restore-fails"></a>如果還原失敗
如果還原作業因為任何原因而失敗，您會收到警示。 如果發生這種情況，請重新整理備份清單，以確認備份仍然有效。 如果備份有效，且您是從雲端還原，則連線問題可能會造成問題。 

若要完成還原作業，請使主機上的磁碟區離線，然後重試還原作業。 在還原程序期間所執行的所有磁碟區資料修改都會遺失。

## <a name="next-steps"></a>後續步驟
* 了解如何 [管理 StorSimple 磁碟區](storsimple-manage-volumes-u2.md)。
* 了解如何 [使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

