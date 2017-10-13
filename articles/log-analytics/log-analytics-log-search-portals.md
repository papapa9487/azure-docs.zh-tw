---
title: "Azure Log Analytics 中用於建立和編輯記錄查詢的入口網站 | Microsoft Docs"
description: "本文會說明可以在 Azure Log Analytics 中用來建立和編輯記錄搜尋的入口網站。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: magoedte; bwren
ms.openlocfilehash: b205f226d95d94b938a70a834ac0147e76d459ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Azure Log Analytics 中用於建立和編輯記錄查詢的入口網站

您可以在 Log Analytics 中的多處使用記錄搜尋，以從工作區中擷取資料。  除了以互動方式使用傳回的資料，針對實際建立和編輯查詢，您有兩個選項，如下所述。  

## <a name="log-search"></a>記錄搜尋 
[記錄搜尋] 頁面可從 Azure 入口網站存取。  它適合用來建立可在單行中建立的基本查詢。  [記錄搜尋] 不必啟動外部入口網站就可以使用，它可以透過記錄搜尋來執行各種功能，包括建立警示規則、建立電腦群組，以及匯出查詢結果。  

[記錄搜尋] 提供編輯查詢的多項功能，而不需具備查詢語言的完整知識。  您可以在[使用記錄搜尋在 Azure Log Analytics 中建立記錄搜尋](log-analytics-log-search-log-search-portal.md)中取得這些功能的摘要。


![[記錄搜尋] 頁面](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>進階 Analytics 入口網站
進階 Analytics 入口網站是一個專用的入口網站，可從 Azure 入口網站提供 [記錄搜尋] 中無法使用的進階功能。  功能包括在多行上編輯查詢、選擇性執行程式碼、區分內容的 Intellisense 和 Smart Analytics。  進階 Analytics 入口網站最適合設計複雜的查詢，其可能儲存為記錄搜尋或者複製並貼到其他 Log Analytics 元素中。  您可以從 [記錄搜尋] 頁面上的連結啟動進階 Analytics 入口網站。

![進階 Analytics 入口網站](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


由於進階 Analytics 入口網站的進階功能，通常會使用它作為建立和編輯查詢的主要工具。  判定查詢如預期般運作之後，即可將其複製並貼到其他位置，例如 [記錄搜尋] 頁面或檢視設計工具。  由於進階 Analytics 入口網站支援多行的查詢，從這個入口網站複製查詢時，請考慮下列事項。

- 必須從查詢移除註解，才能將查詢複製並貼到另一個位置。  您可以在一行前加上兩個斜線 (//) 以對該行進行註解。  將多行查詢貼到單行時，會移除分行符號。  如果包含了註解，則第一個註解後的所有字元都視為註解的一部分。


## <a name="next-steps"></a>後續步驟

- 使用[記錄搜尋](log-analytics-tutorial-viewdata.md)了解如何使用查詢語言建立查詢的逐步教學課程
- 檢視[進階 Analytics 入口網站](https://go.microsoft.com/fwlink/?linkid=856587)以建立複雜的查詢，並使用開發環境作為您的記錄搜尋。

