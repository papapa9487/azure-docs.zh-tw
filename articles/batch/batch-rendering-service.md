---
title: "使用 Azure Batch 轉譯服務在雲端中轉譯 |Microsoft Docs"
description: "Azure 虛擬機器上的轉譯作業直接由 Maya 提供且按使用次數付費。"
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: hero-article
ms.date: 09/14/2017
ms.author: danlep
ms.openlocfilehash: 47ccbd89d5abf04034196ab735c6740d57099023
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-batch-rendering-service"></a>開始使用 Batch 轉譯服務

Azure Batch 轉譯服務提供了按使用次數付費的雲端規模轉譯功能。 Batch 轉譯服務會處理作業排程和佇列、管理失敗和重試，以及針對轉譯作業進行自動調整。 Batch 轉譯服務可支援 [Autodesk Maya](https://www.autodesk.com/products/maya/overview)、[3ds Max](https://www.autodesk.com/products/3ds-max/overview)、[Arnold](https://www.autodesk.com/products/arnold/overview) 和 [V-Ray](https://www.chaosgroup.com/vray/maya)。 適用於 Maya 2017 的 Batch 外掛程式可直接從您的桌面輕鬆地在 Azure 上啟動轉譯作業。

有了 Maya 和 3ds Max，您可以使用 [Batch Labs](https://github.com/Azure/BatchLabs) 桌面應用程式或 [Batch 範本 CLI](batch-cli-templates.md) 執行作業。 使用 Azure Batch CLI，您可以直接執行 Batch 作業而不需要撰寫程式碼。 相反地，您可以使用範本檔案來建立 Batch 集區、作業和工作。 如需詳細資訊，請參閱[使用 Azure Batch CLI 範本和檔案傳輸](batch-cli-templates.md)。


## <a name="supported-applications"></a>支援的應用程式

Batch 轉譯服務目前支援下列應用程式：

- Autodesk Maya
- Autodesk 3ds Max
- Autodesk Arnold for Maya
- Autodesk Arnold for 3ds Max
- Chaos Group V-Ray for Maya
- Chaos Group V-Ray for 3ds Max

## <a name="prerequisites"></a>必要條件

若要使用 Batch 轉譯服務，您需要：

- [Azure 帳戶](https://azure.microsoft.com/free/)。
- **Azure Batch 帳戶**。 如需在 Azure 入口網站中建立 Batch 帳戶的指引，請參閱[使用 Azure 入口網站建立 Batch 帳戶](batch-account-create-portal.md)。
- **Azure 儲存體帳戶**。 轉譯作業所用的資產會儲存在 Azure 儲存體中。 當您設定 Batch 帳戶時，可以自動建立儲存體帳戶。 您也可以使用現有的儲存體帳戶。 若要深入了解儲存體帳戶，請參閱[如何在 Azure 入口網站中建立、管理或刪除儲存體帳戶](https://docs.microsoft.com/azure/storage/storage-create-storage-account)。

若要使用適用於 Maya 的 Batch 外掛程式，您需要：

- [Autodesk Maya 2017](https://www.autodesk.com/products/maya/overview)。
- 支援的轉譯器，例如 Arnold for Maya 或 V-Ray for Maya。

## <a name="basic-batch-concepts"></a>基本 Batch 概念

開始使用 Batch 轉譯服務之前，最好先熟悉一些 Batch 概念，包括計算節點、集區和作業。 若要深入了解 Azure Batch，請參閱[使用 Batch 執行本質平行的工作負載](batch-technical-overview.md)。

### <a name="pools"></a>集區

Batch 是一項平台服務，用於在**計算節點**的**集區**上執行計算密集工作 (例如轉譯)。 集區中的每個計算節點不是執行 Linux 就是執行 Windows 的 Azure 虛擬機器 (VM)。 

如需 Batch 集區和計算節點的詳細資訊，請參閱[使用 Batch 開發大規模的平行計算解決方案](batch-api-basics.md)中的[集區](batch-api-basics.md#pool)和[計算節點](batch-api-basics.md#compute-node)章節。

### <a name="jobs"></a>作業

Batch **作業** 是在集區中計算節點上執行的工作集合。 當您提交轉譯作業時，Batch 會將作業分成數個工作，並將這些工作散發到集區內的計算節點進行執行。

藉由下載應用程式記錄並使用 RDP 或 SSH 從遠端連線至個別的虛擬機器，即可使用 [Azure 入口網站](https://ms.portal.azure.com/)來監視作業及診斷失敗的工作。 您也可以使用 [Batch Labs 用戶端](https://github.com/Azure/BatchLabs)管理、監視以及偵錯。

如需 Batch 作業的詳細資訊，請參閱[使用 Batch 開發大規模的平行計算解決方案](batch-api-basics.md#job)中的[作業](batch-api-basics.md)一節。

## <a name="options-for-provisioning-required-applications"></a>佈建必要應用程式的選項

轉譯工作可能需要多個應用程式，例如 Maya 和 Arnold 或 3ds Max 和 V-Ray 的結合，以及其他第三方外掛程式 (如必要)。 此外，有些客戶可能需要這些應用程式的特定版本。 因此，有數種方法可用來佈建必要的應用程式和軟體：

### <a name="pre-configured-vm-images"></a>預先設定的虛擬機器映像

Azure 針對 Windows 和 Linux 映像各預先安裝一個版本的 Maya、3ds Max、Arnold 和 V-Ray 且可供使用。 建立集區時，您可以在 [Azure 入口網站](https://portal.azure.com)、Maya 外掛程式或[ Batch Labs](https://github.com/Azure/BatchLabs) 中選取這些映像。

在 Azure 入口網站和 Batch Labs 中，您可以使用預先安裝的應用程式來安裝其中一個虛擬機器映像，如下所示：在 Batch 帳戶的 [集區] 區段中，選取 [新增]，然後在 [新增集區] 中，從 [映像類型] 下拉式清單選取 [圖形和轉譯 (Linux/Windows)]：

![選取 Batch 帳戶的映像類型](./media/batch-rendering-service/add-pool.png)

向下捲動並按一下 [圖形和轉譯授權]，以開啟 [選擇授權] 刀鋒視窗，然後選取一或多個軟體授權：

![選取集區的圖形和轉譯授權](./media/batch-rendering-service/graphics-licensing.png)

提供的特定授權版本如下所示：

- Maya 2017
- 3ds Max 2018
- Arnold for Maya 5.0.1.1
- Arnold for 3ds Max 1.0.836
- V-Ray for Maya 3.52.03
- V-Ray for 3ds Max 3.60.01

### <a name="custom-images"></a>自訂映像

Azure Batch 可讓您提供您自己的自訂映像。 使用此選項，可以為您的虛擬機器設定所需要的確切應用程式與特定版本。 如需詳細資訊，請參閱[使用自訂映像來建立虛擬機器的集區](https://docs.microsoft.com/en-us/azure/batch/batch-custom-images)。 請注意，Autodesk 和 Chaos Group 已分別修改 Arnold 和 V-Ray，以驗證我們自己的授權服務。 您將必須確定您的應用程式版本具有此支援，否則按使用次數付費授權將無法運作。 此授權驗證對 Maya 或 3ds Max 並非必要，因為目前發行的版本在執行無周邊 (以批次/命令列模式) 時，不需要授權伺服器。 如果您不確定如何繼續使用此選項，請連絡 Azure 支援。

## <a name="options-for-submitting-a-render-job"></a>用於提交轉譯作業的選項

根據您使用的 3D 應用程式，有各種選項可用來將轉譯作業提交至服務：

### <a name="maya"></a>Maya

有了 Maya，您可以使用：

- [Maya 適用的 Batch 外掛程式](https://docs.microsoft.com/en-us/azure/batch/batch-rendering-service#use-the-batch-plug-in-for-maya-to-submit-a-render-job)
- [Batch Labs](https://github.com/Azure/BatchLabs) 桌面應用程式
- [Batch 範本 CLI](batch-cli-templates.md)

### <a name="3ds-max"></a>3ds Max

有了 3ds Max，您可以使用：

- [Batch Labs](https://github.com/Azure/BatchLabs) 桌面應用程式 (如需使用 3ds Max Batch Labs 範本的指引，請參閱[ Batch Labs 資料](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax))
- [Batch 範本 CLI](batch-cli-templates.md)

3ds Max Batch Labs 範本可讓您使用 Azure Batch 轉譯服務來轉譯 VRay 和 Arnold 場景。 VRay 和 Arnold 的範本有兩種變化，一個用於標準場景，一個用於需要資產和紋理的 3ds Max 路徑檔案 (.mxp 檔案) 的更複雜場景。 如需有關 3ds Max Batch Labs 範本的詳細資訊，請參閱 GitHub 上的 [Batch Labs 資料](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax)儲存機制。

此外，您可以使用 [Batch Python SDK](https://docs.microsoft.com/en-us/azure/batch/batch-python-tutorial) 來將轉譯服務與您現有的管線整合。


## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>使用適用於 Maya 的 Batch 外掛程式提交轉譯作業

透過適用於 Maya 的 Batch 外掛程式，您可以直接從 Maya 將作業提交至 Batch 轉譯服務。 下列各節說明如何從外掛程式設定作業，然後加以提交。 

### <a name="load-the-batch-plug-in-for-maya"></a>載入 Maya 適用的 Batch 外掛程式

Batch 外掛程式位於 [GitHub](https://github.com/Azure/azure-batch-maya/releases)。 將封存解壓縮到您所選的目錄。 您可以直接從 *azure_batch_maya* 目錄載入外掛程式。

若要在 Maya 中載入外掛程式：

1. 執行 Maya。
2. 開啟 [視窗] > [設定/喜好設定] > [外掛程式管理員]。
3. 按一下 [瀏覽] 。
4. 瀏覽並選取 *azure_batch_maya/plug-in/AzureBatch.py*。

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>驗證 Batch 和儲存體帳戶的存取權

若要使用外掛程式，您需要使用您的 Azure Batch 和 Azure 儲存體帳戶金鑰進行驗證。 若要擷取您的帳戶金鑰：

1. 在 Maya 中顯示外掛程式，然後選取 [設定] 索引標籤。
2. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
3. 從左側功能表中選取 [Batch 帳戶]。 如有必要，按一下 [更多服務]並依照 [批次] 篩選。
4. 在清單中找出所需的 Batch 帳戶。
5. 選取 [金鑰] 功能表項目，以顯示您的帳戶名稱、帳戶 URL 和存取金鑰：
    - 將 Batch 帳戶 URL 貼到 Batch 外掛程式的 [服務] 欄位。
    - 將帳戶名稱貼到 [Batch 帳戶] 欄位中。
    - 將主要帳戶金鑰貼到 [Batch 金鑰] 欄位中。
7. 從左側功能表中選取 [儲存體帳戶]。 如有必要，按一下 [更多服務]並依照 [儲存體] 篩選。
8. 在清單中找出所需的儲存體帳戶。
9. 選取 [存取金鑰] 功能表項目，以顯示儲存體帳戶和金鑰。
    - 將儲存體帳戶名稱貼到 Batch 外掛程式的 [儲存體帳戶] 欄位中。
    - 將主要帳戶金鑰貼到 [儲存體金鑰] 欄位中。
10. 按一下 [驗證] 以確保外掛程式可以存取這兩個帳戶。

一旦通過驗證，外掛程式會將 [狀態] 欄位設定為 [已驗證]： 

![驗證 Batch 和儲存體帳戶](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>設定轉譯作業的集區

您已驗證 Batch 和儲存體帳戶之後，請針對您的轉譯作業設定集區。 外掛程式會儲存您在工作階段之間選取的項目。 設定慣用的組態後，您不需要修改組態 (除非它變更)。

下列各節逐步說明 [提交] 索引標籤上可用的選項：

#### <a name="specify-a-new-or-existing-pool"></a>指定新的或現有集區

若要指定要執行轉譯作業的集區，請選取 [提交] 索引標籤。此索引標籤會提供建立集區或選取現有集區的選項：

- 您可以**為此作業自動佈建集區** (預設選項)。 當您選擇此選項時，Batch 會建立目前作業專用的集區，並且在轉譯作業完成時自動刪除集區。 當您有單一轉譯作業要完成時，最好使用此選項。
- 您可以**重複使用現有的永續性集區**。 如果您有現有的集區處於閒置狀態，您可以從下拉式清單中選取該集區，指定用於執行轉譯作業的集區。 重複使用現有的永續性集區可節省佈建集區所需的時間。  
- 您可以**建立新的永續性集區**。 選擇此選項可建立新集區，以便執行作業。 系統不會在作業完成時刪除集區，因此您可在未來作業中重複使用。 當您需要連續執行轉譯作業時，請選取此選項。 對於後續作業，您可以選取**重複使用現有的永續性集區**，以使用您為第一個作業建立的永續性集區。

![指定集區、OS 映像、VM 大小和授權](./media/batch-rendering-service/submit.png)

如需有關 Azure VM 計費方式的詳細資訊，請參閱 [Linux 價格常見問題集](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq)和 [Windows 價格常見問題集](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq)。

#### <a name="specify-the-os-image-to-provision"></a>指定要佈建的 OS 映像

您可以在 [環境] 索引標籤上指定要用於在集區中佈建計算節點的 OS 映像類型。Batch 目前支援轉譯作業的下列映像選項：

|作業系統  |映像  |
|---------|---------|
|Linux     |Batch CentOS 預覽 |
|Windows     |Batch Windows 預覽 |

#### <a name="choose-a-vm-size"></a>選擇 VM 大小

您可以在 [環境] 索引標籤上指定 VM 大小。如需可用 VM 大小的詳細資訊，請參閱 [Azure 中的 Linux VM 大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)和 [Azure 中的 Windows VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。 

![在 [環境] 索引標籤上指定 VM OS 映像和大小](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>指定授權選項

您可以在 [環境] 索引標籤上指定您要使用的授權。選項包括：

- **Maya**，預設會啟用。
- **Arnold**，如果偵測到 Arnold 是 Maya 中的作用中轉譯引擎，則會啟用它。

 如果您想要使用自己的授權進行轉譯，您可以將適當的環境變數新增至資料表，以設定您的授權端點。 如果您這麼做，請務必取消選取預設授權選項。

> [!IMPORTANT]
> 當 VM 在集區中執行時，您需支付授權的使用費用，即使 VM 目前並未用於轉譯亦然。 若要避免產生額外費用，請導覽至 [集區] 索引標籤，然後將集區大小調整為 0 個節點，直到您準備要執行另一項轉譯工作為止。 
>
>

#### <a name="manage-persistent-pools"></a>管理永續性集區

您可以在 [集區] 索引標籤上管理現有的永續性集區。從清單中選取集區可顯示集區的目前狀態。

從 [集區] 索引標籤，您也可以刪除集區並調整集區中的 VM 數目。 您可以將集區大小調整為 0 個節點，以免產生工作負載之間的成本。

![檢視、調整大小和刪除集區](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>設定轉譯作業以便提交

為將要執行轉換作業的集區指定參數後，請設定作業本身。 

#### <a name="specify-scene-parameters"></a>指定場景參數

Batch 外掛程式會偵測您目前在 Maya 中使用的轉譯引擎，並在 [提交] 索引標籤上顯示適當的轉譯設定。這些設定包括開始畫面、結束畫面、輸出前置詞和畫面速度。 您可以在外掛程式中指定不同的設定，以覆寫場景檔案轉譯設定。 您對外掛程式設定所做的變更不會保存回場景檔案轉譯設定，因此您可以逐一作業進行變更，而不需重新上傳場景檔案。

如果您在 Maya 中選取的轉譯引擎不受支援，外掛程式會警告您。

如果您載入新場景，外掛程式開啟時，按一下 [重新整理] 按鈕，以確定已更新的設定。

#### <a name="resolve-asset-paths"></a>解析資產路徑

當您載入外掛程式時，它會掃描場景檔案是否有任何外部檔案參考。 這些參考會顯示於 [資產] 索引標籤。如果無法解析參考的路徑，外掛程式會嘗試在一些預設位置找出檔案，包括：

- 場景檔案的位置 
- 目前專案的 _sourceimages_ 目錄
- 目前的工作目錄。 

如果仍然找不到資產，它會以警告圖示列示：

![遺失的資產會顯示警告圖示](./media/batch-rendering-service/missing_assets.png)

如果您知道未解析檔案參考的位置，可以按一下將出現提示的警告圖示以新增搜尋路徑。 外掛程式會接著使用這個搜尋路徑，嘗試解析任何遺失的資產。 您可以新增任意數目的其他搜尋路徑。

解析參考時，它會以綠燈圖示列示：

![解析的資產會顯示綠燈圖示](./media/batch-rendering-service/found_assets.png)

如果您的場景需要外掛程式未偵測到的其他檔案，您可以新增其他檔案或目錄。 使用 [新增檔案] 和 [新增目錄] 按鈕。 如果您在外掛程式為開啟狀態時載入新場景，務必按一下 [重新整理] 以更新場景的參考。

#### <a name="upload-assets-to-an-asset-project"></a>將資產上傳到資產專案

當您提交轉譯作業時，[資產] 索引標籤中顯示的參考檔案會自動上傳至 Azure 儲存體作為資產專案。 使用 [資產] 索引標籤上的 [上傳] 按鈕，也可以不依賴轉譯作業上傳資產檔案。資產專案名稱指定於 [專案] 欄位中，預設是以目前的 Maya 專案命名。 上傳資產檔案後，會保留本機檔案結構。 

一旦上傳，資產即可由任意數量的轉譯作業參考。 所有上傳的資產可用於任何參考資產專案的作業 (不論是否包含在場景中)。 若要變更下一項作業所參考的資產專案，請變更 [資產] 索引標籤的 [專案] 欄位中的名稱。如果您想從上傳中排除某些參考檔案，請使用清單旁邊的綠色按鈕，取消選取這些檔案。

#### <a name="submit-and-monitor-the-render-job"></a>提交和監視轉譯作業

在外掛程式中設定轉譯作業之後，按一下 [提交] 索引標籤上的 [提交作業] 按鈕，將作業提交至 Batch：

![提交轉譯作業](./media/batch-rendering-service/submit_job.png)

您可以從外掛程式的 [作業] 索引標籤，監視正在進行的作業。 從清單中選取作業，以顯示作業的目前狀態。 您也可以使用此索引標籤來取消和刪除作業，以及下載輸出和轉譯記錄。 

若要下載輸出，請修改 [輸出] 欄位以設定所需的目的地目錄。 按一下齒輪圖示，以啟動可監看作業並在作業進行時下載輸出的背景程序： 

![檢視作業狀態並下載輸出](./media/batch-rendering-service/jobs.png)

您可以關閉 Maya 而不會中斷下載程序。

## <a name="next-steps"></a>後續步驟

若要深入了解 Batch，請參閱[使用 Batch 執行本質平行的工作負載](batch-technical-overview.md)。