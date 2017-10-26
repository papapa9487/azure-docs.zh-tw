---
title: "Azure Log Analytics 中的記錄搜尋 | Microsoft Docs"
description: "您需要記錄搜尋以便從 Log Analytics 擷取任何資料。  本文說明新的記錄搜尋在 Log Analytics 中的使用方式，並且提供在建立之前需要了解的概念。"
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
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 5f040d1480433ccf4c0b2b22c0cf1e25a7151d74
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2017
---
# <a name="understanding-log-searches-in-log-analytics"></a>了解 Log Analytics 中的記錄搜尋

您需要記錄搜尋以便從 Log Analytics 擷取任何資料。  無論您是分析入口網站中的資料、設定在特定條件下進行通知的警示規則，或是使用 Log Analytics API 擷取資料，您會使用記錄搜尋來指定您想要的資料。  本文說明記錄搜尋在 Log Analytics 中的使用方式，並且提供在建立之前應該了解的概念。 請參閱[後續步驟](#next-steps)一節，以取得建立和編輯記錄搜尋的詳細資料，以及取得查詢語言的參考。

## <a name="where-log-searches-are-used"></a>記錄搜尋的使用位置

您在 Log Analytics 中使用記錄搜尋的不同方式包含以下項目：

- **入口網站。** 您可以在 Azure 入口網站或[進階 Analytics 入口網站](https://go.microsoft.com/fwlink/?linkid=856587)的存放庫中，執行資料的互動式分析。  這可讓您在各種不同的格式和視覺效果中編輯查詢及分析結果。  您所建立的大多數查詢會在其中一個入口網站中開始，然後在您確認它如預期般運作時複製。
- **警示規則。** [警示規則](log-analytics-alerts.md)會主動識別您的工作區中資料的問題。  每個警示規則是根據以固定間隔自動執行的記錄搜尋。  會檢查結果來判斷是否應該建立警示。
- **檢視。**  您可以使用[檢視設計工具](log-analytics-view-designer.md)，建立要包含在使用者儀表板中的資料視覺效果。  記錄搜尋會提供各個檢視中[圖格](log-analytics-view-designer-tiles.md)和[視覺效果部分](log-analytics-view-designer-parts.md)使用的資料。  您可以從視覺效果部分向下鑽研至 [記錄搜尋] 頁面，以便針對資料執行進一步的分析。
- **匯出。**  當您將資料從 Log Analytics 工作區匯出至 Excel 或 [Power BI](log-analytics-powerbi.md) 時，您會建立記錄搜尋以定義要匯出的資料。
- **Powershell。** 您可以從命令列或 Azure 自動化 Runbook (使用 [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0)) 執行 PowerShell 指令碼，以從 Log Analytics 擷取資料。  此 Cmdlet 需要查詢來決定要擷取的資料。
- **Log Analytics API。**  [Log Analytics 記錄搜尋 API](log-analytics-log-search-api.md) 可讓任何 REST API 用戶端從工作區擷取資料。  API 要求包含針對 Log Analytics 執行的查詢，來判斷要擷取的資料。

![記錄檔搜尋](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Log Analytics 資料的組織方式
當您建置查詢時，從判斷哪些資料表具有您要尋找的資料開始。 每個[資料來源](log-analytics-data-sources.md)和[解決方案](../operations-management-suite/operations-management-suite-solutions.md)會將其資料儲存在 Log Analytics 工作區中的專用資料表。  每個資料來源和解決方案的文件包含它所建立之資料類型的名稱，以及每個屬性的描述。  許多查詢只需要單一資料表的資料，但是其他查詢可能會使用各種不同的選項以包含來自多個資料表的資料。

![資料表](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>撰寫查詢
Log Analytics 中記錄搜尋的核心是[廣泛的查詢語言](https://docs.loganalytics.io/)，可讓您以各種方式從存放庫擷取和分析資料。  這個相同的查詢語言用於 [Application Insights](../application-insights/app-insights-analytics.md)。  了解如何撰寫查詢，對於在 Log Analytics 中建立記錄搜尋相當重要。  您通常會從基本查詢開始，然後在您的需求變得更複雜時，使用更進階的函式。

查詢的基本結構是一個來源資料表，後面接著一系列由管道字元 `|` 隔開的運算子。  您可以將多個運算子鏈結在一起，以調整資料及執行進階函式。

例如，假設您想要尋找過去幾天具有最多錯誤事件的前十部電腦。

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

或者，您想要尋找過去幾天沒有活動訊號的電腦。

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

使用具有上一週每部電腦處理器使用率的折線圖如何？

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

您可以從這些快速範例看到，不論您使用的資料種類為何，查詢的結構是類似的。  您可以將它細分為不同的步驟，從一個命令產生的資料會透過管線傳送給下一個命令。

您也可以在您的訂用帳戶內跨 Log Analytics 工作區查詢資料。

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 


如需 Azure Log Analytics 查詢語言的完整文件，包括教學課程和語言參考，請參閱 [Azure Log Analytics 查詢語言文件](https://docs.loganalytics.io/)。

## <a name="next-steps"></a>後續步驟

- 深入了解[您用來建立和編輯記錄搜尋的入口網站](log-analytics-log-search-portals.md)。
- 請參閱使用新的查詢語言[撰寫查詢的教學課程](log-analytics-tutorial-viewdata.md)。
