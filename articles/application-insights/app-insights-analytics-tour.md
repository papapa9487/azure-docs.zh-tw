---
title: "Azure Application Insights 中分析的完整教學課程 | Microsoft Docs"
description: "分析 (Application Insights 的強大搜尋工具) 中所有主要查詢的簡短範例。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/06/2017
ms.author: mbullwin
ms.openlocfilehash: a33fedd765acde666eef280ba7dfa72536bf1bd2
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Application Insights 中分析的教學課程
[分析](app-insights-analytics.md)是 [Application Insights](app-insights-overview.md) 的強大搜尋功能。 這些分頁說明 Log Analytics 查詢語言。

* **[觀看簡介影片](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* 如果您的應用程式還未將資料傳送至 Application Insights，則**[在我們的模擬資料上測試分析](https://analytics.applicationinsights.io/demo)**。
* **[SQL 使用者的功能提要](https://aka.ms/sql-analytics)**會翻譯成最常見的習慣用語。

讓我們逐步解說可讓您快速入門的一些基本查詢。

## <a name="connect-to-your-application-insights-data"></a>連接到您的 Application Insights 資料
在 Application Insights 中，從 app 的 [概觀刀鋒視窗](app-insights-dashboards.md) 開啟 [分析]：

![開啟 portal.azure.com，開啟您的 Application Insights 資源，然後按一下 [分析]。](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsioquerylanguagequerylanguagetakeoperatorhtml-show-me-n-rows"></a>[Take](https://docs.loganalytics.io/queryLanguage/query_language_takeoperator.html)：顯示 n 個資料列
記錄使用者作業的資料點 (通常是 Web 應用程式收到的 HTTP 要求) 會儲存在名為 `requests`的資料表。 每個資料列都是從應用程式中的 Application Insights SDK 接收的遙測資料點。

讓我們先檢查資料表的幾個範例資料列︰

![results](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> 先將游標置於陳述式中的某處，再按一下 [執行]。 您可以將陳述式分割成多行，但不要在單一陳述式中放置空白行。 空白行可方便您在視窗中保有數個不同的查詢。
>
>

選擇資料行、加以拖曳，依照資料行分組，以及進行篩選︰

![按一下結果右上方的資料行選取範圍](./media/app-insights-analytics-tour/030.png)

展開任何項目以查看詳細資料︰

![選擇 [資料表]，並使用 [設定資料行]](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> 按一下資料行的標頭，以重新排列網頁瀏覽器中可用的結果。 但請注意，對大型結果集而言，下載至瀏覽器的資料列數目有限。 以這種方式排序只是簡單地排序傳回的結果集，並不一定會顯示實際的最高或最低項目。 若要可靠地將項目排序，請使用 `top` 或 `sort` 運算子。
>
>

## <a name="query-across-applications"></a>跨應用程式進行查詢
如果您想要結合來自多個 Application Insights 應用程式的資料，請使用 **app** 關鍵字來指定應用程式及資料表名稱。  此查詢會使用 **union** 命令結合來自兩個不同應用程式的要求。


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsioquerylanguagequerylanguagetopoperatorhtml-and-sorthttpsdocsloganalyticsioquerylanguagequerylanguagesortoperatorhtml"></a>[Top](https://docs.loganalytics.io/queryLanguage/query_language_topoperator.html) 和 [sort](https://docs.loganalytics.io/queryLanguage/query_language_sortoperator.html)
`take` 可用來取得快速的結果範例，但它不會依特定順序顯示資料表中的資料列。 若要取得已排序的檢視，請使用 `top` (適用於某個範例) 或 `sort` (整個資料表)。

顯示前 n 個資料列 (依特定資料行排序)︰

```AIQL

    requests | top 10 by timestamp desc
```

* 語法︰大部分運算子都有關鍵字參數 (例如 `by`)。
* `desc` = 遞減順序，`asc` = 遞增。

![](./media/app-insights-analytics-tour/260.png)

`top...` 是 `sort ... | take...` 比較有效率的說法。 我們可以撰寫︰

```AIQL

    requests | sort by timestamp desc | take 10
```

結果會相同，但執行速度比較慢一點  (您也可以撰寫 `order`，這是 `sort` 的別名)。

資料表檢視中的資料行標頭也可用來排序畫面上的結果。 當然，如果您已使用 `take` 或 `top` 只擷取部分的資料表，按一下資料行標頭只會重新排列您所擷取的記錄。

## <a name="wherehttpsdocsloganalyticsioquerylanguagequerylanguagewhereoperatorhtml-filtering-on-a-condition"></a>[Where](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html)︰篩選條件

讓我們看一下傳回特定結果碼的要求︰

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

`where` 運算子會採用布林運算式。 以下是其相關的一些重點︰

* `and`、`or`：布林運算子
* `==`、`<>`、`!=`︰等於和不等於
* `=~`、`!~`︰不區分大小寫的字串等於和不等於。 有更多的字串比較運算子。

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>尋找失敗的要求

將字串值轉換為整數，以使用「大於」比較：

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>時間

根據預設，查詢會受限於最近 24 小時。 但您可以變更此範圍︰

![](./media/app-insights-analytics-tour/change-time-range.png)

在 where 子句中撰寫任何提及 `timestamp` 的查詢，以覆寫時間範圍。 例如：

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

時間範圍功能相當於在每個提到的其中一個來源資料表之後插入 'where' 子句。

`ago(3d)` 表示「三天前」。 其他時間單位包括小時 (`2h`、`2.5h`)、分鐘 (`25m`) 和秒 (`10s`)。

其他範例︰

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

```

[日期和時間參考](https://docs.loganalytics.io/concepts/concepts_datatypes_datetime.html)。


## <a name="projecthttpsdocsloganalyticsioquerylanguagequerylanguageprojectoperatorhtml-select-rename-and-compute-columns"></a>[Project](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html)：選取、重新命名和計算資料行
使用 [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) ，只挑出您想要的資料行：

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

您也可以將資料行重新命名並定義新的資料行︰

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![結果](./media/app-insights-analytics-tour/270.png)

* 如果資料行名稱是以括號括起來 (如下所示)，就可以包含空格或符號︰`['...']` 或 `["..."]`
* `%` 是很常見的模數運算子。
* `1d` (這是數字 1，再加上 'd') 是一個時間範圍常值，表示一天。 以下是其他一些時間範圍常值︰`12h`、`30m`、`10s`、`0.01s`。
* `floor` (別名 `bin`) 會將一個值無條件捨去為您提供之基值的最近倍數。 所以 `floor(aTime, 1s)` 會將時間無條件捨去為最接近的秒數。

運算式可以包含所有常見的運算子 (`+`、`-`...)，而且有一系列的實用函式。

## <a name="extend"></a>Extend
如果您只要將資料行加入現有的資料行，請使用 [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html)：

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

如果您要保留所有現有的資料行，使用 [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html) 比 [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) 精簡。

### <a name="convert-to-local-time"></a>轉換為當地時間

時間戳記一律是 UTC 格式。 所以如果您位於美國西岸且正好是冬季，則可能如下所示︰

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```


## <a name="summarizehttpsdocsloganalyticsioquerylanguagequerylanguagesummarizeoperatorhtml-aggregate-groups-of-rows"></a>[Summarize](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html)：彙總資料列群組
`Summarize` 會對資料列群組套用指定的「彙總函式」。

例如，Web 應用程式回應要求所花的時間會在 `duration`欄位中報告。 我們來看看所有要求的平均回應時間︰

![](./media/app-insights-analytics-tour/410.png)

或者，我們可以將結果分成不同名稱的要求︰

![](./media/app-insights-analytics-tour/420.png)

`Summarize` 會將串流中的資料點收集到 `by` 子句評估為相同的群組中。 `by` 運算式中的每個值 (即上述範例中的各個唯一的運算名稱) 會在結果資料表中各產生一個資料列。

或者，我們可以依一天當中的時間將結果分組︰

![](./media/app-insights-analytics-tour/430.png)

請注意我們如何使用 `bin` 函式 (也稱為 `floor`)。 如果我們只使用 `by timestamp`，每個輸入資料列最終都會在它自己的小群組內。 針對任何連續純量，例如時間或數字，我們必須將連續範圍分割成合理數量的離散值。 `bin` - 只是熟悉的 `floor` 捨去函式 - 這是最簡單的做法。

我們可以使用相同的技巧來減少字串的範圍︰

![](./media/app-insights-analytics-tour/440.png)

請注意，您可以在彙總運算式或 by 子句中使用 `name=` 設定結果資料行的名稱。

## <a name="counting-sampled-data"></a>計算取樣的資料
`sum(itemCount)` 是用來計算事件的建議彙總。 在許多情況下，itemCount==1，因此函式只會計算群組中的資料列數目。 但在進行[取樣](app-insights-sampling.md)時，只有一小部分的原始事件會保留作為 Application Insights 中的資料點，因此您看到的每一個資料點會有 `itemCount` 個事件。

例如，如果取樣捨棄了 75%的原始事件，則在保留的記錄中 itemCount==4 - 也就是，針對每筆保留的記錄，會有四筆原始記錄。

調適性取樣在您的應用程式大量使用期間會導致 itemCount 變得更高。

因此，加總 itemCount 可正確估算事件的原始數目。

![](./media/app-insights-analytics-tour/510.png)

另外還有 `count()` 彙總 (以及計數運算)，適用於您確實想要計算群組中的資料列數目的情況。

目前提供一系列的 [彙總函式](https://docs.loganalytics.io/learn/tutorials/aggregations.html)。

## <a name="charting-the-results"></a>製作結果圖表
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

根據預設，結果會顯示成資料表︰

![](./media/app-insights-analytics-tour/225.png)

有比資料表檢視更好的顯示方式。 讓我們使用直條圖選項來看看圖表檢視中的結果︰

![按一下 [圖表]，然後選擇 [直條圖] 並指派 x 和 y 軸](./media/app-insights-analytics-tour/230.png)

請注意，雖然我們並未依時間排序結果 (如資料表顯示中所示），但圖表顯示一律會以正確的順序顯示日期時間。


## <a name="timecharts"></a>時間表
顯示每小時有多少個事件︰

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

選取圖表顯示選項：

![時間圖](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>多個系列
`summarize` 子句中的多個運算式會建立多個資料行。

`by` 子句中的多個運算式會建立多個資料列，每個值組合各有一個資料列。

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![依小時和位置的要求資料表](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>依據維度分割圖表
如果您將具有字串資料行和數值資料行的資料表繪製成圖表，此字串可用於將數值資料分割成不同的點系列。 如果有一個以上的字串資料行，您可以選擇哪個資料行要做為鑑別子。

![分割分析圖表](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>跳出率

將布林值轉換成字串，將它當作鑑別子︰

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>顯示多個計量
如果您將具有多個數值資料行的資料表繪製成圖表，除了時間戳記以外，您可以顯示任何的數值資料行組合。

![分割分析圖表](./media/app-insights-analytics-tour/110.png)

您必須選取 [不分割]，才能選取多個數值資料行。 您無法依字串資料行分割又同時顯示多個數值資料行。

## <a name="daily-average-cycle"></a>每日平均週期
平均一天的使用量如何變化？

依時間模數一天計算要求 (分類收納成數小時)︰

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![平均一天的時數折線圖](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> 請注意，我們目前必須將持續時間轉換成日期時間，才能顯示於折線圖上。
>
>

## <a name="compare-multiple-daily-series"></a>比較多個每日系列
在不同國家/地區內一天當中的使用量如何變化？

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![依 client_CountryOrRegion 分割](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>繪製分佈圖
有多少個不同長度的工作階段？

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

需要最後一行才能轉換成 datetime。 目前只有圖表的 x 軸是 datetime 時才會顯示為純量。

`where` 子句會排除單次發生的工作階段 (sessionDuration==0)，並設定 x 軸的長度。

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsioquerylanguagequerylanguagepercentilesaggfunctionhtml"></a>[百分位數](https://docs.loganalytics.io/queryLanguage/query_language_percentiles_aggfunction.html)
哪些持續時間範圍涵蓋不同的工作階段百分比？

使用上述查詢，但取代最後一行︰

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

我們也移除 where 子句中的上限，以便取得正確的數據，包括有多個要求的所有工作階段︰

![結果](./media/app-insights-analytics-tour/180.png)

我們可以從中看到︰

* 5% 的工作階段的持續時間小於 3 分鐘 34 秒；
* 50% 的工作階段的持續時間小於 36 分鐘；
* 5% 的工作階段的持續時間超過 7 天

若要取得每個國家/地區的個別分解圖，我們只需透過兩個 summarize運算子個別地帶入 client_CountryOrRegion 資料行︰

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Join
我們可以存取數個資料表，包括要求和例外狀況。

若要尋找傳回失敗回應之要求的相關例外狀況，我們可以聯結 `session_Id`上的資料表：

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


在執行聯結之前，使用 `project` 只選取我們需要的資料行是相當好的做法。
在相同的子句中，我們會將時間戳記資料行重新命名。

## <a name="lethttpsdocsloganalyticsioquerylanguagequerylanguageletstatementhtml-assign-a-result-to-a-variable"></a>[Let](https://docs.loganalytics.io/queryLanguage/query_language_letstatement.html)︰將結果指派給變數

使用 `let` 來分隔前一個運算式的各個部分。 結果不變：

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> 在分析用戶端中，不要在查詢的各個部分之間插入空白行。 務必執行所有一切。
>

使用 `toscalar` 將單一表格儲存格轉換為值：

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Functions

使用 Let 來定義函式︰

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>存取巢狀物件
巢狀物件可以輕鬆地存取。 例如，在例外狀況串流中，您可能會看到如下的結構化物件︰

![結果](./media/app-insights-analytics-tour/520.png)

您可以選擇您感興趣的屬性來將它平面化︰

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

請注意，您必須轉換到適當類型。


## <a name="custom-properties-and-measurements"></a>自訂屬性和測量
如果您的應用程式要將[自訂維度 (屬性) 和自訂測量](app-insights-api-custom-events-metrics.md#properties)附加到事件，則您會在 `customDimensions` 和 `customMeasurements` 物件看到它們。

例如，如果您的應用程式包括︰

```C#

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

若要在分析中擷取這些值︰

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type

```

若要驗證自訂維度是否為特定類型︰

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

## <a name="dashboards"></a>儀表板
您可以將結果釘選至儀表板，以便結合所有最重要的圖表和資料表。

* [Azure 共用儀表板](app-insights-dashboards.md#share-dashboards)：按一下釘選圖示。 執行這項作業之前，您必須具有共用儀表板。 在 Azure 入口網站中，開啟或建立儀表板並按一下 [共用]。
* [Power BI 儀表板](app-insights-export-power-bi.md)︰按一下 [匯出]、[Power BI 查詢]。 此替代方案的優點是您可以顯示查詢，還有各種來源的其他結果。

## <a name="combine-with-imported-data"></a>與匯入的資料結合

儀表板上的分析報告看起來很不錯，但有時候您想要將資料轉譯為更容易消化的表單。 例如，假設已驗證的使用者在遙測中是依照別名識別。 您想要在結果中顯示其真實名稱。 若要這樣做，您只需要有 CSV 檔案從別名對應至真實名稱。

您可以匯入資料檔案，並且就像任何標準資料表 (要求、例外狀況等等) 一樣使用它。 單獨查詢它，或是將它與其他資料表聯結。 例如，如果您有名為 usermap 的資料表，且其中包含 `realName` 和 `userId` 資料行，您即可使用它來轉譯要求遙測中的 `user_AuthenticatedId` 欄位︰

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

若要匯入資料表，請在 [結構描述] 刀鋒視窗的 [其他資料來源] 下，依指示上傳資料樣本來新增資料來源。 然後，您可以使用此定義來上傳資料表。

匯入功能目前為預覽狀態，所以在 [其他資料來源] 下，您最初會看到 [與我們連絡] 連結。 請利用此連結來註冊預覽方案，之後，連結會換成 [新增資料來源] 按鈕。


## <a name="tables"></a>資料表
從應用程式收到的遙測串流可透過數個資料表來存取。 每個資料表可用的屬性結構描述會顯示在視窗左邊。

### <a name="requests-table"></a>要求資料表
計算 Web 應用程式的 HTTP 要求數量並依頁面名稱分割︰

![計算要求數量，依名稱分割](./media/app-insights-analytics-tour/analytics-count-requests.png)

尋找最失敗的要求︰

![計算要求數量，依名稱分割](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>自訂事件資料表
如果您使用 [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) 傳送您自己的事件，則可以從這個資料表查看事件。

讓我們舉包含下列程式行的應用程式程式碼為例︰

```C#

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

顯示這些事件的頻率︰

![顯示自訂事件的速率](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

擷取事件的度量值和維度︰

![顯示自訂事件的速率](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>自訂度量表
如果您使用 [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) 傳送您自己的度量值，您會在 **customMetrics** 串流中發現它的結果。 例如：  

![Application Insights 分析中的自訂度量](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> 在[計量瀏覽器](app-insights-metrics-explorer.md)中，附加到任何遙測類型的所有自訂度量，會連同使用 `TrackMetric()` 傳送的計量一起出現在 [計量] 刀鋒視窗中。 但在分析中，自訂度量仍會附加到其所執行的任何一種遙測類型 (事件或要求等)，而且 TrackMetric 所傳送的度量會出現在自己的串流中。
>
>

### <a name="performance-counters-table"></a>效能計數器資料表
[效能計數器](app-insights-performance-counters.md)會對您顯示應用程式的基本系統度量，例如 CPU、記憶體和網路使用率。 您可以設定 SDK 來傳送其他計數器，包括您自己的自訂計數器。

**PerformanceCounters** 結構描述會公開每個效能計數器的 `category`、`counter` 名稱和 `instance` 名稱。 計數器執行個體名稱只適用於某些效能計數器，而且通常會指出與計數相關的處理程序名稱。 在每個應用程式的遙測中，您只會看到該應用程式的計數器。 例如，若要查看哪些計數器可用︰

![Application Insights 分析中的效能計數器](./media/app-insights-analytics-tour/analytics-performance-counters.png)

若要取得所選期間的可用記憶體圖表︰

![Application Insights 分析中的記憶體時間圖表](./media/app-insights-analytics-tour/analytics-available-memory.png)

和其他遙測一樣，**performanceCounters** 也有 `cloud_RoleInstance` 資料行會指出應用程式執行所在主機機器的身分識別。 例如，若要比較不同機器上的應用程式效能︰

![Application Insights 分析中依角色執行個體分割的效能](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>例外狀況資料表
此資料表中有提供[應用程式所報告的例外狀況](app-insights-asp-net-exceptions.md)。

若要尋找例外狀況引發時應用程式正在處理的 HTTP 要求，請聯結 operation_Id︰

![在 operation_Id 聯結例外狀況與要求](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>瀏覽器計時資料表
`browserTimings` 會顯示使用者的瀏覽器所收集到的頁面載入資料。

[為應用程式設定用戶端遙測](app-insights-javascript.md)才能看到這些度量。

結構描述包含[指出頁面載入處理程序之不同階段長度的度量](app-insights-javascript.md#page-load-performance) (這些度量不會指出使用者讀取頁面的時間長度)。  

顯示不同頁面的熱門度以及每個頁面的載入時間︰

![分析中的頁面載入時間](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>可用性結果資料表
`availabilityResults` 會顯示 [Web 測試](app-insights-monitor-web-app-availability.md)的結果。 每個測試位置每次執行的測試會分開報告。

![分析中的頁面載入時間](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>相依性資料表
包含應用程式對資料庫和 REST API 所發出呼叫的結果，以及對 TrackDependency() 所發出的其他呼叫結果。 也包含從瀏覽器進行的 AJAX 呼叫。

從瀏覽器進行的 AJAX 呼叫︰

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

從伺服器進行的相依性呼叫︰

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

如果未安裝 Application Insights 代理程式，則伺服器端相依性結果一律顯示 `success==False`。 不過，其他資料都正確。

### <a name="traces-table"></a>追蹤資料表
包含應用程式使用 TrackTrace() 或[其他記錄架構](app-insights-asp-net-trace-logs.md)傳送的遙測。

## <a name="video"></a>影片 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

進階查詢：

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>後續步驟
* [分析語言參考](app-insights-analytics-reference.md)
* [SQL 使用者的功能提要](https://aka.ms/sql-analytics)會翻譯成最常見的習慣用語。

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
