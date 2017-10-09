--- 
title: "預測性維護實際案例 | Microsoft Docs"
description: "使用 PySpark 的預測性維護實際案例"
services: machine-learning
author: jaymathe
ms.author: jaymathe
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7bb5672a2b9de0a62af8b6155bfdead6a0105eb5
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

--- 

# <a name="predictive-maintenance-real-world-scenario"></a>預測性維護實際案例


對所有企業來說，設備在非排程的時間停機將會造成不利影響。 因此，務必要讓現場設備保持運作，並減少代價很高的非排程停機時間，才能達到最大使用率和效能。 及早發現問題有助於以更符合成本效益的方式配置為數不多的維護資源，並增強品質和供應鏈流程。 

在此案例中，我們會使用較[大規模的資料](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)來引導使用者逐步完成主要步驟，讓他們擷取資料、進行特徵設計、建置模型，並於最後運作和部署模型。 整個程序的程式碼是以 PySpark 撰寫，並且會在 Azure ML Workbench 內使用 Jupyter 筆記本來實作。 最後，我們會使用 Azure Machine Learning 模型管理來運作最佳模型，以在生產環境用來進行即時的故障預測。   

## <a name="link-to-the-gallery-github-repository"></a>連結至資源庫 GitHub 存放庫

以下是公用 GitHub 存放庫的連結：[https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>使用案例概觀

在普遍擁有龐大固定資產的產業中，企業所面臨的主要問題在於，如果機械故障而導致生產延遲，將會讓成本大幅上揚。 企業無不極力預測這些問題會在何時發生，以便事先主動避免。 這麼做會降低成本，因為不僅停機時間減少了，而且在某些情況下還能提升安全性。 如需常見使用案例和預測性維護建模方法的詳細說明，請參閱[預測性維護腳本](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)。

此案例會利用腳本中的構想，其目的是提供步驟，讓您為綜合了多個實際企業問題的案例實作預測性模型。 此範例結合了許多在預測性維護使用案例中所觀察到的常見資料元素。

此模擬資料的企業問題是要預測元件故障所造成的問題。 因此，企業問題是「機器因為元件故障而當機的機率有多高？」 這個問題會格式化為多級分類問題 (每個機器的多個元件)，並使用機器學習演算法來建立預測性模型。 我們會使用從機器收集到的歷史資料來訓練模型。 在此案例中，使用者會經歷各種步驟，以在 Azure Machine Learning Workbench 環境中實作這類模型。

## <a name="prerequisites"></a>必要條件

* [Azure 帳戶](https://azure.microsoft.com/en-us/free/) (有提供免費試用)。
* 遵循[快速入門安裝指南](./quickstart-installation.md)所安裝的 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 版本，以便安裝程式並建立 Workbench。
* 要供此案例中的所有 Jupyter 筆記本使用的中繼結果會儲存在 Azure Blob 儲存體容器內。 Azure 儲存體帳戶的設定指示則可在此[連結](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-python-how-to-use-blob-storage)中取得。 
* 若要[運作](https://github.com/Azure/Machine-Learning-Operationalization)此模型，使用者最好是執行在本機安裝並執行的 [Docker 引擎](https://www.docker.com/)。 如果不是，您可以使用叢集選項，但請注意，[Azure Container Service (ACS)](https://azure.microsoft.com/en-us/services/container-service/)的執行成本通常很高。
* 此案例假設使用者是在 Windows 10 機器上 (已於本機安裝 Docker 引擎) 執行 Azure ML Workbench。 
* 此案例是在 Windows 10 機器上建置並進行測試，其規格如下：Intel Core i7-4600U CPU @ 2.10 GHz、8-GB RAM、64 位元作業系統、x64 架構處理器，Docker 版本為 17.06.0-ce-win19 (12801)。 
* 模型是使用下列版本的 Azure ML CLI 來運作：azure-cli-ml==0.1.0a22

## <a name="create-a-new-workbench-project"></a>建立新的 Workbench 專案

使用此範例作為範本來建立新專案：
1.  開啟 Azure Machine Learning Workbench
2.  在 [專案] 頁面上，按一下 **+** 符號，然後選取 [新增專案]
3.  在 [建立新專案] 窗格中，填入新專案的資訊
4.  在 [搜尋專案範本] 搜尋方塊中輸入「預測性維護」並選取範本
5.  按一下 [建立] 

## <a name="data-description"></a>資料說明

[模擬資料](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)包含五個逗號分隔值 (.csv) 檔案。 

* [機器](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv)：以特徵來區別每一部機器。 例如，存在時間和模型。
* [錯誤](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv)：錯誤記錄包含機器仍在運作時所擲回的非重大錯誤。 系統不會將這些錯誤視為故障，但這些錯誤或許能預測未來的故障事件。 遙測資料是以每小時一次的速率在收集，所以系統會將錯誤的日期時間四捨五入到最接近的整點。
* [維護](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv)：維護記錄同時包含排程和非排程的維護記錄。 排程的維護會與元件的定期檢查對應，非排程的維護則可能來自機械故障或其他效能降低情形。 遙測資料是以每小時一次的速率在收集，所以系統會將維護的日期時間四捨五入到最接近的整點。
* [遙測](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv)：遙測時間序列資料包含從每部機器所收集到的電壓、旋轉、壓力和震動感應器即時量值。 這些資料是一小時的平均值，並且會儲存在遙測記錄中
* [故障](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv)：故障會與維護記錄中的元件更換事件對應。 每一筆記錄都包含機器識別碼、元件類型和更換日期與時間。 我們會使用這些記錄來建立將由模型試著預測的機器學習標籤。

請參閱＜Code＞區段中的[資料擷取](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter 筆記本案例，以從 GitHub 存放庫下載未經處理的資料集，並建立要供此分析使用的 PySpark 資料集。

## <a name="scenario-structure"></a>案例結構
案例內容可於 [GitHub 存放庫](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)取得。 

此存放庫中有[讀我](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md)檔案，以概述從準備資料、到建置幾個模型，並於最後運作其中一個最佳模型的所有程序。 存放庫內的 [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) 資料夾有四個 Jupyter 筆記本。   

接下來，我們將說明逐步操作案例的工作流程。 整個案例是以 PySpark 來撰寫，並分割為四個筆記本，如下所示：

* [擷取資料](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb)：此筆記本會擷取五個 .csv 輸入檔案的資料、進行初步的清除、建立一些摘要圖來確認資料是否已下載，最後再將產生的資料集儲存在 Azure Blob 容器中以供下一個筆記本使用。

* [功能設計](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb)：使用已在上一個步驟進行過清除的資料集，為遙測感應器建立延隔時間特徵，並進行其他特徵設計來建立「自上一次更換後所經過的天數」之類的變數。 最後，為相關記錄加上故障標籤以建立最終的資料集，並儲存在 Azure Blob 中以供下一個步驟使用。 

* [模型建置](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb)：已完成特徵設計的最終資料集接著會根據日期時間戳記，分割為訓練和測試這兩個資料集。 然後，系統會以訓練資料集為基礎來建置「隨機樹系分類器」和「決策樹分類器」這兩個模型，再將模型置於測試資料集來進行評分。 

* [模型運作和部署](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb)：上一個步驟所建立的最佳模型接著會儲存為 .model 檔案以及相關的 .json 配置檔案以供部署之用。 init() 和 run() 函式會先在本機測試，然後才會使用 Azure Machine Learning 模型管理來運作模型，以在生產環境中用來進行即時的故障預測。  

## <a name="conclusion"></a>結論

此案例可讓讀者大致了解如何在 Azure ML Workbench 中，於 Jupyter 筆記本環境內使用 PySpark 來建置完整的預測性維護解決方案。 此案例也會指導讀者，讓他們了解如何使用 Azure Machine Learning 模型管理環境來輕鬆地運作和部署最佳模型，以在生產環境中用來進行即時的故障預測。 然後，讀者可以編輯案例的相關部分，使案例符合其企業需求。  

## <a name="references"></a>參考

此使用案例先前已在多個平台上開發過，如下所示：

* [預測性維護解決方案範本](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [預測性維護建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [使用 SQL R 服務的預測性維護建模指南](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [預測性維護建模指南 Python 筆記本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [使用 PySpark 的預測性維護](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)



