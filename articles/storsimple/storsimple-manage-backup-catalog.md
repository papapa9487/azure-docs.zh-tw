---
title: "管理您的 StorSimple 備份類別目錄 | Microsoft Docs"
description: "說明如何使用 StorSimple Manager 服務的 [備份類別目錄] 頁面列出、選取和刪除磁碟區的備份組。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ad81bee9-fe43-40b3-a384-b15fb274ecd9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/28/2016
ms.author: v-sharos
ms.openlocfilehash: 5ee9855e1428c7a2d871d9c215d302c5c3b7101a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>使用 StorSimple Manager 服務管理備份類別目錄
## <a name="overview"></a>概觀
StorSimple Manager 服務 [備份類別目錄]  頁面會顯示在進行手動備份或排程備份時所建立的所有備份組。 您可以使用此頁面來列出備份原則或磁碟區的所有備份、選取或刪除備份，或是使用備份來還原或複製磁碟區。

本教學課程說明如何列出、選取和刪除備份組。 若要了解如何透過備份還原裝置內容，請移至 [從備份組還原裝置](storsimple-restore-from-backup-set.md)。 若要了解如何複製磁碟區，請移至 [複製 StorSimple 磁碟區](storsimple-clone-volume.md)。

![備份類別目錄](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

[備份類別目錄]  頁面提供查詢，可讓您縮小備份組選取範圍。 您可以篩選根據下列參數擷取的備份組：

* **裝置** - 建立備份組所在的裝置。
* **備份原則或磁碟區** – 與此備份組相關聯的備份原則或磁碟區。
* **起迄** – 建立備份組的日期和時間範圍。

接著會根據下列屬性，將篩選的備份組列表顯示：

* **名稱** - 與備份組相關聯的備份原則或磁碟區的名稱。
* **大小** - 備份組的實際大小。
* **建立日期** - 建立備份的日期和時間。 
* **類型** - 備份組可以是本機快照集或雲端快照集。 本機快照是本機儲存於裝置上的所有磁碟區資料備份，而雲端快照是指位於雲端的磁碟區資料備份。 本機快照可提供更快速的存取，而雲端快照是選擇來進行資料復原。
* **啟動者** – 備份可由排程自動啟動，或由使用者手動啟動。 您可以使用備份原則排程備份。 或者，可以使用 [進行備份]  選項來進行手動備份。

## <a name="list-backup-sets-for-a-volume"></a>列出磁碟區的備份組
完成下列步驟，以列出磁碟區的所有備份。

#### <a name="to-list-backup-sets"></a>若要列出備份組
1. 在 StorSimple Manager 服務頁面上，按一下  [備份類別目錄]  索引標籤。
2. 如下方所示，篩選選取項目：
   
   1. 選取適當的裝置。
   2. 在下拉式清單中，選擇要檢視對應備份的磁碟區。
   3. 指定時間範圍。
   4. 按一下核取圖示  ![核取圖示](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 以執行此查詢。
      
      與選取的磁碟區相關的備份應會顯示在備份組清單中。

## <a name="select-a-backup-set"></a>選取備份組
完成下列步驟，選取磁碟區的備份組或備份原則。

#### <a name="to-select-a-backup-set"></a>若要選取備份組
1. 在 StorSimple Manager 服務頁面上，按一下  [備份類別目錄]  索引標籤。
2. 如下方所示，篩選選取項目：
   
   1. 選取適當的裝置。
   2. 在下拉式清單中，針對要選取的備份選擇磁碟區或備份原則。
   3. 指定時間範圍。
   4. 按一下核取圖示  ![核取圖示](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 以執行此查詢。
      
      與選取的磁碟區或備份原則相關聯的備份應該會出現在備份組清單中。
3. 選取並展開備份組。 [還原] 與 [刪除] 選項會顯示在頁面底部。 您可以在選取的備份組上執行以上任一動作。

## <a name="delete-a-backup-set"></a>刪除備份組
不再需要保留與備份相關的資料時，請將備份刪除。 執行下列步驟以刪除備份組。

#### <a name="to-delete-a-backup-set"></a>若要刪除備份組
1. 請在 StorSimple Manager 服務頁面上，按一下 [備份類別目錄] 索引標籤。
2. 如下方所示，篩選選取項目：
   
   1. 選取適當的裝置。
   2. 在下拉式清單中，針對要選取的備份選擇磁碟區或備份原則。
   3. 指定時間範圍。
   4. 按一下核取圖示  ![核取圖示](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 以執行此查詢。
      
      與選取的磁碟區或備份原則相關聯的備份應該會出現在備份組清單中。
3. 選取並展開備份組。 [還原] 與 [刪除] 選項會顯示在頁面底部。 按一下 [刪除] 。
4. 刪除進行中和順利完成時，您會收到通知。 刪除完成之後，重新整理此頁面上的查詢。 已刪除的備份組將不會再顯示於備份組清單中。

## <a name="next-steps"></a>後續步驟
* 了解如何使用備份類別目錄以 [從備份組還原裝置](storsimple-restore-from-backup-set.md)。
* 了解如何 [使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

