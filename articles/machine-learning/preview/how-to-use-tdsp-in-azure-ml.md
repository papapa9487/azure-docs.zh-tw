---
title: "使用 Team Data Science Process 範本建構專案 | Microsoft Docs"
description: "如何在 Azure ML 中將 Team Data Science Process (TDSP) 範本具現化，以建構專案來進行共同作業。"
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
ms.date: 09/17/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2e03b6bcb1bc5a617234c7801f22f8207a9f5a4e
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="structure-projects-with-team-data-science-process-template"></a>使用 Team Data Science Process 範本建構專案

本文件提供如何在 Azure Machine Learning 中使用 Team Data Science Process (TDSP) 範本來建立資料科學專案，以建構專案來進行共同作業和取得重現性。 


## <a name="what-is-team-data-science-process"></a>何謂 Team Data Science Process？
Team Data Science Process 是一個敏捷式、反覆性的資料科學程序，可用於執行和傳遞進階的分析解決方案。 它設計來改善企業組織中資料科學團隊的共同作業與效率。 它透過四個主要元件來支援這些目標：

1. 標準的[資料科學生命週期](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) \(英文\) 定義。
2. 標準化的專案結構[專案文件和報告範本](https://github.com/Azure/Azure-TDSP-ProjectTemplate) \(英文\)。
3. 適用於專案執行的基礎結構與資源，例如，計算和儲存體基礎結構，以及程式碼存放庫。
4. 適用於資料科學專案工作的[工具與公用程式](https://github.com/Azure/Azure-TDSP-Utilities) \(英文\)，例如，共同作業的版本控制和程式碼檢閱、資料探索和模型化，以及工作規劃。

如需 TDSP 的更完整討論，請參閱 [Team Data Science Process 概觀](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/README.md) \(英文\)。

## <a name="why-should-you-use-tdsp-structure-and-templates"></a>為什麼您應該使用 TDSP 結構和範本？
標準化的結構、生命週期，以及資料科學專案的文件，是在資料科學團隊之間促進有效共同作業的關鍵。 使用 TDSP 範本建立 Azure Machine Learning 專案，可為協調的團隊工作提供適用的架構。

我們先前發行了[適用於 TDSP 專案結構和範本的 GitHub 存放庫](https://github.com/Azure/Azure-TDSP-ProjectTemplate) \(英文\)，可協助您達成這些目標。 但到目前為止，仍無法在資料科學工具中將 TDSP 結構和範本具現化。 現在能夠建立 Azure Machine Learning 專案，來將 TDSP 結構和文件範本具現化。 

## <a name="things-to-note-before-creating-a-new-project"></a>建立新專案之前要注意的事項
建立新專案之前，您應該注意或檢閱下列事項：
* TDSP Azure Machine Learning [範本](https://aka.ms/tdspamlgithubrepo) \(英文\)。
* 內容 ('docs' 資料夾中的除外) 大小必須小於 25 MB。 請參閱下方**注意事項**。
* Sample\_data 資料夾只適用小型資料檔案 (小於 5 MB)，您可以使用這類檔案測試程式碼或進行早期開發。
* 儲存檔案 (例如 Office Word、PowerPoint 等) 實質上可以增加 'docs' 資料夾的大小。 我們建議您找出共同作業的 Wiki、[SharePoint](https://products.office.com/en-us/sharepoint/collaboration) 或其他共同作業的資源來儲存這類檔案。
* 若要在 Azure Machine Learning 中處理大型檔案和輸出，請閱讀[這篇文章](http://aka.ms/aml-largefiles)。

> [!NOTE]
> 確定除了 readme.md 檔案以外，'docs' 資料夾中所有文件相關的內容 (文字、Markdown、影像、其他文件檔案) 都不會在專案執行期間使用。 這是會遭到 Azure Machine Learning 執行忽略的特殊資料夾，因此，不需將此資料夾中的內容複製到計算目標。 此資料夾中的物件也不會計入專案大小的 25 MB 上限，因此，舉例來說，您可能會儲存文件中所需的大型影像檔。 Git 仍會透過執行歷程記錄來追蹤它們。 

## <a name="instantiating-tdsp-structure-and-templates-from-the-azure-machine-learning-template-gallery"></a>從 Azure Machine Learning 範本庫將 TDSP 結構和範本具現化
若要使用 Team Data Science Process 結構和文件範本建立新專案，請完成下列程序： 

### <a name="click-on-new-project"></a>按一下 [新增專案]
開啟 Azure Machine Learning。 在左上方的 [專案] 下方，按一下 [+]，然後選取 [新增專案] 來建立新專案。

![新增專案](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="creating-a-new-tdsp-structured-project"></a>建立新的 TDSP 結構化專案
在相關的方塊中指定參數和資訊：

- 專案名稱
- 專案目錄
- 專案描述
- 空的 Git 存放庫路徑
- 工作區名稱

然後在 [搜尋] 方塊中，輸入 *TDSP*。 出現 [使用 TDSP 建構專案] 時，按一下以選取該範本。 然後按一下 [建立] 按鈕，以建立含 TDSP 結構的新專案。 如果您在建立專案期間 (在適當的方塊中) 提供空的 Git 存放庫，則會在建立專案之後，使用專案結構和內容填入該存放庫。

![建立 TDSP 專案](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>檢查 TDSP 專案結構
建立新專案之後，您可以檢查它的結構 (下圖中的左面板)。 它包含適合用來進行商務了解之各個層面的標準化文件、TDSP 生命週期的階段、資料位置、定義，以及此文件範本中的架構。 這個結構衍生自[這裡](https://github.com/Azure/Azure-TDSP-ProjectTemplate) \(英文\) 發佈的 TDSP 結構，但有一些修改。 例如，將數個文件範本合併成一個 Markdown，也就是 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport)。 

### <a name="project-folder-structure"></a>專案資料夾結構
TDSP 專案範本包含下列最上層資料夾：
1. **code**：包含程式碼
2. **docs**：包含有關專案 (例如，Markdown 檔案、相關媒體等) 的必要文件
3. **sample_data**：包含**範例 (小型)** 資料，可用於早期開發或測試。 一般而言，不能超過數個 (5) MB。 不適用於完整或大型資料集。

![範例資料](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="using-the-tdsp-structure-and-templates"></a>使用 TDSP 結構和範本
結構和範本需要使用專案特有的資訊來填入。 您應該使用執行和傳遞專案所需的程式碼和資訊來填入這些項目。 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) 檔案是一個範本，應該直接使用與您專案相關的資訊來修改。 它隨附一組問題，可協助您針對 [Team Data Science Process 生命週期](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) \(英文\) 之四個階段中的每一個填寫資訊。

以下提供專案結構在執行期間或完成之後看起來如何的範例 (下圖中的左面板)。 此範例來自 [Team Data Science Process 範例專案：在 Azure Machine Learning 中將美國人口普查資料的收入分類](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) \(英文\)。

![範例專案結構](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="documenting-your-project"></a>記錄您的專案
請參閱 [TDSP 文件範本](https://github.com/Azure/Azure-TDSP-ProjectTemplate) \(英文\)，以了解如何記錄您的專案。 在目前的 Azure Machine Learning TDSP 文件範本中，我們建議您在 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) \(英文\) 檔案中包含所有資訊。 您應該使用專案特定的資訊來填寫這個範本。 

我們也會提供其他的 [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) \(英文\) 範本，來包含主要專案文件中未包含但仍值得記錄的任何資訊。 

### <a name="example-project-report"></a>範例專案報告
您可以在[這裡](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliveralbe_docs/ProjectReport.md) \(英文\) 找到範例專案報告。 這是[美國收入分類範例專案](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) \(英文\) 的專案報告，其中示範如何針對資料科學專案具現化和使用 TDSP 範本。

## <a name="next-steps"></a>後續步驟
為了協助您了解如何在 Azure Machine Learning 專案中使用 TDSP 結構和範本，我們會在適用於 Azure Machine Learning 的文件中提供數個已解決的專案範例。

- 如需示範如何在 Azure Machine Learning 中建立 TDSP 專案的範例，請參閱 [Team Data Science Process 範例專案：在 Azure Machine Learning 中將美國人口普查資料的收入分類](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) \(英文\) 
- 如需在 Azure Machine Learning 上已將 TDSP 具現化的專案中，於 NLP 中使用深入學習的範例，請參閱[使用自然語言處理搭配深入學習來進行生物醫學實體辨識](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction) \(英文\)

