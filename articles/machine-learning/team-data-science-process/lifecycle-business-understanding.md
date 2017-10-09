---
title: "Team Data Science Process 生命週期的了解商務階段 - Azure | Microsoft Docs"
description: "資料科學專案了解商務階段的目標、工作和交付項目。"
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
ms.openlocfilehash: 2adce61f8185bd86a6a870bb5752fe936701b0af
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="business-understanding"></a>了解商務

本主題將概要說明與 Team Data Science Process **了解商務階段**相關聯的目標、工作和交付項目。 此流程會提供建議的生命週期，供您建構資料科學專案。 生命週期可描繪出專案一般執行時 (通常會反覆進行) 的主要階段：

* **了解商務**
* **資料取得與認知**
* **模型化**
* **部署**
* **客戶接受度**

以下是 **Team Data Science Process 生命週期**的視覺化呈現。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目標
* 指定要做為**模型目標**以及要使用誰的相關標準來判斷專案是否成功的**重要變數**。
* 找到企業有權存取或需要取得的相關**資料來源**。

## <a name="how-to-do-it"></a>作法
此階段會解決兩項主要工作︰ 

* **定義目標**︰與客戶和其他專案關係人合作，以了解並找出企業的問題。 制訂可定義商業目標和資料科學技術可鎖定的問題。
* **識別資料來源**︰尋找可協助您回答定義出專案目標之問題的相關資料。

### <a name="11-define-objectives"></a>1.1 定義目標

1. 此步驟的中心目標是要識別分析工作需要預測的重要**業務變數**。 這些變數又稱為**模型目標**，而與其相關聯的標準可用來決定專案是否成功。 這類目標的兩個範例是銷售預測或訂單被詐騙的機率。

2. 詢問並改進相關、特定且模稜兩可的「尖銳」問題以定義**專案目標**。 資料科學是使用名稱和數字來回答這類問題的程序。 如需詢問尖銳問題的詳細資訊，請參閱 [How to do Data Science](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) (如何進行資料科學) 部落格。 資料科學/機器學習服務通常可用來回答五種問題︰
 
   * 有多少？ (迴歸)
   * 什麼類別？ (分類)
   * 哪個群組？ (叢集)
   * 這很奇怪嗎？ (異常偵測)
   * 應採用哪個選項？ (建議)

    決定這些問題中要詢問哪些，以及如何解答以達到商務目標。

3. 指定專案小組成員的角色和責任以定義**專案小組**。 在發現更多資訊時，開發可逐一查看的高階里程碑計劃。  

4. **定義成功標準**。 例如︰在為期 3 個月的專案結束時，達到 X% 的客戶流失預測精確度，以便我們能提供可減少客戶流失的促銷活動。 標準必須夠**聰明 (SMART)**： 
   * **S**pecific (具體) 
   * **M**easurable (可測量)
   * **A**chievable (可達成) 
   * **R**elevant (具有相關性) 
   * **T**ime-bound (有時限) 

### <a name="12-identify-data-sources"></a>1.2 識別資料來源
識別包含已知的尖銳問題解答範例之資料來源。 尋找下列資料︰

* 與問題**相關**的資料。 我們有目標的量值和與目標相關的功能嗎？
* 可**精確測量**模型目標和感興趣之功能的資料。

舉例來說，我們常會發現現有的系統必須收集並記錄其他類型的資料，才能解決問題並達成專案目標。 在此情況下，您可能會想要尋找外部資料來源或更新系統以收集新資料。

## <a name="artifacts"></a>構件
以下是此階段的交付項目︰

* [**規範文件**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md)：TDSP 專案結構定義中會提供標準範本。 這是會變動的文件，當有新的發現以及商務需求變更時，便會在整個專案中加以更新。 關鍵在於要反覆審視這份文件，並隨著探索程序的進行，新增更多詳細資料。 讓客戶和其他專案關係人參與變更，並清楚地說明進行這些變更的原因。  
* [**資料來源**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources)：這是**資料定義**報表的**未經處理的資料來源**區段，位於 TDSP 專案的**資料報表**資料夾中。 它會指定未經處理資料的原始和目的地位置。 在後面的階段中，您會填入其他詳細資料，例如用以將資料移至分析環境的指令碼。  
* [**資料字典**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries)：本文件提供用戶端提供資料的說明。 這些說明包含結構描述的相關資訊 (資料類型、驗證規則相關資訊，如有)，以及實體關聯性圖表 (如有)。

## <a name="next-steps"></a>後續步驟

以下是 Team Data Science Process 生命週期中每個步驟的連結：

* [1.了解商務](lifecycle-business-understanding.md)
* [2.資料取得與認知](lifecycle-data.md)
* [3.模型化](lifecycle-modeling.md)
* [4.部署](lifecycle-deployment.md)
* [5.客戶接受度](lifecycle-acceptance.md)

此外也會提供完整的端對端逐步解說，說明 **特定案例** 之程序中的所有步驟。 [範例逐步解說](walkthroughs.md)主題中會列出這些逐步解說以及簡短說明的連結。 這些逐步解說說明如何將雲端、內部部署工具及服務組合成工作流程或管線，以建立智慧型應用程式。 

針對使用 Azure Machine Learning Studio 的 Team Data Science Process，如需其中執行步驟的範例，請參閱[使用 Azure ML](http://aka.ms/datascienceprocess) 學習路徑。
