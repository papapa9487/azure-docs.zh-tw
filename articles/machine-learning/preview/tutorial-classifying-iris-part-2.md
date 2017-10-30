---
title: "建置 Azure Machine Learning 服務 (預覽) 的模型 | Microsoft Docs"
description: "這個完整的教學課程會示範如何使用 Azure Machine Learning 服務 (預覽) 端對端。 這屬於測試的第 2 部分。"
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
ms.openlocfilehash: 5d86f3bdf19603d2f92fc1a704376beefd7323c0
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2017
---
# <a name="classifying-iris-part-2-build-a-model"></a>分類鳶尾花第 2 部分：建置模型
Azure Machine Learning 服務 (預覽) 是一套整合的端對端資料科學以及進階分析解決方案，可供專業資料科學家用來以雲端規模準備資料、開發測試以及部署模型。

本教學課程是三部分系列的第二部分。 隨著本教學課程，您可以使用 Azure Machine Learning 服務 (預覽) 了解如何：

> [!div class="checklist"]
> * 使用 Azure Machine Learning Workbench
> * 開啟指令碼並檢閱程式碼
> * 在本機環境中執行指令碼
> * 檢閱執行歷程記錄
> * 在本機 Docker 環境中執行指令碼
> * 在本機 Azure CLI 視窗中執行指令碼
> * 在遠端 Docker 環境中執行指令碼
> * 在雲端 HDInsight 環境中執行指令碼

為易於說明，本教學課程使用不受時間影響的[鳶尾花資料集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 螢幕擷取畫面是 Windows 專屬，但是 macOS 體驗幾乎完全相同。

## <a name="prerequisites"></a>必要條件
您應該完成本教學課程系列的第一個部分。 在開始本教學課程中的步驟之前，請遵循[資料準備教學課程](tutorial-classifying-iris-part-1.md)來建立 Azure Machine Learning 資源，並安裝 Azure Machine Learning Workbench 應用程式。

(選擇性) 您可以試驗針對本機 Docker 容器來執行指令碼。 若要這樣做，您必須在 Windows 或 macOS 機器的本機上，安裝並啟動 Docker 引擎 (Community Edition 就已足夠)。 深入了解 [Docker 安裝指示](https://docs.docker.com/engine/installation/)。

如果您想要試驗分派指令碼以在遠端 Azure VM 的 Docker 容器中執行或在 HDInsight Spark 叢集中執行，您可以依照[建立 Ubuntu 型 Azure 資料科學虛擬機器或 HDI 叢集的指示](how-to-create-dsvm-hdi.md)來進行。

## <a name="review-irissklearnpy-and-configuration-files"></a>檢閱 iris_sklearn.py 和組態檔
1. 啟動 **Azure Machine Learning Workbench** 應用程式，並開啟您在教學課程系列的前一個部分中所建立的 **myIris** 專案。

2. 開啟專案後，按一下 Azure Machine Learning Workbench 左邊工具列上的 [檔案] 按鈕 (資料夾圖示)，在您的專案資料夾中開啟檔案清單。

3. 選取 **iris_sklearn.py** 檔案，Python 程式碼會隨即會在 Workbench 內新的文字編輯器索引標籤內開啟。

   ![開啟檔案](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >因為此範例專案會經常更新，您看到的程式碼可能不會與上述程式碼完全相同。

4. 檢閱 Python 指令碼，以熟悉程式碼撰寫樣式。 請注意，指令碼會執行下列作業：

   - 載入資料準備封裝 **iris.dprep** 以建立 [pandas 資料框架](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)。 

        >[!NOTE]
        >我們使用範例專案所隨附的 `iris.dprep` 資料準備封裝，它應該是您在本教學課程的第 1 部分建置的 `iris-1.dprep` 檔案相同。

   - 新增隨機功能，讓問題更難解決。 (隨機性是必要的，因為鳶尾花是可以以接近 100% 的精確度輕鬆地分類的小型資料集。)

   - 使用 [scikit-learn](http://scikit-learn.org/stable/index.html) 機器學習程式庫來建置簡單的羅吉斯迴歸模型。 

   - 使用 [pickle](https://docs.python.org/2/library/pickle.html) 程式庫將模型序列化為 `outputs` 資料夾中的檔案，然後將其載入，並將它取消序列化回記憶體

   - 使用已還原序列化的模型來對新記錄進行預測。 

   - 使用 [matplotlib](https://matplotlib.org/) 程式庫繪製兩個圖表 -- 混淆矩陣和多類別 ROC 曲線，並將其儲存在 `outputs` 資料夾中。

   - 會全面使用 `run_logger` 物件來將正則化速率和模型精確度記錄到記錄檔，而記錄檔會自動在執行歷程記錄內繪製。


## <a name="execute-irissklearnpy-script-in-local-environment"></a>在本機環境中執行 iris_sklearn.py 指令碼

讓我們來準備第一次執行 **iris_sklearn.py** 指令碼。 此指令碼需要 scikit-learn和 matplotlib 封裝。 **scikit-learn** 已由 Azure ML Workbench 安裝。 不過，我們需要安裝 **matplotlib**。 

1. 在 Azure Machine Learning Workbench 中，按一下 [檔案] 功能表，然後選擇 [開啟命令提示字元] 來啟動命令提示字元。 我們將這個命令列介面視窗稱為 Azure Machine Learning Workbench CLI 視窗中或簡稱 CLI 視窗。

2. 在 CLI 視窗中，輸入下列命令來安裝 **matplotlib** Python 封裝。 它應該會在一分鐘內完成。

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >如果您略過上述的 `pip install` 命令，`iris_sklearn.py` 中的程式碼的確成功執行，但它不會產生混淆矩陣輸出，並且會繪製多類別 ROC 曲線，如歷程記錄視覺效果中所示。

3. 返回 Workbench 應用程式視窗。 

4. 在 **iris_sklearn.py** 索引標籤的左上方，於儲存圖示旁按一下下拉式清單來選擇 [回合組態] 選取項目。  選擇 **local** 作為執行環境，以及 `iris_sklearn.py` 為要執行的指令碼。

5. 接下來，移至相同索引標籤的右邊，以 `0.01` 的值填入 [引數] 欄位。 

   ![影像](media/tutorial-classifying-iris/run_control.png)

6. 按一下 [執行] 按鈕。 立即會排定作業。 作業會列在 Workbench 視窗右邊的 [作業] 面板。 

7. 在幾分鐘之後，作業的狀態會從**提交中**轉換為**執行中**，並且之後很快變成**已完成**。

   ![執行 sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. 按一下 [作業] 面板的作業狀態文字中的字組**已完成**。 快顯視窗隨即會開啟，並顯示執行中指令碼的標準輸出 (stdout) 文字。 若要關閉 stdout 文字，請按一下快顯功能表上右上方的 **X** 按鈕。

9. 在 [作業] 面板的相同作業狀態中，按一下 [已完成] 狀態和開始時間正上方的藍色文字 **iris_sklearn.py [n]** (_n_ 是執行編號)。 [執行屬性] 頁面隨即開啟，並顯示來自該特定執行的 [執行屬性] 資訊、**輸出**檔案、任何**視覺效果**和**記錄**。 

   執行完成時，快顯視窗會顯示下列結果：

   >[!NOTE]
   >因為我們稍早已對定型集引入了一些隨機化項目，確切的結果可能所不同。

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details page.
   ```

10. 關閉 [執行屬性] 索引標籤，然後返回 **iris_sklearn.py** 索引標籤。 

11. 重複額外的執行。 

    在範圍從 `0.001` 至 `10` 的 [引數] 欄位中輸入一系列中的不同數值。 按一下 [執行] 來執行程式碼數次。 您每次變更的引數值會在程式碼中傳送至羅吉斯迴歸演算法，每次產生不同的結果。

## <a name="review-run-history-in-detail"></a>檢閱詳細的執行歷程記錄
在 Azure Machine Learning Workbench 中，每個指令碼執行都會以執行歷程記錄的形式擷取。 您可以開啟 [執行] 檢視來檢視特定指令碼的執行歷程記錄。

1. 按一下左邊工具列上的 [執行] 按鈕 (時鐘圖示)，以開啟 [執行] 的清單。 然後按一下 **iris_sklearn.py** 以顯示 `iris_sklearn.py` 的 [執行儀表板]。

   ![影像](media/tutorial-classifying-iris/run_view.png)

2. [執行儀表板] 索引標籤隨即開啟。 檢閱跨多個執行擷取的統計資料。 圖形會在索引標籤上方轉譯，並在頁面底部的資料表中列出每個編號執行的詳細資料。

   ![影像](media/tutorial-classifying-iris/run_dashboard.png)

3. 篩選資料表，然後按一下圖表，以互動方式來檢視每個執行的狀態、持續時間、精確度，以及正則化速率。 

4. 在 [執行] 資料表中選取兩個或三個執行，然後按一下 [比較] 按鈕來開啟詳細的比較頁面。 檢閱並排比較。 按一下比較頁面右上方的 [執行清單] 返回按鈕，以返回 [執行儀表板]。

5. 按一下個別執行以查看執行詳細資料檢視。 注意所選取執行的統計資料會列在 [執行屬性] 區段中。 寫入到輸出資料夾的檔案會列於 [輸出] 區段，而且可以下載。

   ![影像](media/tutorial-classifying-iris/run_details.png)

   兩個繪圖，混淆矩陣和多類別 ROC 曲線，會在**視覺效果**區段中轉譯。 所有記錄檔也都可在 **Logs** 區段找到。

## <a name="execute-scripts-in-the-local-docker-environment"></a>在本機 Docker 環境中執行指令碼

Azure ML 可讓您輕鬆地設定額外的執行環境，例如 Docker，並在這些環境中執行指令碼。 

>[!IMPORTANT]
>若要完成此步驟，您必須在本機上安裝並啟動 Docker 引擎。 如需詳細資料，請參閱安裝指南。

1. 在左邊工具列上，選取資料夾圖示，即可開啟您的專案的 [檔案] 清單。 展開 `aml_config` 資料夾。 

2. 請注意，有預先設定的數個環境，例如 **docker-python**、**docker-spark** 和 **local**。 

   每個環境有兩個檔案，例如 `docker-python.compute` 和 `docker-python.runconfig`。 開啟每一種檔案，您會注意到，某些選項可以在文字編輯器中設定。  

   關閉 (X) 任何所開啟文字編輯器的索引標籤來進行清理。

3. 使用 **docker-python** 環境執行 **iris_sklearn.py** 指令碼。 

   - 在左邊工具列中，按一下時鐘圖示以開啟 [執行] 面板。 按一下 [全部執行]。 
   - 在 [所有執行] 索引標籤的最上層，選擇 **docker-python** 作為目標環境，而非預設的 **local**。 
   - 接下來，移到右邊，選擇 **iris_sklearn.py** 作為要執行的指令碼。 
   - 將 [引數] 欄位保留空白，因為指令碼會指定預設值。 
   - 按一下 [執行] 按鈕。

4. 觀察新作業會啟動，如 Workbench 視窗右邊的 [作業] 面板中所示。

   當您第一次對 Docker 執行時，需要多幾分鐘的時間才能完成。 

   在幕後，Azure Machine Learning Workbench 會建置新的 Docker 檔案，該檔案參考 `docker.compute` 檔案中指定的Docker 基礎映像，以及 `conda_dependencies.yml` 檔案中指定的相依性 Python 封裝。 然後，Docker 引擎會從 Azure 下載基礎映像、安裝 `conda_dependencies.yml` 檔案中指定的 Python 封裝，然後啟動 Docker 容器。 然後它會複製 (或參考，取決於回合組態) 專案資料夾的本機副本，然後執行 `iris_sklearn.py` 指令碼。 最後，您應該會看到與目標為 **local** 時完全相同的結果。

5. 現在讓我們來試試 Spark。 Docker 基礎映像包含預先安裝和設定的 Spark 執行個體。 因為這個緣故，您可以在其中執行 PySpark 指令碼。 這是開發和測試您的 Spark 程式的簡單方式，而不需自己花費時間安裝和設定 Spark。 

   開啟 `iris_pyspark.py` 檔案。 此指令碼會載入 `iris.csv` 資料檔案，並使用來自 Spark ML 程式庫的羅吉斯迴歸演算法，以分類鳶尾花資料集。 現在將執行環境變更為 **docker-spark**，以及將指令碼變更為 **iris_pyspark.py**，然後再次執行。 因為必須在 Docker 容器內建立並啟動 Spark 工作階段，可能需要較長的時間。 您也可以看到 stdout 與 `iris_pyspark.py` 的 stdout 不同。

6. 多進行幾次執行，並使用不同的引數播放。 

7. 開啟 `iris_pyspark.py` 檔案來查看使用 Spark ML 程式庫建置的簡單羅吉斯迴歸模型。 

8. 與 [作業] 面板互動、執行歷程記錄清單檢視，以及執行跨不同執行環境執行的詳細資料檢視。

## <a name="execute-scripts-in-the-azure-ml-cli-window"></a>在 Azure ML CLI 視窗中執行指令碼

1. 使用 Azure Machine Learning Workbench，透過按一下 [檔案] 功能表，然後按一下 [開啟命令提示字元] 來啟動命令列視窗。 您的命令提示字元會從專案資料夾的提示字元 `C:\Temp\myIris\>` 開始。

   >[!Important]
   >您必須使用命令列視窗 (從 Workbench 啟動)，來完成下列步驟：

2. 使用命令提示字元 (CLI) 來登入 Azure。 

   對 Azure 資源進行驗證時，Workbench 應用程式和 CLI 會使用獨立的認證快取。 您只需要執行這項操作一次，直到快取的權杖過期為止。 **az account list** 命令會傳回可供您的登入使用的訂用帳戶。 如果有一個以上訂用帳戶，請使用來自所需訂用帳戶的識別碼值，並將其設定為要搭配 **az set account -s** 命令使用的預設帳戶，提供訂用帳戶識別碼值。 然後確認使用帳戶的設定顯示命令。

   ```azurecli
   REM login using aka.ms/devicelogin site.
   az login
   
   REM list all Azure subscriptions you have access to. 
   az account list -o table
   
   REM set the current Azure subscription to the one you want to use.
   az set account -s <subscriptionId>
   
   REM verify your current subscription is set correctly
   az account show
   ```

3. 一旦您通過驗證，並且目前的 Azure 訂用帳戶內容已設定好，請在 CLI 視窗中輸入下列命令來安裝 matplotlib，並將 python 指令碼提交作為執行的測試。

   ```azurecli
   REM You don't need to do this if you have installed matplotlib locally from the previous steps.
   pip install matplotlib
   
   REM Kick off an execution of the iris_sklearn.py file against local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. 檢閱輸出。 請注意，此輸出和結果與您先前在本教學課程中使用 Workbench 執行指令碼所執行的相同。 

5. 如果您已在您的電腦上安裝 Docker，請使用 Docker 執行環境執行相同的指令碼。

   ```azurecli
   REM Execute iris_sklearn.py in local Docker container Python environment.
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM Execute iris_pyspark.py in local Docker container Spark environment.
   az ml experiment submit -c docker-spark .\iris_pyspark.py 0.1
   ```
6. 在 Azure Machine Learning Workbench 中，按一下左邊工具列上的 [資料夾] 圖示來列出專案檔案，然後開啟名為 **run.py** 的 Python 指令碼。 

   此指令碼對各種正則化速率進行重複，以及使用這些速率執行多次測試很實用。 此指令碼會以 `10.0` (非常大的數字) 的正則化速率啟動 `iris_sklearn.py` 作業，並在下一個執行中將速率縮減一半，依此類推，直到速率小於 `0.005` 為止。 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   若要啟動 **run.py** 指令碼，請從命令列執行下列命令：

   ```cmd
   REM Submit iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   當 `run.py` 完成時，您會在 Azure Machine Learning Workbench 的執行歷程記錄清單檢視中看到圖表。

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>在遠端電腦上的 Docker 容器中執行
若要在遠端 Linux 電腦上的 Docker 容器中執行指令碼，您需要具備該遠端電腦的 SSH 存取 (使用者名稱和密碼)。 而且該遠端電腦必須安裝並執行 Docker 引擎。 取得這類 Linux 機器的最簡單方式是在 Azure 上建立[以 Ubuntu 為基礎的資料科學虛擬機器 (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)。 (請注意，不支援以 CentOS 為基礎的 DSVM。) 

1. 一旦建立 VM，您可以使用下列命令來產生一組 `.runconfig` 和 `.compute` 檔案，將 VM 附加為執行環境。 讓我們將新的環境命名為 `myvm`。
 
   ```azurecli
   REM create myvm compute target
   az ml computetarget attach --name myvm --address <IP address> --username <username> --password <password> --type remotedocker
   ```
   
   >[!NOTE]
   >IP 位址區域也可以是可公開定址的 FQDN (完整網域名稱)，例如 `vm-name.southcentralus.cloudapp.azure.com`。 將 FQDN 新增到您的 DSVM 並在該處使用它 (而非使用 IP 位址) 是良好的作法，因為您可能會在某個時間點關閉 VM 以節省成本。 此外，下次您啟動 VM 時，IP 位址可能已變更。

   接下來，執行下列命令在 VM 中建構 Docker 映像，以準備好執行指令碼。
   
   ```azurecli
   REM prepare the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >您也可以將 `myvm.runconfig` 中 `PrepareEnvironment` 的值從預設的 `false` 變更為 `true`。 這將會自動準備第一次執行 Docker 容器。

2. 編輯在 `aml_config` 下產生的 `myvm.runconfig` 檔案，並將架構從預設的 `PySpark` 變更為 `Python`：

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >將架構設定保留為 PySpark 應該也可以正常運作。 但是，如果實際上不需要 Spark 工作階段來執行 Python 指令碼，則效率會稍微不佳。

3. 發出如同您先前在 CLI 視窗中的相同命令，但這次我們的目標是 _myvm_：
   ```azurecli
   REM execute iris_sklearn.py in remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   命令會執行正如同您使用的是 `docker-python` 環境，不同之處在於執行是發生在遠端 Linux VM 上。 CLI 視窗會顯示相同的輸出資訊。

4. 讓我們在容器中試試 Spark。 開啟檔案總管 (如果您熟悉基本的檔案操作命令，則也可以從 CLI 視窗執行此動作)。 建立一份 `myvm.runconfig` 檔案並將其命名為 `myvm-spark.runconfig`。 編輯新檔案，將 `Framework` 設定從 `Python` 變更為 `PySpark`：
   ```yaml
   "Framework": "PySpark"
   ```
   不要對 `myvm.compute` 檔案進行任何變更。 會將相同 VM 上的相同 Docker 映像用於 Spark 執行。 在新 `myvy-spark.runconfig` 中，`target` 欄位會透過其名稱 `myvm` 指向相同的 `myvm.compute` 檔案。

5. 在以下輸入要在遠端 Docker 容器的 Spark 執行個體中執行的命令：
   ```azureli
   REM execute iris_pyspark.py in Spark instance on remote Docker container
   az ml experiment submit -c myvm-spark .\iris_pyspark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>在 HDInsight 叢集中執行指令碼
您也可以在實際的 Spark 叢集中執行此指令碼。 

1. 如果您有適用於 Azure HDInsight 的 Spark 叢集的存取權，請如下所示產生 HDI 回合組態命令。 提供 HDInsight 叢集名稱、您的 HDInsight 使用者名稱和密碼作為參數。 使用下列命令：

   ```azurecli
   REM create a compute target that points to a HDI cluster
   az ml computetarget attach --name myhdi --address <cluster head node FQDN> --username <username> --password <password> --type cluster

   REM prepare the HDI cluster
   az ml experiment prepare -c myhdi
   ```

   叢集前端節點的完整網域名稱 (FQDN) 通常是 `<cluster_name>-ssh.azurehdinsight.net`。

   >[!NOTE]
   >`username` 是叢集 SSH 使用者名稱。 如果您沒有在 HDI 佈建期間變更它，預設值是 `sshuser`。 它不是 `admin`，它是其他使用者在佈建期間建立，用來啟用對叢集管理網站的存取。 

2. 執行下列命令，指令碼即會在 HDInsight 叢集中執行：

   ```azurecli
   REM execute iris_pyspark on the HDI cluster
   az ml experiment submit -c myhdi .\iris_pyspark.py
   ```

   >[!NOTE]
   >針對遠端 HDI 叢集執行時，您也可以在 `https://<cluster_name>.azurehdinsight.net/yarnui` 使用 `admin` 使用者帳戶檢視 YARN 作業執行的詳細資料。


## <a name="next-steps"></a>後續步驟
在三部分教學課程系列的第二部分中，您已經學會如何使用 Azure Machine Learning 服務來：
> [!div class="checklist"]
> * 使用 Azure Machine Learning Workbench
> * 開啟指令碼並檢閱程式碼
> * 在本機環境中執行指令碼
> * 檢閱執行歷程記錄
> * 在本機 Docker 環境中執行指令碼
> * 在本機 Azure CLI 視窗中執行指令碼
> * 在遠端 Docker 環境中執行指令碼
> * 在雲端 HDInsight 環境中執行指令碼

您已準備好進行系列中的第三個部分。 既然我們已經建立羅吉斯迴歸模型，讓我們將它部署為即時 Web 服務。

> [!div class="nextstepaction"]
> [部署模型](tutorial-classifying-iris-part-3.md)
