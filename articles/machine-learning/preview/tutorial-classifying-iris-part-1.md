---
title: "在 Machine Learning 服務 (預覽) 中針對分類鳶尾花教學課程準備資料 | Microsoft Docs"
description: "這個完整的教學課程會示範如何使用 Azure Machine Learning 服務 (預覽) 端對端。 這是資料準備的第 1 部分。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/28/2017
ms.openlocfilehash: 975a86c1f9d9692f6eadd232177f33cbbbeeff2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-1-prepare-data"></a>分類鳶尾花第 1 部分：準備資料
Azure Machine Learning 服務 (預覽) 是一套整合的端對端資料科學以及進階分析解決方案，可供專業資料科學家用來以雲端規模準備資料、開發測試以及部署模型。

本教學課程是三部分系列的第一部分。 在本教學課程，讓我們逐步說明 Azure Machine Learning 服務 (預覽) 的基本概念。 在本教學課程中，您將了解如何：
> [!div class="checklist"]
> * 在 Azure Machine Learning Workbench 中建立專案
> * 建立資料準備封裝
> * 產生 Python/PySpark 程式碼來叫用資料準備封裝

為易於說明，本教學課程使用不受時間影響的[鳶尾花資料集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 螢幕擷取畫面是 Windows 專屬，但是 macOS 體驗幾乎完全相同。

## <a name="prerequisites"></a>必要條件
- 建立 Azure Machine Learning 測試帳戶
- 安裝 Azure Machine Learning Workbench

您可以遵循[安裝和建立快速入門](quickstart-installation.md)來安裝 Azure Machine Learning Workbench 應用程式。 該安裝也包含命令列介面 (CLI)。

## <a name="create-a-new-project-in-azure-ml-workbench"></a>在 Azure ML Workbench 中建立新專案
1. 啟動 Azure Machine Learning Workbench 應用程式，並登入 (如果需要)。 在 [專案] 窗格中，按一下 **+** 圖示來建立 [新專案]。

   ![新增工作區](media/tutorial-classifying-iris/new_ws.png)

2. 填入 [建立新專案] 詳細資料。 

   ![New Project](media/tutorial-classifying-iris/new_project.png)

   - 以專案的名稱填寫 [專案名稱] 欄位。 例如，使用值 **myIris**。
   - 選擇要在其中建立專案的 [專案目錄]。 例如，使用值 **C:\Temp**。 
   - 輸入**專案描述**。 
   - [Git 儲存機制] 欄位是選擇性的，可以保留空白。 您可以在 VSTS 上提供現有的空白 Git 儲存機制 (沒有主要分支的儲存機制)。 這樣做可讓您稍後啟用漫遊及共用案例。 如需詳細資訊，請參閱[使用 Git 儲存機制](using-git-ml-project.md)文章。 
   - 選擇 [工作區]，例如，本教學課程使用的是 **IrisGarden**。 
   - 從專案範本清單選取 [分類鳶尾花] 範本。 

3. 按一下 [建立] 按鈕以建立專案。 現在已為您建立並開啟專案。

## <a name="create-a-data-preparation-package"></a>建立資料準備封裝
1. 從 [檔案檢視] 開啟 **iris.csv** 檔案。 檔案是簡單的資料表，具有 5 個資料行和 150 個資料列。 它有四個數值功能資料行和一個字串目標資料行。 它沒有資料行標題。

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > 不建議在您的專案資料夾中包含資料檔案，特別是當檔案大小很大時。 我們在此範本中包含 **iris.csv** 以供示範，因為它很小。 如需詳細資訊，請參閱[如何讀取及寫入大型資料檔案](how-to-read-write-files.md)文章。

2. 在 [資料檢視]，按一下 **+** 圖示來新增新的資料來源。 [新增資料來源] 精靈隨即啟動。 

   ![資料檢視](media/tutorial-classifying-iris/data_view.png)

3. 完成資料準備精靈。 
   - 在第一個畫面上，選取 [檔案/目錄] 選項，然後按 [下一步]。
   - 在第二個畫面中，選擇 **iris.csv** 本機檔案，例如 "C:\Temp\myIris\iris.csv"。
   - 第三個螢幕上的 [檔案詳細資料]，接受預設值。
   - 第四個螢幕上 [資料類型]，將 Column1 到 _Column4_的 [資料類型] 從 [字串] 變更為 [數值]，因為這些資料行是數值。 
   - 在第五個和第六個畫面上，接受預設設定。
   - 按一下 [完成] 按鈕。

   ![選取鳶尾花](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >針對此練習，請確定您從目前的專案目錄內選取 **iris.csv** 檔案，否則後續步驟可能會失敗。 

4. 新檔案 **iris-1.dsource** 隨即建立。 因為範例專案中隨附未編號的 **iris.dsource** 檔案，會將檔案加上虛線唯一地命名。  

   檔案會開啟，並且顯示資料。 從 **Column1** 至 **Column5** 的一系列資料行標題，會自動新增到此資料集。 捲動到底部，並注意資料集的最後一個資料列是空的。 它是因為 csv 檔案中的額外分行符號。

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/iris_data_view.png)

5. 現在按一下 [計量] 按鈕。 觀察長條圖和為您針對每個資料行計算的一組完整的統計資料。 您也可以按一下 [資料] 按鈕再次查看資料。 

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/iris_metrics_view.png)

6. 按一下 [準備] 按鈕。 [準備] 對話方塊隨即顯示。 

   範例專案中隨附 **iris.dprep** 檔案，因此，依預設它會要求您在現有的 **iris.dprep** 資料準備套件中建立新的資料流程。 

   將下拉式清單中的值變更為 **+ 新增資料準備封裝**，並輸入新的值 "iris-1"，然後按一下確定。

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/new_dprep.png)

   名為 **iris-1.dprep** 的新資料準備套件隨即建立，並在資料準備編輯器中開啟。

7. 現在讓我們執行一些基本的資料準備動作。 按一下每個資料行標題，並讓標題文字可供編輯，即可重新命名資料行名稱。 

   分別針對五個資料行輸入 [萼片長度]、[萼片寬度]、[花瓣長度]、[花瓣寬度] 和 [物種]。

   ![重新命名資料行](media/tutorial-classifying-iris/rename_column.png)

8. 若要計算相異值數目，選取 [物種] 資料行，並在其上按一下滑鼠右鍵。 選擇 [值計數]。 

   ![按一下值計數](media/tutorial-classifying-iris/value_count.png)

   這個動作會開啟 [偵測器] 窗格，並顯示具有四個長條的長條圖。 請注意，目標資料行有三個相異值：**Iris_virginica**、**Iris_versicolor**、**Iris-setosa** 和 **(null)** 值。

9. 選取圖中代表 null 值的長條，以篩選出 null。 有一個資料列具有 **(null)** 值。 若要移除這個資料列，請按一下 **-** 篩選按鈕。

   ![值計數長條圖](media/tutorial-classifying-iris/filter_out.png)

10. 請注意 [步驟] 窗格中詳述的個別步驟。 在您處理資料行重新命名並篩選 null 值資料列時，每個動作都會錄製為資料準備步驟。 您可以編輯個別的步驟，以調整設定、重新排列步驟，甚至移除步驟。

   ![步驟](media/tutorial-classifying-iris/steps.png)

11. 現在，按一下名為 **iris-1** 且具有圖形圖示的索引標籤上的 **X**，即可關閉資料準備編輯器。 您的工作會自動儲存到顯示在 [資料準備] 標題之下的 **iris-1.dprep** 檔案中。

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>產生 Python/PySpark 程式碼來叫用資料準備封裝

1. 以滑鼠右鍵按一下 **iris-1.dprep** 檔案來顯示內容功能表，然後選擇 [產生資料存取碼檔案]。 

   ![產生程式碼](media/tutorial-classifying-iris/generate_code.png)

2. 名為 **iris-1.py** 的新檔案隨即開啟，其中包含下列幾行程式碼：

   ```python
   # This code snippet will load the referenced package and return a DataFrame.
   # If the code is run in a PySpark environment, the code will return a
   # Spark DataFrame. If not, the code will return a Pandas DataFrame.

   from azureml.dataprep.package import run
   df = run('iris.dprep', dataflow_idx=0)
   ```

   此程式碼片段會以資料準備封裝形式叫用您建立的邏輯。 根據執行此程式碼所在的內容，`df` 代表不同種類的資料框架。 在 Python 執行階段中執行時使用 [pandas 資料框架](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)，或在 Spark 內容中執行時使用 [Spark 資料框架](https://spark.apache.org/docs/latest/sql-programming-guide.html)。 

   如需有關如何在 Azure Machine Learning Workbench 中準備資料的詳細資訊，請參考[開始進行資料準備](data-prep-getting-started.md)指南。

## <a name="next-steps"></a>後續步驟
在三部分教學課程系列的第一部分中，您已使用 Azure Machine Learning Workbench 來：
> [!div class="checklist"]
> * 建立新專案 
> * 建立資料準備封裝
> * 產生 Python/PySpark 程式碼來叫用資料準備封裝

您已準備好進行系列中的下一個部分，建置機器學習模型。
> [!div class="nextstepaction"]
> [建置模型](tutorial-classifying-iris-part-2.md)
