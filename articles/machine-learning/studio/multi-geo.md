---
title: "Multi-Geo 說明文件 | Microsoft Docs"
description: "了解如何在美國中南部 (SCUS) Azure 區域以外的 Azure 區域中建立工作區並發佈 Web 服務。"
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: rmca14
tags: 
ms.assetid: ed0ca8a8-fa53-4e56-b824-2d7e44641967
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/6/2017
ms.author: tedway; neerajkh
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e4941ccf8c6d7a0c77527e9c1d722bc3a770114a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="multi-geo-help-documentation"></a>Multi-Geo 說明文件
本文將說明如何在不同的 Azure 區域中建立工作區及發佈 Web 服務。  [依區域的 Azure 產品頁面](https://azure.microsoft.com/en-us/regions/services/)會列出可以使用 Azure Machine Learning 的區域。

## <a name="create-a-workspace"></a>建立工作區
1. 登入 Azure 傳統入口網站。
2. 按一下 [+ 新增] > [資料服務] > [機器學習服務] > [快速建立]。  在 [位置] 選取另一個區域，例如 [東南亞]。
   ![Multi-Geo 說明影像 1][1]
3. 選取工作區，然後按一下 [登入 ML Studio] 。
   ![Multi-Geo 說明影像 2][2]
4. 您現在已於其他地區中建立工作區，您可如同任何其他工作區一樣使用。 若要切換工作區，請查看您螢幕的右上方。 按一下下拉式清單，選取 [區域]，然後選取 [工作區]。 其中每一項都位於該工作區區域。  例如，所有已從工作區建立的 Web 服務會與工作區位於相同區域。
   ![Multi-Geo 說明影像 3][3]

## <a name="open-an-experiment-from-gallery"></a>從 [資源庫] 開啟實驗
如果從 [資源庫] 開啟實驗，您也可以選取要複製實驗的目標區域。

![Multi-Geo 說明影像 4][4a]

## <a name="web-service-management"></a>Web 服務管理
若要以程式設計方式管理 Web 服務，例如重新訓練、使用特定地區的地址：

* https://asiasoutheast.management.azureml.net
* https://europewest.management.azureml.net

### <a name="things-to-note"></a>注意事項
1. 您只能以這樣的方式複製屬於相同區域工作區之間的實驗。 如果您需要在不同區域中跨工作區複製實驗，您可以使用 [PowerShell](http://aka.ms/amlps) commandlet [*Copy-AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) 來完成。 另一個解決方法是使用未列出的模式將實驗發佈至組件庫，然後在其他區域的工作區中開啟。
2. 區域選取器一次只會顯示一個區域的工作區。  
3. 免費工作區或 [來賓存取] \(匿名) 工作區會在美國中南部建立並裝載。  
4. 從位於東南亞的工作區部署的 Web 服務也會裝載於東南亞。  

## <a name="more-information"></a>詳細資訊
您可在 [Azure Machine Learning 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning)中提出問題。

<!--Image references-->
[1]: ./media/multi-geo/multi-geo_1.png
[2]: ./media/multi-geo/multi-geo_2.png
[3]: ./media/multi-geo/multi-geo_3.png
[4a]: ./media/multi-geo/multi-geo_4a.png

