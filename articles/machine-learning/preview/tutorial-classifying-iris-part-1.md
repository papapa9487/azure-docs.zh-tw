---
title: "在 Azure Machine Learning 服務 (預覽) 中針對分類鳶尾花教學課程準備資料 | Microsoft Docs"
description: "這個完整的教學課程會示範如何使用 Azure Machine Learning 服務 (預覽) 端對端。 這是第一個部分，會討論資料準備工作。"
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
ms.openlocfilehash: fcef59d38cce5f8860508ebeeda0b1b97f5e83f1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2017
---
# <a name="classify-iris-part-1-prepare-the-data"></a>分類鳶尾花第 1 部分：準備資料
Azure Machine Learning 服務 (預覽) 是一套整合的端對端資料科學以及進階分析解決方案，可供專業資料科學家用來以雲端規模準備資料、開發測試以及部署模型。

本教學課程是三部分系列的第一部分。 在本教學課程，我們會逐步說明 Azure Machine Learning 服務 (預覽) 的基本概念。 您會了解如何：
> [!div class="checklist"]
> * 在 Azure Machine Learning Workbench 中建立專案。
> * 建立資料準備套件。
> * 產生 Python/PySpark 程式碼來叫用資料準備套件。

本教學課程使用不受時間影響的[鳶尾花資料集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 螢幕擷取畫面是 Windows 專屬，但是 Mac OS 體驗幾乎完全相同。

## <a name="prerequisites"></a>必要條件
- 建立 Azure Machine Learning 測試帳戶。
- 安裝 Azure Machine Learning Workbench。

您可以遵循[安裝和建立快速入門](quickstart-installation.md)文章中的指示來安裝 Azure Machine Learning Workbench 應用程式。 此安裝也包括 Azure 跨平台命令列工具或 Azure CLI。

## <a name="create-a-new-project-in-azure-machine-learning-workbench"></a>在 Azure Machine Learning Workbench 中建立新的專案
1. 開啟 Azure Machine Learning Workbench 應用程式，並登入 (如果需要)。 在 [專案] 窗格中，選取加號 (**+**) 圖示來建立 [新專案]。

   ![新增工作區](media/tutorial-classifying-iris/new_ws.png)

2. 填入 [建立新專案] 詳細資料： 

   ![新增專案](media/tutorial-classifying-iris/new_project.png)

   - 以專案的名稱填寫 [專案名稱] 方塊。 例如，使用值 **myIris**。
   - 選取要在其中建立專案的 [專案目錄]。 例如，使用值 `C:\Temp\`。 
   - 輸入 [專案描述] 是選擇性的。 
   - [Git 存放庫] 欄位也是選擇性的，可以保留空白。 您可以在 Visual Studio Team Services 上提供現有的空白 Git 存放庫 (沒有主要分支的存放庫)。 如果您是使用已經存在的 Git 存放庫，可以稍後啟用漫遊和共用的情節。 如需詳細資訊，請參閱 [使用 Git 存放庫](using-git-ml-project.md)。 
   - 選取 [工作區]，例如，本教學課程使用的是 **IrisGarden**。 
   - 從專案範本清單選取 [分類鳶尾花] 範本。 

3. 選取 [建立] 按鈕。 現在已為您建立並開啟專案。

## <a name="create-a-data-preparation-package"></a>建立資料準備封裝
1. 從 [檔案檢視] 開啟 **iris.csv** 檔案。 檔案是具有 5 個資料行和 150 個資料列的資料表。 它有四個數值功能資料行和一個字串目標資料行。 它沒有資料行標題。

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > 請勿在您的專案資料夾中包含資料檔案，特別是當檔案大小很大時。 我們在此範本中包含 **iris.csv** 以供示範，因為它很小。 如需詳細資訊，請參閱[如何讀取及寫入大型資料檔案](how-to-read-write-files.md)。

2. 在 [資料檢視] 中，按一下加號 (**+**) 來新增新的資料來源。 [新增資料來源] 頁面隨即開啟。 

   ![資料檢視](media/tutorial-classifying-iris/data_view.png)

3. 保留預設值，然後選取 [下一步] 按鈕。  
 
   ![選取鳶尾花](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >針對此練習，請確定您從目前的專案目錄內選取 **iris.csv** 檔案。 否則，接下來的步驟可能會失敗。
   
4. 選取檔案之後，請選取 [完成] 按鈕。

4. 名為 **iris-1.dsource** 的新檔案隨即建立。 因為範例專案中隨附未編號的 **iris.dsource** 檔案，會將檔案唯一地命名為 dash-1。  

   檔案隨即開啟，並且顯示資料。 從 **Column1** 至 **Column5** 的一系列資料行標題，會自動新增到此資料集。 捲動到底部，並注意資料集的最後一個資料列是空的。 資料列是空的，因為 CSV 檔案中有額外的分行符號。

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/iris_data_view.png)

5. 選取 [計量] 按鈕。 觀察長條圖。 每個資料行會計算出一組完整的統計資料。 您也可以選取 [資料] 按鈕再次查看資料。 

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/iris_metrics_view.png)

6. 選取 [準備] 按鈕。 [準備] 對話方塊隨即開啟。 

   範例專案會隨附 **iris.dprep** 檔案。 根據預設，它會要求您在已經存在的 **iris.dprep** 資料準備套件中建立新的資料流。 

   從下拉式功能表中選取 [+ 新增資料準備套件]、輸入新的值作為套件名稱、使用 **iris-1**，然後選取 [確定]。

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/new_dprep.png)

   名為 **iris-1.dprep** 的新資料準備套件隨即建立，並在資料準備編輯器中開啟。

7. 現在讓我們執行一些基本的資料準備動作。 將資料行名稱重新命名。 選取各個資料行標頭，讓標頭文字可進行編輯。 

   分別針對五個資料行輸入 [萼片長度]、[萼片寬度]、[花瓣長度]、[花瓣寬度] 和 [物種]。

   ![將資料行重新命名](media/tutorial-classifying-iris/rename_column.png)

8. 若要計算相異值數目，選取 [物種] 資料行，然後按一下滑鼠右鍵來加以選取。 從下拉式功能表中選取 [值計數]。 

   ![選取值計數](media/tutorial-classifying-iris/value_count.png)

   這個動作會開啟 [偵測器] 窗格，並顯示具有四個長條的長條圖。 目標資料行有三個相異值：**Iris_virginica**、**Iris_versicolor**、**Iris-setosa** 和 **(null)** 值。

9. 如需篩選出 null，請選取圖中代表 null 值的長條。 有一個資料列具有 **(null)** 值。 若要移除此資料列，請選取減號 (**-**)。

   ![值計數長條圖](media/tutorial-classifying-iris/filter_out.png)

10. 請注意 [步驟] 窗格中詳述的個別步驟。 當您將資料行重新命名並篩選 null 值資料列時，每個動作都會記錄為資料準備步驟。 您可以編輯個別的步驟，以調整設定、重新排列步驟，並移除步驟。

   ![步驟](media/tutorial-classifying-iris/steps.png)

11. 關閉資料準備編輯器。 在包含圖形圖示的 **iris-1**索引標籤上，選取 [關閉] (x)。 您的工作會自動儲存到顯示在 [資料準備] 標題之下的 **iris-1.dprep** 檔案中。

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>產生 Python/PySpark 程式碼來叫用資料準備套件

1. 以滑鼠右鍵按一下 **iris-1.dprep** 檔案來顯示內容功能表，然後選取 [產生資料存取碼檔案]。 

   ![產生程式碼](media/tutorial-classifying-iris/generate_code.png)

2. 名為 **iris-1.py** 的新檔案隨即開啟，其中包含下列幾行程式碼：

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   此程式碼片段會以資料準備封裝形式叫用您建立的邏輯。 根據執行此程式碼所在的內容，`df` 代表不同種類的資料框架。 在 Python 執行階段中執行時使用 [pandas 資料框架](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)，或在 Spark 內容中執行時使用 [Spark 資料框架](https://spark.apache.org/docs/latest/sql-programming-guide.html)。 

   如需有關如何在 Azure Machine Learning Workbench 中準備資料的詳細資訊，請參閱[開始進行資料準備](data-prep-getting-started.md)指南。

## <a name="next-steps"></a>後續步驟
在三部分教學課程系列的第一部分中，您已使用 Azure Machine Learning Workbench 來：
> [!div class="checklist"]
> * 建立新專案。 
> * 建立資料準備套件。
> * 產生 Python/PySpark 程式碼來叫用資料準備套件。

您已準備好進行系列中的下一個部分，將了解如何建置 Azure Machine Learning 模型：
> [!div class="nextstepaction"]
> [建置模型](tutorial-classifying-iris-part-2.md)
