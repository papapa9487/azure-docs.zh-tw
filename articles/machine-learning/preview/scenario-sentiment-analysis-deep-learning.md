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
ms.openlocfilehash: 39ae2aa7217b45e8fab77f528b27b77a1b1256bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>在 Azure Machine Learning 中使用深度學習來進行情感分析

在自然語言處理的領域中，情感分析是眾所周知的工作。 在提供一組文字後，此工作的目的是要判斷該文字的情感。 此解決方案的目標是使用 CNTK 來作為 Keras 的後端 (Keras 是一個模型等級程式庫，會提供高階的建置組塊供您開發深度學習模型)，並透過電影評論來實作情感分析。

此解決方案位於 https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>連結至資源庫 GitHub 存放庫

依循以下連結來前往公用 GitHub 儲存機制：

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>使用案例概觀

資訊爆炸和行動裝置數量激增，讓客戶有了大量機會隨時表達他們對一切事物的看法和態度。 這個意見或「情感」通常是透過社交管道，以評論、聊天、分享、按贊和推文等形式來產生的。對於想要改善產品和服務、做出更明智決策，並進一步推廣其品牌的企業，情感是無價之寶。

為了從情感分析獲取價值，企業必須挖掘大量非結構化的社交資料以獲得可採取相應措施的深入剖析資訊。 在此範例中，我們會開發幾個深度學習模型，以使用 AMLWorkbench 對電影評論進行情感分析

## <a name="prerequisites"></a>必要條件

* [Azure 帳戶](https://azure.microsoft.com/free/) (有提供免費試用)。

* 遵循[快速入門安裝指南](./quickstart-installation.md)所安裝的 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 版本，以便安裝程式並建立 Workbench。

* 若要進行運作，您最好是在本機安裝並執行 Docker 引擎。 如果不是，您可以使用叢集選項。 不過，Azure Container Service (ACS) 的執行成本很高。

* 這個解決方案是假設您使用本機安裝的 Docker 引擎，在 Windows 10 上執行 Azure Machine Learning Workbench。 在 macOS 上，操作指示大致相同。

## <a name="data-description"></a>資料說明

此範例所使用的資料集是手工製作的小型資料集，並存放在 [data 資料夾](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data)中。

第一個資料行包含電影評論，第二個資料行則包含其情感 (0 - 負面，1 - 正面)。 資料集僅供示範之用，但一般來說，如果要獲得更穩定的情感分數，您需要大型的資料集。 例如，Keras 的 [IMDB 電影評論情感分類問題](https://keras.io/datasets/#datasets )所包含的資料集，便擁有 25,000 條來自 IMDB 並加上情感標籤 (正面或負面) 的電影評論。 這個實驗室的目的是要說明如何在 AMLWorkbench 中使用深度學習來進行情感分析。

## <a name="scenario-structure"></a>案例結構

資料夾結構的排列方式如下所示：

1. 與使用 AMLWorkbench 來進行情感分析有關的所有程式碼都位於根資料夾中
2. data：包含解決方案中使用的資料集
3. docs：包含所有線上實習實驗室

實現解決方案的實際操作實驗室順序如下所示：

| 順序| 檔案名稱 | 相關的檔案 |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| 2 | [`SentimentAnalysisModelingKerasWithCNTKBackend.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKerasWithCNTKBackend.md) | 'SentimentExtraction.py' |
| 3 | [`SentimentAnalysisModelingDocker.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingDocker.md) | 'SentimentExtractionDocker.py' |
| 4 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | 'Operaionalization' |

## <a name="conclusion"></a>結論

總而言之，此解決方案會向您介紹如何在 Azure Machine Learning Workbench 中使用深度學習來進行情感分析。 文中所呈現的解決方案可以彈性選擇使用 CNTK/Tensorflow 來作為 Keras 的後端。 此外，我們也會使用 HDF5 模型來運作。
