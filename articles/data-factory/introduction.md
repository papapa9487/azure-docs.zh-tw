---
title: "Azure Data Factory 簡介 | Microsoft Docs"
description: "了解 Azure Data Factory，這是一項雲端資料整合服務，用來協調及自動移動和轉換資料。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2017
ms.author: shlo
ms.openlocfilehash: 58e141498ed5cbaa110622d949a9627c98827ac3
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="introduction-to-azure-data-factory"></a>Azure Data Factory 簡介 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-introduction.md)
> * [第 2 版 - 預覽](introduction.md)

在巨量資料的世界中，未經處理、未經組織的資料通常是儲存在關聯式、非關聯式及其他儲存體系統中。 不過，未經處理資料本身並沒有適當的內容或意義，因此無法為分析人員、資料科學人員或業務決策者提供有意義的深入解析。 

巨量資料需要以下服務：可協調和運作程序，將這些龐大的未經處理資料儲存區精簡成可操作的業務深入解析。 Azure Data Factory 是一個針對這些複雜的混合式「擷取、轉換和下載」(ETL)、「擷取、下載和轉換」(ELT) 及資料整合專案建立的受管理雲端服務。

例如，想像有一個收集雲端遊戲所產生之數 PB 遊戲記錄的遊戲公司。 該公司想要分析這些記錄，深入了解客戶喜好設定、人口統計和使用行為。 這家公司也想要識別向上銷售和交叉銷售機會，開發強大的新功能來推動業務成長，以及為客戶提供更好的體驗。

為了分析這些記錄，此公司必須使用參考資料，例如內部部署資料存放區中的客戶資訊、遊戲資訊及行銷活動資訊。 此公司想要利用這份來自內部部署資料存放區的資料，將其與雲端資料存放區中的額外記錄資料結合。 

為了擷取深入解析，它希望使用雲端中的 Spark 叢集 (Azure HDInsight) 來處理聯結的資料，然後將轉換後的資料發佈到雲端資料倉儲 (例如 Azure SQL 資料倉儲)，來輕鬆地以該資料建立報告。 他們想要自動執行此工作流程，並且每日按照排程監視和管理此工作流程。 他們也想要在檔案進入 Blob 存放區容器時執行該工作流程。

Azure Data Factory 就是解決這類資料案例的平台。 這是一項*雲端式資料整合服務，可讓您在雲端建立資料驅動工作流程，以便協調及自動進行資料移動和資料轉換*。 使用 Azure Data Factory，可以建立並排程資料驅動的工作流程 (稱為管線)，它可以從不同的資料存放區擷取資料。 使用計算服務 (例如，Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure Machine Learning) 可以處理或轉換資料。 

此外，您可以將輸出資料發佈至資料存放區，例如 Azure SQL 資料倉儲，讓商業智慧 (BI) 應用程式取用。 最後，透過 Azure Data Factory，即可將未經處理資料組織到有意義的資料存放區和資料湖中，以供做出更好的業務決策。

![Data Factory 的最上層檢視](media/introduction/big-picture.png)

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [Data Factory 第 1 版簡介](v1/data-factory-introduction.md)。

## <a name="how-does-it-work"></a>運作方式
Azure Data Factory 中的管線 (資料導向工作流程) 通常會執行下列四個步驟︰

![資料導向工作流程的四個步驟](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>連線及收集

企業有位於內部部署環境和雲端中截然不同來源的各種類型資料 (結構化、非結構化及半結構化)，全都以不同的間隔和速度抵達。 

建置資訊生產系統的第一步，就是連線到所有必要的資料和處理來源 (例如軟體即服務 (SaaS) 服務、資料庫、檔案共用和 FTP Web 服務)。 下一個步驟為視需要將資料移至集中式位置進行後續處理。

沒有 Data Factory，企業必須建置自訂的資料移動元件或撰寫自訂服務，以整合這些資料來源和處理。 整合和維護這類系統相當耗費成本而且困難。 此外，這些系統經常會缺少企業等級監視、警示與完全受管理的服務可以提供的控制項。

有了 Data Factory，您就可以使用資料管線中的[複製活動](copy-activity-overview.md)，將內部部署和雲端來源資料存放區內的資料都移到雲端中的集中資料存放區，以供進一步分析。 例如，您可以收集 Azure Data Lake Store 中的資料，之後使用 Azure Data Lake Analytics 計算服務來轉換資料。 您也可以收集 Azure Blob 儲存體中的資料，之後使用 Azure HDInsight Hadoop 叢集來轉換資料。

### <a name="transform-and-enrich"></a>轉換及擴充
在資料存在於雲端的集中式資料存放區之後，請使用計算服務 (例如 HDInsight Hadoop、Spark、Data Lake Analytics 和 Machine Learning) 來處理或轉換所收集的資料。 您想要在可維護且可控制的排程中可靠地產生轉換的資料，以將信任的資料饋送至生產環境。

### <a name="publish"></a>發佈
在未經處理資料已精簡成符合業務需求的可取用形式之後，將該資料載入到 Azure 資料倉儲、Azure SQL Database、Azure CosmosDB，或業務使用者可從其商業智慧工具指向的任何分析引擎。

### <a name="monitor"></a>監視
在您順利建置並部署資料整合管線之後 (從精簡資料提供業務價值)，請監視所排定活動和管線的成功和失敗率。 Azure Data Factory 提供內建支援，可讓您透過 Azure 監視器、API、PowerShell、Microsoft Operations Management Suite 及 Azure 入口網站上的健康情況面板監視管線。

## <a name="whats-different-in-version-2"></a>第 2 版有何不同？
Azure Data Factory 第 2 版是以原始 Azure Data Factory 資料移動和轉換服務為基礎，延伸至一組更廣泛的雲端優先資料整合案例。 Azure Data Factory 第 2 版提供下列功能：

- 控制流程和規模
- 在 Azure 中部署和執行 SQL Server Integration Services (SSIS) 套件

在第 1 版發行之後，我們體認到客戶需要設計要求在雲端、內部部署環境及雲端 VM 中移動和處理資料的複雜混合式資料整合案例。 這些要求帶來一項需求，亦即在受保護的虛擬網路環境內轉送和處理資料，並使用依需求提供的處理能力進行相應放大。

隨著資料管線變成商務分析策略的一個重要部分，我們已目睹這些重要的資料活動需要彈性的排程，以支援增量資料載入和事件觸發的執行。 最後，隨著這些作業的複雜性增加，要求服務支援常見工作流程範例 (包括分支、迴圈及條件式處理) 的需求也隨之增加。

在第 2 版中，您也可以將現有的 SSIS 套件移轉到雲端。 您可以將 SSIS 原形移轉為在 ADF 內管理的 Azure 服務，並且利用「整合執行階段」(IR)」這想新功能。 藉由在第 2 版中運作 SSIS IR，您將能夠在雲端執行、管理、監視及建置 SSIS 套件。

### <a name="control-flow-and-scale"></a>控制流程和規模 
為了支援現代化資料倉儲中的各種整合流程和模式，Data Factory 已啟用一個不再與時間序列資料繫結的新彈性資料管線模型。 在此版本中，您可以在資料管線的控制流程中為條件式和分支建立模型，然後明確地在這些流程內及跨這些流程傳遞參數。

您現在可以自由地為其資料整合所需的任何流程樣式建立模型，其可依需求分派或依時鐘排程重複地分派。 幾個現在已啟用但先前無法使用的常見流程包括：   

- 控制流程：
    - 將管線內的活動循序鏈結
    - 將管線內的活動分支
    - 參數
        - 可在管線層級定義參數，而在依需求或從觸發程序叫用管線時，可以傳遞引數。
        - 活動可以取用傳遞給管線的引數。
    - 自訂狀態傳遞
        - 活動輸出 (包括狀態) 可供管線中的後續活動取用。
    - 迴圈容器
        - For-each 
- 以觸發程序為基礎的流程
    - 可依需求或依時鐘時間觸發管線。
- 差異流程
    - 在從內部部署環境或雲端中的關聯式存放區移動維度或參考資料表，以將資料載入到 Data Lake 中時，使用參數並為差異複本定義上限標準。 

如需詳細資訊，請參閱[在 Data Factory 管道中將活動分支和鏈結](tutorial-control-flow.md)。

### <a name="deploy-ssis-packages-to-azure"></a>將 SSIS 套件部署至 Azure 
如果您想要移動 SSIS 工作負載，可以建立一個 Data Factory 第 2 版，然後佈建 Azure-SSIS 整合執行階段 (IR)。 Azure-SSIS IR 是一個完全受管理的 Azure VM (節點) 叢集，專門用來執行您的雲端 SSIS 套件。 如需逐步指示，請參閱教學課程[將 SQL Server Integration Services 套件部署至 Azure](tutorial-deploy-ssis-packages-azure.md)。 
 

### <a name="sdks"></a>SDK
如果您是進階使用者且正在尋找程式設計介面，則第 2 版提供一組豐富的 SDK，可供您使用慣用的 IDE 來撰寫、管理及監視管線。

- *.NET SDK*：針對第 2 版更新 .NET SDK。 
- *PowerShell*：針對第 2 版更新 PowerShell Cmdlet。 第 2 版 Cmdlet 的名稱中會有 **DataFactoryV2**。 例如：Get-AzureRmDataFactoryV2。 
- *Python SDK*：此 SDK 是第 2 版的新增項目。
- *REST API*：針對第 2 版更新 REST API。  

針對第 2 版更新的 SDK 不具與第 1 版用戶端的回溯相容性。 

### <a name="monitoring"></a>監視
目前，第 2 版僅支援使用 SDK 來監視資料處理站。 入口網站尚未支援監視第 2 版資料處理站。 

## <a name="load-the-data-into-a-lake"></a>將資料載入到資料湖中
Data Factory 有超過 30 種連接器，可讓您將混合式和異質環境中的資料載入到 Azure 中。 如需來自內部測試的最新效能結果和微調建議，請參閱[效能和微調指南](copy-activity-performance.md)。 

此外，我們最近已針對您安裝在私人網路環境中的自我裝載「整合執行階段」啟用「高可用性」和「延展性」。 這可解決大型第 1 層企業客戶對更佳可用性和延展性的需求。

## <a name="top-level-concepts-in-version-2"></a>第 2 版中的最上層概念
Azure 訂用帳戶可能會有一或多個 Azure Data Factory 執行個體 (或資料處理站)。 Azure Data Factory 是由四個主要元件所組成。 這些元件會一起運作，以提供平台讓您撰寫具有資料移動和轉換步驟的資料驅動工作流程。

### <a name="pipeline"></a>管線
資料處理站可以有一或多個管線。 管線是一個執行某個單位工作的活動邏輯群組。 管線中的活動會合作執行一項工作。 例如，管線可以包含一組活動，以從 Azure Blob 內嵌資料，然後對 HDInsight 叢集執行 Hive 查詢來分割資料。 

這麼做的好處是，您可以將這些活動作為一個集合來進行管線，而不是個別管理每個活動。 管線中的活動可以鏈結在一起以循序方式運作，或是以平行方式獨立運作。

### <a name="activity"></a>活動
活動代表管線中的處理步驟。 例如，您可以使用複製活動將資料從某個資料存放區複製到另一個資料存放區。 同樣地，您可以使用在 Azure HDInsight 叢集上執行 Hive 查詢的 Hive 活動，來轉換或分析您的資料。 Data Factory 支援三種類型的活動︰資料移動活動、資料轉換活動，以及控制活動。

### <a name="datasets"></a>資料集
資料集代表資料存放區中的資料結構，其只會指出或參考您要在活動中作為輸入或輸出的資料。 

### <a name="linked-services"></a>連結的服務
已連結的服務非常類似連接字串，可定義 Data Factory 連接到外部資源所需的連線資訊。 這麼說吧：連結的服務會定義與資料來源的連線，而資料集代表資料的結構。 例如，Azure 儲存體連結的服務會指定連接字串以連線到 Azure 儲存體帳戶。 此外，Azure Blob 資料集會指定包含資料的 Blob 容器和資料夾。

Data Factory 中的連結服務，有兩個用途：

- 用來代表**資料存放區**，其中包含 (但不限於) 內部部署 SQL Server 資料庫、Oracle 資料庫、檔案共用或 Azure Blob 儲存體帳戶。 如需所支援資料存放區的清單，請參閱[複製活動](copy-activity-overview.md)一文。

- 用來代表可裝載活動執行的 **計算資源** 。 例如，HDInsightHive 活動會在 HDInsight Hadoop 叢集上執行。 如需轉換活動和所支援計算環境的清單，請參閱[轉換資料](transform-data.md)一文。

### <a name="triggers"></a>觸發程序
觸發程序代表處理單位，用來決定何時需要開始執行管線。 針對不同類型的事件，有不同類型的觸發程序。 就預覽版而言，我們支援時鐘排程器觸發程序。 


### <a name="pipeline-runs"></a>管線執行
管線執行是管線執行的執行個體。 「管線執行」通常是藉由將引數傳遞給管線中定義的參數來具現化。 傳遞引數時，可以藉由手動方式傳遞，或在觸發程序定義內傳遞。

### <a name="parameters"></a>參數
參數是唯讀設定的鍵值組。  參數定義於管線中。 傳遞所定義參數的引數時，則是在從觸發程序或手動執行之管線所建立的執行內容中執行時傳遞。 管線內的活動會取用參數值。

資料集是一種強型別參數，也是可重複使用/可參考的實體。 活動可以參考資料集，而且可以取用資料集定義中所定義的屬性。

連結服務也是一種強型別參數，其中包含資料存放區或計算環境的連線資訊。 它也是可重複使用/可參考的實體。

### <a name="control-flow"></a>控制流程
控制流程是管線活動的協調流程，其中包括將活動循序鏈結、分支、在管線層級定義參數，以及在依需求或從觸發程序叫用管線時傳遞引數。 此外，也包括自訂狀態傳遞和迴圈容器，亦即 For-each 列舉程式。


如需 Data Factory 概念的詳細資訊，請參閱下列文章：

- [資料集和連結服務](concepts-datasets-linked-services.md)
- [管線和活動](concepts-pipelines-activities.md)
- [整合執行階段](concepts-integration-runtime.md)

## <a name="supported-regions"></a>支援區域

您目前可以在「美國東部」和「美國東部 2」區域建立資料處理站。 不過，Data Factory 可以存取其他 Azure 區域的資料存放區和計算資料，以在資料存放區之間移動資料或使用計算服務處理資料。

Azure Data Factory 本身不會儲存任何資料。 它可讓您建立資料導向工作流程，藉由使用其他區域或內部部署環境中的計算服務，協調所支援資料存放區之間的資料移動和資料處理。 它也可讓您使用程式設計方式和 UI 機制來監視和管理工作流程。

雖然只有「美國東部」和「美國東部 2」區域有提供 Data Factory，但為 Data Factory 中的資料移動提供技術支援的服務卻是全球數個區域中都有提供。 如果資料存放區位於防火牆後面，則會改由內部部署環境中所安裝的資料管理閘道負責移動資料。

如需範例，讓我們假設您的計算環境 (例如 Azure HDInsight 叢集和 Azure 機器學習服務) 即將用盡西歐區域的資源。 您可以在北歐建立和利用 Azure Data Factory 執行個體，並用它排程您在西歐區域之計算環境上的工作。 只要幾毫秒的時間，Data Factory 就能觸發計算環境上的作業，但執行計算環境上作業所需的時間則不會改變。

## <a name="next-steps"></a>後續步驟
依照下列快速入門中的逐步指示操作，以了解如何建立資料處理站：[PowerShell](quickstart-create-data-factory-powershell.md)[.NET](quickstart-create-data-factory-dot-net.md)[Python](quickstart-create-data-factory-python.md)[REST API](quickstart-create-data-factory-rest-api.md) 及 Azure 入口網站。 
