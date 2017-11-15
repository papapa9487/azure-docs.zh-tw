---
title: "檢視和管理 StorSimple 工作 | Microsoft Docs"
description: "說明 StorSimple Manager 服務工作頁面，以及如何使用該頁面來追蹤最近、目前和排程的備份工作。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 55922cd0-d490-48eb-938a-012a67c1c09e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 426fd3ec76157670c9d192f007adacc4284abb42
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs"></a>使用 StorSimple Manager 服務來檢視和管理 StorSimple 工作
> [!NOTE]
> StorSimple 的傳統入口網站已過時。 按照淘汰排程，StorSimple 裝置管理員會自動移至新的 Azure 入口網站。 您將收到關於此移動的電子郵件和入口網站通知。 本文件也很快就會淘汰。 若有關於移動的任何問題，請參閱[常見問題集：移至 Azure 入口網站](storsimple-8000-move-azure-portal-faq.md)。

[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>概觀
[工作] 頁面提供單一中央入口網站，可針對連線到 StorSimple Manager 服務的裝置，檢視及管理在裝置上啟動的工作。 您可以針對多個裝置，檢視已排程、執行中、完成和失敗的工作。 結果會以表格式格式呈現。

![[工作] 頁面](./media/storsimple-manage-jobs/HCS_JobsPage.png)

您可以透過篩選欄位，快速找到您所感興趣的工作，例如：

* **狀態** – 工作可以是執行中、已排程、失敗、完成、取消中或已取消。
* **類型** – 工作可以透過排程或隨選備份 (**進行備份**)、複製、裝置還原或更新作業而建立。
* **裝置** – 工作會在連線到服務的特定裝置上進行初始化。
* **起訖** – 工作可以根據日期和時間範圍進行篩選。

篩選的工作接著會根據下列屬性製成表格：

* **類型** – 備份、複製、還原、容錯移轉或更新。
* **狀態** – 執行中、已排程、失敗、完成、取消中或已取消。
* **實體** – 工作可以與磁碟區、備份原則或裝置相關聯。 複製工作與磁碟區相關聯，而排程的備份工作則與備份原則相關聯。 裝置工作是透過災害復原 (DR) 或還原作業而建立。
* **裝置** – 用來啟動工作之裝置的名稱。
* **開始於** – 啟動工作的時間。
* **進度** – 執行中工作的完成百分比。 對於完成的工作，百分比應該永遠是 100%。

工作清單每隔 30 秒會重新整理。

您可以在此頁面上執行下列工作相關的動作：

* 檢視工作詳細資料
* 取消工作

## <a name="view-job-details"></a>檢視工作詳細資料
執行下列步驟來檢視任何工作的詳細資料。

#### <a name="to-view-job-details"></a>若要檢視工作詳細資料
1. 在 [工作] 頁面上，透過搭配適當的篩選器執行查詢來顯示您所感興趣的工作。 您可以搜尋完成、執行中或已取消工作。
2. 選取一個工作。
3. 按一下頁面底部的 [詳細資料]。
4. 在 [備份工作詳細資料] 對話方塊中，您可以檢視狀態、詳細資料、時間統計資料和資料統計資料。

## <a name="cancel-a-job"></a>取消工作
執行下列步驟來取消執行中工作。

### <a name="to-cancel-a-job"></a>取消工作
1. 在 [工作] 頁面上，透過搭配適當的篩選器執行查詢來顯示您要取消的執行中工作。
2. 選取工作。
3. 按一下頁面底部的 [取消]。
4. 系統提示您進行確認時，按一下 [是]。

即會取消此工作。

## <a name="next-steps"></a>後續步驟
* 了解如何[管理您的 StorSimple 備份原則](storsimple-manage-backup-policies.md)。
* 了解如何[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

