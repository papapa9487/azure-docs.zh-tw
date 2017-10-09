---
title: "Team Data Science Process 生命週期的資料取得與認知階段 - Azure | Microsoft Docs"
description: "資料科學專案資料取得與認知階段的目標、工作和交付項目。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: eea3c357ebf6ad920c0ddebdb979aa07aece4794
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="data-acquisition-and-understanding"></a>資料取得與認知

本主題將概要說明與 Team Data Science Process **資料取得與認知階段**相關聯的目標、工作和交付項目。 此流程會提供建議的生命週期，供您建構資料科學專案。 生命週期可描繪出專案一般執行時 (通常會反覆進行) 的主要階段：

* **了解商務**
* **資料取得與認知**
* **模型化**
* **部署**
* **客戶接受度**

以下是 **Team Data Science Process 生命週期**的視覺化呈現。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目標
* 全新的高品質資料集，已了解其與目標變數的關聯，且變數位於合適的分析環境中，已可供建立模型。
* 已開發出用以定期重新整理資料並加以評分之資料管線的方案架構。

## <a name="how-to-do-it"></a>作法
此階段會解決三項主要工作︰

* **內嵌資料**到目標分析環境。
* **瀏覽資料**來判斷資料品質是否適合用來回答問題。 
* **設定資料管線**來對新資料或定期重新整理的資料進行評分。

### <a name="21-ingest-the-data"></a>2.1 內嵌資料
設定程序以將資料從來源位置移到目標位置，以在其中執行訓練和預測等分析作業。 如需如何使用各種 Azure 資料服務來執行此作業的技術詳細資料和選項，請參閱[將資料載入至儲存體環境以便進行分析](ingest-data.md)。 

### <a name="22-explore-the-data"></a>2.2 探索資料
在訓練模型之前，您必須全面了解資料。 真實的資料集通常會有雜訊、遺漏值或具有許多不一致之處。 資料摘要和視覺效果可用來稽核資料的品質，並提供所需資訊來處理資料以便讓資料準備好建立模型。 此程序通常會反覆進行。

TDSP 提供了自動化公用程式 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)，以協助將資料視覺化及準備資料的摘要報告。 我們建議您先從 IDEAR 來瀏覽資料，以協助您在不必編寫程式碼的情況下，以互動方式來初步了解資料，然後撰寫自訂程式碼來探索資料和加以視覺化。 如需清理資料的指引，請參閱[準備增強機器學習服務的資料的工作](prepare-data.md)。  

您滿意清理過之資料的品質後，下一個步驟是深入了解資料中固有的模式，以幫助您選擇及開發用於目標的適當預測模型。 尋找資料和目標關聯程度的證據，以及是否有足夠資料可供繼續進行下一個模型化步驟。 同樣地，此程序通常會反覆進行。 您可能需要尋找具有更精確或更相關資料的新資料來源，以擴大前一個階段中所初步找到的資料集。  

### <a name="23-set-up-a-data-pipeline"></a>2.3 設定資料管線
除了一開始要內嵌和清理資料外，您通常還需要設定用來對新資料評分或定期重新整理資料的程序，以做為持續學習程序的一部分。 這可以透過設定資料管線或工作流程來完成。 以下是如何使用 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 設定管線的[範例](move-sql-azure-adf.md)。 

這個階段會開發資料管線的方案架構。 資料科學專案的下列階段也會平行開發管線。 根據業務需求和此方案將整合到之現有系統的條件約束，管線可能是批次型、串流/即時或混合式。 

## <a name="artifacts"></a>構件
以下是此階段的交付項目。

* [**資料品質報告**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md)︰這份報告包含資料摘要、每個屬性與目標之間的關聯性以及變數排名等等。TDSP 中所提供的 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) 工具，可在任何表格式資料集 (例如 CSV 檔案或關聯式資料表) 上快速產生這份報告。 
* **方案架構**︰在您建置模型後，這可以是用來對新資料執行評分或預測之資料管線的圖表或描述。 它也包含可根據新資料重新訓練模型的管線。 在使用 TDSP 目錄結構範本時，此文件會儲存在這個[專案](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project)目錄。
* **檢查點決策**︰在完整的功能設計和模型建置開始之前，您可以重新評估專案，以判斷預期值是否足以繼續追求此專案。 比方說，您可能已準備好繼續、需要收集更多資料，或是因為不存在可以回答問題的資料而放棄專案。

## <a name="next-steps"></a>後續步驟

以下是 Team Data Science Process 生命週期中每個步驟的連結：

* [1.了解商務](lifecycle-business-understanding.md)
* [2.資料取得與認知](lifecycle-data.md)
* [3.模型化](lifecycle-modeling.md)
* [4.部署](lifecycle-deployment.md)
* [5.客戶接受度](lifecycle-acceptance.md)

此外也會提供完整的端對端逐步解說，說明 **特定案例** 之程序中的所有步驟。 [範例逐步解說](walkthroughs.md)主題中會列出這些逐步解說以及簡短說明的連結。 這些逐步解說說明如何將雲端、內部部署工具及服務組合成工作流程或管線，以建立智慧型應用程式。  

針對使用 Azure Machine Learning Studio 的 Team Data Science Process，如需其中執行步驟的範例，請參閱[使用 Azure ML](http://aka.ms/datascienceprocess) 學習路徑。
