---
title: "Team Data Science Process 生命週期 | Microsoft Docs"
description: "執行資料科學專案所需的步驟。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: bradsev;gokuma
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e2519631ddfabfa5cad57a6811489f3b213313d7
ms.contentlocale: zh-tw
ms.lasthandoff: 07/21/2017

---
# <a name="team-data-science-process-lifecycle"></a>Team Data Science Process 生命週期

Team Data Science Process (TDSP) 會提供建議的生命週期供您建構資料科學專案的開發。 生命週期會概述專案在執行時通常會遵循之從開始到完成的步驟。 如果您正在使用另一個資料科學生命週期 (例如 [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)、[KDD](https://wikipedia.org/wiki/Data_mining#Process) 或您組織本身的自訂程序)，您仍可在這些開發生命週期的內容中使用工作型 TDSP。 

此生命週期是針對要在智慧型應用程式中隨附的資料科學專案所設計。 這些應用程式會部署機器學習服務或人工智慧模型來做預測性分析。 探勘資料科學專案以及臨機操作或開關式分析專案也可因使用此程序而受益，但在這類案例中，可能就不需要本文所述的某些步驟。    

以下是 **Team Data Science Process 生命週期**的視覺化呈現。 

![TDSP-Lifecycle](./media/data-science-process-overview/tdsp-lifecycle.png) 

TDSP 生命週期是由反覆執行的五個主要階段所組成。 其中包含：

* **了解商務**
* **資料取得與認知**
* **模型化**
* **部署**
* **客戶接受度**

對於每個階段，我們會提供下列資訊：

* **目標**︰特定的目標。
* **作法**︰概述的特定工作和所提供的完成指引。
* **成品**：交付項目和其生產支援。


## <a name="1-business-understanding"></a>1.了解商務

### <a name="goals"></a>目標
* 指定要做為**模型目標**以及要使用誰的相關標準來判斷專案是否成功的**重要變數**。
* 找到企業有權存取或需要取得的相關**資料來源**。

### <a name="how-to-do-it"></a>作法
此階段會解決兩項主要工作︰ 

* **定義目標**︰與客戶和其他專案關係人合作，以了解並找出企業的問題。 制訂可定義商業目標和資料科學技術可鎖定的問題。
* **識別資料來源**︰尋找可協助您回答定義出專案目標之問題的相關資料。

#### <a name="11-define-objectives"></a>1.1 定義目標

1. 此步驟的中心目標是要識別分析工作需要預測的重要**業務變數**。 這些變數又稱為**模型目標**，而與其相關聯的標準可用來決定專案是否成功。 這類目標的兩個範例是銷售預測或訂單被詐騙的機率。

2. 詢問並改進相關、特定且模稜兩可的「尖銳」問題以定義**專案目標**。 資料科學是使用名稱和數字來回答這類問題的程序。 如需詢問尖銳問題的其他指引，請參閱 [How to do Data Science](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) (如何進行資料科學) 部落格。 資料科學/機器學習服務通常可用來回答五種問題︰
 
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

#### <a name="12-identify-data-sources"></a>1.2 識別資料來源
識別包含已知的尖銳問題解答範例之資料來源。 尋找下列資料︰

* 與問題**相關**的資料。 我們有目標的量值和與目標相關的功能嗎？
* 可**精確測量**模型目標和感興趣之功能的資料。

舉例來說，我們常會發現現有的系統必須收集並記錄其他類型的資料，才能解決問題並達成專案目標。 在此情況下，您可能會想要尋找外部資料來源或更新系統以收集新資料。

### <a name="artifacts"></a>構件
以下是此階段的交付項目︰

* [**規範文件**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md)：TDSP 專案結構定義中會提供標準範本。 這是會變動的文件，當有新的發現以及商務需求變更時，便會在整個專案中加以更新。 關鍵在於要反覆審視這份文件，並隨著探索程序的進行，新增更多詳細資料。 讓客戶和其他專案關係人參與變更，並清楚地說明進行這些變更的原因。  
* [**資料來源**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources)：這是**資料定義**報表的**未經處理的資料來源**區段，位於 TDSP 專案的**資料報表**資料夾中。 它會指定未經處理資料的原始和目的地位置。 在後面的階段中，您會填入其他詳細資料，例如用以將資料移至分析環境的指令碼。  
* [**資料字典**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries)：本文件提供用戶端提供資料的說明。 這些說明包含結構描述的相關資訊 (資料類型、驗證規則相關資訊，如有)，以及實體關聯性圖表 (如有)。


## <a name="2-data-acquisition-and-understanding"></a>2.資料取得與認知

### <a name="goals"></a>目標
* 全新的高品質資料集，已了解其與目標變數的關聯，且變數位於合適的分析環境中，已可供建立模型。
* 已開發出用以定期重新整理資料並加以評分之資料管線的方案架構。

### <a name="how-to-do-it"></a>作法
此階段會解決三項主要工作︰

* **內嵌資料**到目標分析環境。
* **瀏覽資料**來判斷資料品質是否適合用來回答問題。 
* **設定資料管線**來對新資料或定期重新整理的資料進行評分。

#### <a name="21-ingest-the-data"></a>2.1 內嵌資料
設定程序以將資料從來源位置移到目標位置，以在其中執行訓練和預測等分析作業。 如需如何使用各種 Azure 資料服務來執行此作業的技術詳細資料和選項，請參閱[將資料載入至儲存體環境以便進行分析](machine-learning-data-science-ingest-data.md)。 

#### <a name="22-explore-the-data"></a>2.2 探索資料
在訓練模型之前，您必須全面了解資料。 真實的資料集通常會有雜訊、遺漏值或具有許多不一致之處。 資料摘要和視覺效果可用來稽核資料的品質，並提供所需資訊來處理資料以便讓資料準備好建立模型。 此程序通常會反覆進行。

TDSP 提供了自動化公用程式 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)，以協助將資料視覺化及準備資料的摘要報告。 我們建議您先從 IDEAR 來瀏覽資料，以協助您在不必編寫程式碼的情況下，以互動方式來初步了解資料，然後撰寫自訂程式碼來探索資料和加以視覺化。 如需清理資料的指引，請參閱[準備增強機器學習服務的資料的工作](machine-learning-data-science-prepare-data.md)。  

您滿意清理過之資料的品質後，下一個步驟是深入了解資料中固有的模式，以幫助您選擇及開發用於目標的適當預測模型。 尋找資料和目標關聯程度的證據，以及是否有足夠資料可供繼續進行下一個模型化步驟。 同樣地，此程序通常會反覆進行。 您可能需要尋找具有更精確或更相關資料的新資料來源，以擴大前一個階段中所初步找到的資料集。  

#### <a name="23-set-up-a-data-pipeline"></a>2.3 設定資料管線
除了一開始要內嵌和清理資料外，您通常還需要設定用來對新資料評分或定期重新整理資料的程序，以做為持續學習程序的一部分。 這可以透過設定資料管線或工作流程來完成。 以下是如何使用 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 設定管線的[範例](machine-learning-data-science-move-sql-azure-adf.md)。 

這個階段會開發資料管線的方案架構。 資料科學專案的下列階段也會平行開發管線。 根據業務需求和此方案將整合到之現有系統的條件約束，管線可能是批次型、串流/即時或混合式。 

### <a name="artifacts"></a>構件
以下是此階段的交付項目。

* [**資料品質報告**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md)︰這份報告包含資料摘要、每個屬性與目標之間的關聯性以及變數排名等等。TDSP 中所提供的 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) 工具，可在任何表格式資料集 (例如 CSV 檔案或關聯式資料表) 上快速產生這份報告。 
* **方案架構**︰在您建置模型後，這可以是用來對新資料執行評分或預測之資料管線的圖表或描述。 它也包含可根據新資料重新訓練模型的管線。 在使用 TDSP 目錄結構範本時，此文件會儲存在這個[專案](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project)目錄。
* **檢查點決策**︰在完整的功能設計和模型建置開始之前，您可以重新評估專案，以判斷預期值是否足以繼續追求此專案。 比方說，您可能已準備好繼續、需要收集更多資料，或是因為不存在可以回答問題的資料而放棄專案。


## <a name="3-modeling"></a>3.模型化

### <a name="goals"></a>目標
* 最適合機器學習服務模型的資料功能。
* 最能精確預測目標的參考 ML 模型。
* 適用於生產環境的 ML 模型。

### <a name="how-to-do-it"></a>作法
此階段會解決三項主要工作︰

* **功能設計**︰從未經處理資料建立資料功能，以便訓練模型。
* **模型訓練**︰比較各個模型的成功標準，以找出最能精確回答問題的模型。
* 判斷您的模型是否**適用於生產環境**。

#### <a name="31-feature-engineering"></a>3.1 功能設計
功能設計包括納入、彙總和轉換未經處理的變數，以建立用於分析的功能。 如果您想要深入了解模型驅動因子，您必須了解功能之間的關聯方式，以及機器學習服務演算法要如何使用這些功能。 要執行此步驟，必須有創意地結合網域知識和獲取自資料瀏覽步驟的深入資訊。 這可平衡參考變數的尋找及納入，同時避免有太多不相關的變數。 參考變數可改善我們的結果；不相關的變數會對模型產生不必要的雜訊。 您也必須為評分期間所取得的任何新資料產生這些功能。 因此，這些功能的產生只會取決於評分時所能取得的資料。 如需使用不同的 Azure 資料技術時之功能設計的技術指引，請參閱[資料科學程序中的功能設計](machine-learning-data-science-create-features.md)。 

#### <a name="32-model-training"></a>3.2 模型訓練
根據您嘗試回答的問題類型，會有許多可用的模型化演算法。 如需如何選擇演算法的指引，請參閱[如何選擇 Microsoft Azure 機器學習的演算法](machine-learning-algorithm-choice.md)。 雖然本文是針對 Azure Machine Learning 所撰寫，但它提供的指引可用於任何機器學習服務專案。 

模型訓練程序包括下列步驟︰ 

* 將用於模型化的**輸入資料隨機分割**成訓練資料集和測試資料集。
* 使用訓練資料集來**建置模型**。
* **評估** (訓練和測試資料集) 一系列的競爭機器學習服務演算法，以及專為使用目前資料回答感興趣問題的各種相關微調參數 (稱為參數掃掠)。
* 比較替代方法之間的成功標準，以**決定用來回答問題的「最佳」方案**。

> [!NOTE]
> **避免外洩**︰納入訓練資料集以外的資料可能會造成資料外洩，而讓模型或機器學習服務演算法做出誤以為良好的預測。 當資料科學家取得好得過頭的預測結果時，會讓他們感到不安的常見原因就是資料外洩。 這些相依性很難察覺。 為了避免這種情況，通常會需要反覆審視建置分析資料集、建立模型和評估精確度的作業。 
> 
> 

我們透過 TDSP 提供[自動化模型和報告工具](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling)，其可透過多個演算法及參數掃掠來執行，以產生基準模型。 它也會產生基準模型報告，摘要說明每個模型和參數組合的效能 (包括變數重要性)。 此程序也會反覆進行，因為這可促成進一步的功能設計。 

### <a name="artifacts"></a>構件
此階段所產生的成品包括︰

* [**功能集**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets)︰**資料定義**報告的**功能集**區段會說明針對模型化所開發的功能。 它包含程式碼的指標，以產生功能以及功能產生方式的描述。
* [**模型報表**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md)：每個嘗試過的模型，都會產生標準的範本型報表，提供每項實驗的詳細資料。
* **檢查點決策**︰評估模型的效能是否良好到足以將它部署到生產系統。 需要詢問的一些重要問題是︰
  * 在給訂測試資料的情況下，模型是否有足夠的信心來回答問題？ 
  * 應該嘗試任何替代方法︰收集其他資料、進行更多功能設計，或試驗其他演算法？


## <a name="4-deployment"></a>4.部署

### <a name="goal"></a>目標
* 有 Data Pipeline 的模型會部署至生產環境或類生產環境，以進行最後的使用者接受度測試。 

### <a name="how-to-do-it"></a>作法
此階段解決的主要工作︰

* **實作模型**︰將模型和管線部署到生產環境或類生產環境以供取用應用程式。

#### <a name="41-operationalize-a-model"></a>4.1 實作模型
在您擁有一組妥善執行的模型後，這些模型即可開始運作，供其他應用程式取用。 預測可根據商業需求，以即時或批次的形式執行。 模型必須透過可輕易從各種不同的應用程式 (例如線上網站、試算表、儀表板或企業營運和後端應用程式) 取用的開放式 API 介面加以公開，才能開始運作。 如需使用 Azure Machine Learning Web 服務實作模型的範例，請參閱[部署 Azure Machine Learning Web 服務](machine-learning-publish-a-machine-learning-web-service.md)。 您也最好將遙測和監視建置到所部署的生產模型和資料管線，以協助後續的系統狀態報告和疑難排解。  

### <a name="artifacts"></a>構件
* 系統健康狀態和重要度量的狀態儀表板。
* 具有部署詳細資料的最終模型報告。
* 最終的方案架構文件。

## <a name="5-customer-acceptance"></a>5.客戶接受度

### <a name="goal"></a>目標
* **完成專案的交付項目**︰確認生產環境中的管線、模型及其部署完全滿足客戶的目標。

### <a name="how-to-do-it"></a>作法
此階段會解決兩項主要工作︰

* **系統驗證**︰確認所部署的模型和管線符合客戶需求。
* **專案移交**︰移交給將在生產環境中執行系統的實體。

客戶應該驗證系統是否符合其商務需求並以可接受的精確度回答問題，以將系統部署到生產環境以供其用戶端應用程式使用。 所有文件皆已完成並檢閱。 將專案移交給負責運作之實體的作業已完成。 比方說，這可能是 IT 或客戶的資料科學小組或負責在生產環境中執行系統的客戶代理人。 

### <a name="artifacts"></a>構件
此最終階段所產生的主要成品是**客戶專案結束報表**。 這份技術報告包含有利於了解和操作系統的專案全部詳細資料。 TDSP 會提供[結束報表](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md)範本供您依原狀使用，或經過自訂以符合特定用戶端需求。 

## <a name="summary"></a>摘要
[Team Data Science Process 生命週期](http://aka.ms/datascienceprocess)會模型化為一系列反覆執行的步驟，以針對使用預測模型所需的工作提供指引。 這些模型可部署在生產環境，以用來建置智慧型應用程式。 此程序生命週期的目標是要繼續將資料科學專案推向清楚的參與終點。 雖然資料科學確實是研究和探索的運用，但若能夠使用一組妥善定義且採用標準化範本的成品，清楚地向您的小組和客戶說明這一點，您就能避免誤解，並增加成功完成複雜資料科學專案的機會。

## <a name="next-steps"></a>後續步驟
此外也會提供完整的端對端逐步解說，說明 **特定案例** 之程序中的所有步驟。 [Team Data Science Process 逐步解說](data-science-process-walkthroughs.md)主題中會列出這些逐步解說以及連結的縮圖描述。


