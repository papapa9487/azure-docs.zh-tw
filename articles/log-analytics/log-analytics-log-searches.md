---
title: "使用 Azure Log Analytics 中的記錄檔搜尋來尋找資料 | Microsoft Docs"
description: "記錄檔搜尋可讓您結合和相互關聯您環境內多個來源的任何電腦資料。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 0d7b6712-1722-423b-a60f-05389cde3625
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.openlocfilehash: d679ca7a01a96bd398b26e6a545e33674ae33390
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2017
---
# <a name="find-data-using-log-searches-in-log-analytics"></a>在 Log Analytics 中使用記錄搜尋以尋找資料

>[!NOTE]
> 本文說明在 Azure Log Analytics 中使用舊版查詢語言的記錄搜尋。  如果您的工作區已升級為[新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則您應該參閱[了解 Log Analytics 中的記錄搜尋 (新)](log-analytics-log-search-new.md)。


Log Analytics 的核心是記錄檔搜尋功能，可讓您結合和相互關聯您環境內多個來源的任何電腦資料。 解決方案也是由記錄搜尋發動，可讓您針對特定問題領域進行度量。

在 [搜尋] 頁面上，您可以建立查詢，然後在搜尋時，您可以使用 Facet 控制篩選結果。 您也可以建立進階查詢來轉換、篩選和報告結果。

常見的記錄搜尋查詢會出現在大部分解決方案頁面中。 在整個 OMS 主控台中，您可以按一下圖格或向內切入其他項目，使用 [記錄檔搜尋] 來檢視項目的詳細資料。

在本教學課程中，我們將逐步解說範例，以涵蓋您使用記錄搜尋時的所有基本項目。

我們會以簡單、實用的範例開頭，然後根據這些範例進一步說明，讓您可以了解實際的使用案例，了解如何使用語法從資料擷取您想要的觀點。

在熟悉搜尋技術之後，您可以檢閱 [Log Analytics 記錄檔搜尋參考資料](log-analytics-search-reference.md)。

## <a name="use-basic-filters"></a>使用基本篩選器
首先要知道的事情是搜尋查詢的第一個部分，在任何 "|" 分隔號字元之前，一律為「篩選器」 。 您可以將它當做 TSQL 中的 WHERE 子句--它會判斷要從 OMS 資料存放區提取「哪個」  資料子集。 在資料存放區中搜尋主要是有關指定您想要擷取的資料特性，因此查詢會很自然地以 WHERE 子句開頭。

您可以使用之最基本的篩選器為「關鍵字」 ，例如 'error' 或 'timeout' 或電腦名稱。 這些簡單的查詢類型通常會在相同的結果集內傳回各種資料圖形。 這是因為 Log Analytics 在系統中有不同「類型」  的資料。

### <a name="to-conduct-a-simple-search"></a>進行簡單搜尋
1. 在 OMS 入口網站中，按一下 [記錄檔搜尋] 。  
    ![搜尋圖格](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. 在查詢欄位中，輸入 `error`，然後按一下 [搜尋]。  
    ![搜尋錯誤](./media/log-analytics-log-searches/oms-search-error.png)  
    例如，在下圖中，查詢 `error` 會傳回 100,000 個 **Event** 記錄 (由記錄檔管理所收集)、18 個 **ConfigurationAlert** 記錄 (由組態評估所產生)，以及 12 個 **ConfigurationChange** 記錄 (由變更追蹤所擷取)。   
    ![搜尋結果](./media/log-analytics-log-searches/oms-search-results01.png)  

這些篩選器不一定是物件類型/類別。  只是附加到資料片段的標記或屬性，或字串/名稱/類別。 系統中的某些文件會標記為 **Type:ConfigurationAlert**，而某些會標記為 **Type:Perf** 或 **Type:Event** 等等。 每個搜尋結果、文件、記錄或項目都會顯示所有未經處理的屬性及它們針對每個資料片段的値，當您只想擷取欄位在其中具有指定値的記錄時，您可以在篩選器中使用那些欄位名稱來指定。

「類型」其實只是所有記錄都有的欄位，與任何其他欄位並沒有不同。 這是根據 [類型] 欄位的值所建立的。 該記錄會有不同的圖形或形式。 順帶一提，**Type=Perf** 或 **Type=Event** 也是您需要了解以查詢效能資料或事件的語法。

您可以在欄位名稱之後和值之前使用冒號 (:) 或等號 (=)。 **Type:Event** and **Type=Event** 在意義上是相等的，您可以選擇您偏好的樣式。

因此，如果 Type=Perf 記錄具有名為 'CounterName' 的欄位，您就可以撰寫類似於 `Type=Perf CounterName="% Processor Time"`的查詢。

如此可以只提供您效能資料，其中的效能計數器名稱是"%Processor Time"。

### <a name="to-search-for-processor-time-performance-data"></a>搜尋處理器時間效能資料
* 在搜尋查詢欄位中，輸入 `Type=Perf CounterName="% Processor Time"`

您也可以更明確地在查詢中使用 **InstanceName=_'Total'**，此為 Windows 效能計數器。 您也可以選取 facet 和另一個 **field:value**。 篩選器會自動加入至查詢列的篩選器中。 您可以在下列映像中看到此情況。 其中向您顯示在哪裡按一下，以將 **InstanceName:’_Total’** 新增至查詢，而不需要輸入任何項目。

![搜尋 facet](./media/log-analytics-log-searches/oms-search-facet.png)

您的查詢現在已成為 `Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

在此範例中，您不必指定 **Type=Perf** 來取得這個結果。 因為 CounterName 和 InstanceName 欄位只會為 Type=Perf 的記錄而存在，所以特定的查詢會傳回的結果和先前較長的結果相同：

```
CounterName="% Processor Time" InstanceName="_Total"
```

這是因為查詢中的所有篩選器都會彼此評估為「AND」  。 事實上，您將愈多欄位加入至準則，您得到的更特定且精緻的結果愈少。

例如，查詢 `Type=Event EventLog="Windows PowerShell"` 等同於 `Type=Event AND EventLog="Windows PowerShell"`。 它會傳回記錄於 Windows PowerShell 事件記錄檔中以及從該事件記錄檔收集到的所有事件。 如果您藉由重複選取相同的 facet 以多次加入篩選器，此問題會相當明確--它可能會干擾 [搜尋] 列，但它仍會傳回相同的結果，因為隱含 AND 運算子一律都會在其中。

您可以藉由明確使用 NOT 運算子，輕鬆地反轉隱含 AND 運算子。 例如：

`Type:Event NOT(EventLog:"Windows PowerShell")` 或其相等的 `Type=Event EventLog!="Windows PowerShell"` 會從「不是」Windows PowerShell 記錄檔的所有其他記錄檔傳回所有事件。

或者，您可以使用其他布林運算子，例如 'OR'。 下列查詢會傳回 EventLog 為應用程式或系統的記錄。

```
EventLog=Application OR EventLog=System
```

使用上述查詢，您會在相同的結果集中取得兩個記錄的項目。

不過，如果您藉由保留隱含 AND 以移除 OR，下列查詢就不會產生任何結果，因為沒有屬於這兩個記錄的事件記錄項目。 每個事件記錄項目只會寫入至兩個記錄之一。

```
EventLog=Application EventLog=System
```


## <a name="use-additional-filters"></a>使用其他篩選器
下列查詢會傳回已傳送資料的所有電腦的 2 個事件記錄的項目。

```
EventLog=Application OR EventLog=System
```

![搜尋結果](./media/log-analytics-log-searches/oms-search-results03.png)

選取其中一個欄位或篩選器會縮小查詢範圍到特定的電腦，並排除所有其他電腦。 產生的查詢如下所示。

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

因為隱含 AND 的緣故，這相當於下列內容。

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

每個查詢都會以下列明確的順序進行評估。 請注意括號。

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

就像事件記錄欄位一般，您可以藉由新增 OR，即可只擷取一組特定電腦的資料。 例如：

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

同樣地，下列查詢只會傳回已選取之兩部電腦的 **% CPU 時間** 。

```
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```

### <a name="field-types"></a>欄位類型
建立篩選時，您應該了解使用記錄搜尋所傳回之不同欄位類型的差異。

**可搜尋的欄位**會在搜尋結果中以藍色顯示。  您可以在欄位特定搜尋條件中使用可搜尋的欄位，如下所示：

```
Type: Event EventLevelName: "Error"
Type: SecurityEvent Computer:Contains("contoso.com")
Type: Event EventLevelName IN {"Error","Warning"}
```

**自然語言檢索搜尋旗標欄位**會在搜尋結果中以灰色顯示。  這些欄位無法像是可搜尋的欄位一樣搭配欄位特定搜尋條件使用。  只有在跨所有欄位執行查詢時才能進行搜尋，如下所示。

```
"Error"
Type: Event "Exception"
```


### <a name="boolean-operators"></a>布林運算子
在日期時間和數字欄位中，您可以使用「大於」、「小於」和「小於或等於」來搜尋值。 您可以使用簡單的運算子，例如查詢搜尋列中的 >、<、>=、<=、!=。

您可以查詢特定一段時間的特定事件記錄。 例如，過去 24 小時會使用下列助憶鍵運算式表示。

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### <a name="to-search-using-a-boolean-operator"></a>使用布林運算子來搜尋
* 在搜尋查詢欄位中，輸入 `EventLog=System TimeGenerated>NOW-24HOURS`  
    ![使用布林值來搜尋](./media/log-analytics-log-searches/oms-search-boolean.png)

雖然您可以以圖形方式控制時間間隔，而且大多數時候您可能會想這麼做，但是將時間篩選器直接包含在查詢中還是有其優點。 例如，這非常適合儀表板，無論儀表板頁面上的「全域」  時間選取器為何，您都可以在其中覆寫每個磚的時間。 如需詳細資訊，請參閱 [時間對儀表板很重要](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/)。

藉由時間篩選時，請記住，您會得到兩個時間週期的「交集」  結果：一個時間週期指定於 OMS 入口網站 (S1)，另一個時間週期指定於查詢 (S2) 中。

![交集](./media/log-analytics-log-searches/oms-search-intersection.png)

這表示，如果時間週期沒有交集，例如，您在 OMS 入口網路中選擇**本週**，但在查詢中定義**上週**，則沒有交集，您將不會收到任何結果。

用於 TimeGenerated 欄位的比較運算子在其他情況下也很有用。 例如，用於數值欄位。

例如，假設組態評估的警示有下列嚴重性值：

* 0 = 資訊
* 1 = 警告
* 2 = 重大

您可以查詢警告和重大警示，並且排除具備下列查詢的資訊警示：

```
Type=ConfigurationAlert  Severity>=1
```


您也可以使用範圍查詢。 這表示您可以序列提供値的開始和結束範圍。 例如，如果您想要來自 Operations Manager 事件記錄 (其中 EventID 大於或等於 2100，但小於 2199) 的事件，下列查詢就會傳回它們。

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


> [!NOTE]
> 您必須使用的範圍語法是冒號 (:) field:value 分隔符號，「不」是等號 (=)。 用方括號括住範圍的下限和上限結尾，並使用兩個句點 (..) 隔開它們。
>
>

## <a name="manipulate-search-results"></a>操控搜尋結果
當您在搜尋資料時，您會想要精簡搜尋查詢，並已經對結果有相當程度的控制。 擷取結果時，您可以套用命令以將其轉換。

Log Analytics 搜尋中的命令「必須」  跟在分隔號字元 (|) 之後。 篩選器一律為查詢字串的第一個部分。 它會定義您正在使用的資料集，然後將那些結果「輸送」入命令。 然後您可以使用管道來新增其他命令。 這樣有點類似 Windows PowerShell 管線。

一般而言，Log Analytics 搜尋語言會嘗試遵循 PowerShell 樣式和指導方針，使它類似於 IT 專業人員，並簡化學習曲線。

命令具有動詞的名稱，因此您可以輕易地分辨它們執行的動作。  

### <a name="sort"></a>排序
sort 命令可讓您利用一或多個欄位來定義排序順序。 即使您不使用它，根據預設，會強制執行時間遞減順序。 最新的結果永遠在搜尋結果的頂端。 這表示當您執行搜尋時，真正利用 `Type=Event EventID=1234` 執行的內容如下：

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

這是因為它是您在記錄中熟悉的經驗類型。 例如，在 Windows 事件檢視器中。

您可以使用 Sort 來變更傳回結果的方式。 下列範例顯示此運作方式。

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


上述的簡單範例顯示命令的運作方式--它們會變更篩選器傳回結果的圖形。

### <a name="limit-and-top"></a>Limit 和 top
另一個較少人知道的命令是 LIMIT。 Limit 為類似 PowerShell 的動詞。 Limit 在功能上和 TOP 命令相同。 下列查詢會傳回相同的結果。

```
Type=Event EventID=600 | Limit 1
```

```
Type=Event EventID=600 | Top 1
```


#### <a name="to-search-using-top"></a>使用 top 搜尋
* 在搜尋查詢欄位中，輸入 `Type=Event EventID=600 | Top 1`   
    ![搜尋 top](./media/log-analytics-log-searches/oms-search-top.png)

在上圖中，有 358 個記錄含有 EventID=600。 左邊的欄位、facet 和篩選器永遠會顯示查詢的「篩選器部分」  所傳回的結果資訊，也就是任何縱線字元之前的部分。 [結果]  窗格只會傳回最新的 1 個結果，因為範例命令會圖形化和轉換結果。

### <a name="select"></a>選取
SELECT 命令的行為類似 PowerShell 中的 Select-Object。 它會傳回不包含所有原始屬性的篩選結果。 相反地，它只會選取您所指定的屬性。

#### <a name="to-run-a-search-using-the-select-command"></a>使用 select 命令執行搜尋
1. 在 [搜尋] 中，輸入 `Type=Event`，然後按一下 [搜尋]。
2. 按一下其中一種結果中的 [+ 顯示更多]  ，以檢視結果具有的屬性。
3. 明確地選取部分，查詢會變更為 `Type=Event | Select Computer,EventID,RenderedDescription`。  
    ![搜尋 select](./media/log-analytics-log-searches/oms-search-select.png)

當您想要控制搜尋輸出，並且只選擇對探索真的很重要的資料部分 (通常不是完整記錄) 時，這會是特別有用的命令。 當不同類型的記錄有「部分」通用屬性，但不是「所有」屬性都共用時，這也非常有用。 然後，您可以產生看起來就像資料表一般自然的輸出，或在匯出至 CSV 檔案，然後傳遞訊息至 Excel 中時正常運作。

## <a name="use-the-measure-command"></a>使用 measure 命令
MEASURE 是 Log Analytics 搜尋中最具彈性的命令之一。 它可讓您將統計「函數」  套用至資料，並依照指定的欄位分組來彙總結果。 Measure 支援多個統計函數。

### <a name="measure-count"></a>Measure count()
第一個要使用的統計函數，也是最容易了解的統計函數就是「count()」  函數。

來自任何搜尋查詢 (例如 `Type=Event`) 的結果會在搜尋結果的左邊顯示篩選，又稱為 Facet。 篩選器會在執行的搜尋中透過指定的結果欄位顯示值的分佈。

![搜尋 measure count](./media/log-analytics-log-searches/oms-search-measure-count01.png)

例如，在上面的影像中，您會看到 **Computer** 欄位，它指出在結果中將近 739,000 個事件內，那些記錄中的 **Computer** 欄位有 68 個唯一且不同的值。 磚只會顯示前 5 個値 (也就是最常寫入 **Computer** 欄位中的 5 個值)，依據在該欄位中含有該特定値的文件數目來排序。 在圖中，您可以看到 – 在將近 36 萬 9 千個事件之中 - 9 萬個事件來自 OpsInsights04.contoso.com 電腦，8 萬 3 千個事件來自 DB03.contoso.com 電腦，諸如此類。

由於磚只會顯示前 5 個値，如果要查看所有的值怎麼辦？

measure 命令可以使用 count () 函數達成。 此函數不會使用任何參數。 您只需要指定想做為分組依據的欄位即可 – 在此案例中為 **Computer** 欄位：

`Type=Event | Measure count() by Computer`

![搜尋 measure count](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

不過，**Computer** 只是每個資料片段「中」使用的欄位 - 不涉及關聯式資料庫，其他地方也都沒有別的 **Computer** 物件。 只有資料「中」的値可以描述哪個實體產生它們，以及資料的其他一些特性和面向 - 所以才稱為 *Facet*。 不過，您也可以根據其他欄位分組。 因為輸送到 measure 命令的將近 73 萬 9 千個事件的原始結果也有一個稱為 **EventID** 的欄位，所以您可以套用相同的技巧，依據該欄位分組，並根據 EventID 取得事件計數：

```
Type=Event | Measure count() by EventID
```

如果您對包含特定値的實際記錄計數不感興趣，而只想要取得值本身的清單，您可以在尾端加上「Select」  命令，並只選取第一個資料行：

```
Type=Event | Measure count() by EventID | Select EventID
```

然後您就可以在查詢中取得更複雜和預先排序的結果，或者您也可以只按一下方格中的資料行。

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### <a name="to-search-using-measure-count"></a>使用 measure count 搜尋
* 在搜尋查詢欄位中，輸入 `Type=Event | Measure count() by EventID`
* 將 `| Select EventID` 附加至查詢的結尾。
* 最後，將 `| Sort EventID asc` 附加至查詢的結尾。

有一些要注意與強調的重點：

首先，您看到的結果不是未經處理的結果了。 相反地，它們是彙總的結果 – 基本上是結果的群組。 這不是問題，但您應該了解您正與非常不同的資料圖形互動，其差異在於即時建立為彙總/統計函數結果未經處理的圖形。

第二， **Measure count** 目前只會傳回前 100 個不同結果。 這項限制不適用於其他統計函數。 因此，您通常必須先使用更精確的篩選器來搜尋特定項目，然後再套用 measure count()。

## <a name="use-the-max-and-min-functions-with-the-measure-command"></a>透過 measure 命令使用 max 和 min 函數
有 **Measure Max()** 和 **Measure Min()** 在其中很有用的各種案例。 不過，由於每個函數彼此相反，我們將說明 Max ()，而您可以自己實驗 Min ()。

如果您查詢安全性事件，它們會有不同的**層級**屬性。 例如：

```
Type=SecurityEvent
```

![搜尋 measure count 開始](./media/log-analytics-log-searches/oms-search-measure-max01.png)

如果您想要在指定共用「電腦」(根據欄位的分組) 的情況下，檢視所有安全性事件的最高值，您可以使用

```
Type=ConfigurationAlert | Measure Max(Level) by Computer
```

![搜尋 measure max 電腦](./media/log-analytics-log-searches/oms-search-measure-max02.png)

這將會顯示在具有**層級**記錄的電腦中，大部分都至少為層級 8，而有許多都是層級 16。

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![搜尋 measure max 時間產生電腦](./media/log-analytics-log-searches/oms-search-measure-max03.png)

此函數適用於數字，但它也可以使用日期時間欄位。 您最好檢查最後一個或最新的時間戳記，是否有任何為每台電腦編製索引的資料片段位。 例如︰何時為每一部電腦回報最新的安全性事件？

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## <a name="use-the-avg-function-with-the-measure-command"></a>透過 measure 命令使用avg 函數
和 measure 搭配使用的 Avg () 統計函數可讓您計算某些欄位的平均值，並且根據相同或其他欄位將結果分組。 這適用於各種情況，例如效能資料。

我們將從效能等級開始介紹。 請注意，OMS 目前會收集 Windows 和 Linux 機器的效能計數器。

若要搜尋「所有」  效能資料，最基本的查詢如下：

```
Type=Perf
```

![搜尋 avg 開始](./media/log-analytics-log-searches/oms-search-avg01.png)

首先您會注意到 Log Analytics 顯示三種觀點︰清單，顯示圖表背後的真正記錄；資料表，顯示效能計數器資料的表格式檢視；度量，顯示效能計數器的圖表。

在上述映像中，有兩組標示的欄位，指出下列事項：

* 第一組會識別查詢篩選器中的 Windows 效能計數器名稱、物件名稱和執行個體名稱。 這些是您可能會最常用來做為 facet/篩選器的欄位
* **CounterValue** 是計數器的實際值。 在此範例中，值是 *75*。
* **TimeGenerated** 為 12:51，採用 24 小時制的時間格式。

以下是圖形中的度量檢視。

![搜尋 avg 開始](./media/log-analytics-log-searches/oms-search-avg02.png)

閱讀 Perf 記錄圖形，並閱讀其他搜尋技術之後，您可以使用measure Avg () 來彙總此類型的數值資料。

以下是簡單的範例：

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![搜尋 avg samplevalue](./media/log-analytics-log-searches/oms-search-avg03.png)

在此範例中，您可以選取 CPU 時間總計效能計數器並根據電腦來平均。 如果您想要將結果縮小到只有過去 6 小時，您可以使用時間篩選控制項，或如下所示在查詢中指定︰

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### <a name="to-search-using-the-avg-function-with-the-measure-command"></a>搭配使用 avg 函數和 measure 命令進行搜尋
* 在 [搜尋查詢] 方塊中，輸入 `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`。

您可以「跨」  電腦彙總資料，並將其相互關聯。 例如，假設您有一組某種伺服器陣列的主機，其中的節點彼此相等，而且它們執行的工作類型完全相同，負載也應該大致平衡。 您可以利用下列查詢一次取得所有計數器，並取得整個伺服器陣列的平均。 您可以藉由選擇電腦，以下列範例開頭：

```
Type=Perf AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

您擁有電腦之後，也只會想要選取兩個關鍵效能指標 (KPI)：% CPU 使用量和 % 可用磁碟空間。 因此，查詢的該部分會變成：

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

您現在可以利用下列範例新增電腦和計數器：

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

因為您有非常明確的選取範圍，只要依據 CounterName 分組， **measure Avg ()** 命令可以傳回不是依電腦算出的平均，而是跨伺服器陣列算出的平均。 例如：

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03") | Measure Avg(CounterValue) by CounterName
```

這可提供您數個環境 KPI 的實用精簡檢視。

![搜尋 avg grouping](./media/log-analytics-log-searches/oms-search-avg04.png)

您可以在儀表板中輕鬆使用搜尋查詢。 例如，您可以儲存搜尋查詢，再利用它來建立名為 *Web 伺服陣列 KPI* 的儀表板。 若要深入了解如何使用儀表板，請參閱 [在 Log Analytics 中建立自訂的儀表板](log-analytics-dashboards.md)。

![搜尋 avg 儀表板](./media/log-analytics-log-searches/oms-search-avg05.png)

### <a name="use-the-sum-function-with-the-measure-command"></a>透過 measure 命令使用 sum 函數
sum 函數和 measure 命令的他函數類似。 您可以在 [Microsoft Azure Operational Insights 中的 W3C IIS 記錄搜尋](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)看到如何使用 sum 函數的範例。

您可以搭配使用 Max() 和 Min() 與數字、日期時間和文字字串。 透過文字字串，它們會依字母順序排序，而您會取得第一個和最後一個。

不過，您無法搭配使用 Sum() 與數值欄位以外的任何項目。 這也適用於 Avg()。

### <a name="use-the-percentile-function-with-the-measure-command"></a>搭配使用 percentile 函數與 measure 命令
percentile 函數與 Avg() 和 Sum() 的類似之處在於，您只能將它用在數值欄位。 您可以在數值欄位中使用 1 到 99 之間的任何百分位數。 您也可以同時使用 **percentile** 和 **pct** 命令。 以下提供一些範例：  

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## <a name="use-the-where-command"></a>使用 where 命令
where 命令的運作方式和篩選器類似，但它可以套用在管線中，以進一步篩選由 Measure 命令產生的彙總結果 - 而不是在查詢開頭篩選過的未經處理的結果。

例如：

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

您可以利用下列範例，加入另一個縱線 "|" 字元以及 Where 命令，即可只取得平均 CPU 高於 80% 的電腦：

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

如果您已熟悉 Microsoft System Center Operations Manager，您可以考慮管理組件詞彙中的 where 命令。 如果此範例是一項規則，查詢的第一個部分會是資料來源，而 where 命令會是條件偵測。

您可以在 [我的儀表板] 中將查詢當做一個磚來使用，做為一種監視器來查看電腦 CPU 是否過度使用。 若要深入了解儀表板，請參閱 [在 Log Analytics 中建立自訂的儀表板](log-analytics-dashboards.md)。 您也可以使用行動應用程式建立和使用儀表板。 如需詳細資訊，請參閱 [OMS 行動應用程式 ](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)。 在下列映像底部的兩個磚中，您可以看到監視器顯示清單並做為一個數字。 基本上，您一定想要此數字為零且清單是空的。 否則，它會指出警示條件。 如有需要，您可以使用它來看看那些電腦承受壓力。

![行動儀表板](./media/log-analytics-log-searches/oms-search-mobile.png)

## <a name="use-the-in-operator"></a>使用 in 運算子
*IN* 運算子以及 *NOT IN* 可讓您使用子搜尋，也就是包含另一個搜尋來做為引數的搜尋。 它們會以大括號 {} 包含在另一個「主要」或「外部」搜尋內。 子搜尋的結果 (通常是不同結果的清單) 接著會做為其主要搜尋中的引數。

您可以使用子搜尋來比對無法在搜尋運算式中直接描述，但可以透過搜尋來產生的資料子集。 例如，如果您想要使用某個搜尋來尋找「遺漏安全性更新的電腦」中的所有事件，則您必須設計子搜尋來先識別「遺漏安全性更新的電腦」，再找到隸屬於這些主機的事件。

因此，您可以使用下列查詢來表示「目前遺漏必要安全性更新的電腦」  ︰

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![IN 搜尋範例](./media/log-analytics-log-searches/oms-search-in01-revised.png)

有了清單之後，您可以使用此搜尋做為內部搜尋，將電腦清單送入會尋找這些電腦之事件的外部 (主要) 搜尋。 若要這麼做，您必須以大括號括住內部搜尋，並使用 IN 運算子將其結果提供做為外部搜尋中篩選條件/欄位的可能值。 查詢會類似下列內容︰

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![IN 搜尋範例](./media/log-analytics-log-searches/oms-search-in02-revised.png)

也請注意內部搜尋中使用的時間篩選條件，因為「系統更新評估」每隔 24 小時就會擷取所有電腦的快照。 您可以藉由只搜尋一天來讓內部查詢變得更輕巧精確。 外部搜尋則會使用使用者介面中的時間選項，擷取過去 7 天的事件。 如需時間運算子的詳細資訊，請參閱 [布林運算子](#boolean-operators) 。

由於您實際上只會使用內部搜尋結果做為外部搜尋的篩選條件值，因此您仍然可以在外部搜尋中套用命令。 例如，您仍然可以使用另一個 measure 命令來分組上述事件︰

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![IN 搜尋範例](./media/log-analytics-log-searches/oms-search-in03-revised.png)

一般而言，因為 Log Analytics 對內部查詢設定了服務端逾時，因此您會希望它快速執行，並且您也會希望它傳回少量的結果。 如果內部查詢傳回較多個結果，結果清單會遭到截斷，這可能會導致外部搜尋傳回不正確的結果。

另一個規則是，內部搜尋目前需要提供「彙總的」  結果。 換句話說，它必須包含「measure」  命令；您目前無法將未經處理的結果送到外部搜尋。

此外，只能有一個 IN 運算子，而且它必須是查詢中的最後一個篩選條件。 多個 IN 運算子無法使用 OR 來連接，這基本上會防止執行多個子搜尋︰重點是，每個外部搜尋只能有一個子搜尋/內部搜尋。

即使有這些限制，IN 仍可實現許多類型的相互關聯搜尋，並可讓您定義類似群組的物件，例如電腦、使用者或檔案，無論您的資料欄位中包含什麼內容。 以下還有其他範例：

**已停用自動更新設定的電腦中遺失的所有更新**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**執行 SQL Server (即 SQL 評估執行所在) 的電腦中的所有錯誤事件**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**屬於網域控制站 (即 AD 評估執行所在) 的電腦中的所有安全性事件**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**帳戶 BACONLAND\jochan 已登入的電腦上，還有其他哪些帳戶已登入？**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## <a name="use-the-distinct-command"></a>使用 distinct 命令
正如其名，此命令會提供欄位的相異值清單。 它出乎意料地簡單，但卻相當實用。 使用 measure count() 命令也可達到相同結果，如下所示。

```
Type=Event | Measure count() by Computer
```

![DISTINCT 搜尋命令範例](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

不過，如果您只想要相異值清單而不要具有那些值的文件計數，則 DISTINCT 可提供更清晰易懂的輸出和更短的語法，如下所示。

```
Type=Event | Distinct Computer
```
![DISTINCT 搜尋命令範例](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## <a name="use-the-countdistinct-function-with-the-measure-command"></a>搭配使用 countdistinct 函數與 measure 命令
countdistinct 函數會計算每個群組內的相異值數目。 例如，它可以用來計算針對每個類型報告的唯一電腦數目︰

```
* | measure countdistinct(Computer) by Type
```

![OMS-countdistinct](./media/log-analytics-log-searches/oms-countdistinct.png)

## <a name="use-the-measure-interval-command"></a>使用 measure interval 命令
透過接近即時的效能資料收集，您可以在 Log Analytics 中收集和視覺化任何效能計數器。 只要輸入查詢 **Type:Perf** ，就會根據 Log Analytics 環境中的計數器和伺服器數目傳回數千個度量圖形。 透過隨選的度量彙總，您可以在較高的層級查看環境中的整體度量，並視需要深入了解更細微的資料。

例如，假設您想要知道您所有電腦的平均 CPU。 查看每一部電腦的平均 CPU 可能沒什麼幫助，因為結果可能會變得平順。若要深入查看更多細節，您可以彙總較小時間區塊的結果，並深入查看跨越不同維度的時間序列。 例如，您可以跨所有電腦執行每小時的平均 CPU 使用量，如下所示︰

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![測量平均間隔](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

根據預設，這些結果會顯示在多數列的互動式折線圖中。  這個圖表支援數列切換 (透過 y 軸重新調整)、縮放和暫留。  如有必要，資料表顯示選項仍可供檢視原始資料。

您也可以根據其他欄位分組。 在此範例中，我要查看某個特定電腦的所有 % 計數器，我想要知道每個計數器每小時的第 70 個百分位數為何︰

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
請注意一件事，這些查詢並不限於效能計數器。 您可以將它們套用至任何度量。 在此範例中，我要查看 W3C IIS 記錄檔。 我想要知道以 5 分鐘做為間隔時，它最多需要花多少時間來處理每個要求︰

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### <a name="use-multiple-aggregates-in-one-query"></a>在單一查詢中使用多個彙總
您可以在 measure 命令中指定多個彙總子句。  每個子句皆可獨立給予別名。  如果未給予別名，所產生的欄位名稱將會是所使用的彙總函數 (亦即，若使用 avg(CounterValue) 函數，則名稱為 "avg(CounterValue)")。

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![OMS-multiaggregates1](./media/log-analytics-log-searches/oms-multiaggregates1.png)

以下是另一個範例︰

 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## <a name="next-steps"></a>後續步驟
如需關於記錄檔搜尋的其他資訊，請參閱：

* 使用 [Log Analytics 中的自訂欄位](log-analytics-custom-fields.md) 來延伸記錄檔搜尋。
* 檢閱 [Log Analytics 記錄檔搜尋參考資料](log-analytics-search-reference.md) ，以檢視 Log Analytics 中提供的所有搜尋欄位和 Facet。
