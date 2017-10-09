---
title: "在 Azure Machine Learning 中使用深度學習來進行情感分析 | Microsoft Docs"
description: "如何在 Azure ML Workbench 中使用深度學習來進行情感分析。"
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: miprasad
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 6ace1ec1130898d5cdc4e5c8b957e13aecc87174
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>在 Azure Machine Learning 中使用深度學習來進行情感分析

在自然語言處理的領域中，情感分析是眾所周知的工作。 在提供一組文字後，此工作的目的是要判斷該文字的情感。 此解決方案的目標是使用 CNTK 來作為 Keras 的後端 (Keras 是一個模型等級程式庫，會提供高階的建置組塊供您開發深度學習模型)，並透過電影評論來實作情感分析。

此解決方案位於 https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>連結至資源庫 GitHub 存放庫

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>使用案例概觀

資訊爆炸和行動裝置數量激增，讓客戶有了大量機會隨時表達他們對一切事物的看法和態度。 此一意見或「情感」通常是透過評論、聊天、分享、按贊和推文等各種社交管道所產生。對於想要改善產品和服務、做出更明智決策，並進一步推廣其品牌的企業，情感是無價之寶。

為了從情感分析獲取價值，企業必須挖掘大量非結構化的社交資料以獲得可採取相應措施的深入剖析資訊。 在此範例中，我們會開發幾個深度學習模型，以使用 AMLWorkbench 對電影評論進行情感分析

## <a name="prerequisites"></a>必要條件

* [Azure 帳戶](https://azure.microsoft.com/free/) (有提供免費試用)。

* 遵循[快速入門安裝指南](./quickstart-installation.md)所安裝的 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 版本，以便安裝程式並建立 Workbench。

* 若要進行運作，您最好是在本機安裝並執行 Docker 引擎。 如果不是，您可以使用叢集選項。 不過，Azure Container Service (ACS) 的執行成本很高。

* 這個解決方案是假設您使用本機安裝的 Docker 引擎，在 Windows 10 上執行 Azure Machine Learning Workbench。 在 macOS 上，操作指示大致相同。

## <a name="create-a-new-workbench-project"></a>建立新的 Workbench 專案

使用此範例作為範本來建立新專案：
1.  開啟 Azure Machine Learning Workbench
2.  在 [專案] 頁面上，按一下 **+** 符號，然後選取 [新增專案]
3.  在 [建立新專案] 窗格中，填入新專案的資訊
4.  在 [搜尋專案範本] 搜尋方塊中輸入「使用深度學習來進行情感分析」並選取範本
5.  按一下 [建立] 

## <a name="data-description"></a>資料說明

此範例所使用的資料集是手工製作的小型資料集，並存放在 [Github 存放庫](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/Data) 的 Data 資料夾中。

第一個資料行包含電影評論，第二個資料行則包含其情感 (0 - 負面，1 - 正面)。 資料集僅供示範之用，但一般來說，如果要獲得更穩定的情感分數，您需要大型的資料集。 例如，Keras 的 [IMDB 電影評論情感分類問題](https://keras.io/datasets/#datasets )所包含的資料集，便擁有 25,000 條來自 IMDB 並加上情感標籤 (正面或負面) 的電影評論。 這個實驗室的目的是要說明如何在 AMLWorkbench 中使用深度學習來進行情感分析。

## <a name="scenario-structure"></a>案例結構

資料夾結構的排列方式如下所示：

1. 程式碼：包含與使用 AMLWorkbench 來進行情感分析有關的所有程式碼  
2. 資料：包含解決方案中使用的資料集  
3. 實驗室：包含所有實際操作實驗室  

實現解決方案的實際操作實驗室順序如下所示：

| 順序| 檔案名稱 | 相關的檔案 |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/Docs/SentimentAnalysisDataPreparation.md) | 'Data/sampleReviews.txt' |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/Docs/SentimentAnalysisModelingDocker.md) | 'Code/SentimentExtraction.py' |
| 3 | [`ModelingAndEvaluationDocker.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/Docs/SentimentAnalysisModelingKerasWithCNTKBackend.md) | 'Code/SentimentExtractionDocker.py' |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/Docs/SentimentAnalysisOperationalization.md) | 'Code/Operaionalization' |

## <a name="conclusion"></a>結論

總而言之，此解決方案會向您介紹如何在 Azure Machine Learning Workbench 中使用深度學習來進行情感分析。 文中所呈現的解決方案可以彈性選擇使用 CNTK/Tensorflow 來作為 Keras 的後端。 此外，我們也會使用 HDF5 模型來運作。

