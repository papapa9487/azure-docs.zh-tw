---
title: "Team Data Science Process 生命週期 - Azure | Microsoft Docs"
description: "執行資料科學專案所需的步驟。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 264386b527626f3241966bfdb2fb061781121be1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="team-data-science-process-lifecycle"></a>Team Data Science Process 生命週期

Team Data Science Process (TDSP) 會提供建議的生命週期供您建構資料科學專案。 生命週期會概述專案在執行時通常會遵循之從開始到完成的步驟。 如果您正在使用另一個資料科學生命週期 (例如 [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)、[KDD](https://wikipedia.org/wiki/Data_mining#Process) 或您組織本身的自訂程序)，您仍可對於這些開發生命週期使用工作型 TDSP。 

此生命週期是針對要在智慧型應用程式中隨附的資料科學專案所設計。 這些應用程式會部署機器學習服務或人工智慧模型來做預測性分析。 探勘資料科學專案和臨機操作分析專案也可以從使用此程序而獲益。 但是，在這種情況下，可能不需要所述的某些步驟。    

TDSP 生命週期是由反覆執行的五個主要階段所組成。 其中包含：

* [1. 了解商務](lifecycle-business-understanding.md)
* [2. 資料取得與認知](lifecycle-data.md)
* [3. 模型化](lifecycle-modeling.md)
* [4. 部署](lifecycle-deployment.md)
* [5. 客戶接受度](lifecycle-acceptance.md)

以下是 **Team Data Science Process 生命週期**的視覺化呈現。 

![TDSP-Lifecycle](./media/lifecycle/tdsp-lifecycle2.png) 


Team Data Science Process 生命週期會建立為一系列反覆執行步驟的模型，可針對需要使用預測模型的工作來提供指引。 這些模型可部署在生產環境，以用來建置智慧型應用程式。 此程序生命週期的目標是要繼續將資料科學專案推向清楚的參與終點。 雖然資料科學確實是研究和探索的運用，但若能夠使用一組妥善定義且採用標準化範本的成品，清楚地向您的小組和客戶說明這些工作，您就能避免誤解，並增加成功完成複雜資料科學專案的可能性。

對於每個階段，我們會提供下列資訊：

* **目標**︰特定的目標。
* **作法**︰概述的特定工作和所提供的完成指引。
* **成品**：交付項目和其生產支援。

## <a name="next-steps"></a>後續步驟
此外也會提供完整的端對端逐步解說，說明 **特定案例** 之程序中的所有步驟。 [範例逐步解說](walkthroughs.md)主題中會列出這些逐步解說以及簡短說明的連結。 這些逐步解說說明如何將雲端、內部部署工具及服務組合成工作流程或管線，以建立智慧型應用程式。 

針對使用 Azure Machine Learning Studio 的 Team Data Science Process，如需其中執行步驟的範例，請參閱[使用 Azure ML](http://aka.ms/datascienceprocess) 學習路徑。

