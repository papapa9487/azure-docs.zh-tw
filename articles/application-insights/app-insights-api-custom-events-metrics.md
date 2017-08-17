---
title: "自訂事件和度量的 Application Insights API | Microsoft Docs"
description: "在您的裝置或桌面應用程式、網頁或服務中插入幾行程式碼，來追蹤使用狀況及診斷問題。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/17/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: fe769fb433d65374109fec60c6c6d032b1ad97fb
ms.contentlocale: zh-tw
ms.lasthandoff: 07/13/2017

---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>自訂事件和度量的 Application Insights API

在您的應用程式中插入幾行程式碼，以了解使用者對它進行的動作或協助診斷問題。 您可以從裝置和桌面應用程式、Web 用戶端以及 Web 伺服器傳送遙測。 使用 [Azure Application Insights](app-insights-overview.md) 核心遙測 API 來傳送自訂的事件和度量，以及您自己的標準遙測版本。 這個 API 與標準 Application Insights 資料收集器所使用的 API 相同。

## <a name="api-summary"></a>API summary
API 是跨所有平台統一的，除了一些小變化形式。

| 方法 | 用於 |
| --- | --- |
| [`TrackPageView`](#page-views) |頁面、畫面、刀鋒視窗或表單。 |
| [`TrackEvent`](#trackevent) |使用者動作和其他事件。 用來追蹤使用者行為，或監視效能。 |
| [`TrackMetric`](#trackmetric) |效能度量，例如與特定事件不相關的佇列長度。 |
| [`TrackException`](#trackexception) |記錄例外狀況以供診斷。 追蹤與其他事件的發生相對位置，並且檢查堆疊追蹤。 |
| [`TrackRequest`](#trackrequest) |記錄伺服器要求的頻率和持續時間以進行效能分析。 |
| [`TrackTrace`](#tracktrace) |診斷記錄訊息。 您也可以擷取第三方記錄檔。 |
| [`TrackDependency`](#trackdependency) |記錄應用程式所依賴之外部元件呼叫的持續時間及頻率。 |

您可以 [附加屬性和度量](#properties) 至這裡大部分的遙測呼叫。

## <a name="prep"></a>開始之前
如果您還沒有 Application Insights SDK 的參考：

* 將 Application Insights SDK 加入至專案：

  * [ASP.NET 專案](app-insights-asp-net.md)
  * [Java 專案](app-insights-java-get-started.md)
  * [每個網頁中的 JavaScript](app-insights-javascript.md) 
* 在裝置或 Web 伺服器程式碼中，加入：

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic：*`Imports Microsoft.ApplicationInsights`

    *Java：* `import com.microsoft.applicationinsights.TelemetryClient;`

## <a name="constructing-a-telemetryclient-instance"></a>建構 TelemetryClient 執行個體
建構 `TelemetryClient` 的執行個體 (除了網頁中的 JavaScript)：

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient 具備執行緒安全。

我們建議針對您每個應用程式的模組使用 TelemetryClient 執行個體。 比方說，您可能在 Web 服務中有一個 TelemetryClient 執行個體用來報告傳入的 HTTP 要求，以及中介類別中的另一個執行個體用來告報商業邏輯事件。 您可以設定如 `TelemetryClient.Context.User.Id` 的屬性以追蹤使用者和工作階段，或 `TelemetryClient.Context.Device.Id` 來識別電腦。 這項資訊會附加至執行個體所傳送的所有事件。

## <a name="trackevent"></a>TrackEvent
在 Application Insights 中，「自訂事件」是您可以在[計量瀏覽器](app-insights-metrics-explorer.md)顯示為彙總計數，以及在[診斷搜尋](app-insights-diagnostic-search.md)中顯示為個別發生點的資料點。 (它與 MVC 或其他架構的「事件」不相關。)

在您的程式碼中插入 `TrackEvent` 呼叫，以計算各種事件。 使用者選擇特定功能的頻率、達成特定目標的頻率，或他們犯特定類型錯誤的頻率。

例如，在遊戲應用程式中，每當使用者贏得遊戲時傳送事件：

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");

### <a name="view-your-events-in-the-microsoft-azure-portal"></a>在 Microsoft Azure 入口網站中檢視您的事件
若要查看事件計數，請開啟 [[計量瀏覽器](app-insights-metrics-explorer.md)] 刀鋒視窗、新增圖表，然後再選取 [事件]。  

![查看自訂事件的計數](./media/app-insights-api-custom-events-metrics/01-custom.png)

若要比較不同事件的計數，請將圖表類型設為 [方格]，並依事件名稱進行分組：

![設定圖表類型和群組](./media/app-insights-api-custom-events-metrics/07-grid.png)

在方格中逐一點選事件名稱，以查看該事件的個別發生次數。 若要查看詳細資料 - 按一下清單中的任何發生項目。

![鑽研事件](./media/app-insights-api-custom-events-metrics/03-instances.png)

若要在「搜尋」或「計量瀏覽器」中專注查看特定事件，請將刀鋒視窗篩選器設為您感興趣的事件名稱：

![開啟 [篩選器]，展開 [事件名稱]，然後選取一或多個值](./media/app-insights-api-custom-events-metrics/06-filter.png)

### <a name="custom-events-in-analytics"></a>分析中的自訂事件

[Application Insights 分析](app-insights-analytics.md)的 `customEvents` 資料表中有提供遙測資料。 每個資料列各代表應用程式中的一個 `trackEvent(..)` 呼叫。 

如果[取樣](app-insights-sampling.md)運作中，itemCount 屬性會顯示大於 1 的值。 例如，itemCount==10 表示在 trackEvent() 的 10 個呼叫中，取樣處理序只會傳輸其中一個。 若要取得正確的自訂事件計數，您應該使用程式碼，例如 `customEvent | summarize sum(itemCount)`。


## <a name="trackmetric"></a>TrackMetric

Application Insights 可以將未附加至特定事件的計量繪製成圖表。 例如，您可以定期監視佇列長度。 當您使用計量時，個別測量的重要性就不如變化和趨勢，因此統計圖表很有用。

為了將計量傳送至 Application Insights，您可以使用 `TrackMetric(..)` API。 您有兩種方式可以傳送計量： 

* 單一值： 每次在應用程式中執行一個測量，都會將對應值傳送至 Application Insights。 例如，假設您有一個描述容器中項目數的計量。 在特定期間內，您先將 3 個項目放入容器中，再移除 2 個項目。 因此，您會呼叫 `TrackMetric` 兩次：先傳遞值 `3`，再傳遞值 `-2`。 Application Insights 會代替您儲存這兩個值。 

* 彙總： 使用計量時，每個單一測量並不重要。 相反地，在特定期間內發生的狀況摘要才重要。 這類摘要稱為_彙總_。 在上述範例中，該期間的彙總計量總和為 `1`，而計量值的計數為 `2`。 使用彙總方法時，您只會在每段期間叫用 `TrackMetric` 一次，並傳送彙總值。 這是建議的方法，因為它可以藉由傳送較少資料點至 Application Insights，同時仍收集所有相關資訊，來大幅降低成本和效能負擔。

### <a name="examples"></a>範例：

#### <a name="single-values"></a>單一值

若要傳送單一計量值︰

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

C#、Java

```C#
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

#### <a name="aggregating-metrics"></a>彙總計量

建議先彙總計量再從應用程式傳送計量，以降低頻寬、成本，並提升效能。
程式碼的彙總範例如下：

*C#*

```C#
using System;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

namespace MetricAggregationExample
{
    /// <summary>
    /// Aggregates metric values for a single time period.
    /// </summary>
    internal class MetricAggregator
    {
        private SpinLock _trackLock = new SpinLock();

        public DateTimeOffset StartTimestamp    { get; }
        public int Count                        { get; private set; }
        public double Sum                       { get; private set; }
        public double SumOfSquares              { get; private set; }
        public double Min                       { get; private set; }
        public double Max                       { get; private set; }
        public double Average                   { get { return (Count == 0) ? 0 : (Sum / Count); } }
        public double Variance                  { get { return (Count == 0) ? 0 : (SumOfSquares / Count)
                                                                                  - (Average * Average); } }
        public double StandardDeviation         { get { return Math.Sqrt(Variance); } }

        public MetricAggregator(DateTimeOffset startTimestamp)
        {
            this.StartTimestamp = startTimestamp;
        }

        public void TrackValue(double value)
        {
            bool lockAcquired = false;

            try
            {
                _trackLock.Enter(ref lockAcquired);

                if ((Count == 0) || (value < Min))  { Min = value; }
                if ((Count == 0) || (value > Max))  { Max = value; }
                Count++;
                Sum += value;
                SumOfSquares += value * value;
            }
            finally
            {
                if (lockAcquired)
                {
                    _trackLock.Exit();
                }
            }
        }
    }   // internal class MetricAggregator

    /// <summary>
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    /// </summary>
    public sealed class Metric : IDisposable
    {
        private static readonly TimeSpan AggregationPeriod = TimeSpan.FromSeconds(60);

        private bool _isDisposed = false;
        private MetricAggregator _aggregator = null;
        private readonly TelemetryClient _telemetryClient;

        public string Name { get; }

        public Metric(string name, TelemetryClient telemetryClient)
        {
            this.Name = name ?? "null";
            this._aggregator = new MetricAggregator(DateTimeOffset.UtcNow);
            this._telemetryClient = telemetryClient ?? throw new ArgumentNullException(nameof(telemetryClient));

            Task.Run(this.AggregatorLoopAsync);
        }

        public void TrackValue(double value)
        {
            MetricAggregator currAggregator = _aggregator;
            if (currAggregator != null)
            {
                currAggregator.TrackValue(value);
            }
        }

        private async Task AggregatorLoopAsync()
        {
            while (_isDisposed == false)
            {
                try
                {
                    // Wait for end end of the aggregation period:
                    await Task.Delay(AggregationPeriod).ConfigureAwait(continueOnCapturedContext: false);

                    // Atomically snap the current aggregation:
                    MetricAggregator nextAggregator = new MetricAggregator(DateTimeOffset.UtcNow);
                    MetricAggregator prevAggregator = Interlocked.Exchange(ref _aggregator, nextAggregator);

                    // Only send anything is at least one value was measured:
                    if (prevAggregator != null && prevAggregator.Count > 0)
                    {
                        // Compute the actual aggregation period length:
                        TimeSpan aggPeriod = nextAggregator.StartTimestamp - prevAggregator.StartTimestamp;
                        if (aggPeriod.TotalMilliseconds < 1)
                        {
                            aggPeriod = TimeSpan.FromMilliseconds(1);
                        }

                        // Construct the metric telemetry item and send:
                        var aggregatedMetricTelemetry = new MetricTelemetry(
                                Name,
                                prevAggregator.Count,
                                prevAggregator.Sum,
                                prevAggregator.Min,
                                prevAggregator.Max,
                                prevAggregator.StandardDeviation);
                        aggregatedMetricTelemetry.Properties["AggregationPeriod"] = aggPeriod.ToString("c");

                        _telemetryClient.Track(aggregatedMetricTelemetry);
                    }
                }
                catch(Exception ex)
                {
                    // log ex as appropriate for your application
                }
            }
        }

        void IDisposable.Dispose()
        {
            _isDisposed = true;
            _aggregator = null;
        }
    }   // public sealed class Metric
}
```

### <a name="custom-metrics-in-metrics-explorer"></a>[計量瀏覽器] 中的自訂計量

若要查看結果，開啟 [計量瀏覽器] 並加入新的圖表。 請編輯圖表以顯示您的計量。

> [!NOTE]
> 自訂計量可能需要幾分鐘的時間才會出現在可用計量清單中。
>

![新增圖表或選取圖表，並在 [自訂] 底下選取您的度量](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>Analytics 中的自訂計量

[Application Insights 分析](app-insights-analytics.md)的 `customMetrics` 資料表中有提供遙測資料。 每個資料列各代表應用程式中的一個 `trackMetric(..)` 呼叫。
* `valueSum` - 這是測量結果的總和。 若要取得平均值，請將它除以 `valueCount`。
* `valueCount` - 彙總到這個 `trackMetric(..)` 呼叫的測量數目。

## <a name="page-views"></a>頁面檢視
在裝置或網頁應用程式中，每個畫面或頁面載入時預設會傳送頁面檢視遙測。 但是，您可以變更為在其他或不同的時間追蹤頁面檢視。 例如，在顯示索引標籤或刀鋒視窗的應用程式中，您可能想要在使用者每次開啟新的刀鋒視窗時追蹤頁面。

![[概觀] 刀鋒視窗上的使用方式透鏡](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

使用者和工作階段資料會與頁面檢視一起傳送為屬性，當有頁面檢視遙測時，讓使用者與工作階段圖表顯現。

### <a name="custom-page-views"></a>自訂頁面檢視
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Visual Basic*

    telemetry.TrackPageView("GameReviewPage")


如果您在不同的 HTML 網頁內有數個索引標籤，您也可以指定 URL：

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>計時頁面檢視
根據預設，回報為**頁面檢視載入時間**的時間是測量從瀏覽器傳送要求開始，直到呼叫瀏覽器的頁面載入事件為止的時間。

相反地，您可以：

* 在 [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) 呼叫中設定明確的持續時間：`appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`。
* 使用頁面檢視計時呼叫 `startTrackPage` 和 `stopTrackPage`。

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

做為第一個參數的名稱會將開始和停止呼叫相關聯。 預設為目前的頁面名稱。

產生後顯示在計量瀏覽器中的頁面載入持續時間是衍生自開始和停止呼叫之間的間隔。 取決於您實際計時的間隔。

### <a name="page-telemetry-in-analytics"></a>分析中的頁面遙測

在[分析](app-insights-analytics.md)中，有兩個資料表顯示瀏覽器作業的資料：

* `pageViews` 資料表包含 URL 和網頁標題的相關資料
* `browserTimings` 資料表包含用戶端效能的相關資料，例如處理傳入資料所花費的時間

若要了解瀏覽器處理不同頁面所花費的時間：

```
browserTimings | summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name 
```

若要探索不同瀏覽器的熱門程度：

```
pageViews | summarize count() by client_Browser
```

若要將頁面檢視與 AJAX 呼叫產生關聯，請聯結相依性：

```
pageViews | join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest
伺服器 SDK 會使用 TrackRequest 來記錄 HTTP 要求。

如果您想要在沒有 Web 服務模組執行的內容中模擬要求，您也可以自行呼叫。

不過，建議用來傳送要求遙測的方式是在要求作為<a href="#operation-context">作業內容</a>的地方。

## <a name="operation-context"></a>作業內容
您可藉由為遙測項目附加通用的作業識別碼，讓它們能夠關聯在一起。 標準的要求追蹤模組會針對在處理 HTTP 要求時傳送的例外狀況和其他事件執行此動作。 在[搜尋](app-insights-diagnostic-search.md)和[分析](app-insights-analytics.md)中，您可以使用此識別碼，輕易地找出任何與要求相關聯的事件。

設定此識別碼的最簡單方式是使用下列模式來設定作業內容：

*C#*

```C#
// Establish an operation context and associated telemetry item:
using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetry.TrackTrace(...); // or other Track* calls
    ...
    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetry.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

在設定作業內容時，`StartOperation` 會建立所指定類型的遙測項目。 它會在您處置作業時或您明確地呼叫 `StopOperation` 時傳送遙測項目。 如果您使用 `RequestTelemetry` 做為遙測類型，則其持續時間會設定為開始與停止之間的時間間隔。

作業內容不可為巢狀。 如果已經有作業內容，則其識別碼會與所有內含項目 (包括使用 `StartOperation` 建立的項目) 相關聯。

在搜尋中，會使用作業內容來建立 [相關項目] 清單：

![相關項目](./media/app-insights-api-custom-events-metrics/21.png)

如需有關自訂作業追蹤的詳細資訊，請參閱 [application-insights-custom-operations-tracking.md]。

### <a name="requests-in-analytics"></a>分析中的要求 

在 [Application Insights 分析](app-insights-analytics.md)中，要求會顯示在 `requests` 資料表中。

如果[取樣](app-insights-sampling.md)運作中，itemCount 屬性將會顯示大於 1 的值。 例如，itemCount==10 表示在 trackRequest() 的 10 個呼叫中，取樣處理序只會傳輸其中一個。 若要取得依要求名稱分割的正確要求計數和平均持續時間，請使用類似如下的程式碼：

```AIQL
requests | summarize count = sum(itemCount), avgduration = avg(duration) by name
```


## <a name="trackexception"></a>TrackException
傳送例外狀況至 Application Insights︰

* 以[計算數目](app-insights-metrics-explorer.md)，來指出問題的頻率。
* 以[檢查個別出現次數](app-insights-diagnostic-search.md)。

報告包含堆疊追蹤。

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }

SDK 將自動攔截許多例外狀況，所以您不一定需要明確呼叫 TrackException。

* ASP.NET：[撰寫程式碼以攔截例外狀況](app-insights-asp-net-exceptions.md)。
* J2EE：[自動攔截例外狀況](app-insights-java-get-started.md#exceptions-and-request-failures)。
* JavaScript：自動攔截例外狀況。 如果您想要停用自動收集，請在您插入網頁的程式碼片段中加入一行：

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

### <a name="exceptions-in-analytics"></a>分析中的例外狀況

在 [Application Insights 分析](app-insights-analytics.md)中，例外狀況會顯示在 `exceptions` 資料表中。

如果[取樣](app-insights-sampling.md)運作中，`itemCount` 屬性會顯示大於 1 的值。 例如，itemCount==10 表示在 trackException() 的 10 個呼叫中，取樣處理序只會傳輸其中一個。 若要取得依例外狀況類型分割的正確例外狀況計數，請使用類似如下的程式碼：

```
exceptions | summarize sum(itemCount) by type
```

大多數重要堆疊資訊已擷取到不同的變數中，但您可以拉開 `details` 結構以取得更多資訊。 由於這是動態結構，因此您應該將結果轉換成預期的類型。 例如：

```AIQL
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

若要將例外狀況與其相關要求產生關聯，請使用聯結：

```
exceptions
| join (requests) on operation_Id 
```

## <a name="tracktrace"></a>TrackTrace
使用 TrackTrace 可協助您藉由將 "breadcrumb trail" 傳送至 Application Insights 來診斷問題。 您可以傳送診斷資料區塊，並且在[診斷搜尋](app-insights-diagnostic-search.md)中檢查。

[記錄配接器](app-insights-asp-net-trace-logs.md)使用此 API 將第三方記錄傳送至入口網站。

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);


您可以搜尋訊息內容，但是 (不同於屬性值) 您無法在其中進行篩選。

`message` 上的大小限制比屬性上的限制高得多。
TrackTrace 的優點在於您可以將較長的資料放在訊息中。 例如，您可以在該處編碼 POST 資料。  

此外，您可以在訊息中新增嚴重性層級。 就像其他遙測一樣，您可以新增屬性值以供協助篩選或搜尋不同的追蹤集。 例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

在[搜尋](app-insights-diagnostic-search.md)中，您便可以輕鬆地篩選出與特定資料庫相關且具有特定嚴重性層級的所有訊息。


### <a name="traces-in-analytics"></a>分析中的追蹤

在 [Application Insights 分析](app-insights-analytics.md)中，TrackTrace 的呼叫會顯示在 `traces` 資料表中。

如果[取樣](app-insights-sampling.md)運作中，itemCount 屬性會顯示大於 1 的值。 例如，itemCount==10 表示在 `trackTrace()` 的 10 個呼叫中，取樣處理序只會傳輸其中一個。 若要取得正確的追蹤呼叫計數，您應該使用程式碼，例如 `traces | summarize sum(itemCount)`。

## <a name="trackdependency"></a>TrackDependency
您可以使用 TrackDependency 呼叫來追蹤回應時間以及呼叫外部程式碼片段的成功率。 結果會出現在入口網站中的相依性圖表中。

```C#
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

請記住，伺服器 SDK 包含[相依性模組](app-insights-asp-net-dependencies.md)，可用來自動探索和追蹤特定相依性呼叫 (例如資料庫和 REST API)。 您必須在伺服器上安裝代理程式才能讓模組正常運作。 如果您想要追蹤自動化追蹤不會攔截的呼叫，或不想安裝代理程式，您可以使用這個呼叫。

若要關閉標準的相依性追蹤模組，請編輯 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 並刪除 `DependencyCollector.DependencyTrackingTelemetryModule` 的參考。

### <a name="dependencies-in-analytics"></a>分析中的相依性

在 [Application Insights 分析](app-insights-analytics.md)中，trackDependency 呼叫會顯示在 `dependencies` 資料表中。

如果[取樣](app-insights-sampling.md)運作中，itemCount 屬性會顯示大於 1 的值。 例如，itemCount==10 表示在 trackDependency() 的 10 個呼叫中，取樣處理序只會傳輸其中一個。 若要取得依目標元件分割的正確相依性計數，請使用類似如下的程式碼：

```
dependencies | summarize sum(itemCount) by target
```

若要將相依性與其相關要求產生關聯，請使用聯結：

```
dependencies
| join (requests) on operation_Id 
```

## <a name="flushing-data"></a>排清資料
通常 SDK 會在選擇的時間傳送資料以將對使用者的影響降到最低。 不過，在某些情況下您可能想要排清緩衝區，例如，如果您在會關閉的應用程式中使用 SDK。

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

請注意，此函式對[伺服器遙測通道](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)而言是非同步的。

## <a name="authenticated-users"></a>驗證的使用者
在 Web 應用程式中，預設是透過 Cookie 來識別使用者。 如果使用者從不同的電腦或瀏覽器存取您的 app 或刪除 Cookie，就可能多次計算他們。

如果使用者登入您的 app，您可以藉由在瀏覽器程式碼中設定經過驗證的使用者識別碼，來取得更正確的計數：

*JavaScript*

```JS
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

在 ASP.NET Web MVC 應用程式，例如：

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

這不需要用到使用者的實際登入名稱。 只需是使用者的唯一識別碼。 其中不能包含空格或任何 `,;=|` 字元。

使用者識別碼也會設定於工作階段 Cookie 中，並傳送到伺服器。 如果安裝了伺服器 SDK，則會傳送經過驗證的使用者識別碼以作為用戶端和伺服器遙測的內容屬性一部分。 您可以接著對它進行篩選和搜尋。

如果您的 app 會將使用者群組為帳戶，您也可以傳遞該帳戶的識別碼 (具有相同的字元限制)。

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

在[計量瀏覽器](app-insights-metrics-explorer.md)中，您可建立可計算 [已驗證的使用者] 和 [使用者帳戶] 的圖表。

您也可以[搜尋](app-insights-diagnostic-search.md)具有特定使用者名稱和帳戶的用戶端資料點。

## <a name="properties"></a>使用屬性篩選、搜尋和分割資料
您可以將屬性和測量結果附加至您的事件 (同時還有度量，頁面檢視、例外狀況和其他的遙測資料)。

*屬性* 是可在使用情況報告中用來篩選遙測的字串值。 例如，如果您的應用程式提供數個遊戲，則您可以將遊戲的名稱附加至每個事件，以了解哪些遊戲較受歡迎。

字串長度限制為 8192 個。 (如果您想要傳送大量的資料區塊，請使用訊息參數 [TrackTrace](#track-trace)。)

*度量* 是可以用圖表方式呈現的數值。 例如，您可能想要查看玩家達到的分數是否逐漸增加。 圖表可以依據隨事件傳送的屬性分割，讓您可以針對不同遊戲取得個別或堆疊圖表。

若要讓計量值正確顯示，這些值應該大於或等於 0。

有一些 [屬性、屬性值和度量的數目限制](#limits) 可供您使用。

*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string>
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);


*Visual Basic*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> 切勿在屬性中記錄個人識別資訊。
>
>

*如果您使用度量*，請開啟 [計量瀏覽器]，然後從**自訂**群組中選取度量：

![開啟計量瀏覽器，選取圖表，並選取度量](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> 若未顯示您的計量或 [自訂] 標題不存在，請關閉選取的刀鋒視窗並於稍後重試。 有時候度量經由管線彙總時可能需要一個小時。

*如果您使用屬性和度量*，依據屬性分割度量：

![設定群組，然後在 [群組依據] 底下選取屬性](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

*在「診斷搜尋」中*，您可以檢視事件個別發生次數的屬性和度量。

![選取執行個體，然後選取 [...]](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

使用 [搜尋] 欄位來查看具有特定屬性值的事件出現次數。

![將詞彙輸入 [搜尋] 中](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[深入了解搜尋運算式](app-insights-diagnostic-search.md)。

### <a name="alternative-way-to-set-properties-and-metrics"></a>設定屬性和度量的替代方式
如果更加方便，您可以收集個別物件中事件的參數：

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> 不要重複使用相同的遙測項目執行個體 (此範例中的 `event`) 來呼叫 Track*() 多次。 這可能會讓遙測隨著不正確的組態傳送。
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>分析中的自訂測量和屬性

在[分析](app-insights-analytics.md)中，自訂計量和屬性會顯示在每個遙測記錄的 `customMeasurements` 和 `customDimensions` 屬性中。

例如，如果您將一個名為 "game" 的屬性新增至您的要求遙測，此查詢將會計算不同 "game" 值出現的次數，並顯示自訂計量 "score" 的平均值：

```
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game) 
```

請注意：

* 當您從 customDimensions 或 customMeasurements JSON 中擷取值時，其類型為動態，因此您必須將它轉換成 `tostring` 或 `todouble`。
* 考量到[取樣](app-insights-sampling.md)的可能性，您應該使用 `sum(itemCount)`，而不是 `count()`。



## <a name="timed"></a> 計時事件
有時候您想要繪製執行某些動作耗費多少時間的圖表。 例如，您可能想要知道使用者在遊戲中思考選項時花費多少時間。 您可以對此使用測量參數。

*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string>
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## <a name="defaults"></a>自訂遙測資料的預設屬性
如果您想為您撰寫的一些自訂事件設定預設屬性值，您可以在 TelemetryClient 執行個體中設定它們。 它們會附加至從該用戶端傳送的每個遙測項目。

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*Visual Basic*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");



個別遙測呼叫可以覆寫其屬性字典中的預設值。

*針對 JavaScript Web 用戶端*， [請使用 JavaScript 遙測初始設定式](#js-initializer)。

*若要將屬性新增至所有遙測*，並包括來自標準集合模組的資料，請[實作 `ITelemetryInitializer`](app-insights-api-filtering-sampling.md#add-properties)。

## <a name="sampling-filtering-and-processing-telemetry"></a>取樣、篩選及處理遙測資料
您可以撰寫程式碼，在從 SDK 傳送遙測資料前加以處理。 處理包括從標準遙測模組 (如 HTTP 要求收集和相依性收集) 的資料。

實作 `ITelemetryInitializer` 以[屬性](app-insights-api-filtering-sampling.md#add-properties)至遙測資料。 例如，您可以新增版本號碼或從其他屬性計算得出的值。

[篩選](app-insights-api-filtering-sampling.md#filtering)可以先修改或捨棄遙測，再藉由實作 `ITelemetryProcesor` 從 SDK 傳送遙測。 您可控制要傳送或捨棄的項目，但是您必須考量這對您的度量的影響。 視您捨棄項目的方式而定，您可能會喪失在相關項目之間瀏覽的能力。

[取樣](app-insights-api-filtering-sampling.md)是減少從應用程式傳送至入口網站的資料量的套件方案。 它在這麼做時並不會影響顯示的度量。 而且它在這麼做時可藉由在相關項目 (如例外狀況、要求和頁面檢視) 之間瀏覽，而不會影響您診斷問題的能力。

[深入了解](app-insights-api-filtering-sampling.md)。

## <a name="disabling-telemetry"></a>停用遙測
*動態停止和開始* 收集及傳輸遙測資料：

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

若要*停用選取的標準收集器* (例如效能計數器、HTTP 要求或相依性)，請刪除或註解化 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中的相關行。 例如，如果您想要傳送自己的 TrackRequest 資料，可以這麼做。

## <a name="debug"></a>開發人員模式
偵錯期間，讓您的遙測透過管線加速很有用，如此您就可以立即看到結果。 您也會取得額外的訊息，協助您追蹤任何遙測的問題。 在生產環境中將它關閉，因為它可能會拖慢您的應用程式。

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*Visual Basic*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a>設定已選取自訂遙測的檢測金鑰
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> 動態檢測金鑰
若要避免混合來自開發、測試和實際執行環境的遙測，您可以[建立個別的 Application Insights 資源](app-insights-create-new-resource.md)，並且依據環境變更其金鑰。

而不是從組態檔取得檢測金鑰，您可以在程式碼中設定。 在初始化方法中設定金鑰，例如 ASP.NET 服務中的 global.aspx.cs：

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey;



在網頁中，您可能想要從 Web 伺服器的狀態設定，而不是按其原義編碼至指令碼。 例如，在 ASP.NET 應用程式中產生的網頁：

*Razor 中的 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient 具有內容屬性，其中包含與所有遙測資料一起傳送的值。 它們通常由標準遙測模組設定，但是您也可以自行設定它們。 例如：

    telemetry.Context.Operation.Name = "MyOperationName";

如果您自行設定這些值，請考慮從 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 移除相關的程式碼行，讓您的值和標準值不致混淆。

* **元件**：應用程式及其版本。
* **裝置**︰應用程式執行所在裝置的相關資料  (在 Web 應用程式中，這是傳送遙測的伺服器或用戶端裝置)。
* **InstrumentationKey**：Azure 中遙測顯示之位置的 Application Insights 資源。 通常會揀選自 ApplicationInsights.config。
* **位置**：裝置的地理位置。
* **作業**：在 Web 應用程式中，目前的 HTTP 要求。 在其他應用程式類型中，您可以設定以將事件群組在一起。
  * **識別碼**：產生的值，與不同事件相互關聯，如此當您在診斷搜尋中檢查任何事件時，您可以發現相關項目。
  * **名稱**：識別碼，通常是 HTTP 要求的 URL。
  * **SyntheticSource**：如果不為 null 或空白，這個字串表示要求的來源已被識別為傀儡程式或 Web 測試。 根據預設，會從計量瀏覽器的計算中排除。
* **屬性**：與所有遙測資料一起傳送的屬性。 可以在個別 Track* 呼叫中覆寫。
* **工作階段**︰使用者的工作階段。 識別碼會設為產生的值，當使用者一段時間沒有作用時會變更。
* **使用者**：使用者資訊。

## <a name="limits"></a>限制
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

若要避免達到資料速率限制，請使用[取樣](app-insights-sampling.md)。

若要決定資料的保留時間，請參閱[資料保留和隱私權](app-insights-data-retention-privacy.md)。

## <a name="reference-docs"></a>參考文件
* [ASP.NET 參考](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java 參考](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript 參考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>SDK 程式碼
* [ASP.NET 核心 SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Windows Server 套件](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [所有平台](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>問題
* *Track_() 呼叫會擲回什麼例外狀況？*

    無。 您不需要將它們包裝在 try-catch 子句中。 如果 SDK 發生問題，它會在偵錯主控台輸出以及 (若訊息得以傳輸過去) 診斷搜尋中記錄訊息。
* *是否有 REST API 可供從入口網站中取得資料？*

    是，[資料存取 API](https://dev.applicationinsights.io/)。 其他擷取資料的方法包括[從分析匯出至 Power BI](app-insights-export-power-bi.md) 和[連續匯出](app-insights-export-telemetry.md)。

## <a name="next"></a>接續步驟
* [搜尋事件和記錄](app-insights-diagnostic-search.md)

* [疑難排解](app-insights-troubleshoot-faq.md)



