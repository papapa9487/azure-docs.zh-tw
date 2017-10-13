---
title: "在 Azure 上佈建 Windows 資料科學虛擬機器 | Microsoft Docs"
description: "在 Azure 上設定和建立資料科學虛擬機器以進行分析和機器學習。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: a438a0d83ad4749953297b6481cba9cec5900140
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>在 Azure 上佈建 Windows 資料科學虛擬機器
Microsoft 資料科學虛擬機器是預先安裝並設定數個常用於資料分析和機器學習之熱門工具的 Windows Azure 虛擬機器 (VM) 映像。 這些工具包括：

* Microsoft R Server Developer Edition
* Anaconda Python 散佈
* Jupyter notebook (使用 R、Python 核心)
* Visual Studio Community 版本
* Power BI 桌面
* SQL Server 2016 Developer Edition
* 機器學習服務與資料分析工具
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK)︰來自 Microsoft Research 的深入學習軟體工具組。
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit)︰快速的機器學習系統，支援像是線上、雜湊，allreduce、簡化、learning2search、主動和互動式學習的技術。
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/)︰提供快速且正確的推進式決策樹實作的工具。
  * [Rattle](http://rattle.togaware.com/) (R Analytical Tool To Learn Easily)︰此工具可讓您利用 GUI 型資料探索，輕鬆地使用 R 架構開始進行資料分析與機器學習，以及自動產生 R 程式碼來建立模型。
  * [mxnet](https://github.com/dmlc/mxnet)︰效率和彈性的深入學習架構
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/)︰Java 中的視覺化資料採礦和機器學習服務軟體。
  * [Apache Drill](https://drill.apache.org/)：適用於 Hadoop、NoSQL 和雲端儲存體的無結構描述 SQL 查詢引擎。  支援 ODBC 和 JDBC 介面，能夠從 PowerBI、Excel、Tableau 等標準 BI 工具查詢 NoSQL 和 檔案。
* R 和 Python 語言的程式庫，可用於 Azure Machine Learning 和其他 Azure 服務
* 包括 Git Bash 的 Git，可搭配原始程式碼儲存機制 (包括 GitHub、Visual Studio Team Services) 使用
* Windows 連接埠，含可透過命令提示字元存取的數個熱門 Linux 命令列公用程式 (包括 awk、sed、perl、grep、find、wget 及 curl 等)。 

執行資料科學涉及反覆進行一連串的工作︰

1. 尋找、載入和前置處理資料
2. 建置和測試模型
3. 部署要在智慧型應用程式中使用的模型

資料科學家使用各種工具來完成這些工作。 尋找適當版本的軟體然後下載並安裝它們是相當耗費時間的工作。 Microsoft Data Science 虛擬機器提供可佈建於 Azure 上的現成映像，其中含有已預先安裝和設定的數種常用工具，能有助於減輕工作負擔。 

Microsoft 資料科學虛擬機器可快速啟動分析專案。 它能讓您用各種語言處理工作，包含 R、Python、SQL 與 C#。 Visual Studio 提供整合式開發環境 (IDE) 以開發和測試您的程式碼，很容易使用。 包含在 VM 的 Azure SDK 可讓您使用 Microsoft 的雲端平台上的各種服務，建置您的應用程式。 

這個資料科學 VM 映像沒有任何軟體費用。 您只需針對佈建的虛擬機器大小支付 Azure 使用費。 計算費用的詳細資訊可在 [資料科學虛擬機器](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) 頁面的＜價格詳細資料＞一節中找到。 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>其他版本的資料科學虛擬機器
另提供 [CentOS](linux-dsvm-intro.md) 映像，其中包含許多與 Windows 映像相同的工具。 亦提供 [Ubuntu](dsvm-ubuntu-intro.md) 映像，其中包含許多類似的工具及深入學習架構。

## <a name="prerequisites"></a>必要條件
您必須先具有下列項目，才可以建立 Microsoft 資料科學虛擬機器：

* **Azure 訂用帳戶**：若要取得訂用帳戶，請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **Azure 儲存體帳戶**：若要建立帳戶，請參閱 [建立 Azure 儲存體帳戶](../../storage/common/storage-create-storage-account.md#create-a-storage-account)。 或者，如果您不想使用現有的帳戶，可以在建立 VM 過程中建立儲存體帳戶。

## <a name="create-your-microsoft-data-science-virtual-machine"></a>建立 Microsoft 資料科學虛擬機器
建立 Microsoft 資料科學虛擬機器執行個體的步驟如下：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm)上的虛擬機器清單。
2. 選取底部的 [建立] 按鈕以進入精靈。![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)
3. 針對此圖右邊列舉的**五個步驟**，用來建立 Microsoft 資料科學虛擬機器的精靈會要求**輸入**每個步驟。 以下是設定每個步驟所需的輸入：
   
   1. **基本概念**
      
      1. **名稱**：您建立的資料科學伺服器名稱。
      2. **使用者名稱**：系統管理員帳戶登入識別碼。
      3. **密碼**：系統管理員帳戶密碼。
      4. **訂用帳戶**：如果您有多個訂用帳戶，請選取要用來建立機器和開立帳單的訂用帳戶。
      5. **資源群組**：您可以建立新群組或使用現有的群組。
      6. **位置**：選取最適合的資料中心。 它通常是擁有您大部分的資料或者是最接近您的實際位置以取得最快速度的網路存取的資料中心。
   2. **大小**：選取其中一個符合您的功能性需求和成本條件約束的伺服器類型。 您可以藉由選取 [檢視全部] 取得更多的 VM 大小的選項。
   3. **設定**：
      
      1. **磁碟類型**：如果您偏好固態硬碟 (SSD)，請選擇「高階」，否則請選擇「標準」。
      2. **儲存體帳戶**：您可以在訂用帳戶中建立新的 Azure 儲存體帳戶，或使用在精靈的**基本**步驟上選擇的相同「位置」中的現有 Azure 儲存體帳戶。
      3. **其他參數**：您通常只會使用預設值。 如果您考慮使用非預設值，您可以將滑鼠停留在特定欄位的資訊連結上以取得說明。
   4. **摘要**：請確認您輸入的所有資訊都正確無誤。
   5. **購買**：按一下 [購買] 以開始佈建。 會提供一個交易條款的連結。 VM 除了計算您在 [大小]  步驟中所選擇的伺服器大小之外，不會收取任何其他費用。 

> [!NOTE]
> 佈建大約 10-20 分鐘。 在 Azure 入口網站中會顯示佈建的狀態。
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>如何存取 Microsoft 資料科學虛擬機器
建立 VM 之後，您可以使用您在前面的 **基本** 區段中設定的系統管理員帳戶認證從遠端桌面登入 VM。 

一旦建立並佈建您的 VM，您已準備好開始使用在上面安裝及設定的工具。 我們為許多工具提供了開始功能表磚和桌面圖示。 

## <a name="how-to-create-a-strong-password-for-jupyter-and-start-the-notebook-server"></a>如何建立 Jupyter 的強式密碼並啟動 Notebook 伺服器
根據預設，Jupyter Notebook 伺服器在 VM 上已預先設定，但在您設定 Jupyter 密碼之前一直停用。 若要為電腦上安裝的 Jupyter Notebook 伺服器建立強式密碼，請在資料科學虛擬機器上從命令提示字元執行下列命令，「或」從本機 VM 系統管理員帳戶按兩下我們提供的桌面捷徑：**Jupyter 設定密碼和啟動**。

    C:\dsvm\tools\setup\JupyterSetPasswordAndStart.cmd

依照訊息進行，看到提示時選擇強式密碼。

上述指令碼會建立密碼雜湊，並將它儲存在 Jupyter 設定檔 **C:\ProgramData\jupyter\jupyter_notebook_config.py** 中的參數名稱 ***c.NotebookApp.password*** 之下。

此指令碼也會在背景啟用和執行 Jupyter 伺服器。 Jupyter 伺服器會在 Windows 工作排程器中建立為一項 Windows 工作，稱為 **Start_IPython_Notebook**。  設定密碼之後，您可能需要等候幾秒鐘，再於瀏覽器中開啟 Notebook。 請參閱下一節 **Jupyter Notebook**，以了解如何存取 Jupyter Notebook 伺服器。 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Microsoft 資料科學虛擬機器上所安裝的工具

### <a name="microsoft-r-server-developer-edition"></a>Microsoft R Server Developer Edition
如果您想要將 R 用於分析，VM 已安裝 Microsoft R Server Developer Edition。 Microsoft R Server 是一個能夠廣泛部署的企業級分析平台，以支援的 R 為基礎，可調整規模且十分安全。 R Server 支援各種巨量資料統計資料、預測性模型化和機器學習能力，因此可支援各種類型的分析 – 瀏覽、分析、視覺化及模型化。 透過使用及擴充開放原始碼 R，Microsoft R Server 能夠與 R 指令碼、函式及 CRAN 封裝完全相容，以分析企業規模的資料。 它也會透過加入資料的平行和區塊處理，解決開放原始碼 R 的記憶體內部限制問題。 這可讓您對遠大於主記憶體可負荷的資料量執行資料分析。  VM 隨附的 Visual Studio Community Edition 包含 R Tools for Visual Studio 擴充功能，提供搭配 R 工具使用的完整整合式開發環境 (IDE)。您也可以下載並使用其他 IDE，例如 [RStudio](http://www.rstudio.com)。 

### <a name="python"></a>Python
為了能夠使用 Python 進行開發，我們已安裝了 Anaconda Python 散佈 2.7 與 3.5。 這個散發套件包含基本的 Python 以及大約 300 個最受歡迎的數學運算、工程設計和資料分析封裝。 您可以使用安裝在 Visual Studio 2015 Community 版本內的 Python Tools for Visual Studio (PTVS)，或隨附於 Anaconda 的其中一個整合式開發環境 (IDE)，像是 IDLE 或 Spyder。 您可以在搜尋列上搜尋以啟動其中一個 (**Win** + **S** 鍵)。

> [!NOTE]
> 若要將 Python Tools for Visual Studio 指向 Anaconda Python 2.7 與 3.5，您必須為各版本建立自訂的環境。 若要在 Visual Studio 2015 Community Edition 中設定這些環境路徑，請瀏覽至 工具 -> Python 工具 -> Python 環境，然後按一下+ 自訂。 
> 
> 

Anaconda Python 2.7 安裝在 C:\Anaconda 之下，Anaconda Python 3.5 則安裝在 c:\Anaconda\envs\py35 之下。 如需詳細步驟，請參閱 [PTVS 文件](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) 。 

### <a name="jupyter-notebook"></a>Jupyter Notebook
Jupyter Notebook 中也隨附 Anaconda 散發套件，這是一個共用程式碼與分析的環境。 Jupyter Notebook 伺服器已經預先設定 Python 2.7、Python 3.4、Python 3.5 及 R 核心。 有一個名為 Jupyter Notebook 的桌面圖示可以啟動瀏覽器來存取 Notebook 伺服器。 如果您是透過遠端桌面登入 VM，也可以在登入 VM 後造訪 [https://localhost:9999/](https://localhost:9999/) 以存取 Jupyter Notebook 伺服器。

> [!NOTE]
> 如果您收到任何憑證警告，請繼續。 
> 
> 

我們已封裝 Python 和 R 格式的數個 Notebook 範例。在登入 Jupyter 之後，Jupyter Notebook 會示範如何使用 Microsoft R Server、SQL Server 2016 R 服務 (資料庫內分析)、Python、用於深入學習的 Microsoft Cognitive ToolKit 和其他 Azure 技術。 當您使用在較早步驟中建立的密碼向 Jupyter Notebook 驗證之後，您就可以在 Notebook 首頁看到範例的連結。 

### <a name="visual-studio-2015-community-edition"></a>Visual Studio 2015 Community 版本
VM 上安裝的 Visual Studio Community 版本。 它是 Microsoft 提供的熱門 IDE 的免費版本，可供用於進行評估，且適合小型團隊。 您可以在[這裡](https://www.visualstudio.com/support/legal/mt171547)查看授權條款。  按兩下桌面圖示或 [開始] 功能表以開啟 Visual Studio。 您也可以使用 **Win** + **S** 並輸入 “Visual Studio” 來搜尋程式。 之後，您就可以使用像是 C#、Python、R 及 node.js 等語言來建立專案。 另外已安裝了外掛程式，以方便您使用 Azure 服務，例如 Azure 資料目錄、Azure HDInsight (Hadoop、Spark) 及 Azure Data Lake。 

> [!NOTE]
> 您可能會收到訊息，表示您的評估期間已過期。 請輸入 Microsoft 帳戶認證或建立新的免費帳戶，以取得 Visual Studio Community 版本的存取權。 
> 
> 

### <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition
VM 上提供含 R 服務的 SQL Server 2016 開發人員版本，以執行資料庫內分析。 R 服務提供用於開發和部署智慧型應用程式的平台。 您可以使用豐富且功能強大的 R 語言和社群的許多封裝來建立模型，並為 SQL Server 資料產生預測。 您可以讓分析貼近資料，因為 R 服務 (資料庫內) 會整合 R 語言與 SQL Server。 這樣就可免除與資料移動相關聯的成本和安全性風險。

> [!NOTE]
> SQL Server 2016 Developer Edition 只能用於進行開發和測試。 您需要授權，才能在生產環境中執行它。 
> 
> 

您可以藉由啟動 [SQL Server Management Studio] 來存取 SQL Server。 您的 VM 名稱會填入做為伺服器名稱。 在 Windows 上以系統管理員身分登入時，使用 Windows 驗證。 當您在 SQL Server Management Studio 中，可以建立其他使用者、建立資料庫、匯入資料以及執行 SQL 查詢。 

若要使用 Microsoft R 啟用資料庫內分析，在以伺服器系統管理員身分登入後，請在 SQL Server Management Studio 中執行下列命令 (一次性動作)。 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
有數個 Azure 工具會安裝於 VM 上：

* 有一個桌面捷徑可用來存取 Azure SDK 文件。 
* **AzCopy**：用於將資料從 Microsoft Azure 儲存體帳戶移入和移出。 若要查看使用情況，在命令提示字元中輸入 **Azcopy** 即可查看使用情況。 
* **Microsoft Azure 儲存體總管**：可用來瀏覽已儲存在 Azure 儲存體帳戶中的物件，並在 Azure 儲存體收發資料。 若要存取此工具，您可以在搜尋中輸入 **儲存體總管** 或在 Windows [開始] 功能表上尋找。 
* **Adlcopy**︰用於將資料移至 Azure Data Lake。 若要查看使用情況，請在命令提示字元中輸入 **adlcopy** 。 
* **dtui**︰用於在 Azure Cosmos DB (雲端上的一種 NoSQL 資料庫) 上來回移動資料。 在命令提示字元中輸入 **dtui** 。 
* **Microsoft 資料管理閘道**︰可在內部部署資料來源和雲端之間移動資料。 其使用於 Azure Data Factory 之類的工具。 
* **Microsoft Azure Powershell**：利用 Powershell 指令碼語言來管理您的 Azure 資源的工具，也會安裝在您的 VM 上。 

### <a name="power-bi"></a>Power BI
為了協助您建立儀表板和絕佳的視覺效果，已安裝 **Power BI Desktop** 。 使用此工具以從不同來源提取資料，來撰寫您的儀表板和報告並發佈至雲端。 如需詳細資訊，請參閱 [Power BI](http://powerbi.microsoft.com) 網站。 您可以在 [開始] 功能表上找到 Power BI 桌面。 

> [!NOTE]
> 您需要 Office 365 帳戶才能存取 Power BI。 
> 
> 

## <a name="additional-microsoft-development-tools"></a>其他 Microsoft 開發工具
[**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) 可以用來探索並下載其他 Microsoft 開發工具。 另外在 Microsoft 資料科學虛擬機器桌面上也有提供工具的捷徑。  

## <a name="important-directories-on-the-vm"></a>VM 上的重要目錄
| 項目 | 目錄 |
| --- | --- |
| Jupyter Notebook 伺服器組態 |C:\ProgramData\jupyter |
| Jupyter Notebook 範例的主目錄 |c:\dsvm\notebooks |
| 其他範例 |c:\dsvm\samples |
| Anaconda (預設值︰Python 2.7) |c:\Anaconda |
| Anaconda Python 3.5 環境 |c:\Anaconda\envs\py35 |
| R 伺服器 (獨立式) 執行個體目錄 (以 R3.2.2 為基礎的預設 R 執行個體) |C:\Program Files\Microsoft SQL Server\130\R_SERVER |
| R 伺服器 (資料庫內) 執行個體目錄 (R3.2.2) |C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES |
| Microsoft R Open (R3.3.1) 執行個體目錄 |C:\Program Files\Microsoft\MRO-3.3.1 |
| 其他工具 |c:\dsvm\tools |

> [!NOTE]
> 在 1.5.0 以前建立的 Microsoft 資料科學虛擬機器執行個體 (2016 年 9 月 3 日以前)，使用的目錄結構與上表指定的稍有不同。 
> 
> 

## <a name="next-steps"></a>後續步驟
以下是繼續您的學習和探索的一些後續步驟。 

* 按一下開始功能表並查看功能表中列出的工具，來探索資料科學 VM 上的各種資料科學工具。
* 瀏覽至 **C:\Program Files\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts**，以取得在 R 中使用 RevoScaleR 程式庫的範例，其支援企業規模的資料分析。  
* 閱讀文章： [您可以在 Data Science Virtual Machine 上做的 10 件事](http://aka.ms/dsvmtenthings)
* 了解如何使用 [Team Data Science Process](https://azure.microsoft.com/documentation/learning-paths/data-science-process/)以系統化方式建置端對端分析方案。
* 造訪 [Cortana Intelligence 資源庫](http://gallery.cortanaintelligence.com) ，以取得使用 Cortana Intelligence Suite 的機器學習服務和資料分析範例。 我們也已經在虛擬機器的 **開始** 功能表與桌面上提供此資源庫的圖示。

