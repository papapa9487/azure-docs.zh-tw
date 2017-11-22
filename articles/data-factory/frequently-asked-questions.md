---
title: "Azure Data Factory：常見問題集 | Microsoft Docs"
description: "獲得 Azure Data Factory 常見問題的解答。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: shlo
ms.openlocfilehash: 197ecff6728a7a2ce78cc6ca2861a10222a6b56b
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory 常見問題集
本文適用於第 2 版的 Azure Data Factory 服務。 本文提供 Data Factory 常見問題集的解答。  

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版常見問題集](v1/data-factory-faq.md)。

## <a name="what-is-azure-data-factory"></a>Azure 資料處理站是什麼？ 
Data Factory 是完全受管理並以雲端為基礎的資料整合服務，用來自動化資料的移動和轉換。 就像會運轉設備以將原物料轉換成成品的工廠一樣，Azure Data Factory 會協調現有的服務來收集未經處理資料，並將其轉換成隨時可用的資訊。 

您可使用 Azure Data Factory 建立資料驅動工作流程，以在內部部署與雲端資料存放區之間移動資料。 可使用 Azure HDInsight、Azure Data Lake Analytics 以及 SQL Server Integration Services (SSIS) 整合執行階段等計算服務來處理和轉換資料。 

透過 Data Factory，能夠在以 Azure 為基礎的雲端服務上執行資料處理，也可以使用自己的自我託管計算環境 (例如 SSIS、SQL Server 或 Oracle)。 建立可執行所需動作的管線之後，即可排定讓其定期執行 (例如每小時、每天或每週)，或是在事件發生時觸發管線。 如需詳細資訊，請參閱 [Azure Data Factory 簡介](introduction.md)。

## <a name="whats-different-in-version-2"></a>第 2 版有何不同？
Azure Data Factory 第 2 版是以原始 Azure Data Factory 資料移動和轉換服務為基礎，延伸至一組更廣泛的雲端優先資料整合案例。 Azure Data Factory 第 2 版提供下列功能：

- 控制流程和規模
- 在 Azure 中部署和執行 SSIS 套件

在第 1 版發行之後，我們體認到，客戶需要設計出複雜的混合式整合案例，需要在雲端、內部部署和雲端虛擬機器 (VM) 中兼顧資料的移動和處理。 這些要求帶來一項需求，亦即在受保護的虛擬網路環境內轉送和處理資料，並使用依需求提供的處理能力進行相應放大。

隨著資料管線逐漸成為商務分析策略不可或缺的一部分，我們已發現這些活動需要彈性的排程，以支援增量資料載入和事件觸發的執行。 隨著複雜性增加，要求服務支援常見工作流程範例 (包括分支、迴圈及條件式處理) 的需求也隨之增加。

在第 2 版中，您也可以將現有的 SSIS 封裝移轉到雲端。 此動作會將 SSIS 平移為在 Data Factory 內部受管理的 Azure 服務，採用了最新的整合執行階段功能。 藉由在第 2 版中運作 SSIS 整合執行階段，您可以在雲端執行、管理、監視及建置 SSIS 封裝。

### <a name="control-flows-and-scale"></a>控制流程和規模 
為了支援現代化資料倉儲中的各種整合流程和模式，Data Factory 已啟用一個不再與時間序列資料繫結的新彈性資料管線模型。 在此版本中，您可以在資料管線的控制流程中為條件式和分支建立模型，然後明確地跨越這些流程在其內部傳遞參數。

您現在可以自由地為其資料整合所需的任何流程樣式建立模型，其可依需求分派或是依排程重複分派。 幾個現在已啟用但先前無法使用的常見流程包括：   

- 控制流程：
    - 將管線內的活動循序鏈結。
    - 將管線內的活動分支。
    - 參數
        - 定義管線層級的參數並傳遞引數，同時依需求或透過觸發程序來叫用管線。
        - 活動可以取用傳遞給管線的引數。
    - 自訂狀態傳遞
        - 活動輸出 (包括狀態) 可供管線中的後續活動取用。
    - 迴圈容器
        - For-each 
- 以觸發程序為基礎的流程：
    - 可依需求或依時鐘時間觸發管線。
- 差異流程：
    - 在從內部部署環境或雲端中的關聯式存放區移動維度或參考資料表，以將資料載入到資料湖中時，使用參數並為差異複本定義上限標準標記。 

如需詳細資訊，請參閱[教學課程：控制流程](tutorial-control-flow.md)。

### <a name="deploy-ssis-packages-to-azure"></a>將 SSIS 套件部署至 Azure 
如果您想要移動 SSIS 工作負載，可以建立一個 Data Factory 第 2 版，然後佈建 Azure-SSIS 整合執行階段。 Azure-SSIS 整合執行階段是一個完全受管理的 Azure VM (節點) 叢集，專門用來執行您的雲端 SSIS 封裝。 如需逐步指示，請參閱教學課程：[將 SSIS 封裝部署至 Azure](tutorial-deploy-ssis-packages-azure.md)。 
 

### <a name="sdks"></a>SDK
如果您是進階使用者且正在尋找程式設計介面，則第 2 版提供一組豐富的 SDK，您可以使用慣用的 IDE 來撰寫、管理及監視管線。

- **.NET SDK**：針對第 2 版更新 .NET SDK。 
- **PowerShell**：針對第 2 版更新 PowerShell Cmdlet。 第 2 版 Cmdlet 的名稱中會有 *DataFactoryV2*。 例如：*Get-AzureRmDataFactoryV2*。 
- **Python SDK**：此 SDK 是第 2 版的新增項目。
- **REST API**：針對第 2 版更新 REST API。  

針對第 2 版更新的 SDK 不具與第 1 版用戶端的回溯相容性。 

### <a name="monitoring"></a>監視
目前，第 2 版支援僅使用 SDK 來監視資料處理站。 入口網站尚未支援監視第 2 版資料處理站。 

## <a name="what-is-integration-runtime"></a>什麼是整合執行階段？
整合執行階段是 Azure Data Factory 所使用的計算基礎結構，可跨各種網路環境提供下列資料整合功能：

- **資料移動**：在公用網路中的資料存放區與私人網路 (內部部署或虛擬私人網路) 中的資料存放區之間移動資料。 它支援內建的連接器、格式轉換、資料行對應，以及高效能和可調式資料轉送。
- **分派活動**：分派和監視在 Azure HDInsight、Azure Machine Learning、Azure SQL Database、SQL Server 等各種計算服務上執行的轉換活動。
- **執行 SSIS 封裝**：在受管理 Azure 計算環境中原生執行 SSIS 封裝。

您可視需要部署一或多個整合執行階段執行個體，以移動和轉換資料。 整合執行階段可以在 Azure 公用網路中執行，或是在私人網路 (內部部署、Azure 虛擬網路或 Amazon Web Services 虛擬私人雲端 [VPC]) 中執行。 

如需詳細資訊，請參閱 [Azure Data Factory 中的整合執行階段](concepts-integration-runtime.md)。

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>整合執行階段數目的限制為何？
您在資料處理站中可擁有多少個整合執行階段個體，並無硬性限制。 不過，整合執行階段可根據訂用帳戶用於 SSIS 套件封裝的虛擬機器核心數目，將會受到限制。 如需詳細資訊，請參閱 [Data Factory 限制](../azure-subscription-service-limits.md#data-factory-limits)。

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>何時使用第 2 版，而不是第 1 版？ 
如果您不熟悉 Azure Data Factory，請直接從第 2 版開始。 如果您已經使用第 1 版，則請在第 2 版上重建資料處理站。

> [!WARNING]
> 第 2 版的 Data Factory 為預覽版，且非正式運作 (GA)。 因此，它不是列在與 Data Factory 第 1 版 (其為 GA) 相同的 Azure 服務等級協定 (SLA) 承諾下。 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>第 2 版的最上層概念為何？
Azure 訂用帳戶可能會有一或多個 Azure Data Factory 執行個體 (或資料處理站)。 Azure Data Factory 是由四個重要元件所組成，這些元件會一起運作，以提供平台讓您撰寫具有資料移動和轉換步驟的資料驅動工作流程。

### <a name="pipelines"></a>管線
資料處理站可以有一或多個管線。 管線是一個執行某個單位工作的活動邏輯群組。 管線中的活動會合作執行一項工作。 例如，管線可能包含一組活動，以從 Azure Blob 內嵌資料，然後對 HDInsight 叢集執行 Hive 查詢來分割資料。 其中的優勢在於，您可使用管線按組別管理活動，而無須個別管理每個活動。 您可將管線中的活動鏈結在一起以循序方式運作，或是以平行方式獨立運作。

### <a name="activity"></a>活動
活動代表管線中的處理步驟。 例如，您可以使用*複製*活動，將資料從某個資料存放區複製到另一個資料存放區。 同樣地，您可以使用在 Azure HDInsight 叢集上執行 Hive 查詢的 Hive 活動，來轉換或分析您的資料。 Data Factory 支援三種類型的活動︰資料移動活動、資料轉換活動，以及控制活動。

### <a name="data-sets"></a>資料集
資料集代表資料存放區中的資料結構，其只會指出或參考您要在活動中作為輸入或輸出的資料。 

### <a name="linked-services"></a>連結的服務
已連結的服務非常類似連接字串，可定義 Data Factory 連接到外部資源所需的連線資訊。 這麼說吧：連結的服務會定義與資料來源的連線，而資料集代表資料的結構。 例如，Azure 儲存體連結的服務會指定連接字串以連線到 Azure 儲存體帳戶。 而且，Azure Blob 資料集會指定包含資料的 Blob 容器和資料夾。

Data Factory 中的連結服務，有兩個用途：

- 用來代表*資料存放區*，其包含 (但不限於) 內部部署 SQL Server 執行個體、Oracle 資料庫執行個體、檔案共用或 Azure Blob 儲存體帳戶。 如需支援的資料存放區清單，請參閱 [Azure Data Factory 中的複製活動](copy-activity-overview.md)。
- 用來代表可裝載活動執行的 *計算資源* 。 例如，HDInsight Hive 活動會在 HDInsight Hadoop 叢集上執行。 如需轉換活動和受支援計算環境的清單，請參閱 [Azure Data Factory 中的資料轉換](transform-data.md)。

### <a name="triggers"></a>觸發程序
觸發程序代表用來決定何時需要啟動管線執行的處理單位。 針對不同類型的事件，有不同類型的觸發程序。 就預覽版而言，我們支援時鐘排程器觸發程序。 


### <a name="pipeline-runs"></a>管線執行
管線執行是管線執行的執行個體。 通常會藉由將引數傳遞給管線中定義的參數，來具現化管線執行。 您可以藉由手動方式，或是在觸發程序定義內傳遞引數。

### <a name="parameters"></a>參數
參數是唯讀設定的機碼值組。 您可在管線中定義參數，並在執行內容的過程中傳遞已定義參數的引數。 執行內容是由觸發程序，或是您手動執行的管線所建立。 管線內的活動會取用參數值。

資料集是一種強型別參數，亦是可重複使用或參考的實體。 活動可以參考資料集，且可取用資料集定義中所定義的屬性。

連結服務亦是一種強型別參數，其包含資料存放區或計算環境的連線資訊。 其亦是可重複使用或參考的實體。

### <a name="control-flows"></a>控制流程
控制流程負責協調管線活動，其中包括將活動循序鏈結、分支，以及可在管線層級定義的參數，還有依需求或從觸發程序叫用管線時所傳遞的引數。 控制流程也包括自訂狀態傳遞和迴圈容器 (亦即 for-each 列舉程式)。


如需 Data Factory 概念的詳細資訊，請參閱下列文章：

- [資料集和連結服務](concepts-datasets-linked-services.md)
- [管線和活動](concepts-pipelines-activities.md)
- [整合執行階段](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Data Factory 的定價模型為何？
請參閱 [Data Factory 定價詳細資料](https://azure.microsoft.com/pricing/details/data-factory/)頁面，以瞭解 Azure Data Factory 的定價詳細資料。

## <a name="what-regions-support-azure-data-factory-version-2"></a>哪些區域支援 Azure Data Factory 第 2 版？
您目前可以在「美國東部」和「美國東部 2」和「西歐」區域建立第 2 版的資料處理站。 不過，資料處理站可以在另一個區域中使用整合執行階段，以在資料存放區之間移動資料、對計算服務分派活動，或分派 SSIS 封裝。 如需詳細資訊，請參閱 [Data Factory 位置](concepts-integration-runtime.md#integration-runtime-location)。

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>如何掌握 Data Factory 的最新資訊？
移至下列網站掌握 Azure Data Factory 的最新資訊：

- [部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [文件首頁](/azure/data-factory)
- [產品首頁](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>深入技術性討論 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>可否並行執行第 1 版和第 2 版管線？
否。 第 2 版與第 1 版資料處理站不可包含其他版本的實體 (例如：連結的服務、資料集或管線)。   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>是否仍需在第 2 版中定義資料集？
資料集不再是大部分活動的必要實體。 針對複製、機器學習、查閱、驗證，以及使用結構描述和資料集中其他中繼資料資訊進行轉換的自訂活動，則必須使用資料集。 其他活動不再需要使用資料集。

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>可否在第 2 版中鏈結兩個沒有資料集的活動？
是。 您可在第 2 版中將活動鏈結在一起，而不需要資料集。 在管線的 JSON 定義中使用 **dependsOn** 屬性，即可鏈結活動。 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>第 2 版支援所有第 1 版活動嗎？ 
是，第 2 版支援所有第 1 版活動。

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>如何排定第 2 版管線？ 
您可以使用排程器觸發程序來排定第 2 版管線。 觸發程序使用時鐘行事曆排程，並可讓您透過定期方式或使用以行事曆為基礎的週期性模式，來排程管線 (例如，每週星期一下午 6 時和星期四下午 9 時)。 如需詳細資訊，請參閱[管道執行和觸發程序](concepts-pipeline-execution-triggers.md)。

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>可以將參數傳遞給在第 2 版中執行的管線嗎？
是，參數是第 2 版中的第一級最上層概念。 您可定義管線層級的參數並傳遞引數，同時依需求或使用觸發程序來執行管線執行。  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>可以定義管線參數的預設值嗎？ 
是。 您可以定義管線中參數的預設值。 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>管線中的活動可否取用傳遞給管線執行的引數？ 
是。 管線內的每個活動皆可使用 `@parameter` 建構，來取用傳遞給管線執行的參數值。 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>可以在另一個活動中取用活動輸出屬性？ 
是。 可以使用 `@activity` 建構在後續活動中取用活動輸出。
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>如何正常處理活動輸出中的 Null 值？ 
您可以在運算式中使用 `@coalesce` 建構來正常處理 Null 值。 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>可否在第 2 版的活動層級使用重試和逾時？
是。 您可設定活動層級的重試和逾時，以控管第 2 版中與第 1 版類似的活動執行。 

## <a name="next-steps"></a>後續步驟
如需建立 Data Factory 第 2 版的逐步指示，請參閱下列教學課程：

- [快速入門：建立 Data Factory](quickstart-create-data-factory-dot-net.md)
- [教學課程：複製雲端中的資料](tutorial-copy-data-dot-net.md)

