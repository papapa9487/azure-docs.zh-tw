---
title: Microsoft Azure StorSimple Data Manager UI | Microsoft Docs
description: "說明如何使用 StorSimple Data Manager 服務 UI (私人預覽)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 53a8599df2c647613122cd791b680e2e658586b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-using-the-storsimple-data-manager-service-ui-private-preview"></a>使用 StorSimple Data Manager 服務 UI 來管理 (私人預覽)

本文說明如何使用 StorSimple Data Manager UI，對 StorSimple 8000 系列裝置上的資料執行資料轉換。 然後，轉換後的資料就可供其他 Azure 服務取用，例如 Azure 媒體服務、Azure HDInsight、Azure Machine Learning 和 Azure 搜尋服務。 


## <a name="use-storsimple-data-transformation"></a>使用 StorSimple 資料轉換

StorSimple Data Manager 是可讓您具現化資料轉換的資源。 資料轉換服務可讓您將資料從 StorSimple 內部部署裝置移至 Azure 儲存體中的 blob。 因此，針對您的 StorSimple 裝置和您想要移至儲存體帳戶的資料，您必須在工作流程中指定詳細資訊。

### <a name="create-a-storsimple-data-manager-service"></a>建立 StorSimple Data Manager 服務

執行下列步驟來建立 StorSimple Data Manager 服務。

1. 若要建立 StorSimple Data Manager 服務，請移至 [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)

2. 按一下 **+** 圖示，並搜尋 StorSimple Data Manager。 按一下您的 StorSimple Data Manager 服務，然後按一下建立。

3. 如果您的訂用帳戶已啟用來建立此服務，您會看到下列刀鋒視窗。

    ![建立 StorSimple Data Managers 資源](./media/storsimple-data-manager-ui/create-new-data-manager-service.png)

4. 輸入後按一下 [建立]。 指定的位置要用來裝載您的儲存體帳戶和 StorSimple Manager 服務。 目前，僅支援美國西部和西歐區域。 因此，您的 StorSimple Manager 服務、資料管理員服務和相關聯的儲存體帳戶，都必須位於上述支援的區域。 需要一分鐘的時間來建立服務。

### <a name="create-a-data-transformation-job-definition"></a>建立資料轉換作業定義

在 StorSimple Data Manager 服務中，您需要建立資料轉換作業定義。 針對您想要以原生格式移至儲存體帳戶的資料，作業定義指定其詳細資訊。 

執行下列步驟來建立新的資料轉換作業定義。

1.  瀏覽至您所建立的服務。 按一下 [+ 作業定義]。

    ![按一下 [+ 作業定義]](./media/storsimple-data-manager-ui/click-add-job-definition.png)

2. 新的作業定義刀鋒視窗隨即開啟。 指定作業定義名稱，然後按一下 [來源]。 在 [設定資料來源] 刀鋒視窗中，指定您的 StorSimple 裝置和感興趣資料的詳細資訊。

    ![建立作業定義](./media/storsimple-data-manager-ui//create-new-job-deifnition.png)

3. 由於這是新的資料管理員服務，尚未設定任何資料儲存機制。 若要新增您的 StorSimple Manager 做為資料儲存機制，請在資料儲存機制下拉式清單中按一下 新增，然後按一下新增資料儲存機制。

4. 選擇 [StorSimple 8000 系列 Manager] 做為儲存機制類型，然後輸入 **StorSimple Manager** 的屬性。 在 [資源識別碼] 欄位中，您必須輸入 StorSimple Manager 註冊金鑰中 **:** 前面的數字。

    ![建立資料來源](./media/storsimple-data-manager-ui/create-new-data-source.png)

5.  完成時按一下 [確定]。 這樣會儲存您的資料儲存機制，而且此 StorSimple Manager 可以重複使用在其他作業定義中，不需要再次輸入這些參數。 按一下 [確定] 後，經過幾秒，StorSimple Manager 就會出現在下拉式清單中。

6.  在 [設定資料來源] 刀鋒視窗中，輸入您感興趣的資料所在的裝置名稱和磁碟區名稱。

7.  在 [篩選] 子區段中，輸入您感興趣的資料所在的根目錄 (此欄位的開頭應該為 `\`)。 您也可以在這裡新增任何檔案篩選。

8.  資料轉換服務會處理透過快照推送至 Azure 的資料。 執行此作業時，您可以選擇每次執行此作業時進行備份 (以處理最新資料)，或使用雲端中存在的最新備份 (如果您正在使用一些保存資料)。

    ![新增資料來源詳細資料](./media/storsimple-data-manager-ui/new-data-source-details.png)

9. 接下來，必須設定「目標」設定。 有 2 種支援的目標 – Azure 儲存體帳戶和 Azure 媒體服務帳戶。 選擇儲存體帳戶，將檔案放入該帳戶中的 blob。 選擇媒體服務帳戶，將檔案放入該帳戶中的資產。 同樣地，我們需要新增儲存機制。 在下拉式清單中，選取 [新增]，然後選取 [進行設定]。

    ![建立資料接收](./media/storsimple-data-manager-ui/create-new-data-sink.png)

10. 在這裡，您可以選取想要新增的儲存機制類型，以及與儲存機制相關聯的其他參數。 在這兩種情況下，執行此作業時都會建立儲存體佇列。 此佇列中會填入已轉換的 blob 備妥時的相關訊息。 此佇列的名稱與作業定義的名稱相同。 如果您選取 [媒體服務] 做為儲存機制類型，則您也可以輸入建立佇列的儲存體帳戶認證。

    ![新增資料接收詳細資料](./media/storsimple-data-manager-ui/new-data-sink-details.png)

11. 新增資料儲存機制之後 (需要幾秒鐘)，您在 [目標帳戶名稱] 的下拉式清單中會看到此儲存機制。  選擇您需要的目標。

12. 按一下 [確定] 來建立作業定義。 現在已設定作業定義。 您可以透過 UI 來多次使用此作業定義。

    ![新增作業定義](./media/storsimple-data-manager-ui/add-new-job-definition.png)

### <a name="run-the-job-definition"></a>執行作業定義

每當您需要將資料從 StorSimple 移至您在作業定義中指定的儲存體帳戶時，您必須叫用它。 每次叫用作業時變更參數會比較有彈性。 步驟如下：

1. 選取您的 StorSimple Data Manager 服務，然後移至 [監視]。 按一下 [立即執行]。

    ![Trigger job definition](./media/storsimple-data-manager-ui/run-now.png)

2. 選擇您想要執行的作業定義。 按一下 [回合設定]，修改您可能想針對這次作業執行來變更的任何設定。

    ![執行作業設定](./media/storsimple-data-manager-ui/run-settings.png)

3. 按一下 確定，然後按一下執行 來啟動您的作業。 若要監視此作業，請移至 StorSimple Data Manager 的 [作業] 頁面。

    ![作業清單及狀態](./media/storsimple-data-manager-ui/jobs-list-and-status.png)

4. 除了在 [作業] 刀鋒視窗中監視，您也可以聆聽儲存體佇列，每次有檔案從 StorSimple 移至儲存體帳戶時就會加入訊息。


## <a name="next-steps"></a>後續步驟

[使用 .NET SDK 啟動 StorSimple Data Manager 作業](storsimple-data-manager-dotnet-jobs.md)。