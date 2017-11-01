---
title: "自行車共享教學課程 - 使用 Azure Machine Learning Workbench 的進階資料準備"
description: "以及使用 Azure Machine Learning Workbench 的端對端資料準備教學課程"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial, azure
ms.topic: article
ms.date: 09/21/2017
ms.openlocfilehash: 6d1845e27c6b0fff66b80a683f59d14238e2ad71
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>自行車共享教學課程：使用 Azure Machine Learning Workbench 的進階資料準備
Azure Machine Learning 服務 (預覽) 是一套整合性的端對端資料科學，以及進階分析解決方案，適用於專業資料科學家準備資料、開發實驗並部署雲規模模型。

在本教學課程中，您可以使用 Azure Machine Learning 服務 (預覽) 了解如何：
> [!div class="checklist"]
> * 使用 Azure Machine Learning 資料準備工具，以互動方式準備資料
> * 匯入、轉換及建立測試資料集
> * 產生資料準備封裝
> * 使用 Python 執行資料準備封裝
> * 針對其他輸入檔案重複使用資料準備封裝以產生訓練資料集

> [!IMPORTANT]
> 本教學課程只會準備資料，不會建立預測模型。
>
> 您可以使用準備的資料來定型您自己的預測模型。 例如，您可以建立一個模型來預測 2 小時期間內的自行車需求。

## <a name="prerequisites"></a>必要條件
* Azure Machine Learning Workbench 必須安裝在本機上。 如需詳細資訊，請遵循[安裝快速入門](quickstart-installation.md)。
* 熟悉在 Workbench 中建立新專案。

## <a name="data-acquisition"></a>資料擷取
本教學課程使用來自 [NOAA](http://www.noaa.gov/)的[波士頓 Hubway 資料集](https://s3.amazonaws.com/hubway-data/index.html)與波士頓天氣資料。

1. 從下列連結將資料檔案下載至本機的開發環境中。 
   * [波士頓天氣資料](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)。 
   * 來自 Hubway 網站的 Hubway 路線資料。

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. 解壓縮下載後的每個 .zip 檔案。

## <a name="learn-about-the-datasets"></a>深入了解資料集
1. __波士頓天氣__檔案包含下列天氣相關欄位，每小時進行回報：
   * 日期
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. __Hubway__ 資料會根據年份和月份整理成檔案。 例如，名稱為 `201501-hubway-tripdata.zip` 的檔案包含內有 2015 年 1 月資料的 .csv 檔案。 資料包含下列欄位，每個資料列代表一個自行車路線：

   * 路線持續時間 (以秒為單位)
   * 開始時間與日期
   * 停止時間與日期
   * 起點車站名稱與識別碼
   * 終點車站名稱與識別碼
   * 自行車識別碼
   * 使用者類型 (一般 = 24 小時或 72 小時單次使用的使用者；會員 = 年費或月費會員)
   * 郵遞區號 (如果使用者是會員)
   * 性別 (由會員自行呈報)

## <a name="create-a-new-project"></a>建立新專案
1. 從您的開始功能表或啟動程式啟動 **Azure Machine Learning Workbench**。

2. 建立新的 Azure Machine Learning 專案。  按一下 [專案] 頁面上的 **+** 按鈕，或按一下 [檔案] > [新增]。
   - 使用**空白專案**範本。
   - 將您的專案命名為 **BikeShare**。 

## <a id="newdatasource"></a>建立新的資料來源

1. 建立新的資料來源。 按一下左側工具列上的 [資料] 按鈕 (磁柱圖示)。 這會顯示**資料檢視**。

   ![資料檢視索引標籤的映像](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. 新增資料來源。 選取 **+** 圖示，然後選取 [新增資料來源]。

   ![「新增資料來源」項目的映像](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>新增天氣資料

1. **資料存放區**：選取包含資料的資料存放區。 由於您正在使用檔案，請選取 [檔案/目錄]。 選取 [下一步] 以繼續操作。

   ![「檔案/目錄」項目的映像](media/tutorial-bikeshare-dataprep/datasources.png)

2. **檔案選擇**：新增天氣資料。 瀏覽並選取您稍早下載的 `BostonWeather.csv` 檔案。 按一下 [下一步] 。

   ![包含所選取 BostonWeater.csv 之檔案選取的映像](media/tutorial-bikeshare-dataprep/pickweatherdatafile.png)

3. **檔案詳細資料**：確認偵測到的檔案結構描述。 Azure Machine Learning Workbench 會分析檔案中的資料，並推測要使用的結構描述。

   ![檔案詳細資料的映像](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > 在某些情況下，Workbench 無法偵測到正確的結構描述。 請務必驗證資料集的參數是否正確。 如需天氣資料，請確認這些參數已設為下列值：
   >
   > * __檔案類型__：分隔的檔案 (csv、tsv、txt 等等)
   > * __分隔符號__：逗號 [,]
   > * __註解行字元__：未設定任何值。
   > * __跳過行模式__：請勿跳過
   > * __檔案編碼__：utf-8
   > * __升階標頭模式__：使用第一個檔案的標頭

   資料的預覽應該顯示下列資料行：
   * **路徑**
   * **DATE**
   * **REPORTTYPE**
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

   若要繼續，請選取 [下一步]。

4. **資料類型**：檢閱自動偵測到的資料類型。 Azure Machine Learning Workbench 會分析檔案中的資料，並推測要使用的資料類型。

   針對此資料，將所有資料行的 `DATA TYPE` 變更為 `String`。

   > [!NOTE]
   > `String` 用於反白顯示本教學課程稍後介紹的 Workbench 功能。 

   ![資料類型的映像](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   若要繼續，請選取 [下一步]。 

5. **取樣**：若要建立取樣配置，請選取 [+ 新增] 按鈕。 選取新增的__前 10000 個__資料列，然後選取 [編輯]。 將__取樣策略__設為**完整檔案**，然後選取 [套用]。

   ![新增取樣策略的映像](media/tutorial-bikeshare-dataprep/weatherdatasampling.png)

   若要使用__完整檔案__策略，請選取 [完整檔案] 項目，然後選取 [設定為使用中]。 __完整檔案__旁會出現星號，表示它是使用中的策略。

   ![完整檔案作為使用中策略的映像](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   若要繼續，請選取 [下一步]。

6. **路徑資料行**：[路徑資料行] 區段可讓您包含完整檔案路徑作為匯入資料中的資料行。 選取 [Do Not Include Path Column]\(不包含路徑資料行\)。

   > [!TIP]
   > 如果您要匯入具有不同檔案名稱的許多檔案資料夾，包含路徑作為資料行是有用的。 如果檔案名稱包含您稍後要解壓縮的資訊，這也會相當有用。

   ![將路徑資料行設為不包含的映像](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **完成**：若要完成建立資料來源，請選取 [完成] 按鈕。

    名稱為 __BostonWeather__ 的新資料來源索引標籤隨即開啟。 資料取樣會顯示在格線檢視中。 此樣本是根據稍早指定的使用中取樣配置。

    請注意，畫面右側的 [步驟] 窗格會顯示建立此資料來源時所採取的個別動作。

   ![顯示資料來源、取樣和步驟的映像](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>檢視資料來源計量

選取索引標籤格線檢視左上方的 [計量] 按鈕。 此檢視顯示取樣資料的分佈和其他彙總統計資料。

![計量顯示的映像](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> 若要設定統計資料的可見性，請使用 [選擇計量] 下拉式功能表。 勾選和取消勾選計量，以變更格線檢視。

若要返回__資料檢視__，請從頁面左上方選取 [資料]。

## <a name="add-data-source-to-data-preparation-package"></a>將資料來源新增至資料準備封裝

1. 選取 [準備] 以開始準備資料。 

2. 出現提示時，輸入資料準備封裝的名稱，例如 `BikeShare Data Prep`。 

3. 選取 [確定]  以繼續操作。

   ![準備對話方塊的映像](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. 名稱為 **BikeShare Data Prep** 的新封裝隨即出現，位置在 [資料] 索引標籤的 [資料準備] 區段下方。 

   若要顯示封裝，請選取此項目。 

5. 選取 **>>** 按鈕，以展開顯示封裝中包含的__資料流程__。 在此範例中，__BostonWeather__ 是唯一的資料流程。

   > [!IMPORTANT]
   > 一個封裝可以包含多個資料流程。

   ![封裝中包含之資料流程的映像](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>依值篩選資料
1. 若要篩選資料，請用滑鼠右鍵按一下具有特定值的資料格，並選取 [篩選]，然後選取篩選類型。

2. 對於此教學課程，請選取包含值 `FM-15` 的資料格，然後將篩選的篩選條件設為**等於**。  現在資料已篩選為只傳回 __REPORTTYPE__ 為 `FM-15` 的資料列。

   ![篩選對話方塊的映像](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM 15 是一種航空例行天氣報告 (METAR)。 FM 15 報告的觀察經驗是最完整的，幾乎不會缺少數據。

## <a name="remove-a-column"></a>移除資料行

您不再需要 __REPORTTYPE__ 資料行。 以滑鼠右鍵按一下資料行標頭，然後選取 [移除資料行]。

   ![移除資料行選項的映像](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>變更資料類型，並移除錯誤
1. 按下 __Ctrl (在 Mac 上為 Command ⌘)__ 鍵，同時選取資料行標頭，可讓您一次選取多個資料行。 使用此選項以選取下列資料行標頭：
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

2. **以滑鼠右鍵按一下**其中一個選取的資料行標頭，並選取 [將欄位類型轉換成數值]。 這會將資料行的資料類型轉換成數值。

   ![將多個資料行轉換成數值](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. 篩選掉錯誤值。 某些資料行具有資料類型轉換問題。 此問會在資料行的__資料品質列__中，以紅色標示。

   若要移除帶有錯誤的資料列，請用滑鼠右鍵按一下 **HOURLYDRYBULBTEMPF** 資料行標題。 選取 [篩選資料行]。 使用預設的 [我想要] 作為 [保留資料列]。 變更 [條件] 下拉式功能表，以選取 [不是錯誤]。 選取 [確定] 以套用篩選條件。

4. 若要排除其他資料行中剩餘的錯誤資料列，請針對 **HOURLYRelativeHumidity** 和 **HOURLYWindSpeed** 資料行重覆此篩選程序。

## <a name="use-by-example-transformations"></a>使用實例化轉換

若要使用兩小時時間區塊的預測資料，您必須計算兩小時期間的平均氣象條件。 若要這麼做，您可以使用下列動作：

* 將 **DATE** 資料行分割成 **Date** 和 **Time** 資料行。 如需詳細步驟，請參閱下一節。

* 從 **Time** 資料行衍生 **Hour_Range** 資料行。 如需詳細步驟，請參閱以下章節。

* 從 **DATE** 和 **Hour_Range** 資料行衍生 **Date\_Hour\_Range** 資料行。 如需詳細步驟，請參閱以下章節。

### <a name="split-column-by-example"></a>分割資料行實例化

1. 將 **DATE** 資料行分割成分割成 date 和 time 資料行。 以滑鼠右鍵按一下 **DATE** 資料行標頭，並選取 [分割資料行實例化]。

   ![依分割資料行實例化項目的映像](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Azure Machine Learning Workbench 會自動識別有意義的分隔符號，並藉由將資料分割成日期和時間值，以建立兩個資料行。 

3. 選取 [確定] 以接受分割作業結果。

   ![分割 DATE_1 和 DATE_2 資料行的映像](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>衍生資料行實例化

1. 若要衍生兩小時範圍，請以滑鼠右鍵按一下 __DATE\_2__ 資料行標頭，然後選取 [衍生資料行實例化]。

   ![衍生資料行實例化項目的映像](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   新的空白資料行隨即新增 Null 值。

2. 按一下新資料行中第一個空白資料格。 在新資料行中輸入 `12AM-2AM`，然後按 Enter，以提供所需時間範圍的範例。

   ![新資料行包含 12AM-2AM 值的映像](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Azure ML Workbench 會根據您所提供的範例合成程式，並在其餘資料列上套用相同的程式。 所有其他資料列會根據您提供的範例自動填入。 Workbench 也會分析您的資料，並嘗試找出邊緣案例。 

   > [!IMPORTANT]
   > 識別邊緣可能無法在 Mac 的目前版本 Workbench 上運作。 在 Mac 上略過以下的__步驟 3__和__步驟 4__。 而是按下__確定__之後，所有資料列都會填入衍生值。
   
3. 格線上方的文字**分析資料**表示 Workbench 正嘗試偵測的邊緣案例。 完成時，狀態會變更為**檢閱下一個建議的資料列**或**沒有建議**。 在此範例中，會傳回**檢閱下一個建議的資料列**。

4. 若要檢閱建議的變更，請選取 [檢閱下一個建議的資料列]。 顯示畫面上會醒目提示您應該檢閱並 (視需要) 更正的資料列。

   ![檢閱資料列的映像](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    選取 [確定] 以接受轉換。
 
5. 您會返回 __BostonWeather__ 的資料格線檢視。 格線現在包含先前新增的三個資料行。

   ![格線檢視包含新增資料列的映像](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  您所做的所有變更都會都保存在 [步驟] 窗格上。 請移至您在 [步驟] 窗格中建立的步驟，按一下向下箭頭，然後選取 [編輯]。 隨即顯示 [衍生資料行實例化] 的進階視窗。 您的所有範例會保留在這裡。 您也可以按兩下下方格線中的資料列，以手動新增範例。 選取 [取消] 以返回主要格線，而不套用變更。 您也可以在執行**衍生資料行實例化**轉換時選取 [進階模式]，以存取此檢視。

6. 若要重新命名資料行，請按兩下資料行標頭，然後輸入 **Hour Range**。 按 **Enter** 以儲存變更。

   ![重新命名資料行](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. 若要衍生日期和小時範圍，請複選 **Date\_1** 和 **Hour Range** 資料行，按一下滑鼠右鍵，然後選取 [衍生資料行實例化]。

   ![衍生資料行實例化](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   對照第一個資料列輸入 `Jan 01, 2015 12AM-2AM` 作為範例，並按下 **Enter**。

   Workbench 會根據您提供的範例決定轉換。 在此範例中，結果是改變該日期格式，並與兩小時範圍串聯。

   ![範例 `Jan 01, 2015 12AM-2AM 的映像](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > 在 Mac 上，請遵循下列步驟，而不是以下的__步驟 8__。
   >
   > * 移至包含 `Feb 01, 2015 12AM-2AM` 的第一個資料格。 它應該是__資料列 15__。 更正 `Jan 02, 2015 12AM-2AM` 的值，然後按 __Enter__ 鍵。 
   

8. 等待狀態從**分析資料**變更至**檢閱下一個建議的資料列**。 這可能需要數秒鐘的時間。 選取狀態連結，以巡覽至建議的資料列。 

   ![待檢閱建議資料列的映像](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   資料列有 Null 值，因為來源日期值可能是 dd/mm/yyyy 或 mm/dd/yyyy。 請輸入正確值 `Jan 13, 2015 2AM-4AM`，並按 **Enter**。 Workbench 使用這兩個範例來改善其餘資料列的衍生。

   ![正確格式化資料的映像](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. 選取 [確定] 以接受轉換。

   ![已完成轉換格線的映像](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > 您可以按一下 [步驟] 窗格，以針對此步驟使用**衍生資料行實例化**的進階模式。 在資料格線中，資料行名稱 **DATE\_1** 和 **Hour Range** 資料行旁有核取方塊。 取消勾選 **Hour Range** 資料行旁的核取方塊，以查看此動作如何改變輸出。 如果輸入項目中沒有 **Hour Range** 資料行，則會將 **12AM-2AM** 視為常數，並附加至衍生的值。 選取 [取消] 以返回主要格線，而不套用變更。

10. 若要重新命名資料行，請按兩下標頭。 將名稱變更為 **Date Hour Range**，然後按 **Enter**。

11. 複選 **DATE**、**DATE\_1**、**DATE\_2**，以及 **Hour Range** 資料行。 按一下滑鼠右鍵，然後選取 [移除資料行]。

## <a name="summarize-data-mean"></a>總結資料 (平均值)

下一步是採用根據小時範圍分組的平均值，總結天氣條件。

1. 選取 **Date Hour Range** 資料行，然後從 [轉換] 功能表選取 [總結]。

    ![轉換功能表](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. 若要總結資料，您可以將資料行從頁面底部拖曳至頂端的左邊和右邊窗格。 左窗格包含文字**拖曳資料行到這裡以分組資料**。 右窗格包含文字**拖曳資料行到這裡以總結資料**。 

    將 **Date Hour Range** 資料行從底部格線拖曳至左窗格。 拖曳 **HOURLYDRYBULBTEMPF**、**HOURLYRelativeHumidity** 和 **HOURLYWindSpeed** 至右窗格。 

    在右窗格中，選取 [平均值] 作為每個資料行的**彙總**量值。 按一下 [確定] 以完成總結。

   ![總結資料畫面的映像](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>使用指令碼轉換資料流程

將數值資料行中的資料變更為 0-1 的範圍，可快速地聚合某些模型。 目前沒有任何內建的轉換，以一般方式執行此轉換，但 Python 指令碼可以用來執行這項作業。

1. 從 [轉換] 功能表，選取 [Transform Dataflow]\(轉換資料流程\)。

2. 在出現的文字方塊中輸入下列程式碼。 如果您已使用該資料行名稱，程式碼應該不會修改而直接運作。 您使用 Python 撰寫簡單的最小值-最大值正規化邏輯。

    > [!WARNING]
    > 該指令碼預計使用本教學課程中先前使用的資料行名稱。 如果您有不同的資料行名稱，則必須在指令碼中變更名稱。

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > Python 指令碼必須在結尾傳回 `df`。 此值用來填入格線。
    
    ![轉換資料流指令碼對話方塊](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. 選取 [確定] 以使用指令碼。 格線中的數值資料行目前包含 0-1 範圍中的值。

    ![包含 0 和 1 之間值的格線](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

您已完成準備天氣資料。 接下來，準備路線資料。

## <a name="load-trip-data"></a>載入路線資料

1. 若要匯入 `201701-hubway-tripdata.csv` 檔案，請使用[建立新的資料來源](#newdatasource)一節中的步驟。 在匯入程序期間，使用下列選項：

    * __取樣配置__：**完整檔案**取樣配置，讓此樣本處於使用中狀態，以及 
    * __資料類型__：接受預設值。

2. 匯入資料之後，選取 [準備] 按鈕，開始準備資料。 選取現有的 **BikeShare Data Prep.dprep** 封裝，然後選取 [確定]。

    此程序會將**資料流程**新增至現有的**資料準備**檔案，而不是建立一個新檔案。

    ![選取現有封裝的映像](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. 載入格線中之後，展開 __DATAFLOWS__。 現在有兩個資料流程：**BostonWeather** 和 **201701-hubway-tripdata**。 選取 **201701-hubway-tripdata** 項目。

    ![201701-hubway-tripdata 項目的映像](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>使用地圖偵測器

對於資料準備，有一些實用的視覺效果稱為**偵測器**，可用於字串、數值與地理資料，有助於方便了解，並找出極端值。 利用下列步驟以使用地圖偵測器：

1. 複選 **start station latitude** 和 **start station longitude** 資料行。 以滑鼠右鍵按一下其中一個資料行，然後選取 [地圖]。

    > [!TIP]
    > 若要啟用複選，請按住 __Ctrl (在 Mac 上為 Command ⌘)__ 鍵，並選取每個資料行的標頭。

    ![地圖視覺效果的映像](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. 若要最大化地圖視覺效果，請選取**最大化**圖示。 若要將地圖大小調整為符合視窗，請選取視覺效果左上方的 **E** 圖示。

    ![最大化映像](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. 按一下 [最小化] 按鈕，以返回格線檢視。

## <a name="use-column-statistics-inspector"></a>使用資料行統計資料偵測器

若要使用資料行統計資料偵測器，以滑鼠右鍵按一下 __tripduration__ 資料行，然後選取 [資料行統計資料]。

![資料行統計資料項目](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

此程序會在 [偵測器] 窗格中，新增標題為 __tripduration 統計資料__的新視覺效果。

![tripduration 統計資料偵測器的映像](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> 路線持續時間的最大值是 **961,814 分鐘**，大約兩年。 資料集中似乎有一些極端值。

## <a name="use-histogram-inspector"></a>使用長條圖偵測器

若要嘗試識別極端值，以滑鼠右鍵按一下 __tripduration__ 資料行，然後選取 [長條圖]。

![長條圖偵測器](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

長條圖並沒有幫助，因為極端值會扭曲圖形。

## <a name="add-column-using-script"></a>使用指令碼新增資料行

1. 以滑鼠右鍵按一下 **tripduration** 資料行，然後選取 [新增資料行 (指令碼)]。

    ![新增資料行 (指令碼) 功能表的映像](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. 在 [新增資料行 (指令碼)] 對話方塊中，使用下列值：

    * __新增資料行名稱__：logtripduration
    * __將此新資料行插入在下列項目之後__：tripduration
    * __新增資料行程式碼__：`math.log(row.tripduration)`
    * __程式碼區塊類型__：運算式

   ![[新增資料行 (指令碼)] 對話方塊](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. 選取 [確定] 以新增 **logtripduration** 資料行。

4. 以滑鼠右鍵按一下資料行，然後選取 [長條圖]。

    ![logtripduration 資料行的長條圖](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  在視覺上，這個長條圖看起來是常態分佈，卻有一個異常的尾端。

## <a name="use-advanced-filter"></a>使用進階篩選

在資料上使用篩選條件可以使用新的分佈來更新偵測器。 以滑鼠右鍵按一下 **logtripduration** 資料行，然後選取 [篩選資料行]。 在 [編輯] 對話方塊中，使用下列值：

* __篩選此數字資料行__：logtripduration
* __我想要__：保留資料列
* __時機__：任何下列條件為 True (邏輯 OR)
* __如果此資料行__：小於
* __值__：9

![篩選選項](media/tutorial-bikeshare-dataprep/loftripfilter.png)

選取 [確定] 以套用篩選條件。

![套用篩選之後已更新的長條圖](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>光暈效果

1. 最大化 **logtripduration** 長條圖。 在灰色長條圖上有一個藍色長條圖。 此顯示稱為**光暈效果**：

    * **灰色長條圖**表示作業前的分佈 (在此情況下為篩選作業)。
    * **藍色長條圖**表示作業後的長條圖。 

   光暈效果有助於視覺化資料作業的效果。

   ![光暈效果映像](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > 請注意，相較於上一個，藍色直方圖顯示較短。 這是因為自動儲存新範圍內的資料所致。

2. 若要移除光暈效果，請選取 [編輯]，並取消勾選 [顯示光暈]。

    ![長條圖的選項](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. 選取 [確定] 以停用光暈效果，然後最小化長條圖。

### <a name="remove-columns"></a>移除資料行

在路線資料中，每個資料列代表自行車取用事件。 對於此教學課程，您只需要 **starttime** 和 **start station** 資料行。 複選這兩個資料行，以滑鼠右鍵按一下資料行標頭，然後選取 [保留資料行]，以移除其他資料行。 即可移除其他資料行。

![保留資料行選項的映像](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>總結資料 (計數)

若要總結 2 小時期間內的自行車需求，請使用衍生的資料行。

1. 以滑鼠右鍵按一下 **starttime** 資料行，並選取 [衍生資料行實例化]

    ![衍生實例化選項的映像](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. 例如，為第一個資料列輸入值 `Jan 01, 2017 12AM-2AM`。

    > [!IMPORTANT]
    > 在先前衍生資料行的範例中，您已使用多個步驟來衍生包含日期和時間週期的資料行。 在此範例中，您可以看到可藉由提供最終輸出的範例，以單一步驟執行該作業。

    > [!NOTE]
    > 您可以對任何資料列提供範例。 對於此範例，`Jan 01, 2017 12AM-2AM` 值適用於第一個資料列。

    ![範例資料的映像](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > 在 Mac 上，請遵循下列步驟，而不是以下的__步驟 3__。
   >
   > * 移至包含 `Jan 01, 2017 1AM-2AM` 的第一個資料格。 它應該是__資料列 14__。 更正 `Jan 01, 2017 12AM-2AM` 的值，然後按 __Enter__ 鍵。 

3. 等待應用程式計算所有資料列的值。 這可能需要數秒鐘的時間。 分析完成後，使用__檢閱下一個建議的資料列__連結，以檢閱資料。

   ![使用檢閱連結完成分析的映像](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    請確定計算值正確無誤。 如果不正確，請使用預期的值更新值，然後按 Enter。 然後等待分析完成。 完成**檢閱下一個建議的資料列**程序，直到您看見**沒有建議**為止。 看見**沒有建議**之後，表示應用程式已檢閱過邊緣案例，並且滿意合成程式。 最佳做法是先對轉換資料進行視覺檢查，再接受轉換。 

4. 選取 [確定] 以接受轉換。 將新建立的資料行重新命名為 **Date Hour Range**。

    ![重新命名資料行的映像](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. 以滑鼠右鍵按一下 **starttime** 資料行標頭，並選取 [移除資料行]。

6. 若要總結資料，請從 [轉換] 功能表選取 [總結]。 若要建立轉換，請執行下列動作：

    * 將 __Date Hour Range__ 和 __start station id__ 拖曳至左側 (分組依據) 窗格。
    * 將 __start station id__ 拖曳至右側 (總結資料) 窗格。

   ![總結選項的映像](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. 選取 [確定] 以接受總結結果。

## <a name="join-dataflows"></a>聯結資料流程

若要將天氣資料與路線資料聯結，請使用下列步驟：

1. 從 [轉換] 功能表選取 [聯結]。

2. __資料表__：選取 **BostonWeather** 作為左側資料流程，並選取 **201701-hubway-tripdata** 作為右側資料流程。 若要繼續，請選取 [下一步]。

    ![資料表選取項目的映像](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __索引鍵資料行__：在兩個資料表中選取 **Date Hour Range** 資料行，然後選取 [下一步]。

    ![在索引鍵資料行上聯結的映像](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __聯結類型__：選取 [相符資料列] 作為聯結類型，然後選取 [完成]。

    ![相符資料列聯結類型](media/tutorial-bikeshare-dataprep/joinscreen.png)

    此程序會建立名稱為__聯結結果__的新資料流程。

## <a name="create-additional-features"></a>建立其他功能

1. 若要建立包含星期幾的資料行，請以滑鼠右鍵按一下 **Date Hour Range** 資料行，然後選取 [衍生資料行實例化]。 對於星期日發生的日期，使用值 __Sun__。 例如，**Jan 01, 2017 12AM-2AM**。 按下 **Enter**，然後按一下 [確定]。 將此資料行重新命名為 __Weekday__。

    ![建立包含星期幾之新資料行的映像](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. 若要建立包含某資料列之時間期間的資料行，請以滑鼠右鍵按一下 **Date Hour Range** 資料行，然後選取 [衍生資料行實例化]。 針對包含 **Jan 01, 2017 12AM-2AM** 的資料列使用值 **12AM-2AM**。 按下 **Enter**，然後按一下 [確定]。 將此資料行重新命名為 **Period**。

    ![期間資料行的映像](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. 若要移除 **Date Hour Range** 和 **rDate Hour Range** 資料行，請按下 **Ctrl (在 Mac 上為 Command ⌘)** 鍵，並選取每個資料行標頭。 按一下滑鼠右鍵，然後選取 [移除資料行]。

## <a name="read-data-from-python"></a>從 Python 讀取資料

您可以從 Python 或 PySpark 執行資料準備封裝，並擷取結果作為**資料框架**。

若要產生的範例 Python 指令碼，以滑鼠右鍵按一下 __BikeShare 資料準備__，並選取 [產生資料存取碼檔案]。 範例 Python 檔案會建立在您的**專案資料夾**中，而且也會載入 Workbench 內的索引標籤。 下列 Python 指令碼是產生之程式碼的範例：

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

對於此教學課程，檔案名稱為 `BikeShare Data Prep.py`。 稍後會在本教學課程中使用此檔案。

## <a name="save-test-data-as-a-csv-file"></a>將測試資料儲存為 CSV 檔案

若要將**聯結結果**資料流程儲存為 .CSV 檔案，您必須變更 `BikeShare Data Prep.py` 指令碼。 使用下列程式碼更新 Python 指令碼：

```python
from azureml.dataprep.package import run

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTest.csv
df.to_csv('Your Test Data File Path here')
```

從畫面頂端選取 [執行]。 指令碼已在本機電腦上提交為**作業**。 當作業狀態變更為__已完成__之後，檔案已寫入指定的位置。

## <a name="substitute-data-sources"></a>取代資料來源

在先前步驟中，您已經使用 `201701-hubway-tripdata.csv` 和 `BostonWeather.csv` 資料來源以準備測試資料。 若要使用包含其他路線資料檔案的封裝，請使用下列步驟：

1. 使用稍早提供的下列步驟，建立新的**資料來源**，且程序有下列改變：

    * __檔案選取__：選取檔案時，複選剩餘的六個路線 tripdata .CSV 檔案。

        ![載入六個剩餘檔案](media/tutorial-bikeshare-dataprep/selectsixfiles.png)

        > [!NOTE]
        > __+ 5__ 項目表示除了列出的檔案以外，還有五個附加檔案。

    * __檔案詳細資料__：將__升級標頭模式__設為**所有檔案擁有相同標頭**。 這個值表示每個檔案包含相同的標頭。

        ![檔案詳細資料選取項目](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   請儲存此資料來源的名稱，因為會在後續步驟中使用。

2. 選取資料夾圖示，以檢視專案中的檔案。 展開 __aml\_config__ 目錄，然後選取 `local.runconfig` 檔案。

    ![local.runconfig 位置的映像](media/tutorial-bikeshare-dataprep/localrunconfig.png) 

3. 在 `local.runconfig` 檔案的結尾新增下列行，然後選取磁碟圖示以儲存檔案。

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    此變更將原始資料來源取代為包含六個路線資料檔案的資料。

## <a name="save-training-data-as-a-csv-file"></a>將定型資料儲存為 CSV 檔案

巡覽至您稍早編輯的 Python 檔案 `BikeShare Data Prep.py`，並提供不同的檔案路徑以儲存定型資料。

```python
from azureml.dataprep.package import run
# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTrain.csv
df.to_csv('Your Training Data File Path here')
```

若要提交新的作業，請使用頁面頂端的**執行**圖示。 使用新的設定提交**作業**。 此作業的輸出即為定型資料。 這項資料是使用您稍早建立的相同資料準備步驟所建立。 完成這項作業可能需要幾分鐘的時間。

## <a name="next-steps"></a>後續步驟
您已完成「自行車共享資料準備」教學課程。 在本教學課程中，您使用 Azure Machine Learning 服務 (預覽) 了解如何：
> [!div class="checklist"]
> * 使用 Azure Machine Learning 資料準備工具，以互動方式準備資料
> * 匯入、轉換及建立測試資料集
> * 產生資料準備封裝
> * 使用 Python 執行資料準備封裝
> * 針對其他輸入檔案重複使用資料準備封裝以產生訓練資料集

接下來，深入了解資料準備：
> [!div class="nextstepaction"]
> [資料準備試用者指南](data-prep-user-guide.md)
