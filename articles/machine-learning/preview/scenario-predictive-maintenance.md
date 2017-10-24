---
title: "預測性維護實際案例 | Microsoft Docs"
description: "使用 PySpark 的預測性維護實際案例"
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: dc73c052ad9e0fe12af5042289f304a0e48ae413
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>預測性維護實際案例。

對所有企業來說，設備在非排程的時間停機將會造成不利影響。 因此，務必要讓現場設備保持運作，並減少代價很高的非排程停機時間，才能達到最大使用率和效能。 及早發現問題有助於以更符合成本效益的方式配置為數不多的維護資源，並增強品質和供應鏈流程。 

在此案例中，我們會使用較[大規模的資料](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)來引導使用者逐步完成主要步驟，讓他們擷取資料、進行特徵設計、建置模型，並於最後運作和部署模型。 整個程序的程式碼是以 PySpark 撰寫，並且會在 Azure ML Workbench 內使用 Jupyter 筆記本來實作。 最後，我們會使用 Azure Machine Learning 模型管理來運作最佳模型，以在生產環境用來進行即時的故障預測。   

## <a name="link-to-the-gallery-github-repository"></a>連結至資源庫 GitHub 存放庫

以下是公用 GitHub 存放庫的連結：[https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>使用案例概觀

在普遍擁有龐大固定資產的產業中，企業所面臨的主要問題在於，如果機械故障而導致生產延遲，將會讓成本大幅上揚。 企業無不極力預測這些問題會在何時發生，以便事先主動避免。 目標是要藉由縮短停機時間及可能提升安全性來降低成本。 如需常見使用案例以及針對預測性維護所使用之模型化方法的詳細說明，請參閱[預測性維護腳本](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)。

此案例會利用腳本中的構想，其目的是提供步驟，讓您為綜合了多個實際企業問題的案例實作預測性模型。 此範例結合了許多在預測性維護使用案例中所觀察到的常見資料元素。

此模擬資料的企業問題是要預測元件故障所造成的問題。 因此，企業問題是「機器因為元件故障而當機的機率有多高？」 這個問題會格式化為多級分類問題 (每個機器的多個元件)，並使用機器學習演算法來建立預測性模型。 我們會使用從機器收集到的歷史資料來訓練模型。 在此案例中，使用者會經歷各種步驟，以在 Azure Machine Learning Workbench 環境中實作這類模型。

## <a name="prerequisites"></a>必要條件

* [Azure 帳戶](https://azure.microsoft.com/en-us/free/) (有提供免費試用)。
* 遵循[快速入門安裝指南](./quickstart-installation.md)所安裝的 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 版本，以便安裝程式並建立 Workbench。
* 「Azure Machine Learning 操作化」需要一個本機部署環境，以及一個[模型管理帳戶](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview)

此範例可在任何 AML Workbench 計算環境中執行。 不過，建議至少以 16 GB 的記憶體來執行它。 此案例是在執行遠端 DS4_V2 標準[適用於 Linux (Ubuntu) 的資料科學虛擬機器](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)的 Windows 10 電腦上建置並測試的。

模型的操作化是使用 0.1.0a22 版的 Azure ML CLI 來完成的。

## <a name="create-a-new-workbench-project"></a>建立新的 Workbench 專案

使用此範例作為範本來建立新專案：
1.  開啟 Azure Machine Learning Workbench
2.  在 [專案] 頁面上，按一下 **+** 符號，然後選取 [新增專案]
3.  在 [建立新專案] 窗格中，填入新專案的資訊
4.  在 [搜尋專案範本] 搜尋方塊中輸入「預測性維護」並選取範本
5.  按一下 [建立] 

## <a name="prepare-the-notebook-server-computation-target"></a>準備筆記本伺服器計算目標

若要在您的本機電腦上執行，請從 AML Workbench [`File`] \(檔案\) 功能表中，選取 [`Open Command Prompt`] \(開啟命令提示字元\) 或 [`Open PowerShell CLI`] \(開啟 PowerShell CLI\)。 在 CLI 視窗內，執行下列命令：

`az ml experiment prepare --target docker --run-configuration docker`

建議您在「適用於 Linux (Ubuntu) 的資料科學虛擬機器」上執行。 設定完 DSVM 之後，請執行下列兩個命令：

`az ml computetarget attach --name [Desired_Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword] --type remotedocker`

`az ml experiment prepare --target [Desired_Connection_Name] --run-configuration [Desired_Connection_Name]`

備妥 Docker 映像之後，在 AML Workbench 筆記本索引標籤內開啟 Jupyter 筆記本伺服器，或者，若要啟動瀏覽器型伺服器，請執行：`az ml notebook start`。

筆記本儲存在 Jupyter 環境內的 `Code` 目錄中。 我們會循序執行筆記本，從第一個 (`Code\1_data_ingestion.ipynb`) 筆記本開始。 當您開啟每個筆記本時，系統會提示您選取計算核心。 請選擇 [Project_Name]_Template [Desired_Connection_Name]，然後按一下 [Set Kernel] \(設定核心\)。

## <a name="data-description"></a>資料說明

[模擬資料](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)包含五個逗號分隔值 (.csv) 檔案。 請依循下列連結來取得資料集的更詳細描述。

* [機器](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv)：以特徵來區別每一部機器。 例如，存在時間和模型。
* [錯誤](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv)：錯誤記錄包含機器仍在運作時所擲回的非重大錯誤。 系統不會將這些錯誤視為故障，但這些錯誤或許能預測未來的故障事件。 遙測資料是以每小時一次的速率在收集，所以系統會將錯誤的日期時間四捨五入到最接近的整點。
* [維護](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv)：維護記錄同時包含排程和非排程的維護記錄。 排程的維護會與元件的定期檢查對應，非排程的維護則可能來自機械故障或其他效能降低情形。 遙測資料是以每小時一次的速率在收集，所以系統會將維護的日期時間四捨五入到最接近的整點。
* [遙測](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv)：遙測時間序列資料包含從每部機器所收集到的電壓、旋轉、壓力和震動感應器即時量值。 這些資料是一小時的平均值，並且會儲存在遙測記錄中
* [故障](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv)：故障會與維護記錄中的元件更換事件對應。 每一筆記錄都包含機器識別碼、元件類型和更換日期與時間。 我們會使用這些記錄來建立將由模型試著預測的機器學習標籤。

請參閱＜Code＞區段中的[資料擷取](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter 筆記本案例，以從 GitHub 存放庫下載未經處理的資料集，並建立要供此分析使用的 PySpark 資料集。

## <a name="scenario-structure"></a>案例結構
案例內容可於 [GitHub 存放庫](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)取得。 

此存放庫中有[讀我](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md)檔案，以概述從準備資料、到建置幾個模型，並於最後運作其中一個最佳模型的所有程序。 存放庫內的 [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) 資料夾有四個 Jupyter 筆記本。   

接下來，我們將說明逐步操作案例的工作流程。 端對端案例是以 PySpark 撰寫的，並且分成四個筆記本：

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb)：此筆記本會下載 5 個輸入 .csv 檔案、執行一些初步的資料清理及視覺化。 此筆記本會將資料轉換成 PySpark 格式，然後將結果儲存在 Azure Blob 容器中以在日後的工程工作中使用。

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb)：藉由使用來自前個步驟的已清理資料集，會為遙測感應器建立延遲和彙總的功能，而錯誤計數、元件替換、電腦資訊則會加入到遙測資料中。 失敗相關的元件替換會用來建構描述失敗元件的標籤。 已加上標籤的功能資料會儲存在 Azure Blob 中供模型組建工作使用。

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb)模型化筆記本會使用已加上標籤的功能資料集，將資料沿著時間戳記分割成訓練和開發資料集。 此筆記本是具有 `pyspark.ml.classification` 模型的已設定集合實驗。 訓練資料會向量化，而使用者可以使用 `DecisionTreeClassifier` 或 `RandomForestClassifier` 來進行實驗，藉由操作超參數來尋找效能最佳的模型。 效能是藉由評估開發資料集的相關量值統計資料來判斷的。 這些統計資料會往回記錄到 AML Workbench 執行階段畫面中以供追蹤。 每次執行時，筆記本都會將產生的模型儲存至執行 Jupyter 筆記本核心的本機磁碟。 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb)此筆記本會使用儲存至本機 (Jupyter 筆記本核心) 磁碟的最後一個模型，來建置可透過操作化將模型整合至 Azure Web 服務中的元件。 完整的運作資產會壓縮至儲存在另一個 Azure Blob 容器內的 `o16n.zip` 檔案中。 此 ZIP 壓縮檔案包含︰

* `service_schema.json` - 用於部署的結構描述定義檔。 
* `pdmscore.py` - Azure Web 服務所需的 init() 和 run() 函式
* `pdmrfull.model` - 模型定義目錄。
    
 此筆記本會先使用模型定義來測試函式，然後才封裝操作化資產以供部署。 部署指示包含在此筆記本的結尾。

## <a name="conclusion"></a>結論

此案例可讓讀者大致了解如何在 Azure ML Workbench 中，於 Jupyter 筆記本環境內使用 PySpark 來建置完整的預測性維護解決方案。 此案例也會指導讀者，讓他們了解如何使用 Azure Machine Learning 模型管理環境來輕鬆地運作和部署最佳模型，以在生產環境中用來進行即時的故障預測。 然後，讀者可以編輯案例的相關部分，使案例符合其企業需求。  

## <a name="references"></a>參考

此使用案例先前已在多個平台上開發過：

* [預測性維護解決方案範本](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [預測性維護建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [使用 SQL R 服務的預測性維護建模指南](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [預測性維護建模指南 Python 筆記本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [使用 PySpark 的預測性維護](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

# <a name="next-steps"></a>後續步驟

Azure Machine Learning Workbench 內有提供許多其他範例案例來示範此產品的其他功能。 