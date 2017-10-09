---
title: "什麼是 Azure Machine Learning？ | Microsoft Docs"
description: "Azure Machine Learning 測試和模型管理的概觀，這是一套整合的端對端資料科學解決方案，可供專業資料科學家用來以雲端規模開發、測試及部署進階分析應用程式。"
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c4919fb679eeb4d25eb0066b9bf617b057d44354
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="what-is-azure-machine-learning"></a>什麼是 Azure Machine Learning？

Azure Machine Learning 是整合、端對端的資料科學和進階分析解決方案。 它可讓資料科學家用來以雲端規模準備資料、開發測試及部署模型。

Azure Machine Learning 的主要元件包括：
- Azure Machine Learning Workbench
- Azure Machine Learning 測試服務
- Azure Machine Learning 模型管理服務
- 適用於 Apache Spark 的 Microsoft Machine Learning 程式庫 (MMLSpark 程式庫)
- 適用於 AI 的 Visual Studio Code Tools

這些應用程式和服務共同協助您大幅加速資料科學專案的開發和部署。 

![Azure Machine Learning 概念](media/overview-what-is-azure-ml/aml-concepts.png)

## <a name="open-source-compatible"></a>開放原始碼相容

Azure Machine Learning 完全支援開放原始碼技術。 您可以使用數以萬計的開放原始碼 Python 封裝，例如下列機器學習架構：

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft 辨識工具組](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

您可以在受管理的環境，例如 Docker 容器和 Spark 叢集中執行您的測試。 您也可以使用進階硬體，例如[在 Azure 虛擬機器中啟用 GPU](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu) 來加速執行。

Azure Machine Learning 以下列開放原始碼技術為建置基礎：

- [Jupyter Notebook](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

它也包括 Microsoft 的開放原始碼技術，例如[適用於 Apache Spark 的 Microsoft Machine Learning 程式庫](https://github.com/Azure/mmlspark)和辨識工具組。

此外，您也可以從部分 Microsoft 開發，設計目的是要解決大規模問題的最進階、經證實的機器學習技術獲益。 它們實際上可在許多 Microsoft 產品中派上用場，例如：

- Windows
- Bing
- Xbox
- Office
- SQL Server

下列是一些 Azure Machine Learning 隨附的一些 Microsoft Machine Learning 技術：

- [PROSE](https://microsoft.github.io/prose/) (PROgram Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
Azure Machine Learning Workbench 是桌面應用程式加上命令列工具，同時支援 Windows 和 macOS。 它可讓您管理整個資料科學生命週期的機器學習解決方案：

- 資料內嵌和準備
- 模型開發和測試管理
- 各種目標環境中的模型部署

以下是 Azure Machine Learning Workbench 所提供的核心功能：

- 可以透過範例了解資料轉換邏輯的資料準備工具。
- 資料來源抽象可透過 UX 和 Python 程式碼存取。
- Python SDK 用於叫用以視覺化方式建構的資料準備封裝。
- 內建 Jupyter Notebook 服務與用戶端 UX。
- 透過執行歷程記錄檢視測試監視及管理。
- 角色型存取控制，允許透過 Azure Active Directory 的共用及協同作業。
- 由原生的 Git 整合所啟用的每個執行的自動專案快照集，以及明確的版本控制。 
- 與受歡迎 Python IDE 的整合。

如需詳細資訊，請參考下列文章：
- [資料準備使用者指南](data-prep-user-guide.md)
- [使用 Git 搭配 Azure Machine Learning](using-git-ml-project.md)
- [在 Azure Machine Learning 中使用 Jupyter Notebook](how-to-use-jupyter-notebooks.md)
- 漫遊和共用
- [執行歷程記錄指南](how-to-use-run-history-model-metrics.md)
- [IDE 整合](how-to-configure-your-IDE.md)

## <a name="azure-machine-learning-experimentation-service"></a>Azure Machine Learning 測試服務
測試服務可處理機器學習測試的執行。 藉由提供專案管理、Git 整合、存取控制、漫遊以及共用，它也支援 Workbench。 

透過簡單的設定，您可以利用廣泛的運算環境選項執行您的測試：

- 本機原生
- 本機 Docker 容器
- 遠端 VM 上的 Docker 容器
- 在 Azure 中相應放大 Spark 叢集

測試服務會建構虛擬環境，以確保您的指令碼可以隔離執行但具有可重現的結果。 它會記錄執行歷程記錄資訊，並以視覺化方式將歷程記錄呈現給您。 您可以從您的測試執行輕鬆選取最佳的模型。 

如需詳細資訊，請參考[測試執行組態](experiment-execution-configuration.md)。

## <a name="azure-machine-learning-model-management-service"></a>Azure Machine Learning 模型管理服務

模型管理服務可讓資料科學家和 DevOps 小組將預測模型部署到各種不同的環境。 從定型執行到部署均會追蹤模型版本和歷程。 模型會在雲端中儲存、登錄及管理。 

使用簡單的 CLI 命令，您可以將模型、評分指令碼和相依性容器化為 Docker 映像。 這些映像會在您裝載於 Azure 的 Docker 登錄中登錄 (Azure Container Registry)。 可以可靠地將它們部署至下列目標：

- 本機電腦
- 內部部署伺服器
- 雲端
- IoT Edge 裝置

執行於 Azure Container Service (ACS) 中的 Kubernete 可用於雲端向外延展部署。 模型執行遙測會在 AppInsights 中擷取，以進行視覺化的分析。 

如需有關模型管理服務的詳細資訊，請參考[模型管理概觀](model-management-overview.md)


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>適用於 Apache Spark 的 Microsoft Machine Learning 程式庫

[MMLSpark](https://github.com/Azure/mmlspark) (適用於 Apache Spark 的 Microsoft Machine Learning 程式庫) 是提供 Apache Spark 深入學習及資料科學工具的開放原始碼 Spark 封裝。 它整合 [Spark Machine Learning 管線](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)與 [Microsoft 辨識工具組](https://www.microsoft.com/en-us/cognitive-toolkit/)和 [OpenCV](http://opencv.org/) 程式庫。 它可讓您快速為大型影像和文字資料集建立功能強大、具備高度靈活調整能力的預測和分析模型。 一些重點包括：

- 輕鬆地將映像從 HDFS 內嵌到 Spark 資料框架
- 從 OpenCV 使用轉型預先處理映像資料
- 使用 Microsoft 辨識工具組利用預先定型的深入類神經網路將影像特徵化 
- 對醫療實體擷取使用來自 Keras 預先定型的雙向 LSTM
- 在 Azure 上的 N 系列 GPU VM 上定型以 DNN 為基礎的影像分類模型
- 特色是在 SparkML 的基本項目上透過單一轉換程式使用便利 API 的自由格式文字資料
- 輕鬆地透過資料的隱含功能定型分類和迴歸模型
- 計算一組豐富的評估度量，包括每個執行個體的度量

如需詳細資訊，請參考[在 Azure Machine Learning 中使用 MMLSpark](how-to-use-mmlspark.md)。

## <a name="visual-studio-code-tools-for-ai"></a>適用於 AI 的 Visual Studio Code Tools
適用於 AI 的 Visual Studio Code Tools 是 Visual Studio Code 中的延伸模組，可建置、測試及部署深入學習和 AI 解決方案。 它的特色在於與 Azure Machine Learning 的許多整合點，包括：
- 執行歷程記錄檢視，顯示定型執行的效能以及記錄的度量。
- 圖庫檢視，讓使用者能夠使用 Microsoft 辨識工具組、TensorFlow 與許多其他深入學習架構瀏覽並啟動新專案。 
- 用於選取計算目標讓指令碼執行的總管檢視。
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Microsoft 提供的機器學習選項有哪些？
除了 Azure Machine Learning，Azure 中有各種不同的選項可用來建置、部署及管理機器學習模型。 
* SQL Server 中的 Microsoft Machine Learning Services
* Microsoft Machine Learning Server
* 資料科學虛擬機器
* HDInsight 中的 Spark MLLib
* Batch AI 定型服務
* Microsoft 辨識工具組
* Microsoft 辨識服務


### <a name="microsoft-machine-learning-services-in-sql-server"></a>SQL Server 中的 Microsoft Machine Learning Services
[Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) 可讓您使用 R 或 Python 執行、定型和部署機器學習模型。 您可以使用位於內部部署與位於 SQL Server 資料庫的資料。 

當您需要在內部部署或 Microsoft SQL Server 內定型或部署模型時，請使用 Microsoft Machine Learning Services。 使用機器學習服務建置的模型可使用 Azure Machine Learning 模型管理來部署。 

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[Microsoft Machine Learning 伺服器](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)是企業伺服器，用來裝載及管理 R 及 Python 處理程序的並行和分散式工作負載。 Microsoft Machine Learning 伺服器可在 Linux、Windows、Hadoop 和 Apache Spark 上執行。 也可在 [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/) 上取得。 它會為使用 [Microsoft Machine Learning 封裝](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)建置的解決方案提供執行引擎，並利用對下列案例的支援延伸開放原始碼 R 和 Python：

- 高效能分析
- 統計分析
- 機器學習服務
- 大型資料集

透過隨著伺服器安裝的專屬封裝提供加值功能。 對於開發，您可以使用 IDE，例如 [Visual Studio R 工具](https://www.visualstudio.com/vs/rtvs/)和[適用於 Visual Studio 的 Python 工具](https://www.visualstudio.com/vs/python/)。

需要執行下列動作時，請使用 Microsoft Machine Learning 伺服器：

- 在伺服器上建置和部署使用 R 和 Python 建置的模型
- 在 Hadoop 或 Spark 叢集上大規模散發 R 和 Python 定型

### <a name="data-science-virtual-machine"></a>資料科學虛擬機器
[資料科學虛擬機器 (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview) 是 Microsoft Azure 雲端上的自訂 VM 映像，專為進行資料科學建置。 它已預先安裝和預先設定許多常用的資料科學和其他工具，以開始建置智慧應用程式進行進階分析。 我們提供 Windows Server 和 Linux 版。 我們提供了 Server 2016 和 Server 2012 的 Windows 版 DSVM。 我們透過 Ubuntu 16.04 LTS 以及 OpenLogic 7.2 CentOS 型 Linux 發行版本提供 Linux 版 DSVM。 

當您需要在單一節點上執行或裝載您的作業時，請使用資料科學虛擬機器。 或者，如果您需要從遠端相應增加在單一機器上的處理。 支援將資料科學虛擬機器作為 Azure Machine Learning 測試和 Azure Machine Learning 模型管理的目標。 

### <a name="spark-mllib-in-hdinsight"></a>HDInsight 中的 Spark MLLib
[HDInsight 中的 Spark MLLib](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) 可讓您隨著執行巨量資料的 Spark 作業建立模型。 Spark 可讓您在單一作業中輕鬆地轉換及準備資料，然後相應放大模型建立。 透過 Spark MLLib 建立的模型可以透過 Azure Machine Learning 模型管理來部署、管理和監視。 可以使用 Azure Machine Learning 測試來分派和管理定型執行。 Spark 也可用來將在 Machine Learning Workbench 中建立的資料準備作業向外延展。 

當您需要相應放大資料處理並隨著資料管線建立模型時，請使用 Spark。 您可以在 Scala、Java、Python 或 R 中撰寫 Spark 作業。 

### <a name="batch-ai-training"></a>Batch AI 定型 
[Azure Batch AI 定型](https://aka.ms/batchaitraining)可協助您使用任何架構搭配您的 AI 模型並行測試，然後在叢集化的 GPU 之間大規模定型。 描述執行所需的作業需求和設定，我們會處理其餘部分。 

Batch AI 定型可讓您相應放大叢集化的 GPU 之間的深入學習作業，使用架構如：

- 辨識工具組
- Caffe
- Chainer
- TensorFlow

Azure Machine Learning 模型管理可用來取得來自 Batch AI 定型的模型，以部署、管理和監視模型。  Batch AI 定型未來將會與 Azure Machine Learning 測試整合。 

### <a name="microsoft-cognitive-toolkit"></a>Microsoft 辨識工具組
[Microsoft 辨識工具組](https://www.microsoft.com/en-us/cognitive-toolkit/)是深入學習工具組，在有向圖中以計算步驟形式說明類神經網路。 在有向圖中，分葉節點代表輸入值或網路參數，而其他節點則代表其輸入的矩陣作業。 辨識工具組可讓您輕鬆地了解並結合受歡迎的模型類型，例如摘要轉寄 DNN、卷積類神經網路 (CNN) 以及循環式網路 (RNN/LSTM)。 它使用學習自跨多個 GPU 和伺服器的自動差異化和平行化來實作隨機梯度下降 (SGD，錯誤反向傳播)。

當您想要使用深入學習建置模型時，請使用辨識工具組。  辨識工具組可以用於任何先前的服務。

### <a name="microsoft-cognitive-services"></a>Microsoft 辨識服務
Microsoft 辨識服務是具有 30 個 API 的組合，可讓您建置使用自然通訊方法的應用程式。 只要幾行程式碼，這些 API 即讓您的應用程式查看、聽取、說出、了解並解譯我們的需求。 輕鬆地將智慧型功能新增至您的應用程式，例如： 

- 情緒和人氣偵測
- 願景與語音辨識
- 語言理解
- 知識和搜尋

Microsoft 辨識服務可以用來開發跨裝置及平台的應用程式。 API 會持續改進，且易於設定。 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>後續步驟
* [安裝與建立 Azure Machine Learning](quickstart-installation.md)

