---
title: "執行跨 Azure Log Analytics 工作區的查詢 | Microsoft Docs"
description: "本文說明如何執行跨訂用帳戶中多個工作區的查詢，並提供可依循的範例。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>執行跨多個 Log Analytics 工作區的查詢

先前使用 Azure Log Analytics 時，您只能分析來自目前工作區內的資料，這項限制讓您無法跨訂用帳戶中定義的多個工作區執行查詢。  

現在，您可以跨多個工作區執行查詢，這提供了一個全系統的資料檢視。  您只能在[進階入口網站](log-analytics-log-search-portals.md#advanced-analytics-portal) (無法在 Azure 入口網站) 中執行此類型的查詢。  

## <a name="querying-across-log-analytics-workspaces"></a>跨 Log Analytics 工作區執行查詢
若要在您的查詢中參考另一個工作區，請使用 *workspace* 識別碼。  例如，以下查詢會從目前工作區及名為 *contosoretail-it*之另一個工作區的 Update 資料表，傳回其分類所需的更新項目彙總計數。  


## <a name="identifying-resources"></a>識別資源
您可以執行下列數種方式之一來識別工作區：

* 資源名稱 - 是人類看得懂的工作區名稱，有時稱為「元件名稱」。 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >以工作區名稱來識別工作區時，會假設該名稱在所有可存取的訂用帳戶中是唯一的。 如果您有多個應用程式具有該指定名稱，查詢就會因為語意模糊而失敗。 在此情況下，您必須使用其中一種其他識別碼。

* 限定名稱 - 是工作區的「完整名稱」，由訂用帳戶名稱、資源群組及元件名稱所組成，格式如下：*subscriptionName/resourceGroup/componentName*。 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >由於 Azure 訂用帳戶名稱並非唯一，因此這個識別碼可能語意模糊。 
    >

* 工作區識別碼 - 工作區識別碼是指派給每個工作區的唯一、不可變識別碼，會以全域唯一識別碼 (GUID) 來表示。

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Azure 資源識別碼 – Azure 定義的工作區唯一身分識別。 當資源名稱語意模糊時，您可以使用此識別碼。  就工作區而言，格式為：*/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*。  

    例如：
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>後續步驟

若要檢視 Log Analytics 中提供的所有查詢語法選項，請檢閱 [Log Analytics 記錄搜尋參考資料](https://docs.loganalytics.io/docs/Language-Reference)。    