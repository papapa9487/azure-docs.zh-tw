---
title: "Team Data Science Process 生命週期的部署階段 - Azure | Microsoft Docs"
description: "資料科學專案部署階段的目標、工作和交付項目。"
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
ms.openlocfilehash: b49b3ab696c22928c5f5a4566e059345fd810588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="deployment"></a>部署

本主題將概要說明與 Team Data Science Process **部署**相關聯的目標、工作和交付項目。 此流程會提供建議的生命週期，供您建構資料科學專案。 生命週期可描繪出專案一般執行時 (通常會反覆進行) 的主要階段：

* **了解商務**
* **資料取得與認知**
* **模型化**
* **部署**
* **客戶接受度**

以下是 **Team Data Science Process 生命週期**的視覺化呈現。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>目標
* 有 Data Pipeline 的模型會部署至生產環境或類生產環境，以進行最後的使用者接受度測試。 

## <a name="how-to-do-it"></a>作法
此階段解決的主要工作︰

* **實作模型**︰將模型和管線部署到生產環境或類生產環境以供取用應用程式。

### <a name="41-operationalize-a-model"></a>4.1 實作模型
在您擁有一組妥善執行的模型後，這些模型即可開始運作，供其他應用程式取用。 預測可根據商業需求，以即時或批次的形式執行。 模型部署可藉由 Open API 介面來公開模型而達成。 介面可讓各種不同應用程式輕易地取用模型，這些應用程式包括線上網站、試算表、儀表板或企業營運和後端應用程式等。 如需使用 Azure Machine Learning Web 服務實作模型的範例，請參閱[部署 Azure Machine Learning Web 服務](../studio/publish-a-machine-learning-web-service.md)。 對於所要部署的生產模型和資料管線，建議您在其中建置遙測和監視。 這種做法有助於後續的系統狀態報告和疑難排解。  

## <a name="artifacts"></a>構件
* 系統健康狀態和重要度量的狀態儀表板。
* 具有部署詳細資料的最終模型報告。
* 最終的方案架構文件。


## <a name="next-steps"></a>後續步驟

以下是 Team Data Science Process 生命週期中每個步驟的連結：

* [1.了解商務](lifecycle-business-understanding.md)
* [2.資料取得與認知](lifecycle-data.md)
* [3.模型化](lifecycle-modeling.md)
* [4.部署](lifecycle-deployment.md)
* [5.客戶接受度](lifecycle-acceptance.md)

此外也會提供完整的端對端逐步解說，說明 **特定案例** 之程序中的所有步驟。 [範例逐步解說](walkthroughs.md)主題中會列出這些逐步解說以及簡短說明的連結。 這些逐步解說說明如何將雲端、內部部署工具及服務組合成工作流程或管線，以建立智慧型應用程式。 

針對使用 Azure Machine Learning Studio 的 Team Data Science Process，如需其中執行步驟的範例，請參閱[使用 Azure ML](http://aka.ms/datascienceprocess) 學習路徑。