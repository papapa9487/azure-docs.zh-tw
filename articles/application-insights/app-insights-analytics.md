---
title: "分析 - 強大的 Azure Application Insights 搜尋工具 | Microsoft Docs"
description: "分析概觀，強大的 Application Insights 診斷搜尋工具。 "
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: a2dc351bd0346f5ca46f1eaafeff678c3339c8c9
ms.contentlocale: zh-tw
ms.lasthandoff: 08/02/2017

---
# <a name="analytics-in-application-insights"></a>Application Insights 中的分析
[分析](app-insights-analytics.md)是 [Application Insights](app-insights-overview.md) 的強大搜尋功能。 這些分頁說明 Log Analytics 查詢語言。 

* **[觀看簡介影片](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* 如果您的應用程式還未將資料傳送至 Application Insights，則**[在我們的模擬資料上測試分析](https://analytics.applicationinsights.io/demo)**。
* **[SQL 使用者的功能提要](https://aka.ms/sql-analytics)**會翻譯成最常見的習慣用語。
* **[語言參考](app-insights-analytics-reference.md)** 了解如何使用 Log Analytics 查詢語言的分析查詢語言的所有強大功能。


## <a name="queries-in-analytics"></a>分析中的查詢功能
標準的查詢是一個「來源」資料表，後面接著一系列由 `|` 隔開的「運算子」。 

例如，讓我們來了解海德拉巴的市民在一天當中的哪些時間試用我們的 Web 應用程式。 同時我們要看看針對他們的 HTTP 要求傳回哪些結果碼。 

```AIQL
requests
| where timestamp > ago(30d)
| summarize ClientCount = dcount(client_IP) by bin(timestamp, 1h), resultCode
| extend LocalTime = timestamp - 4h
| order by LocalTime desc
| render barchart
```

我們會計算不同的用戶端 IP 位址，並以過去 7 天每天的小時為單位將它們群組。 

> [!NOTE]
> 若要取得過去 24 小時之外的結果，請在查詢中明確包含 'timestamp'，或使用時間範圍下拉式功能表。
>

使用橫條圖展示來顯示結果，選擇以不同的回應碼來堆疊結果：

![選擇橫條圖、X 和 Y 軸，然後分割](./media/app-insights-analytics/020.png)

看來我們的應用程式在海德拉巴的午休時間及就寢時間最受歡迎。 (而且我們應該調查那些 500 的代碼。)

也有強大的統計運算功能︰

![統計查詢的結果](./media/app-insights-analytics/025.png)

這個語言具有許多吸引人的功能︰


* [篩選](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) 未經處理的應用程式遙測，包括您的自訂屬性和計量。
* [加入](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html) 多個資料表 – 將要求與頁面檢視、相依性呼叫、例外狀況和記錄追蹤相互關聯。
* 功能強大的統計 [彙總](https://docs.loganalytics.io/learn/tutorials/aggregations.html)。
* 功能如同 SQL 一般強大，但更容易用來進行複雜的查詢︰您可以使用管線將資料從某一個基本運算傳送到下一個運算，而不需使用巢串陳述式。
* 立即且功能強大的視覺效果。
* [將圖表釘選到 Azure 儀表板](app-insights-analytics-using.md#pin-to-dashboard)。
* [將查詢匯出至 Power BI](app-insights-analytics-using.md#export-to-power-bi)。
* 您可以使用 [REST API](https://dev.applicationinsights.io/) 以程式設計方式 (例如從 Powershell) 執行查詢。


## <a name="connect-to-your-application-insights-data"></a>連接到您的 Application Insights 資料
在 Application Insights 中，從 app 的 [概觀刀鋒視窗](app-insights-dashboards.md) 開啟 [分析]： 

![開啟 portal.azure.com，開啟您的 Application Insights 資源，然後按一下 [分析]。](./media/app-insights-analytics/001.png)


## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]



## <a name="query-examples"></a>查詢範例

請嘗試下列逐步解說，以了解 Analytics 的強大功能：

 *  [自動診斷要求持續時間中的突增和步驟跳躍 (英文)](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Automatic%20diagnostics%20of%20sudden%20spikes%20or%20step%20jumps%20in%20requests%20duration&shared=true)
 *  [以時間序列分析對效能降低進行分析 (英文)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20performance%20degradations%20with%20time%20series%20analysis&shared=true)
 *  [以 autocluster 和 diffpatterns 分析應用程式失敗 (英文)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20application%20failures%20with%20autocluster%20and%20diffpatterns&shared=true)
 *  [搭配時間序列分析的進階圖形偵測 (英文)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Advanced%20shape%20detection%20with%20time%20series%20analysis&shared=true)
 *  [使用滑動視窗作業分析應用程式使用情況 (累積的 MAU/DAU 等等) (英文)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Using%20sliding%20window%20calculations%20to%20analyze%20usage%20metrics:%20rolling%20MAU~2FDAU%20and%20cohorts&shared=true)
 *  [根據偵錯記錄檔的分析來偵測服務中斷 (英文)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Detection%20of%20service%20disruptions%20based%20on%20regression%20analysis%20of%20trace%20logs&shared=true)，以及[這篇 (英文)](https://maximshklar.wordpress.com/2017/02/16/finding-trends-in-traces-with-smart-data-analytics) 相對應的部落格文章。
 *  [使用簡單的偵錯記錄檔來分析應用程式的效能 (英文)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Profiling%20applications'%20performance%20with%20simple%20debug%20logs&shared=true)，以及[這篇 (英文)](https://yossiattasblog.wordpress.com/2017/03/13/first-blog-post/) 相對應的部落格文章
 *  [使用簡單的偵錯記錄檔來測量程式碼流程中每個步驟的持續時間 (英文)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Measuring%20the%20duration%20of%20each%20step%20in%20your%20code%20flow%20using%20simple%20debug%20logs&shared=true)，以及[這篇 (英文)](https://yossiattasblog.wordpress.com/2017/03/14/measuring-the-duration-of-each-step-in-your-code-flow-using-simple-debug-logs/) 相對應的部落格文章
 *  [使用簡單的偵錯記錄檔來分析並行 (英文)](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Analyzing%20concurrency%20with%20simple%20debug%20logs&shared=true)，以及[這篇 (英文)](https://yossiattasblog.wordpress.com/2017/03/23/analyzing-concurrency-using-simple-debug-logs/) 相對應的部落格文章



## <a name="next-steps"></a>後續步驟
* 我們建議您從 [語言教學](app-insights-analytics-tour.md)開始。 
* 深入了解如何[使用分析](app-insights-analytics-using.md)。 
* [語言參考](app-insights-analytics-reference.md)。 

