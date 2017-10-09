---
title: "如何在 Azure Machine Learning Workbench 中使用模型管理資料收集功能 | Microsoft Docs"
description: "本文件說明如何在 Azure Machine Learning Workbench 中使用模型管理資料收集功能"
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e751e2f0ec812de43a03749e04ff165fa62ec649
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="how-to-collect-model-data-using-data-collection"></a>如何使用資料收集來收集模型資料

模型資料收集功能可讓您保存模型輸入以及來自 Machine Learning Web 服務的預測。 本文件說明以下層面的模型資料收集：

- 如何安裝資料收集封裝
- 如何使用資料收集
- 如何檢視和取用收集的資料

## <a name="how-to-install-the-data-collection-package"></a>如何安裝資料收集封裝
資料收集程式庫可以原生方式安裝於 Linux 和 Windows 上。

### <a name="windows"></a>Windows
在 Windows 上，可以使用下列命令來安裝資料收集器模組：

    pip install azureml.datacollector

### <a name="linux"></a>Linux
在 Linux 上，必須先安裝 libxml++ 程式庫。 執行下列命令，此命令必須在 sudo 下方發出：

    sudo apt-get install libxml++2.6-2v5

接著發出以下命令：

    pip install azureml.datacollector
## <a name="how-to-use-data-collection"></a>如何使用資料收集

若要使用模型資料收集，您需要對計分檔進行下列變更：

1. 在檔案開頭處新增下列程式碼
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. 將下列程式碼行新增至 `init()` 函式。
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. 將下列程式碼行新增至 `run(input_df)` 函式。
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    請確定變數 `input_df` 和 `pred` (來自 `model.predict()` 的預測值) 會先初始化，然後您才能在其上呼叫 `collect()` 函式。

4. 使用 `az ml service create realtime` 命令搭配 `--collect-model-data true` 參數，以建立即時 Web 服務。 這確保會在服務執行時收集模型資料。

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. 若要測試資料收集，請執行 `az ml service run realtime`。

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="how-to-view-and-consume-the-collected-data"></a>如何檢視和取用收集的資料
檢視 Blob 儲存體中收集的資料：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [更多服務]。
3. 在搜尋方塊中，輸入 `Storage accounts` 並按 **Enter** 鍵。
4. 從 [儲存體帳戶] 搜尋刀鋒視窗中，選取 [儲存體帳戶] 資源。 若要判斷您的儲存體帳戶，請使用下列步驟：

    a. 移至 Azure ML Workbench、選取您正在處理的專案，然後從 [檔案] 功能表開啟命令提示字元
    
    b. 輸入 `az ml env show -v`，並檢查 *storage_account* 值。 這是您儲存體帳戶的名稱

5. 按一下 [資源] 刀鋒視窗功能表中的 [容器]，然後按一下名為 **modeldata** 的容器。 您可能需要在第一個 Web 服務要求之後最多等候 10 分鐘，才能看到資料開始傳播至儲存體帳戶。 資料會透過下列容器路徑流入 Blob：

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

您可以使用 Microsoft 軟體和開放原始碼工具，利用各種不同方式從 Azure Blob 取用資料。 這裡有一些範例：
 - Azure ML Workbench：將 csv 檔案新增為資料來源，藉以在 Azure ML Workbench 中開啟 csv 檔案
 - Excel：開啟每日的 csv 檔案作為試算表
 - [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/) \(英文\)：使用從 Blob 中之 csv 資料提取的資料建立圖表
 - [Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-overview)：使用大部分的 csv 資料建立資料框架
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started)：將 csv 資料載入至 Hive 資料表，並直接針對 Blob 執行 SQL 查詢


