---
title: "Team Data Science Process 生命週期的模型化階段 - Azure | Microsoft Docs"
description: "資料科學專案模型化階段的目標、工作和交付項目。"
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
ms.openlocfilehash: 0c855faa96d7feb9f762ecaed7654669a5a8a5b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="modeling"></a>模型化

本主題將概要說明與 Team Data Science Process **模型化階段**相關聯的目標、工作和交付項目。 此流程會提供建議的生命週期，供您建構資料科學專案。 生命週期可描繪出專案一般執行時 (通常會反覆進行) 的主要階段：

* **了解商務**
* **資料取得與認知**
* **模型化**
* **部署**
* **客戶接受度**

以下是 **Team Data Science Process 生命週期**的視覺化呈現。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目標
* 最適合機器學習服務模型的資料功能。
* 最能精確預測目標的參考 ML 模型。
* 適用於生產環境的 ML 模型。

## <a name="how-to-do-it"></a>作法
此階段會解決三項主要工作︰

* **功能設計**︰從未經處理資料建立資料功能，以便訓練模型。
* **模型訓練**︰比較各個模型的成功標準，以找出最能精確回答問題的模型。
* 判斷您的模型是否**適用於生產環境**。

### <a name="31-feature-engineering"></a>3.1 功能設計
功能設計包括納入、彙總和轉換未經處理的變數，以建立用於分析的功能。 如果您想要深入了解模型驅動因子，您必須了解功能之間的關聯方式，以及機器學習服務演算法要如何使用這些功能。 要執行此步驟，必須有創意地結合網域知識和獲取自資料瀏覽步驟的深入資訊。 這可平衡參考變數的尋找及納入，同時避免有太多不相關的變數。 參考變數可改善我們的結果；不相關的變數會對模型產生不必要的雜訊。 您也必須為評分期間所取得的任何新資料產生這些功能。 因此，這些功能的產生只會取決於評分時所能取得的資料。 如需使用不同的 Azure 資料技術時之功能設計的技術指引，請參閱[資料科學程序中的功能設計](create-features.md)。 

### <a name="32-model-training"></a>3.2 模型訓練
根據您嘗試回答的問題類型，會有許多可用的模型化演算法。 如需如何選擇演算法的指引，請參閱[如何選擇 Microsoft Azure 機器學習的演算法](../studio/algorithm-choice.md)。 雖然本文是針對 Azure Machine Learning 所撰寫，但它提供的指引可用於任何機器學習服務專案。 

模型訓練程序包括下列步驟︰ 

* 將用於模型化的**輸入資料隨機分割**成訓練資料集和測試資料集。
* 使用訓練資料集來**建置模型**。
* **評估** (訓練和測試資料集) 一系列的競爭機器學習服務演算法，以及專為使用目前資料回答感興趣問題的各種相關微調參數 (稱為參數掃掠)。
* 比較替代方法之間的成功標準，以**決定用來回答問題的「最佳」方案**。

> [!NOTE]
> **避免外洩**︰納入訓練資料集以外的資料可能會造成資料外洩，而讓模型或機器學習服務演算法做出誤以為良好的預測。 當資料科學家取得好得過頭的預測結果時，會讓他們感到不安的常見原因就是資料外洩。 這些相依性很難察覺。 為了避免外洩，通常會需要反覆審視建置分析資料集、建立模型和評估精確度的作業。 
> 
> 

我們透過 TDSP 提供[自動化模型和報告工具](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling)，其可透過多個演算法及參數掃掠來執行，以產生基準模型。 它也會產生基準模型報告，摘要說明每個模型和參數組合的效能 (包括變數重要性)。 此程序也會反覆進行，因為這可促成進一步的功能設計。 

## <a name="artifacts"></a>構件
此階段所產生的成品包括︰

* [**功能集**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets)︰**資料定義**報告的**功能集**區段會說明針對模型化所開發的功能。 它包含程式碼的指標，以產生功能以及功能產生方式的描述。
* [**模型報表**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md)：每個嘗試過的模型，都會產生標準的範本型報表，提供每項實驗的詳細資料。
* **檢查點決策**︰評估模型的效能是否良好到足以將它部署到生產系統。 需要詢問的一些重要問題是︰
  * 在給訂測試資料的情況下，模型是否有足夠的信心來回答問題？ 
  * 應該嘗試任何替代方法︰收集其他資料、進行更多功能設計，或試驗其他演算法？

## <a name="next-steps"></a>後續步驟

以下是 Team Data Science Process 生命週期中每個步驟的連結：

* [1.了解商務](lifecycle-business-understanding.md)
* [2.資料取得與認知](lifecycle-data.md)
* [3.模型化](lifecycle-modeling.md)
* [4.部署](lifecycle-deployment.md)
* [5.客戶接受度](lifecycle-acceptance.md)

此外也會提供完整的端對端逐步解說，說明 **特定案例** 之程序中的所有步驟。 [範例逐步解說](walkthroughs.md)主題中會列出這些逐步解說以及簡短說明的連結。 這些逐步解說說明如何將雲端、內部部署工具及服務組合成工作流程或管線，以建立智慧型應用程式。 

針對使用 Azure Machine Learning Studio 的 Team Data Science Process，如需其中執行步驟的範例，請參閱[使用 Azure ML](http://aka.ms/datascienceprocess) 學習路徑。 