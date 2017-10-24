---
title: "適用於 Linux 和 Windows 的 Azure 資料科學虛擬機器簡介 | Microsoft Docs"
description: "適用於 Windows 和 Linux 資料科學虛擬機器的重要分析案例與元件。"
keywords: "資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 8398af10d4826373e16420be3309c6ae8246ca4b
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>適用於 Linux 和 Windows 的 Azure 資料科學虛擬機器簡介

資料科學虛擬機器 (DSVM) 是 Microsoft Azure 雲端上的自訂 VM 映像，專為進行資料科學建置。 它已預先安裝和預先設定許多常用的資料科學和其他工具，以開始建置智慧應用程式進行進階分析。 我們提供 Windows Server 和 Linux 版。 我們提供了 Server 2016 和 Server 2012 的 Windows 版 DSVM。 我們透過 Ubuntu 16.04 LTS 以及 OpenLogic 7.2 CentOS 型 Linux 發行版本提供 Linux 版 DSVM。 

本主題討論您可以使用資料科學 VM 來做什麼、說明使用 VM 的一些主要案例、逐項列出 Windows 和 Linux 版本中可使用的主要功能，並提供如何開始使用的相關指示。


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>我可以使用資料科學虛擬機器來做什麼？
資料科學虛擬機器的目標是提供資料專業人員具有友善資料科學環境的所有技能等級和角色。 如果您已自行推出相當的環境，此 VM 可為您節省可觀的時間。 相反地，立即在新建立的 VM 執行個體中啟動您的資料科學專案。 

資料科學 VM 是針對使用廣泛用途案例而設計和設定的。 您可以在您的專案需要變更時，相應增加或相應減少您的環境。 可以使用您偏好的語言對資料科學工作進行程式設計。 您可以針對您確切的需求安裝其他工具和自訂系統。

## <a name="key-scenarios"></a>主要案例
本節建議一些資料科學 VM 可以部署的主要案例。

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>在雲端中預先設定分析桌面
資料科學 VM 提供資料科學小組外觀的基準組態，將其本機桌面取代為受管理的雲端桌面。 此基準確保小組的所有資料科學家都有一致的設定，用來確認實驗，並提升共同作業。 它也會藉由降低系統管理員的負擔並且節省評估、安裝所需的時間，及維護執行進階分析所需的各種軟體套件，來減少成本。  

### <a name="data-science-training-and-education"></a>資料科學訓練和教育
教導資料科學課程的企業訓練者和教育者，通常會提供虛擬機器映像，以確保他們的學生擁有一致的設定且範例如預期般運作。 資料科學 VM 會建立具有一致設定的隨選環境，緩解支援和不相容性挑戰。 在必須經常建置這些環境的案例中，特別是針對較短的訓練課程，可以有大幅獲益。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>大型專案的隨選彈性容量
資料科學黑客松 (Hackathon)/競賽或大型資料模型化和探索需要相應放大硬體容量，通常是很短的期間。 資料科學 VM 可幫助在相應放大的伺服器上快速隨選複寫資料科學環境，允許需要強大運算資源執行的實驗。

### <a name="short-term-experimentation-and-evaluation"></a>短期實驗和評估
資料科學 VM 可以用來評估或學習工具，例如 Microsoft R Server、SQL Server、Visual Studio 工具、Jupyter、深入學習/ML 工具組，與社群中需要最少設定的受歡迎新工具。 因為資料科學 VM 可快速設定，它可以套用至其他短期使用案例，例如複寫已發佈的實驗、執行示範、遵循線上課程或會議教學課程的逐步解說。

### <a name="deep-learning"></a>深入學習
資料科學 VM 可以用於在 GPU (圖形處理單元) 型硬體使用深入學習演算法的定型模型。 利用 Azure 雲端的 VM 規模調整功能，DSVM 可協助您依需求在雲端使用 GPU 型硬體。 當訓練大型模型或需要高速計算同時保留相同的 OS 磁碟時，可以切換到 GPU 型 VM。  Windows Server 2016 版的 DSVM 預先安裝了 GPU 驅動程式、架構和 GPU 版本的深入學習演算法。 對於 Linux，只有在 [Linux (Ubuntu) 版資料科學虛擬機器](http://aka.ms/dsvm/ubuntu)上，GPU 上的深入學習才會啟用。 您可以將 Ubuntu/Windows-2016 版資料科學 VM 部署到非 GPU 型 Azure 虛擬機器，所有深入學習架構在此情況下將恢復為 CPU 模式。 之前，針對 Windows Server 2012，我們發行了[深入學習工具組](http://aka.ms/dsvm/deeplearning)，但現在我們建議您針對 Windows 型深入學習工作負載使用 Windows Server 2016。 CentOS 型 Linux 版 DSVM 僅包含一些輸入學習工具 (Microsoft 辨識工具組、TensorFlow、MXNet) 的 CPU 組建，但是不會預先安裝 GPU 驅動程式和架構。 

## <a name="whats-included-in-the-data-science-vm"></a>資料科學 VM 中包含什麼？
資料科學虛擬機器已安裝和設定許多常用的資料科學和深入學習工具。 它也包含工具，可讓您輕鬆地使用各種 Azure 資料與分析產品。 您可以使用 Microsoft R Server 或 SQL Server 2016 在大型資料集上探索和建置預測模型。 來自開放原始碼社群及 Microsoft 的許多其他工具也會包含在內，以及範例程式碼和筆記本。 下表列舉並比較 Windows 和 Linux 版本的資料科學虛擬機器所包含的主要元件。


| **工具**                                                           | **Windows 版本** | **Linux 版本** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| 已預先安裝熱門套件的 [Microsoft R Open](https://mran.microsoft.com/open/)   |Y                      | Y             |
| [Microsoft R Server](https://msdn.microsoft.com/microsoft-r/) Developer Edition 包括： <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-getting-started) 平行和分散式高效能 R 架構<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://msdn.microsoft.com/microsoft-r/microsoftml-introduction) - Microsoft 的最新 ML 演算法 <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R 實作](https://msdn.microsoft.com/microsoft-r/operationalize/about)                                            |Y                      | Y <br/> (MicrosoftML 尚無法使用)|
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro-Plus 含共用啟用 - Excel、Word 和 PowerPoint   |Y                      |N              |
| 已預先安裝熱門套件的 [Anaconda Python](https://www.continuum.io/) 2.7、3.5    |Y                      |Y              |
| 已預先安裝 Julia 語言熱門套件的 [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |Y                      |Y              |
| 關聯式資料庫                                                            | [SQL Server 2016 SP1](https://www.microsoft.com/sql-server/sql-server-2016) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/)(僅限 CentOS) |
| 資料庫工具                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC 驅動程式| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (查詢工具)， <br /> * bcp、sqlcmd <br /> * ODBC/JDBC 驅動程式|
| 可調整資料庫中分析與 SQL Server R 服務 | Y     |N              |
| 採用下列核心的 **[Jupyter Notebook Server](http://jupyter.org/) ，**                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python 2.7 & 3.5 | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | N | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (僅限 Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | Y |
| JupyterHub (多使用者筆記型電腦伺服器)| N | Y |
| **開發工具、IDE 和程式碼編輯器**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) >含 Git Plugin、Azure HDInsight (Hadoop)、Data Lake、SQL Server Data Tools、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](http://aka.ms/ptvs) 和 [Visual Studio R 工具 (RTVS)](http://microsoft.github.io/RTVS-docs/) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](http://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim 和 Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git 和 GitBash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* .Net Framework | Y | N |
| PowerBI Desktop | Y | N |
| SDK，用以存取服務的 Azure 和 Cortana Intelligence Suite | Y | Y |
| **資料移動和管理工具** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure 儲存體總管 | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure/overview) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy(Azure Data Lake 儲存體)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB 資料移轉工具](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx)：在 OnPrem 與雲端之間移動資料 | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix/Linux 命令列公用程式 | Y | Y |
| [Apache Drill](http://drill.apache.org) for Data exploration | Y | Y |
| **機器學習工具** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* 與 [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R、Python) 整合 | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (僅限 Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/) | N | Y (僅限 Ubuntu) |
| **GPU 型 Deep Learning Tools** |Windows Server 2016 版  |Ubuntu 版 |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft 辨識工具組 (先前稱為 CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | Y | Y|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe 和 Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA、CUDNN、Nvidia 驅動程式](https://developer.nvidia.com/cuda-toolkit) | Y | Y |
| **巨量資料平台 (僅限 Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* 本機獨立 [Spark](http://spark.apache.org/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* 本機 [Hadoop](http://hadoop.apache.org/) (HDFS、YARN) | N | Y |



## <a name="get-started-with-the-windows-data-science-vm"></a>開始使用 Windows 資料科學 VM
* 瀏覽至
  * [Windows Server 2016 型 DSVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm)
  
  或 
  * [Windows Server 2012 型 DSVM](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)，以建立所需的 Windows DSVM 版本執行個體 
* 按一下 [立即取得] 按鈕。
* 使用您建立 VM 時指定的認證，從遠端桌面登入 VM。
* 若要探索和啟動可用的工具，請按一下 [啟動] 功能表。

## <a name="get-started-with-the-linux-data-science-vm"></a>開始使用 Linux 資料科學 VM
* 瀏覽至 
  * [Ubuntu 型 DSVM](http://aka.ms/dsvm/ubuntu)

  或

  * [OpenLogic CentOS 型 DSVM](http://aka.ms/dsvm/centos)，以建立所需的 Linux DSVM 版本執行個體

  
* 按一下 [立即取得] 按鈕。
* 使用您建立 VM 時指定的認證，從 SSH 用戶端登入 VM，例如 Putty 或 SSH 命令。
* 在殼層提示字元中，輸入 dsvm-more-info。
* 對於圖形化桌面，在[這裡](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)針對您的用戶端平台下載 X2Go 用戶端，並且遵循 Linux 資料科學 VM 文件[佈建 Linux 資料科學虛擬機器](linux-dsvm-intro.md#installing-and-configuring-x2go-client)中的指示。

## <a name="next-steps"></a>後續步驟
### <a name="for-the-windows-data-science-vm"></a>對於 Windows 資料科學 VM
* 如需有關如何在 Windows 版本上執行特定工具的詳細資訊，請參閱[佈建 Microsoft 資料科學虛擬機器](provision-vm.md)和
* 如需有關如何執行您在 Windows VM 上的資料科學專案所需的各種工作的詳細資訊，請參閱[您可以在資料科學虛擬機器上做的十件事](vm-do-ten-things.md)。

### <a name="for-the-linux-data-science-vm"></a>對於 Linux 資料科學 VM
* 如需有關如何在 Linux 版本上執行特定工具的詳細資訊，請參閱[佈建 Linux 資料科學虛擬機器](linux-dsvm-intro.md)。
* 如需為您示範如何使用 Linux VM 執行數個常見資料科學工作的逐步解說，請參閱 [Linux 資料科學虛擬機器上的資料科學](linux-dsvm-walkthrough.md)。

