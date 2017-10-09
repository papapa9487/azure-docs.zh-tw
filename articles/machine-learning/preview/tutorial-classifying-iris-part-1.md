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
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e88b6bf74b7492353e5d5d004bde12fa4787e405
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-1-prepare-data"></a>分類鳶尾花第 1 部分：準備資料
Azure Machine Learning 服務 (預覽) 是一套整合的端對端資料科學以及進階分析解決方案，可供專業資料科學家用來以雲端規模準備資料、開發測試以及部署模型。

本教學課程是三部分系列的第一部分。 在本教學課程，讓我們逐步說明 Azure Machine Learning 服務 (預覽) 的基本概念。 在本教學課程中，您將了解如何：
> [!div class="checklist"]
> * 在 Azure Machine Learning Workbench 中建立專案
> * 建立資料準備封裝
> * 產生 Python/PySpark 程式碼來叫用資料準備封裝

為易於說明，本教學課程使用不受時間影響的[鳶尾花資料集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 螢幕擷取畫面是 Windows 專屬，但是 macOS 體驗幾乎完全相同。

## <a name="launch-azure-machine-learning-workbench"></a>啟動 Azure Machine Learning Workbench
請遵循[安裝及建立快速入門](quickstart-installation.md)以安裝 Azure Machine Learning Workbench 應用程式，其也包含命令列介面 (CLI)。 啟動 Azure Machine Learning Workbench 應用程式，並登入 (如果需要)。

## <a name="create-a-new-project"></a>建立新專案
1. 在 [專案] 窗格中，按一下 **+** 圖示來建立 [新專案]。

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
1. 從 [檔案檢視] 開啟 `iris.csv` 檔案。 檔案是簡單的資料表，具有 5 個資料行和 150 個資料列。 它有四個數值功能資料行和一個字串目標資料行。 它沒有資料行標題。

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   >附註：不建議在您的專案資料夾中包含資料檔案，特別是當檔案大小很大時。 我們在此範本中包含 `iris.csv` 供示範用途，因為它很小。 如需詳細資訊，請參閱[如何讀取及寫入大型資料檔案](how-to-read-write-files.md)文章。

2. 在 [資料檢視]，按一下 **+** 圖示來新增新的資料來源。 [新增資料來源] 精靈隨即啟動。 

   ![資料檢視](media/tutorial-classifying-iris/data_view.png)

3. 選取 [檔案/目錄] 選項，然後選擇 `iris.csv` 本機檔案。 對每個畫面接受預設設定，然後最後按一下 [完成]。 

   ![選取鳶尾花](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >針對此練習，請確定您從目前的專案目錄內選取 `iris.csv` 檔案，否則後續步驟可能會失敗。 

4. 新檔案 `iris-1.dsource` 隨即建立。 因為範例專案中隨附未編號的 `iris.dsource` 檔案，會將檔案加上虛線唯一地命名。  檔案會開啟，並且顯示資料。 從 `Column1` 至 `Column5` 的一系列資料行標題，會自動新增到此資料集。 捲動到底部，並注意資料集的最後一個資料列是空的。 它是因為 csv 檔案中的額外分行符號。

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/iris_data_view.png)

5. 現在按一下 [計量] 按鈕。 觀察長條圖和為您針對每個資料行計算的一組完整的統計資料。 您也可以按一下 [資料] 按鈕再次查看資料。 

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/iris_metrics_view.png)

6. 按一下 [度量] 旁邊的 [準備]按鈕 (或如果您在 [計量] 檢視上，則為 [資料] 按鈕)。 [準備] 對話方塊隨即顯示。 範例專案中已隨附 `iris.dprep` 檔案，因此，依預設它會要求您在現有的 **iris.dprep** 資料準備封裝中建立新的資料流程。 將下拉式清單中的值變更為 **+ 新增資料準備封裝**，並輸入新的值 "iris-1"，然後按一下 [確定]。

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/new_dprep.png)

名為 `iris-1.dprep` 的新資料準備封裝隨即建立，並在資料準備編輯器中開啟。

現在讓我們執行一些簡單的資料準備動作。 按一下每個資料行標題，並讓標題文字可供編輯，即可重新命名資料行名稱。 分別為五個資料行輸入 `Sepal Length`、`Sepal Width`、`Petal Length`、`Petal Width` 和 `Species`。

![重新命名資料行](media/tutorial-classifying-iris/rename_column.png)

選取 `Species` 資料行，並以滑鼠右鍵按一下它。 選擇 [值計數]。 

![值計數](media/tutorial-classifying-iris/value_count.png)

這個動作會建立有四個橫條的長條圖。 請注意，我們的目標資料行有三個相異值，`Iris_virginica`、`Iris_versicolor`、`Iris-setosa`。 還有一個資料列具有 `(null)` 值。 讓我們透過選取代表 Null 值的長條，並且按一下 **-** 篩選器按鈕來移除它，藉此擺脫此資料列。 

![值計數](media/tutorial-classifying-iris/filter_out.png)

在您處理資料行重新命名並篩選出 Null 值資料列時，您採取的每個動作將會在 [步驟] 窗格中錄製為資料準備步驟。 您可以編輯它們 (以調整其設定)、重新排序，或甚至移除它們。

![steps](media/tutorial-classifying-iris/steps.png)

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>產生 Python/PySpark 程式碼來叫用資料準備封裝

現在關閉 DataPrep 編輯器。 (不必擔心，它會自動儲存。)以滑鼠右鍵按一下 **iris-1.dprep** 檔案來顯示內容功能表，然後選擇 [產生資料存取碼檔案]。 

![產生程式碼](media/tutorial-classifying-iris/generate_code.png)

建立的 **iris-1.py** 檔案已預先填入下列兩行程式碼 (以及一些註解)：

```python
# This code snippet will load the referenced package and return a DataFrame.
# If the code is run in a PySpark environment, the code will return a
# Spark DataFrame. If not, the code will return a Pandas DataFrame.

from azureml.dataprep.package import run
df = run('iris.dprep', dataflow_idx=0)
```
此程式碼片段會以資料準備封裝形式叫用您建立的邏輯。 根據執行此程式碼所在的內容，如果在 Python 執行階段中執行，`df` 可以是 [pandas 資料框架](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)，或如果在 Spark 內容中執行，則可以是 [Spark 資料框架](https://spark.apache.org/docs/latest/sql-programming-guide.html)。 如需有關如何在 Azure Machine Learning Workbench 中準備資料的詳細資訊，請參考[開始進行資料準備](data-prep-getting-started.md)指南。

## <a name="next-steps"></a>後續步驟
在三部分教學課程系列的第一部分中，您已使用 Azure Machine Learning Workbench 來：
> [!div class="checklist"]
> * 建立新專案 
> * 建立資料準備封裝
> * 產生 Python/PySpark 程式碼來叫用資料準備封裝

您已準備好進行系列中的下一個部分，建置機器學習模型。
> [!div class="nextstepaction"]
> [建置模型](tutorial-classifying-iris-part-2.md)

