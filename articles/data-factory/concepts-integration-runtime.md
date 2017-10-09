---
title: "Azure Data Factory 中的整合執行階段 | Microsoft Docs"
description: "了解 Azure Data Factory 中的整合執行階段。"
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
ms.date: 09/15/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: cf05a28c0fe40839e26cef1af51f3f0317e1e154
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="integration-runtime-in-azure-data-factory"></a>Azure Data Factory 中的整合執行階段
整合執行階段 (IR) 是 Azure Data Factory 所使用的計算基礎結構，可跨不同網路環境提供下列資料整合功能：

- **資料移動**：在公用網路中的資料存放區與私人網路 (內部部署或虛擬私人網路) 中的資料存放區之間移動資料。 它支援內建的連接器、格式轉換、資料行對應，以及高效能和可調式資料傳輸。
- **活動分派**：分派和監視在 Azure HDInsight、Azure Machine Learning、Azure SQL Database、SQL Server 等各種計算服務上執行的轉換活動。
- **SSIS 套件執行**：在受管理 Azure 計算環境中，以原生方式執行 SQL Server Integration Services (SSIS) 套件。


> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用 Data Factory 服務的 1 版 (正式運作版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-introduction.md)。

在 Data Factory 中，活動可定義要執行的動作。 連結服務可定義目標資料存放區或計算服務。 整合執行階段提供活動與連結服務之間的橋樑。  由連結服務所參考，並提供計算環境來執行活動或分派活動。  如此一來，就能在最接近目標資料存放區或計算服務的區域執行活動，效率最高，又滿足安全性和合規性需求。

## <a name="integration-runtime-types"></a>整合執行階段類型
Data Factory 提供三種整合執行階段，您應該選擇最符合所需之資料整合功能和網路環境需求的類型。  這三種類型為：

- Azure
- 自我裝載
- Azure-SSIS

下表描述每個整合執行階段類型的功能和網路支援：

IR 類型 | 公用網路 | 私人網路
------- | -------------- | ---------------
Azure | 資料移動<br/>活動分派 | &nbsp;
自我裝載 | 資料移動<br/>活動分派 | 資料移動<br/>活動分派
Azure-SSIS | SSIS 套件執行 | SSIS 套件執行

下圖顯示如何結合使用不同的整合執行階段，以提供豐富的資料整合功能和網路支援：

![不同類型的整合執行階段](media\concepts-integration-runtime\different-integration-runtimes.png)


## <a name="azure-integration-runtime"></a>Azure 整合執行階段
Azure 整合執行階段能夠：

- 在雲端資料存放區之間執行複製活動
- 在公用網路中分派下列轉換活動：HDInsight Hive 活動、HDInsight Pig 活動、HDInsight MapReduce 活動、HDInsight Spark 活動、HDInsight 串流活動、Machine Learning 批次執行活動、Machine Learning 更新資源活動、預存程序活動、Data Lake Analytics U-SQL 活動、.Net 自訂活動、網路活動、查閱活動，以及取得中繼資料活動。

### <a name="network-environment"></a>網路環境
在具有公開存取端點的公用網路中，Azure 整合執行階段支援連線至其中的資料存放區和計算服務。 在 Azure 虛擬網路環境中使用自我裝載整合執行階段。

### <a name="compute-resource-and-scaling"></a>計算資源和調整規模
Azure 整合執行階段在 Azure 中提供完整受管理的無伺服器計算。  您不必擔心基礎結構佈建、軟體安裝、修補或容量大小調整。  此外，您只需支付實際使用時間。

Azure 整合執行階段提供原生計算，能夠以安全、可靠且高效能的方式，在雲端資料存放區之間移動資料。  您可以設定要在複製活動上使用的資料移動單位數量，Azure IR 的計算大小會很有彈性地相應增加，您不必明確地調整 Azure 整合執行階段的大小。

活動分派是輕量型的作業，可將活動路由傳送至目標計算服務，所以在此情節中，不需要相應增加計算大小。

如需有關建立及設定 Azure IR 的資訊，請參閱操作說明指南中的＜如何建立和設定 Azure IR＞。 

## <a name="self-hosted-integration-runtime"></a>自我裝載整合執行階段
自我裝載 IR 能夠：

- 在雲端資料存放區和私人網路中的資料存放區之間執行複製活動。
- 在內部部署或 Azure 虛擬網路中，針對計算資源分派下列轉換活動：HDInsight Hive 活動 (BYOC)、HDInsight Pig 活動 (BYOC)、HDInsight MapReduce 活動 (BYOC)、HDInsight Spark 活動 (BYOC)、HDInsight 串流活動 (BYOC)、Machine Learning 批次執行活動、Machine Learning 更新資源活動、預存程序活動、Data Lake Analytics U-SQL 活動、.Net 自訂活動、查閱活動，以及取得中繼資料活動。

> [!NOTE] 
> 使用自我裝載整合執行階段來支援需要自備驅動程式 (例如 SAP Hana、MySQL 等) 的資料存放區。如需詳細資訊，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。

### <a name="network-environment"></a>網路環境
如果想要在私人網路環境 (無法從公用雲端環境直接存取) 中安全地執行資料整合，您可以在內部部署環境、公司防火牆後方或虛擬私人網路內安裝自我裝載 IR。  自我裝載整合執行階段對於開放網際網路，只會建立以 HTTP 為基礎的輸出連線。

### <a name="compute-resource-and-scaling"></a>計算資源和調整規模
自我裝載 IR 必須安裝在私人網路內的內部部署機器或虛擬機器上。 目前，我們只支援在 Windows 作業系統上執行自我裝載 IR。  

若要達到高可用性和延展性，您可以在主動-主動模式下，將邏輯執行個體和多個內部部署機器產生關聯，以相應放大自我裝載 IR。  如需詳細資訊，請參閱操作說明指南中的＜如何建立和設定自我裝載 IR＞主題。

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS 整合執行階段
若要隨即轉移現有的 SSIS 工作負載，您可以建立 Azure-SSIS IR，以原生方式執行 SSIS 套件。

### <a name="network-environment"></a>網路環境
Azure-SSIS IR 可以佈建在公用網路或私人網路中。  將 Azure-SSIS IR 加入已連線至內部網路的虛擬網路 (VNet)，即可支援內部部署資料存取。 目前僅支援傳統 VNet。 

### <a name="compute-resource-and-scaling"></a>計算資源和調整規模
Azure-SSIS IR 是一個受到完整管理的 Azure VM 叢集，專門用來執行您的 SSIS 套件。 您可以自備 Azure SQL Database 或受管理的執行個體 (私人預覽) 伺服器，以裝載要附加至伺服器的 SSIS 專案/套件 (SSISDB) 目錄。 指定節點大小可以相應增加計算能力，指定叢集的節點數目可以相應放大計算能力。 您可以依需求來停止和啟動 Azure-SSIS 整合執行階段，以掌控其執行成本。

如需詳細資訊，請參閱操作說明指南中的＜如何建立和設定 Azure-SSIS IR＞主題。  建立之後，您可以使用熟悉的工具，例如 SQL Server Data Tools (SSDT) 和 SQL Server Management Studio (SSMS)，就像在內部部署環境中使用 SSIS 一樣，不太需要變更就能部署和管理現有的 SSIS 套件。

## <a name="determining-which-ir-to-use"></a>決定使用哪一個 IR
每個轉換活動都有一個指向整合執行階段的目標計算「連結服務」。 轉換活動就是從這個整合執行階段執行個體分派而來。

若是複製活動，需要來源和接收連結服務來定義資料流程的方向。 下列邏輯可決定使用哪個整合執行階段執行個體來執行複製： 

- **在兩個雲端資料來源之間複製**：當來源和接收連結服務都使用 Azure IR 時，就會使用接收連結服務所用的整合執行階段來執行複製活動。
- **在雲端資料來源與私人網路中的資料來源之間複製**：如果任一來源或接收連結服務指向自我裝載 IR，則會在自我裝載整合執行階段上執行複製活動。
- **在私人網路中的兩個資料來源之間複製**：來源和接收連結服務都必須指向相同的整合執行階段執行個體，而該整合執行階段會用來執行複製活動。

下圖顯示兩個複製活動範例：

- 對於複製活動 1，其來源為參考自我裝載 IR A 的 SQL Server 連結服務，而其接收為參考 Azure IR B 的 Azure 儲存體連結服務。當複製活動執行時，它會在自我裝載 IR A 上執行。
- 對於複製活動 2，其來源為參考 Azure IR C 的 Azure SQL Server Database 連結服務，而其接收為參考 Azure IR B 的 Azure 儲存體連結服務。當複製活動執行時，它會在 Azure IR B 上執行，因為這是接收連結服務所用的整合執行階段。

![使用哪一個 IR](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>整合執行階段位置
Data Factory 位置中儲存資料處理站的中繼資料，也是觸發管道的源頭。 目前支援的 Data Factory 位置為：美國東部、美國東部 2。 不過，Data Factory 可以存取其他 Azure 區域的資料存放區和計算資料，以在資料存放區之間移動資料或使用計算服務處理資料。 此行為是透過多個區域通用的 IR 來達成，可確保資料合規性、效率，並降低網路輸出成本。

「IR 位置」定義其後端計算的位置，基本上還會定義執行資料移動、活動分派和 SSIS 套件執行的位置。 IR 位置及其所屬的資料處理站位置可能不同。 下圖顯示 Data Factory 及其整合執行階段的位置設定：

![整合執行階段位置](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>Azure IR
Data Factory 會使用相同地理位置中最接近接收之區域中的 Azure IR 來移動資料。 如需對應資訊，請參閱下表︰

接收資料存放區的地理位置 | 接收資料存放區的位置 | 用於 Azure 整合執行階段的位置
-------------------------------| ----------------| ------------------
美國 | 美國東部 | 美國東部
&nbsp; | 美國東部 2 | 美國東部 2
&nbsp; | 美國中部 | 美國中部
&nbsp; | 美國中北部 | 美國中北部
&nbsp; | 美國中南部 | 美國中南部
&nbsp; | 美國中西部 | 美國中西部
&nbsp; | 美國西部 | 美國西部
&nbsp; | 美國西部 2 | 美國西部
加拿大 | 加拿大東部 | 加拿大中部
&nbsp; | 加拿大中部 | 加拿大中部
巴西 | 巴西南部 | 巴西南部
歐洲 | 北歐 | 北歐
&nbsp; | 西歐 | 西歐
英國 | 英國西部 | 英國南部
&nbsp; | 英國南部 | 英國南部
亞太地區 | 東南亞 | 東南亞
&nbsp; | 東亞 | 東南亞
澳大利亞 | 澳洲東部 | 澳洲東部
&nbsp; | 澳大利亞東南部 | 澳大利亞東南部
日本 | 日本東部 | 日本東部
&nbsp; | 日本西部 | 日本東部
韓國 | 韓國中部 | 韓國南部
&nbsp; | 韓國南部 | 韓國南部
印度 | 印度中部 | 印度中部
&nbsp; | 印度西部 | 印度中部
&nbsp; | 印度南部 | 印度中部

您也可以將 Azure IR 的位置設定為自動解析，這表示 Data Factory 會根據連結服務定義，盡力自動偵測最佳位置來使用。

> [!NOTE] 
> 如果目的地資料存放區的區域不在清單中，或無法偵測到，則活動會失敗，而不會基於合規性理由而進入替代區域。 在此情況下，請明確指出用來執行複製的替代位置。
 
下圖顯示 Azure IR 的位置設定為自動解析時的有效位置範例。 複製活動執行時會偵測資料目的地的位置，在此範例中是日本西部。  根據此表格，日本東部的一個 Azure IR 用來執行實際複製資料。 使用相同的 IR 連線至 HDInsight 來執行 Spark 活動時，將會從 Data Factory 位置執行 Spark 應用程式提交，在此範例中是美國東部，而 Spark 應用程式實際上是在 HDInsight 伺服器位置上執行。 

![有效的位置](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>自我裝載 IR
自我裝載 IR 在邏輯上會向 Data Factory 註冊，而用來支援其功能的計算由您提供。 因此，自我裝載 IR 沒有明確的位置屬性。 

用來執行資料移動時，自我裝載 IR 會從來源取出資料，並寫入目的地。

### <a name="azure-ssis-ir"></a>Azure-SSIS IR
為了在擷取、轉換和下載 (ETL) 工作流程中達到高效能，務必選取正確的 Azure-SSIS IR 位置。  最初有兩個位置可供預覽 (美國東部和北歐)。

- Azure-SSIS IR 的位置並不需要與資料處理站的位置相同，但應該與您自己的 Azure SQL Database/受管理的執行個體 (私人預覽中) 伺服器 (要裝載 SSISDB) 的位置相同。 如此一來，您的 Azure-SSIS 整合執行階段就可以輕易存取 SSISDB，而不會在不同的位置之間產生過多流量。
- 如果您沒有現有的 Azure SQL Database/受管理的執行個體 (私人預覽) 伺服器來裝載 SSISDB，但有內部部署資料來源/目的地，您應該在已連線至內部部署網路之 VNet 的相同位置中，建立新的 Azure SQL Database/受管理的執行個體 (私人預覽) 伺服器。  如此一來，您就可以使用新的 Azure SQL Database/受管理的執行個體 (私人預覽) 伺服器並加入該 VNet，以建立 Azure-SSIS IR，全部都在相同的位置中，能儘量避免在不同位置之間移動資料。
- 如果要裝載 SSISDB 的現有 Azure SQL Database/受管理的執行個體 (私人預覽) 伺服器的位置，與連線至內部部署網路的 VNet 的位置不相同，請先在相同位置使用現有的 Azure SQL Database/受管理的執行個體 (私人預覽) 伺服器並加入另一個 VNet，以建立您的 Azure-SSIS IR，然後設定不同位置之間的 VNet 對 VNet 連線。


## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [建立自我裝載整合執行階段](create-self-hosted-integration-runtime.md)

