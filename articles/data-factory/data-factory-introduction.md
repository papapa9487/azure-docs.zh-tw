---
title: "Data Factory (資料整合服務) 簡介 | Microsoft Docs"
description: "了解 Azure Data Factory 是什麼：一項雲端資料整合服務，用來協調以及自動移動和轉換資料。"
keywords: "資料整合、雲端資料整合、azure data factory 是什麼"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 537bdee67ed9648c3cba2099553d847399609705
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017

---
# <a name="introduction-to-azure-data-factory"></a>Azure Data Factory 簡介 
## <a name="what-is-azure-data-factory"></a>Azure 資料處理站是什麼？
在巨量資料的世界裡，現有資料是如何運用在商業經營上？ 是否有可能使用內部部署資料來源或其他不同資料來源的資料，來擴充雲端所產生的資料？ 例如，遊戲公司會收集雲端遊戲所產生的許多記錄。 其想要分析這些記錄，以深入瞭解客戶的喜好設定、族群結構特徵、使用行為等，以發掘向上銷售與交叉銷售機會，開發新的強大功能，以期推動業務成長並為客戶提供更好的體驗。 

若要分析這些記錄，公司必須使用參考資料，例如內部部署資料存放區中的客戶資訊、遊戲資訊、行銷活動資訊。 因此，公司想要從雲端資料存放區內嵌記錄資料，以及從內部部署資料存放區內嵌參考資料。 然後，使用雲端的 Hadoop (Azure HDInsight) 來處理資料並將結果資料發佈至雲端資料倉儲 (例如 Azure SQL 資料倉儲) 或內部部署資料存放區 (例如 SQL Server)。 公司想要這個工作流程每週執行一次。 

公司需要一個平台來建立可從內部部署和雲端資料存放區內嵌資料的工作流程，以及使用現有計算服務 (例如 Hadoop) 來轉換或處理資料，並將結果發佈至內部部署或雲端資料存放區，以供 BI 應用程式使用。 

![Data Factory 概觀](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory 是這種案例的平台。 這是一項**雲端式資料整合服務，可讓您在雲端建立資料驅動工作流程，以便協調及自動進行資料移動和資料轉換**。 使用 Azure Data Factory，您可以建立和排程資料驅動工作流程 (稱為管線)，這類工作流程可以從不同資料存放區內嵌資料，使用計算服務 (例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure Machine Learning) 來處理/轉換資料，以及將輸出資料發佈至資料存放區 (例如 Azure SQL 資料倉儲)，以供商業智慧 (BI) 應用程式使用。  

它與其說是傳統的擷取-轉換-和-載入 (ETL) 平台，還不如說是擷取並載入 (EL) 而後轉換並載入 (TL) 平台。 執行的轉換是為了使用計算服務來轉換/處理資料，而不是為了執行諸如新增衍生資料行、計算資料列數、排序資料等轉換作業。 

目前在 Azure Data Factory 中，工作流程所取用和產生的資料是**時間配量資料** (每小時、每天、每週等等)。 例如，管線可以讀取輸入資料、處理資料，以及每天產生一次輸出資料。 您也可以只執行一次工作流程。  
  

## <a name="how-does-it-work"></a>運作方式 
Azure Data Factory 中的管線 (資料驅動工作流程) 通常會執行下列三個步驟︰

![Azure Data Factory 的三個階段](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>連線及收集
企業會有位於不同來源的各類型資料。 建置資訊生產系統的第一步，就是連線到所有必要的資料和處理來源 (例如 SaaS 服務、檔案共用、FTP、Web 服務)，然後視需要將資料移動到集中位置以進行後續處理。

沒有 Data Factory，企業必須建置自訂的資料移動元件或撰寫自訂服務，以整合這些資料來源和處理。 此類系統不僅昂貴，而且難以整合和維護，且通常缺乏企業等級的監視和警示功能，以及完全受管理的服務所能提供的控制力。

有了 Data Factory，您就可以使用資料管線中的複製活動，將內部部署和雲端來源資料存放區內的資料全都移動到雲端中的集中資料存放區，以供進一步分析。 例如，您可以收集 Azure Data Lake Store 中的資料，之後使用 Azure Data Lake Analytics 計算服務來轉換資料。 或者，收集 Azure Blob 儲存體中的資料，之後使用 Azure HDInsight Hadoop 叢集來轉換資料。

### <a name="transform-and-enrich"></a>轉換及擴充
資料存在於雲端的集中式資料存放區之後，您想要使用計算服務 (例如 HDInsight Hadoop、Spark、Data Lake Analytics 和 Machine Learning) 來處理或轉會所收集的資料。 您想要在可維護且可控制的排程中可靠地產生轉換的資料，以將信任的資料饋送至生產環境。 

### <a name="publish"></a>發佈 
提供從雲端到內部部署來源 (如 SQL Server) 的轉換資料，或將此資料留在雲端儲存來源中，以供商業智慧 (BI) 及分析工具和其他應用程式取用。

## <a name="key-components"></a>重要元件
Azure 訂用帳戶可能會有一或多個 Azure Data Factory 執行個體 (或資料處理站)。 Azure Data Factory 是由四個重要元件所組成，這些元件會一起運作，以提供平台讓您撰寫具有資料移動和轉換步驟的資料驅動工作流程。 

### <a name="pipeline"></a>管線
資料處理站可以有一或多個管線。 管線是一組活動。 管線中的活動會合作執行一項工作。 例如，管線可能包含一組活動，以從 Azure Blob 內嵌資料，然後對 HDInsight 叢集執行 Hive 查詢來分割資料。 這麼做的好處是，您可以將這些活動作為一個集合來進行管線，而不是個別管理每個活動。 例如，您可以部署管線並為其排程，而非獨立地對每個活動進行這些工作。 

### <a name="activity"></a>活動
管線可以有一或多個活動。 活動會定義在您資料上執行的動作。 例如，您可以使用複製活動將資料從某個資料存放區複製到另一個資料存放區。 同樣地，您可以使用在 Azure HDInsight 叢集上執行 Hive 查詢的 Hive 活動，來轉換或分析您的資料。 Data Factory 支援兩種活動類型︰資料移動活動和資料轉換活動。

### <a name="data-movement-activities"></a>資料移動活動
Data Factory 中的複製活動會將資料從來源資料存放區複製到接收資料存放區。 Data Factory 支援下列資料存放區。 可將來自任何來源的資料寫入任何接收器。 按一下資料存放區，即可了解如何將資料複製到該存放區，以及從該存放區複製資料。

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

如需詳細資訊，請參閱[資料移動活動](data-factory-data-movement-activities.md)文章。

### <a name="data-transformation-activities"></a>資料轉換活動
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

如需詳細資訊，請參閱[資料轉換活動](data-factory-data-transformation-activities.md)文章。

### <a name="custom-net-activities"></a>自訂 .NET 活動
如果您需要將資料移入/移出「複製活動」不支援的資料存放區，或使用您自己的邏輯轉換資料，請建立 **自訂 .NET 活動**。 如需有關建立及使用自訂活動的詳細資料，請參閱 [在 Azure Data Factory 管線中使用自訂活動](data-factory-use-custom-activities.md)。

### <a name="datasets"></a>資料集
活動可取得零或多個資料集作為輸入，並取得一或多個資料集作為輸出。 資料集代表資料存放區中的資料結構，其只會指出或參考您要在活動中作為輸入或輸出的資料。 例如，Azure Blob 資料集會指定管線應從中讀取資料之「Azure Blob 儲存體」中的 Blob 容器和資料夾。 或者，「Azure SQL 資料表」資料集會指定要由活動寫入輸出資料的資料表。 

### <a name="linked-services"></a>連結的服務
已連結的服務非常類似連接字串，可定義 Data Factory 連接到外部資源所需的連線資訊。 這麼說吧：連結的服務會定義與資料來源的連線，而資料集代表資料的結構。 例如，Azure 儲存體連結的服務會指定連接字串以連線到 Azure 儲存體帳戶。 而且，Azure Blob 資料集會指定包含資料的 Blob 容器和資料夾。   

Data Factory 中的連結服務，有兩個用途：

* 用來代表 **資料存放區** ，其中包含 (但不限於) 內部部署 SQL Server、Oracle 資料庫、檔案共用或 Azure Blob 儲存體帳戶。 如需支援的資料存放區清單，請參閱 [資料移動活動](#data-movement-activities) 一節。
* 用來代表可裝載活動執行的 **計算資源** 。 例如，HDInsightHive 活動會在 HDInsight Hadoop 叢集上執行。 如需支援的計算環境清單，請參閱[資料轉換活動](#data-transformation-activities)一節。

### <a name="relationship-between-data-factory-entities"></a>Data Factory 實體之間的關聯性
![圖表︰Data Factory 概觀 (雲端資料整合服務) - 重要概念](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**圖 2.** 資料集、活動、管線和連結服務之間的關聯性。

## <a name="supported-regions"></a>支援區域
您目前可以在 [美國西部]、[美國東部] 和 [北歐] 區域建立 Data Factory。 不過，Data Factory 可以存取其他 Azure 區域的資料存放區和計算資料，以在資料存放區之間移動資料或使用計算服務處理資料。

Azure Data Factory 本身不會儲存任何資料。 它可讓您建立資料導向工作流程，藉由使用其他區域或內部部署環境中的[計算服務](#data-transformation-activities)，協調[所支援資料存放區](#data-movement-activities)之間的資料移動和資料處理。 它也可讓您使用程式設計方式和 UI 機制 [監視和管理工作流程](data-factory-monitor-manage-pipelines.md) 。

即使 Azure Data Factory 只能在**美國西部**、**美國東部**和**北歐**區域使用，但 Data Factory 中支援資料移動的服務可在[全球](data-factory-data-movement-activities.md#global)好幾個區域中使用。 如果資料存放區位於防火牆後面，則會改由內部部署環境中所安裝的[資料管理閘道](data-factory-move-data-between-onprem-and-cloud.md)移動資料。

如需範例，讓我們假設您的計算環境 (例如 Azure HDInsight 叢集和 Azure 機器學習服務) 即將用盡西歐區域的資源。 您可以在北歐建立和利用 Azure Data Factory 執行個體，並用它排程您在西歐區域之計算環境上的工作。 只要幾毫秒的時間，Data Factory 就能觸發計算環境上的作業，但執行計算環境上作業所需的時間則不會改變。

## <a name="get-started-with-creating-a-pipeline"></a>開始建立管線
您可以使用上述其中一項工具或 API，在 Azure Data Factory 中建立管線： 

- Azure 入口網站
- Visual Studio
- PowerShell
- .NET API
- REST API
- Azure Resource Manager 範本。 

若要了解如何建置具有資料管線的 Data Factory，請遵循下列教學課程中的逐步指示：

| 教學課程 | 說明 |
| --- | --- |
| [在兩個雲端資料存放區之間移動資料](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |在本教學課程中，您會建立具有管線的 Data Factory，以從 Blob 儲存體 **移動資料** 至 SQL Database。 |
| [使用 Hadoop 叢集轉換資料](data-factory-build-your-first-pipeline.md) |在本教學課程中，您會在 Azure HDInsight (Hadoop) 叢集上執行 Hive 指令碼，以建立您的第一個 Azure Data Factory 與用來 **處理資料** 的資料管線。 |
| [使用資料管理閘道，在內部部署資料存放區與雲端資料存放區之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) |在本教學課程中，您會建置具有管線的 Data Factory，以從**內部部署** SQL Server 資料庫**移動資料**至 Azure Blob。 在逐步解說中，您會在電腦上安裝及設定資料管理閘道。 |

