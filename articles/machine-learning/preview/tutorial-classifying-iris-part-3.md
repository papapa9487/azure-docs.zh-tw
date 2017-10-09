---
title: "部署 Azure Machine Learning 服務 (預覽) 的模型 | Microsoft Docs"
description: "這個完整的教學課程會示範如何使用 Azure Machine Learning 服務 (預覽) 端對端。 這是部署模型的第 3 部分。"
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 0dfcc965d96949527b3e80285061bff320872621
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-3-deploy-a-model"></a>分類鳶尾花第 3 部分：部署模型
Azure Machine Learning 服務 (預覽) 是一套整合的端對端資料科學以及進階分析解決方案，可供專業資料科學家用來以雲端規模準備資料、開發測試以及部署模型。

本教學課程是三部分系列的第三部分。 隨著本教學課程，您可以使用 Azure Machine Learning 服務 (預覽) 了解如何：

> [!div class="checklist"]
> * 找出模型檔案
> * 產生評分指令碼和結構描述檔案
> * 準備環境
> * 建立即時 Web 服務
> * 執行即時 Web 服務
> * 檢查輸出 Blob 資料 

 為易於說明，本教學課程使用不受時間影響的[鳶尾花資料集](https://en.wikipedia.org/wiki/iris_flower_data_set)。 螢幕擷取畫面是 Windows 專屬，但是 macOS 體驗幾乎完全相同。

## <a name="prerequisites"></a>必要條件
完成本教學課程系列的前兩個部分：
- 首先，請遵循[資料準備教學課程](tutorial-classifying-iris-part-1.md)來建立 Azure Machine Learning 資源，並安裝 Azure Machine Learning Workbench 應用程式。
- 其次，請遵循[建置模型教學課程](tutorial-classifying-iris-part-2.md)，在 Azure Machine Learning 中建置羅吉斯迴歸模型。

## <a name="download-the-model-pickle-file"></a>下載模型序列化檔案
在教學課程的上一個部分中，`iris_sklearn.py` 指令碼是在 Azure Machine Learning Workbench 本機執行。 該動作會使用受歡迎的 Python 物件序列化封裝 **[pickle](https://docs.python.org/2/library/pickle.html)** 來序列化羅吉斯迴歸模型。 

1. 啟動 **Azure Machine Learning Workbench** 應用程式，並開啟您在教學課程系列的前一個部分中所建立的 **myIris** 專案。

2. 開啟專案後，按一下 Azure Machine Learning Workbench 左邊工具列上的 [檔案] 按鈕 (資料夾圖示)，在您的專案資料夾中開啟檔案清單。

3. 選取 **iris_sklearn.py** 檔案，Python 程式碼會隨即會在 Workbench 內新的文字編輯器索引標籤內開啟。

4. 檢閱 **iris_sklearn.py** 檔案來查看序列化檔案產生的位置。 使用 Ctrl+F 來開啟 [尋找] 對話方塊，然後在 python 程式碼中尋找字組 **pickle**。

   這個程式碼片段會示範序列化輸出檔案產生的方式。 請注意，磁碟上的輸出序列化檔案名為 `model.pkl`。 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. 在先前執行的輸出檔案中，找出模型序列化檔案。
   
   執行 **iris_sklearn.py** 指令碼時，模型檔案已使用名稱 `model.pkl` 寫入 `outputs` 資料夾。 此資料夾位於您選擇執行指令碼的執行環境中，而不是在您的本機專案資料夾中。 
   
   - 若要找出檔案，請使用 Azure Machine Learning Workbench 應用程式，然後按一下左邊工具列上的 [執行] 按鈕 (時鐘圖示)，以開啟 [所有執行] 的清單。  
   - [所有執行] 索引標籤隨即開啟。 在執行的資料表中，選取最近執行的其中一個，其目標為 **local** 且指令碼名稱為 **iris_sklearn.py**。 
   - [執行屬性] 頁面隨即開啟。 在頁面的右上角，注意 [輸出] 區段。 
   - 選取 **model.pkl** 檔案旁的核取方塊，然後按一下 [下載] 按鈕來下載序列化檔案。 請將它儲存到您的專案資料夾的根目錄。 後續步驟中需要它。

   ![下載序列化檔案](media/tutorial-classifying-iris/download_model.png)

   在[如何讀取及寫入大型資料檔案](how-to-read-write-files.md)文章中深入了解 `outputs` 資料夾。

## <a name="get-scoring-and-schema-files"></a>取得評分和結構描述檔案
若要部署 Web 服務以及模型檔案，您也需要評分指令碼和用於 Web 服務輸入資料的選擇性結構描述。 評分指令碼會從目前資料夾載入 `model.pkl` 檔案，並使用它來產生新預測的鳶尾花類別。  

1. 啟動 **Azure Machine Learning Workbench** 應用程式，並開啟您在教學課程系列的前一個部分中所建立的 **myIris** 專案。

2. 開啟專案後，按一下 Azure Machine Learning Workbench 左邊工具列上的 [檔案] 按鈕 (資料夾圖示)，在您的專案資料夾中開啟檔案清單。

3. 選取 **iris_score.py** 檔案。 Python 指令碼隨即開啟。 這個檔案會用作評分檔案。

4. 若要取得結構描述檔案，請執行指令碼。 選擇命令列中的 **local** 環境和 **iris_score.py** 指令碼，然後按一下 [執行] 按鈕。 

5. 此指令碼會在 **outputs** 資料夾中建立 JSON 檔案，它會擷取模型所需的輸入資料結構描述。

   ![評分檔案](media/tutorial-classifying-iris/model_data_collection.png)

6. 在 Machine Learning Workbench 視窗右邊的 [作業] 窗格中，等候最新的 **iris_score.py** 作業顯示綠色的**已完成**狀態為止。 然後按一下最新作業執行的超連結 **iris_score.py [1]**，以查看來自 **iris_score.py** 執行的執行詳細資料。 

7. 在 [執行屬性] 頁面上，於 [輸出] 區段中，選取新建立的 **service_schema.json** 檔案。 將檔案儲存到您的專案根資料夾。

8. 返回您開啟指令碼 **iris_score.py** 的索引標籤。 

   注意可讓您從 Web 服務擷取模型輸入和預測的資料收集使用量。 下列各點是資料收集特別有趣的方面。

9. 請在包含模型資料收集功能的 ModelDataCollector 頂端檢閱檔案匯入類別的程式碼：

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. 在具現化 ModelDataCollector 的 `init()` 函式中檢閱下列幾行程式碼：

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. 在收集和預測資料的 `run(input_df)` 函式中檢閱下列幾行程式碼：

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

現在您已準備好要準備讓您的環境對模型作業化。

## <a name="prepare-to-operationalize-locally"></a>準備在本機作業化
使用_本機模式_部署，於本機電腦上的 Docker 容器中執行。

您可以對開發和測試使用_本機模式_。 Docker 引擎必須在本機執行，才能完成將模型作業化的下列步驟。 您可以在命令的結尾使用 `-h` 旗標來取得命令說明。

>[!NOTE]
>如果您的本機沒有 Docker 引擎，您仍可以透過在 Azure 中建立叢集用於部署繼續進行。 只是請務必在本教學課程之後刪除該叢集，使得您不會產生持續性的費用。

1. 在 Azure Machine Learning Workbench 中開啟命令列介面，於 [檔案] 功能表中按一下 [開啟命令提示字元]。

   命令提示字元會在目前的專案資料夾位置 `c:\temp\myIris>` 中開啟。

2. 請確定已在訂用帳戶中註冊 Azure 資源提供者 `Microsoft.ContainerRegistry`。 在步驟 3 中建立環境之前，您必須先登錄這個資源提供者。 您可以使用下列命令來查看是否已登錄：
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   您應該會看到如下的輸出： 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   如果 `Microsoft.ContainerRegistry` 未登錄，您可以使用下列命令登錄它：
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   註冊可能需要幾分鐘的時間，您可以使用上述 `az provider list` 命令或下列命令來檢查其狀態：
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

3. 建立環境。 每個環境 (例如開發或生產環境)，需要執行一次此步驟。對此第一個環境使用_本機模式_。 (您可以在下列命令中嘗試 `-c` 或 `--cluster` 參數，稍後在_叢集模式_中設定環境。)

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   請遵循螢幕上的指示來佈建用於儲存 Docker 映像的儲存體帳戶、用於列出 Docker 映像的 ACR (Azure Container Registry)，以及用於收集遙測的 AppInsight 帳戶。 如果您使用 `-c` 參數，它也會建立 ACS (Azure Container Service) 叢集。
   
   叢集名稱是可讓您識別環境的一個方式，而且位置應該與您從 Azure 入口網站建立之模型管理帳戶的位置相同。

4. 建立模型管理帳戶 (這是一次性的設定)  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. 設定模型管理帳戶  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. 設定環境。
安裝程式完成之後，使用下列命令設定作業化所需的環境變數。 環境名稱是在上述的步驟 1 中使用的名稱。 資源群組名稱是相同程序的輸出，且在設定程序完成時會在命令視窗中。
   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

   若要確認您已針對本機 Web 服務部署正確設定您的作業化環境，請輸入下列命令：

   ```azurecli
   az ml env show
   ```

現在您已準備好建立即時 Web 服務。

## <a name="create-a-real-time-web-service"></a>建立即時 Web 服務
使用下列命令建立即時 Web 服務：

   ```azurecli
   az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   這會產生您可以在稍後使用的 Web 服務識別碼。

   以下是 `az ml service create realtime` 命令的參數：
   * -n：應用程式名稱，必須全部是小寫。
   * -f：評分指令碼檔案名稱
   * --model-file：模型檔案，在此情況下，它是序列化的 model.pkl 檔案
   * -r：模型的類型，在此情況下它是 python 模型
   * --collect-model-data true：啟用資料收集

   >[!IMPORTANT]
   >服務名稱 (這也是新的 Docker 映像名稱) 必須全為小寫，否則您會收到錯誤。 

   執行命令時，模型及評分檔案會上傳到在以上的環境設定時所建立的儲存體帳戶。 部署程序會建置 Docker 映像，其中含有您的模型、結構描述、評分檔案，並將其推送至 ACR 登錄：`<ACR_name>.azureacr.io/<imagename>:<version>`。 然後它會提取該映像到您的電腦本機，並且會啟動以該映像為基礎的 Docker 容器。 (如果您的環境是在叢集模式中設定，Docker 容器會改為部署到 ACS Kubernete 叢集。)

   作為部署的一部分，會在您的本機電腦上建立 Web 服務適用的 HTTP REST 端點。 幾分鐘後命令應該會完成，並出現成功訊息，而您的 Web 服務即已備妥可供使用！

   您可以使用 `docker ps` 命令查看執行中的 Docker 容器：
   ```azurecli
   docker ps
   ```
### <a name="alternative-route"></a>替代路由
作為以上顯示 `az ml service create realtime` 命令的替代方式，您也可以個別執行模型註冊、資訊清單產生、Docker 映像建置和 Web 服務建置步驟。 這可在每個步驟中提供您更大的彈性，而且您可以重複使用從先前步驟中產生的實體，並只在需要時重建實體。 請遵循下列指示來查看如何達成：

1. 藉由提供序列化檔案名稱來登錄模型。

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   這會產生模型識別碼。

2. 建立資訊清單

   若要建立資訊清單，請使用此命令並提供來自前一個步驟的模型識別碼輸出：

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f iris_score.py -r python -i <model ID> -s service_schema.json
   ```
   這會產生資訊清單識別碼。

3. 建立 Docker 映像

   若要建立 Docker 映像，請使用此命令並提供來自前一個步驟的資訊清單識別碼值輸出：

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   這會產生 Docker 映像識別碼。
   
4. 建立服務

   若要建立服務，請使用所列出的命令並提供來自前一個步驟的映像識別碼輸出：

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   這會產生 Web 服務識別碼。

您現在可以開始執行 Web 服務。

## <a name="run-the-real-time-web-service"></a>執行即時 Web 服務

透過將包含四個隨機數字陣列、以 JSON 編碼的記錄饋送給它來測試執行 `irisapp` Web 服務。

1. Web 服務建立包含範例資料。 在本機模式中執行時，您可以呼叫 `az ml service show realtime` 命令來取出您可用來測試服務的範例執行命令。 這也會提供您評分 URL，讓您用來在您自己的自訂應用程式中將服務納入：

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. 若要測試服務，請執行傳回的服務執行命令。

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}
   ```
   輸出是 `"2"`，這是預測的類別。 (您的結果可能不同。) 

3. 如果您想要從 CLI 外部執行服務，您需要取得用於驗證的金鑰：

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>檢視 Azure Blob 儲存體中收集的資料：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 找出您的儲存體帳戶。 若要這麼做，請按一下 [更多服務]

3. 在 [搜尋] 方塊中，輸入**儲存體帳戶**，然後按 **Enter**

4. 從 [儲存體帳戶] 搜尋頁面，選取符合您的環境的**儲存體帳戶**資源。 

   > [!TIP]
   > 若要判斷要使用的儲存體帳戶：請開啟 Azure Machine Learning Workbench，選取您正在處理的專案，然後從 [檔案] 功能表開啟命令提示字元。在命令提示字元中，輸入 `az ml env show -v` 並檢查 *storage_account* 的值。 這是您儲存體帳戶的名稱

5. 一旦 [儲存體帳戶] 頁面開啟，請按一下左邊的清單中的 [容器] 項目。 找出名為 **modeldata** 的容器。 
 
   如果您沒有看見任何資料，您可能需要在第一個 Web 服務要求之後最多等候 10 分鐘，才能看到資料開始傳播至儲存體帳戶。

   資料會透過下列容器路徑流入 Blob：

   `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

6. 您可以使用 Microsoft 軟體和開放原始碼工具，以各種不同方式使用來自 Azure Blob 的此資料。 

   取用輸出 Blob 的範例方法：
   - Azure ML Workbench：將 csv 檔案新增為資料來源，藉以在 Azure ML Workbench 中開啟 csv 檔案。 
   - Excel：開啟每日的 csv 檔案作為試算表。
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)：使用從 Blob 中之 csv 資料提取的資料建立圖表。
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview)：使用大部分的 csv 資料建立資料框架。

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started)：將 csv 資料載入至 Hive 資料表，並直接針對 Blob 執行 SQL 查詢。

## <a name="next-steps"></a>後續步驟
在三部分教學課程系列的第三部分中，您已經學會如何使用 Azure Machine Learning 服務來：
> [!div class="checklist"]
> * 找出模型檔案
> * 產生評分指令碼和結構描述檔案
> * 準備環境
> * 建立即時 Web 服務
> * 執行即時 Web 服務
> * 檢查輸出 Blob 資料 

您已成功在各種運算環境中執行定型指令碼、建立模型、將模型序列化，以及透過以 Docker 為基礎的 Web 服務將模型作業化。 

您已準備好要進行進階資料準備：
> [!div class="nextstepaction"]
> [進階資料準備](tutorial-bikeshare-dataprep.md)


