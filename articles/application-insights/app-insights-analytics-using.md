---
title: "使用分析 - 強大的 Azure Application Insights 搜尋工具 | Microsoft Docs"
description: "使用分析，這是強大的 Application Insights 診斷搜尋工具。 "
services: application-insights
documentationcenter: 
author: danhadari
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 28d32d1e2d82519fc7b2ad4edca8435c3759594f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/02/2017

---
# <a name="using-analytics-in-application-insights"></a>使用 Application Insights 中的分析
[分析](app-insights-analytics.md)是 [Application Insights](app-insights-overview.md) 的強大搜尋功能。 這些分頁說明 Log Analytics 查詢語言。

* **[觀看簡介影片](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* 如果您的應用程式還未將資料傳送至 Application Insights，則**[在我們的模擬資料上測試分析](https://analytics.applicationinsights.io/demo)**。

## <a name="open-analytics"></a>開啟分析
在 Application Insights 中，從您的應用程式的首頁資源，按一下 [分析]。

![開啟 portal.azure.com，開啟您的 Application Insights 資源，然後按一下 [分析]。](./media/app-insights-analytics-using/001.png)

內嵌教學課程會提供您一些可執行作業的概念。

[這裡有更廣泛的教學課程](app-insights-analytics-tour.md)。

## <a name="query-your-telemetry"></a>查詢您的遙測
### <a name="write-a-query"></a>撰寫查詢
![結構描述顯示](./media/app-insights-analytics-using/150.png)

以任何列在左側的資料表名稱 (或 [range](https://docs.loganalytics.io/queryLanguage/query_language_rangeoperator.html) 或 [union](https://docs.loganalytics.io/queryLanguage/query_language_unionoperator.html) 運算子) 開頭。 使用 `|` 建立 [運算子](https://docs.loganalytics.io/learn/cheatsheets/useful_operators.html)的直立線符號。 

IntelliSense 會向您提示您可使用的運算子和運算式元素。 按一下資訊圖示 (或按 CTRL+Space)，即可獲得更長的描述和每個元素的使用方式範例。

請參閱[分析語言教學課程](app-insights-analytics-tour.md)和[語言參考](app-insights-analytics-reference.md)。

### <a name="run-a-query"></a>執行查詢
![執行查詢](./media/app-insights-analytics-using/130.png)

1. 您可以在查詢中使用單一分行符號。
2. 將游標放在您要執行的查詢內部或結尾。
3. 檢查查詢的時間範圍。 (您可以變更它，或是在查詢中包含您自己的 [`where...timestamp...`](https://docs.loganalytics.io/concepts/concepts_datatypes_timespan.html) 子句來覆寫它。)
3. 按一下 [執行] 來執行查詢。
4. 請勿在查詢中放置空白行。 您可以用空白行來分隔一個查詢索引標籤中的數個查詢，讓它們保持分離狀態。 只會執行游標所在的查詢。

### <a name="save-a-query"></a>儲存查詢
![儲存查詢](./media/app-insights-analytics-using/140.png)

1. 儲存目前的查詢檔案。
2. 開啟已儲存的查詢檔案。
3. 建立新的查詢檔案。

## <a name="see-the-details"></a>請參閱詳細資料
展開結果中的任何資料列，以查看其完整屬性清單。 您可以進一步展開任屬於何結構化值的屬性 - 例如，自訂維度或例外狀況中的堆疊清單。

![展開資料列](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>排列結果
您可以排序、篩選、分頁和分組查詢所傳回的結果。

> [!NOTE]
> 在瀏覽器中排序、分組和篩選不會重新執行查詢。 這些作業只會重新排列最後一個查詢所傳回的結果。 
> 
> 若要在傳回結果之前，在伺服器中執行這些工作，請使用 [sort](https://docs.loganalytics.io/queryLanguage/query_language_sortoperator.html)、[summarize](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html) 和 [where](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) 運算子撰寫查詢。
> 
> 

挑選您想要查看的資料行，拖曳資料行標頭進行重新排列，然後拖曳其框線以調整資料行的大小。

![排列資料行](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>排序和篩選項目
按一下資料行的標頭來排序結果。 再次按一下以其他方式排序，而第三次按一下即可還原成查詢所傳回的原始順序。

使用篩選圖示縮小搜尋範圍。

![排序和篩選資料行](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>將項目分組
若要依照多個資料行排序，請使用群組。 請先啟用它，接著將資料行標頭拖曳到資料表上方的空間。

![群組](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>遺漏某些結果？

如果您認為沒有看到所預期的全部結果，則有一些可能的原因。

* **時間範圍篩選**。 根據預設，您只會看到過去 24 小時內的結果。 系統提供一個自動篩選，會限制從來源資料表中擷取的結果範圍。 

    不過，您可以使用下拉式功能表來變更時間範圍篩選。

    或是在查詢中包含您自己的 [`where  ... timestamp ...` 子句](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html)來覆寫自動範圍。 例如：

    `requests | where timestamp > ago('2d')`

* **結果限制**。 從入口網站所傳回的結果有大約 1 萬個資料列的限制。 如果超過此限制，就會顯示警告。 如果發生這種情況，排序資料表中的結果不一定會顯示所有實際的第一個或最後一個結果。 

    最好避免達到限制。 請使用時間範圍篩選或使用運算子，例如：

  * [top 100 by timestamp](https://docs.loganalytics.io/queryLanguage/query_language_topoperator.html) 
  * [take 100](https://docs.loganalytics.io/queryLanguage/query_language_takeoperator.html)
  * [summarize ](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html) 
  * [where timestamp > ago(3d)](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html)

(想要一萬個以上的資料列嗎？ 請考慮改用[連續匯出](app-insights-export-telemetry.md)。 「分析」是設計來進行分析的，不是用來擷取未經處理的資料。)

## <a name="diagrams"></a>圖表
選取您想要的圖表類型︰

![選取圖表類型](./media/app-insights-analytics-using/230.png)

如果您有數個正確類型的資料行，您可以選擇 x 和 y 軸，以及一個資料行的維度來據以分割結果。

根據預設，結果一開始會顯示為資料表，而您會手動選取圖表。 但您可以在查詢結尾使用 [Render 指示詞](https://docs.loganalytics.io/queryLanguage/query_language_renderoperator.html) 以選取圖表。

### <a name="analytics-diagnostics"></a>分析診斷


在時間圖表上，如果您的資料突然激增或升級，您可能會在線上看到反白顯示的點。 這表示分析診斷已識別出可將突然變更篩選掉的屬性組合。 按一下點可取得篩選條件的詳細資料，以及查看篩選的版本。 這可能有助於您找出造成變更的原因。 

[深入了解分析診斷](app-insights-analytics-diagnostics.md)


![分析診斷](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>釘選到儀表板
您可以將圖表或資料表釘選至您的其中一個[共用儀表板](app-insights-dashboards.md) - 只要按一下 [釘選]。 (您可能需要[升級應用程式的資費套餐](app-insights-pricing.md)才能開啟此功能。) 

![按一下 [釘選]](./media/app-insights-analytics-using/pin-01.png)

這表示，當您組建出儀表板來協助您監控 Web 服務的效能或使用量時，您可以在其中加入相當複雜的分析以及其他度量。 

如果資料表有四個或更少的資料行，即可將該資料表釘選到儀表板。 只會顯示前七個資料列。

### <a name="dashboard-refresh"></a>儀表板重新整理
釘選到儀表板的圖表大約每小時會重新執行一次查詢，來自動重新整理。 您也可以按一下 [重新整理] 按鈕。

### <a name="automatic-simplifications"></a>自動簡化

當您將圖表釘選到儀表板時，圖表會套用某些簡化效果。

**時間限制：**查詢會自動限制為過去 14 天。 效果就像您的查詢包含 `where timestamp > ago(14d)` 一樣。

**量化計數限制：**如果您顯示的是有許多不連續量化的圖表 (通常是長條圖)，所佔比例較少的量化會自動分組到單一的「其他」量化。 例如，下列查詢︰

    requests | summarize count_search = count() by client_CountryOrRegion

在分析中看起來像這樣︰

![具有長尾的圖表](./media/app-insights-analytics-using/pin-07.png)

但是，當您將它釘選到儀表板時，它看起來像這樣︰

![具有有限長條的圖表](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>匯出至 Excel
執行查詢之後，您可以下載 .csv 檔案。 按一下 [匯出] > [Excel]。

## <a name="export-to-power-bi"></a>匯出至 Power BI
將游標放在查詢中，然後選擇 [匯出] > [Power BI]。

![從分析匯出至 Power BI](./media/app-insights-analytics-using/240.png)

您可在 Power BI 中執行查詢。 您可以將它設定為依照排程重新整理。

您可以使用 Power BI 來建立儀表板，將從各種來源的資料結合在一起。

[深入了解如何匯出至 Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>深層連結

在 [匯出] > [共用連結] 底下取得您可以傳送給另一位使用者的連結。 如果該使用者[能夠存取您的資源群組](app-insights-resources-roles-access-control.md)，該查詢就會在「分析」UI 中開啟。

(在該連結中，查詢文字會顯示在 "?q=" 之後，以 gzip 格式壓縮並採用 Base-64 編碼。 您可以撰寫程式碼來產生要提供給使用者的深層連結。 不過，建議的方式是使用 [REST API](https://dev.applicationinsights.io/) 從程式碼執行「分析」。)


## <a name="automation"></a>自動化

請使用[資料存取 REST API](https://dev.applicationinsights.io/) 來執行「分析」查詢。 [例如](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (使用 PowerShell)：

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

與「分析」UI 不同，REST API 並不會在您的查詢中自動新增任何時間戳記限制。 請記得新增您自己的 where 子句，以避免收到大量回應。



## <a name="import-data"></a>匯入資料

您可以從 CSV 檔案匯入資料。 典型的用法是從您的遙測匯入可以與資料表聯結的靜態資料。 

例如，如果已驗證的使用者在您的遙測中是由別名或經混淆處理的識別碼識別，則您可以匯入一個將別名與真名對應的資料表。 透過對要求遙測執行聯結，您就可以在「分析」報表中依使用者的真名來識別使用者。

### <a name="define-your-data-schema"></a>定義資料結構描述

1. 按一下 [設定] \(在左上方)，然後按一下 [資料來源]。 
2. 依照指示，新增資料來源。 系統會要求您提供資料範例，此範例應該至少包含 10 個資料列。 接著，您需更正結構描述。

這定義了資料來源，您可以接著使用它來匯入個別資料表。

### <a name="import-a-table"></a>匯入資料表

1. 從清單中開啟您的資料來源定義。
2. 按一下 [上傳]，然後依照指示將資料表上傳。 這包含對 REST API 的呼叫，因此相當容易自動化。 

您的資料表現在已可在「分析」查詢中使用。 它會顯示在「分析」中 

### <a name="use-the-table"></a>使用資料表

假設您的資料來源定義稱為 `usermap`，而它包含 `realName` 和 `user_AuthenticatedId` 這兩個欄位。 `requests` 資料表也有一個名為 `user_AuthenticatedId` 的欄位，因此可以很容易將它們聯結：

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
產生的要求資料表會有一個額外的資料行 `realName`。

### <a name="import-from-logstash"></a>從 LogStash 匯入

如果您使用 [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)，您就可以使用「分析」來查詢您的記錄檔。 請使用[透過管道將資料傳送到分析的外掛程式](https://github.com/Microsoft/logstash-output-application-insights)。 

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


