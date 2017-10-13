---
title: "Team Data Science Process 生命週期的客戶接受度階段 - Azure | Microsoft Docs"
description: "資料科學專案客戶接受度階段的目標、工作和交付項目。"
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
ms.openlocfilehash: 953f090f775da5e48b2f4ed36550a5624ae4596b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="customer-acceptance"></a>客戶接受度

本主題將概要說明與 Team Data Science Process **客戶接受度階段**相關聯的目標、工作和交付項目。 此流程會提供建議的生命週期，供您建構資料科學專案。 生命週期可描繪出專案一般執行時 (通常會反覆進行) 的主要階段：

* **了解商務**
* **資料取得與認知**
* **模型化**
* **部署**
* **客戶接受度**

以下是 **Team Data Science Process 生命週期**的視覺化呈現。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>目標
* **完成專案的交付項目**︰確認生產環境中的管線、模型及其部署完全滿足客戶的目標。

## <a name="how-to-do-it"></a>作法
此階段會解決兩項主要工作︰

* **系統驗證**︰確認所部署的模型和管線符合客戶需求。
* **專案移交**︰移交給將在生產環境中執行系統的實體。

客戶應該驗證系統是否符合其商務需求並以可接受的精確度回答問題，以將系統部署到生產環境以供其用戶端應用程式使用。 所有文件皆已完成並檢閱。 將專案移交給負責運作之實體的作業已完成。 比方說，這個實體可能是 IT 或客戶的資料科學小組或負責在生產環境中執行系統的客戶代理人。 

## <a name="artifacts"></a>構件
此最終階段所產生的主要成品是**客戶專案結束報表**。 這份技術報告包含有利於了解和操作系統的專案全部詳細資料。 TDSP 會提供[結束報表](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md)範本供您依原狀使用，或經過自訂以符合特定用戶端需求。 


## <a name="next-steps"></a>後續步驟

以下是 Team Data Science Process 生命週期中每個步驟的連結：

* [1.了解商務](lifecycle-business-understanding.md)
* [2.資料取得與認知](lifecycle-data.md)
* [3.模型化](lifecycle-modeling.md)
* [4.部署](lifecycle-deployment.md)
* [5.客戶接受度](lifecycle-acceptance.md)

此外也會提供完整的端對端逐步解說，說明 **特定案例** 之程序中的所有步驟。 [範例逐步解說](walkthroughs.md)主題中會列出這些逐步解說以及簡短說明的連結。 這些逐步解說說明如何將雲端、內部部署工具及服務組合成工作流程或管線，以建立智慧型應用程式。 

針對使用 Azure Machine Learning Studio 的 Team Data Science Process，如需其中執行步驟的範例，請參閱[使用 Azure ML](http://aka.ms/datascienceprocess) 學習路徑。