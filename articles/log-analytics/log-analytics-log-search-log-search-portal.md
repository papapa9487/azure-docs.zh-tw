---
title: "在 Azure Log Analytics 中使用記錄搜尋入口網站 | Microsoft Docs"
description: "本文包含一篇教學課程，說明如何使用記錄搜尋入口網站來建立記錄搜尋，以及分析儲存在 Log Analytics 工作區的資料。  教學課程包含執行一些簡單查詢以傳回不同類型的資料並分析結果。"
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
ms.date: 08/23/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 6fc556ceb34cde26d5f3789a2397cdaa34b0b84d
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>在 Azure Log Analytics 中使用記錄搜尋入口網站來建立記錄搜尋

> [!NOTE]
> 本文會說明 Azure Log Analytics 中使用新查詢語言的記錄搜尋入口網站。  您可以在[將 Azure Log Analytics 工作區升級為新的記錄搜尋](log-analytics-log-search-upgrade.md)中，深入了解新的語言，並且取得升級工作區的程序。  
>
> 如果您的工作區尚未升級為新的查詢語言，您應該參閱[在 Log Analytics 中使用記錄搜尋以尋找資料](log-analytics-log-searches.md)，以取得記錄搜尋入口網站目前版本的資訊。

本文包含一篇教學課程，說明如何使用記錄搜尋入口網站來建立記錄搜尋，以及分析儲存在 Log Analytics 工作區的資料。  教學課程包含執行一些簡單查詢以傳回不同類型的資料並分析結果。  其著重在記錄搜尋入口網站的功能，可修改查詢內容卻不必直接修改查詢本身。  如需直接編輯查詢的詳細資訊，請參閱[查詢語言參考](https://go.microsoft.com/fwlink/?linkid=856079)。

若要在進階 Analytics 入口網站 (而非記錄搜尋入口網站) 中建立搜尋，請參閱 [Analytics 入口網站的使用者入門](https://go.microsoft.com/fwlink/?linkid=856587)。  這兩個入口網站使用相同的查詢語言在 Log Analytics 工作區中存取相同的資料。

## <a name="prerequisites"></a>必要條件
本教學課程假設您已擁有 Log Analytics 工作區，且其具有至少一個連線來源以產生供查詢分析的資料。  

- 如果您沒有工作區，可以使用[開始使用 Log Analytics 工作區](log-analytics-get-started.md)的程序來建立免費工作區。
- 至少將一個 [Windows 代理程式](log-analytics-windows-agents.md)或一個 [Linux 代理程式](log-analytics-linux-agents.md)連線到工作區。  

## <a name="open-the-log-search-portal"></a>開啟記錄搜尋入口網站
從開啟記錄搜尋入口網站開始。  您可以在 Azure 入口網站或 OMS 入口網站存取它。

1. 開啟 Azure 入口網站。
2. 瀏覽至 Log Analytics 並選取您的工作區。
3. 請選取 [記錄搜尋] 以停留在 Azure 入口網站，或選取 [OMS 入口網站] 來啟動 OMS 入口網站，然後按一下 [記錄搜尋] 按鈕。

![記錄檔搜尋按鈕](media/log-analytics-log-search-log-search-portal/log-search-button.png)

## <a name="create-a-simple-search"></a>建立簡單搜尋
若要擷取某些資料來使用，最快的方式是使用會傳回資料表中所有記錄的簡單查詢。  如果有任何 Windows 或 Linux 用戶端連線到您的工作區，則您會擁有事件 (Windows) 或 Syslog (Linux) 資料表中的資料。

在搜尋方塊中輸入以下其中一項查詢，然後按一下搜尋按鈕。  

```
Event
```
```
Syslog
```

資料會傳回到預設清單檢視中，您可以看到傳回的記錄總數。

![簡單查詢](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

每筆記錄只會顯示前幾個屬性。  按一下 [顯示更多] 以顯示特定記錄的所有屬性。

![記錄詳細資料](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>設定時間範圍
Log Analytics 所收集的每一筆記錄都有 **TimeGenerated** 屬性，其包含記錄建立的日期和時間。  記錄搜尋入口網站中的查詢只會傳回 **TimeGenerated** 在時間範圍內的記錄，時間範圍會顯示在畫面左側。  

您可以選取下拉式清單或修改滑桿來變更時間篩選條件。  滑桿會顯示出直條圖，圖中則顯示時間範圍內每個區段的相對記錄數目。  區段會依範圍而有所不同。

預設時間範圍是 **1 天**。  將此值變更為 **7 天**，則記錄總數應該會增加。

![日期時間範圍](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>篩選查詢結果
畫面左側是篩選窗格，可讓您新增篩選條件到查詢中，而不需要直接修改查詢。  會顯示傳回記錄的多個屬性及其前十個值與記錄計數。

如果您使用的是**事件**，選取 [EVENTLEVELNAME] 下方 [錯誤] 旁的核取方塊。   如果您使用的是 **Syslog**，選取 [SEVERITYLEVEL] 下方 [錯誤] 旁的核取方塊。  這會將查詢變更為下列其中一個，以將結果限制為錯誤事件。

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![篩選器](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

從其中一個記錄的屬性功能表中選取 [新增至篩選器]，將屬性新增至篩選窗格。

![新增至篩選功能表](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

您可以從記錄的屬性功能表選取具有所要篩選值的 [篩選]，以設定相同的篩選條件。  

只有名稱是藍色的屬性才有 [篩選] 選項。  這些是可搜尋的欄位，已針對搜尋條件編列索引。  灰色的欄位是「自然語言檢索搜尋旗標」欄位，只有 [顯示參考] 選項。  此選項會傳回在任何屬性中具有該值的記錄。

![篩選功能表](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

您可以選取記錄功能表中的 [分組依據] 選項，在單一屬性上群組結果。  這會將[摘要](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator)運算子新增到查詢中，可在圖表中顯示結果。  您可以群組一個以上的屬性，但需要直接編輯查詢。  選取**電腦**屬性旁的記錄功能表，並選取 [依「電腦」分組]。  

![依電腦分組](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>處理結果
記錄搜尋入口網站有各種功能，以供使用查詢結果。  您可以排序、篩選和群組結果來分析資料，而不需修改實際的查詢。  根據預設，不會排序查詢的結果。

若要以可提供其他篩選和排序選項的資料表形式來檢視資料，按一下 [資料表]。  

![資料表檢視](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

按一下記錄旁的箭號以檢視該記錄的詳細資訊。

![排序結果](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

按一下資料行標題，對欄位進行排序。

![排序結果](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

按一下篩選按鈕並提供篩選條件，以篩選出資料行中具特定值的結果。

![篩選結果](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

將資料行標題拖曳至結果上方，以群組資料行。  您可以將多個資料行拖曳至上方，以群組多個欄位。

![群組結果](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>使用效能資料
Windows 和 Linux 代理程式的效能資料都儲存在 Log Analytics 工作區的**效能**資料表中。  效能記錄看起來就像其他任何記錄，我們可以撰寫會傳回所有效能記錄的簡單查詢，就像使用事件一樣。

```
Perf
```

![效能資料](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

針對所有效能物件和計數器傳回數百萬筆記錄並不太實用。  您可以使用與上述相同的方法來篩選資料，或直接在 [記錄搜尋] 方塊中輸入下列查詢。  這對於 Windows 和 Linux 電腦都只會傳回處理器使用率記錄。

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![處理器使用率](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

這可讓資料限制在特定的計數器，但仍無法以非常實用的形式來呈現資料。  您可透過折線圖顯示資料，但首先需要以 [電腦] 與 [TimeGenerated] 進行群組。  若要群組多個欄位，您需要直接修改查詢，因此，請將查詢修改如下。  這是在 **CounterValue** 屬性上使用 [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) 函式來計算每小時的平均值。

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![效能資料圖表](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

資料既已適當分組，您可以新增[轉譯](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator)運算子，以視覺圖表來顯示資料。  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![折線圖](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>後續步驟

- 在 [Analytics 入口網站的使用者入門](https://go.microsoft.com/fwlink/?linkid=856079)中深入了解 Log Analytics 查詢語言。
- 使用[進階 Analytics 入口網站](https://go.microsoft.com/fwlink/?linkid=856587)的教學課程逐步引導，其可讓您執行相同的查詢及存取相同的資料，如同記錄搜尋入口網站。

