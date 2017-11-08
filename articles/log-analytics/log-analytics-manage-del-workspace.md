---
title: "刪除 Azure Log Analytics 工作區 | Microsoft Docs"
description: "了解如何刪除您的 Log Analytics 工作區 (如果您已在個人訂用帳戶中建立工作區) 或重組您的工作區模型。"
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3d99f9869dfdfbe18f82e873bd367cc85f9414f1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>使用 Azure 入口網站來刪除 Azure Log Analytics 工作區
本主題說明如何使用 Azure 入口網站來刪除您可能已不再需要的 Log Analytics 工作區。 

## <a name="to-delete-a-workspace"></a>刪除工作區 
當您刪除 Log Analytics 工作區時，系統會在 30 天內從服務中刪除所有與工作區相關的資料。  刪除工作區時，請小心謹慎，因為可能有會對服務作業造成負面影響的重要資料和設定。  
 
如果您是管理員，而且有多位使用者與工作區關聯，則這些使用者與工作區之間的關聯將會中斷。 如果這些使用者與其他工作區相關聯，則可以繼續搭配其他工作區使用 Log Analytics。 不過，如果他們未與其他工作區相關聯，則必須建立工作區才能使用 Log Analytics。 

1. 登入 [Azure 入口網站](http://portal.azure.com)。 
2. 在 Azure 入口網站中，按一下左下角的 [更多服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics]。
3. 在 Log Analytics 訂用帳戶窗格中，選取工作區，然後從中間窗格的頂端按一下 [刪除]。<br><br> ![來自工作區屬性窗格的刪除選項](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. 當要求您確認刪除工作區的確認訊息窗格出現時，按一下 [是]。<br><br> ![確認刪除工作區](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

