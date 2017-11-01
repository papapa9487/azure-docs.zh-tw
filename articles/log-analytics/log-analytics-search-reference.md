---
title: "Azure Log Analytics 搜尋參考 | Microsoft Docs"
description: "Log Analytics 搜尋參考描述搜尋語言，並且提供一般查詢語法選項，您可以在搜尋資料及篩選運算式時用來幫助您縮小搜尋範圍。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 402615a2-bed0-4831-ba69-53be49059718
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 264ea071dc0b15964af07c68cbf0dee896b07a3e
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2017
---
# <a name="log-analytics-search-reference"></a>Log Analytics 搜尋參考

>[!NOTE]
> 本文說明在 Log Analytics 中使用舊版查詢語言的記錄搜尋。  如果您的工作區已升級為[新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則您應該參閱[新語言的語言參考](https://go.microsoft.com/fwlink/?linkid=856079)。

下列關於搜尋語言的參考章節描述一般查詢語法選項，您可以在搜尋資料及篩選運算式時用來幫助您縮小搜尋範圍。 它也會描述您可以用來在已擷取的資料上採取動作的命令。

您可以在[搜尋欄位和 Facet 參考](#search-field-and-facet-reference)小節中閱讀搜尋中傳回的欄位，以及協助您深入探討相似資料類別的 Facet。

## <a name="general-query-syntax"></a>一般查詢語法
一般查詢的語法如下︰

```
filterExpression | command1 | command2 …
```

篩選運算式 (`filterExpression`) 會定義查詢的 "where" 條件。 命令會套用至查詢所傳回的結果。 多個命令必須以縱線字元 ( | ) 分隔。

### <a name="general-syntax-examples"></a>一般語法範例
範例：

```
system
```

此查詢會傳回在已編製索引的任何欄位中進行全文檢索或詞彙搜尋時包含 system 這個單字的結果。

> [!NOTE]
> 並非所有的欄位都以此方式編製索引，不過最常見的文字欄位 (例如描述和名稱) 通常都是。
>
>

```
system error
```

此查詢會傳回包含 system 和 error 單字的結果。

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

此查詢會傳回包含 system 和 error 單字的結果。 然後會依據 ManagementGroupName 欄位 (依遞增順序)，再依據 TimeGenerated 欄位 (依遞減順序) 排序結果。 它只會採用前 10 個結果。

> [!IMPORTANT]
> 所有的欄位名稱以及字串和文字欄位的值都會區分大小寫。
>
>

## <a name="filter-expressions"></a>篩選運算式
下列小節說明篩選運算式。

### <a name="string-literals"></a>字串常值
字串常值是未經由剖析器辨識為關鍵字或預先定義資料類型 (例如，數字或日期) 的任何字串。

範例：

```
These all are string literals
```

此查詢會搜尋包含五個字全都出現的結果。 若要執行複雜的字串搜尋，請將字串常值以引號括起來。 例如：

```
"Windows Server"
```

這只會傳回結果為 Windows Server 的完全相符項目。

### <a name="numbers"></a>數字
剖析器支援數值欄位的十進位整數和浮點數語法。

範例：

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="dates-and-times"></a>日期和時間
系統中的每個資料片段都有 *TimeGenerated* 屬性，代表記錄的原始日期和時間。 某些資料類型可能另外有日期和時間欄位 (例如，*LastModified*)。

Azure Log Analytics 中的 [時間表/時間] 選取器會顯示經過一段時間的結果分佈 (根據目前執行的查詢)。 這是根據 *TimeGenerated* 欄位。 日期和時間欄位具有特定字串格式，可以在查詢中用來將查詢限制在特定時間範圍內。 您也可以使用語法來參考相對的時間間隔 (例如，「3 天前和 2 小時前之間」)。

以下是有效形式的日期和時間語法：

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


例如：

```
TimeGenerated:2013-10-01T12:20
```

前一個命令只會傳回 *TimeGenerated* 值剛好等於 2013 年 10 月 1 日 12:20 的記錄。

剖析器也支援助憶鍵的日期/時間值，NOW。 (這也不太可能產生任何結果，因為資料無法這麼快速地通過系統)。

這些範例是用於相對與絕對日期的建置組塊。 在接下來的三個子節中，您會透過使用相對日期範圍的範例，看到如何在更進階的篩選條件中使用它們。

### <a name="datetime-math"></a>日期/時間數學
使用日期/時間數學運算子，透過簡單的計算位移或四捨五入日期/時間值。

語法：

```
datetime/unit
```

```
datetime[+|-]count unit
```

| 運算子 | 說明 |
| --- | --- |
| / |將日期/時間四捨五入到指定的單位。 例如，NOW/DAY 會將目前的日期/時間四捨五入至目前日期的午夜。 |
| + 或 - |將日期/時間位移指定的單位數。 例如，NOW+1HOUR 會將目前日期/時間位移晚一個小時。 2013-10-01T12:00-10DAYS 會將日期值位移回 10 天。 |

您可以將日期/時間數學運算子鏈結在一起。 例如：

```
NOW+1HOUR-10MONTHS/MINUTE
```

下表列出受支援的日期/時間單位。

| 日期/時間單位 | 說明 |
| --- | --- |
| YEAR, YEARS |四捨五入為目前的年份，或位移指定的年數。 |
| MONTH, MONTHS |四捨五入為目前的月份，或位移指定的月數。 |
| DAY, DAYS, DATE |四捨五入為目前的月份日期，或位移指定的天數。 |
| HOUR, HOURS |四捨五入為目前的小時，或位移指定的小時數。 |
| MINUTE, MINUTES |四捨五入為目前的分鐘，或位移指定的分鐘數。 |
| SECOND, SECONDS |四捨五入為目前的秒，或位移指定的秒數。 |
| MILLISECOND, MILLISECONDS, MILLI, MILLIS |四捨五入為目前的毫秒，或位移指定的毫秒數。 |

### <a name="field-facets"></a>欄位 facet
使用欄位 Facet，您可以指定特定欄位的搜尋條件及其實際值。 這與針對整個索引的各種詞彙撰寫「任意文字」查詢不同。 您已經在數個先前的範例中看過這項技術。 下列是更複雜的範例。

**語法**

```
field:value
```

```
field=value
```

**說明**

搜尋特定值的欄位。 此值可以是字串常值、數字或日期和時間。

例如：

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**語法**

field>value

field<value

field>=value

field<=value

*field!=value*

**說明**

提供比較。

例如：

```
TimeGenerated>NOW+2HOURS
```

**語法**

```
field:[from..to]
```

**說明**

提供範圍面相化。

例如：

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="in"></a>IN
**IN** 關鍵字可讓您從值清單中選取。 根據您使用的語法，這可以是您所提供值的簡易清單或彙總值的清單。

語法 1：

```
field IN {value1,value2,value3,...}
```

此語法可讓您在簡易清單中包含所有的值。



範例：

```
EventID IN {1201,1204,1210}
```

```
Computer IN {"srv01.contoso.com","srv02.contoso.com"}
```

語法 2：

```
(Outer query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

此語法可讓您建立彙總。 您可以將值清單從該彙總提供到另一個外部 (主要) 搜尋，這個搜尋會尋找具有那些值的事件。 若要這麼做，您必須以大括號括住內部搜尋，並使用 IN 運算子將其結果提供作為外部搜尋中欄位的可能值。

內部查詢範例︰*目前遺漏安全性更新的電腦*，並使用下列彙總查詢：

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

尋找 *目前遺漏安全性更新之電腦的所有 Windows 事件* 的最後查詢與下列類似：

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="contains"></a>Contains
**Contains** 關鍵字可讓您篩選欄位包含所指定字串的記錄。 這區分大小寫、僅適用於字串欄位，而且可能不包含任何逸出字元。

語法：

```
field:contains("string")
```

範例：

```
Type:contains("Event")
```

這會傳回類型包含字串 "Event" 的記錄。 範例包括 **Event**、**SecurityEvent** 和 **ServiceFabricOperationEvent**。



### <a name="regular-expressions"></a>規則運算式
您可以使用 **Regex** 關鍵字，以規則運算式指定欄位的搜尋條件。 如需您可以在規則運算式中使用之語法的完整描述，請參閱[使用規則運算式在 Log Analytics 中篩選記錄搜尋](log-analytics-log-searches-regex.md)。

語法：

```
field:Regex("Regular Expression")
```

範例：

```
Computer:Regex("^C.*")
```

### <a name="logical-operators"></a>邏輯運算子
查詢語言支援邏輯運算子 (AND、OR 和 NOT) 和它們的 C 樣式別名 (分別是 *&&*、*||* 和 *!*)。 您可以使用括號來分組這些運算子。

範例：

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

您可以略過最上層篩選引數的邏輯運算子。 在此情況下，會假設採用 AND 運算子。

| 篩選運算式 | 相當於 |
| --- | --- |
| system error |system AND error |
| system "Windows Server" OR Severity:1 |system AND ("Windows Server" OR Severity:1) |

### <a name="wildcarding"></a>萬用字元
查詢語言支援使用 ( \* ) 字元來代表查詢中值的一個或多個字元。

範例：

 尋找名稱中有 "SQL" 的所有電腦 (例如 "Redmond SQL")。

```
Type=Event Computer=*SQL*
```

> [!NOTE]
> 此時，無法在引號內使用萬用字元。 例如，訊息 `"*This text*"` 將考慮使用 (\*) 作為常值 (\*) 字元。


## <a name="commands"></a>命令


命令會套用至查詢所傳回的結果。 使用縱線字元 ( | ) 將命令套用至擷取的結果。 多個命令必須以縱線字元分隔。

> [!NOTE]
> 命令名稱可以大寫或小寫撰寫，與欄位名稱和資料不同。
>
>

### <a name="sort"></a>排序
語法：

    sort field1 asc|desc, field2 asc|desc, …

依特定欄位排序結果。 依遞增或遞減順序排序結果的 asc/desc 尾碼是選擇性的。 如果將其省略，則會假設採用 *asc* 排序順序。 在 **TimeGenerated** 位中，採用 *desc* 排序順序，因此預設會先傳回最新的結果。

### <a name="toplimit"></a>Top/Limit
語法：

    top number


    limit number
將回應限制為前 N 個結果。

範例：

    Type:Alert errors detected | top 10

傳回前 10 個比對結果。

### <a name="skip"></a>Skip
語法：

    skip number

略過列出結果的數目。

範例：

    Type:Alert errors detected | top 10 | skip 200

從結果 200 開始傳回 10 個比對結果。

### <a name="select"></a>選取
語法：

    select field1, field2, ...

將結果限制為您選擇的欄位。

範例：

    Type:Alert errors detected | select Name, Severity

將傳回的結果欄位限制為 *Name* and *Severity*小節中閱讀搜尋中傳回的欄位，以及協助您深入鑽研相似資料類別的 Facet。

### <a name="measure"></a>Measure
*measure* 命令可用來將統計函式套用至未經處理的搜尋結果。 需要取得資料的 *群組依據* 檢視時，這非常有用。 當您使用 measure 命令時，Log Analytics 搜尋會顯示含有彙總結果的資料表。

**語法：**

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



根據 groupField 彙總結果，並使用 aggregatedField 計算彙總的測量值。

| 量值統計函式 | 說明 |
| --- | --- |
| *aggregateFunction* |彙總函式的名稱 (不區分大小寫)。 支援下列彙總函式：COUNT、MAX、MIN、SUM、AVG、STDDEV、COUNTDISTINCT、PERCENTILE## 或 PCT## (## 是 1 與 99 之間的任意數字)。 |
| *aggregatedField* |正在彙總的欄位。 這是 COUNT 彙總函式的選擇性欄位，但必須是 SUM、MAX、MIN、AVG、STDDEV、PERCENTILE## 或 PCT## 的現有數值欄位 (## 是 1 與 99 之間的任意數字)。 aggregatedField 也可以是任一 **Extend** 支援的函式。 |
| *fieldAlias* |計算彙總值 (選擇性) 別名。 如果未指定，欄位名稱會是 **AggregatedValue**。 |
| *groupField* |用來分組結果集的欄位名稱。 |
| *間隔* |時間間隔，格式為：**nnnNAME**。 **nnn** 是正整數。 **NAME** 是間隔名稱。 支援的間隔名稱區分大小寫，且包括：MILLISECOND[S]、SECOND[S]、MINUTE[S]、HOUR[S]、DAY[S]、MONTH[S] 和 YEAR[S]。 |

間隔選項只能用於日期/時間群組欄位中 (例如 *TimeGenerated* and *TimeCreated*)。 目前，服務不會強制執行，但是沒有傳遞至後端之日期/時間的欄位會造成執行階段錯誤。 實作結構描述驗證時，服務 API 會拒絕某些查詢，其使用的欄位沒有間隔彙總的日期/時間。 目前的 *Measure* 實作支援任何彙總函式的間隔分組。

如果省略 BY 子句，但指定間隔 (作為第二個語法)，則預設採用 TimeGenerated 欄位。

範例：

**範例 1**

    Type:Alert | measure count() as Count by ObjectId

依據 ObjectID 將警示分組，並計算每個群組的警示數目。 傳回的彙總值為 *Count* 欄位 (別名)。

**範例 2**

    Type:Alert | measure count() interval 1HOUR

使用 *TimeGenerated* 欄位，依據 1 小時間隔將警示分組，並傳回每個間隔中的警示數目。

**範例 3**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

如同上述範例，但使用彙總欄位別名 (*AlertsPerHour*)。

**範例 4**

    * | measure count() by TimeCreated interval 5DAYS

使用 *TimeCreated* 欄位，依據 5 天間隔將結果分組，並傳回每個間隔中的結果數目。

**範例 5**

    Type:Alert | measure max(Severity) by WorkflowName

跟據工作負載名稱分組警示，並傳回每個工作流程的最大警示嚴重性值。

**範例 6**

    Type:Alert | measure min(Severity) by WorkflowName

如同上述範例，但使用 min 彙總函式。

**範例 7**

    Type:Perf | measure avg(CounterValue) by Computer

根據 computer 分組 Perf，並計算平均值 (avg)。

**範例 8**

    Type:Perf | measure sum(CounterValue) by Computer

如同上述範例，但使用 sum。

**範例 9**

    Type:Perf | measure stddev(CounterValue) by Computer

如同上述範例，但使用 stddev。

**範例 10**

    Type:Perf | measure percentile70(CounterValue) by Computer

如同上述範例，但使用 percentile70。

**範例 11**

    Type:Perf | measure pct70(CounterValue) by Computer

如同上述範例，但使用 pct70。 請注意，PCT## 只是 PERCENTILE## 函式的別名。

**範例 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

先根據 Computer 然後根據 CounterName 分組 Perf，並計算平均 (avg)。

**範例 13**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

取得最大數目警示的前五個工作流程。

**範例 14**

    * | measure countdistinct(Computer) by Type

計算針對每種類型報告的唯一電腦數目。

**範例 15**

    * | measure countdistinct(Computer) Interval 1HOUR

計算針對每個小時報告的唯一電腦數目。

**範例 16**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

根據 Computer 分組 % Processor Time，並傳回每小時的平均值。

**範例 17**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

根據 method 分組 W3CIISLog，並傳回代表每 5 分鐘的最大值。

**範例 18**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

根據電腦分組 % Processor Time，並傳回每小時的最小值、平均值、第 75 百分位數和最大值。

**範例 19**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

先根據 computer 然後根據 Instance name 分組 % Processor Time，並傳回每小時的最小值、平均值、第 75 百分位數和最大值。

**範例 20**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | measure max(product(CounterValue,60)) as MaxDWPerMin by InstanceName Interval 1HOUR
```

計算您電腦上每個磁碟每分鐘磁碟寫入的最大值。

### <a name="where"></a>Where
語法：

```
**where** AggregatedValue>20
```

只能用於 Measure 命令之後，以進一步篩選 Measure 彙總函式所產生的彙總結果。

範例：

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)



### <a name="dedup"></a>Dedup
語法：

    Dedup FieldName

針對所指定欄位的每個唯一值，傳回第一份找到的文件。

範例：

    Type=Event | Dedup EventID | sort TimeGenerated DESC

這個範例會為每個 EventID 傳回一個事件 (最新事件)。

### <a name="join"></a>Join
聯結兩個查詢的結果以形成單一結果集。  支援下表所述的多個聯結類型。

| 聯結類型 | 說明 |
|:--|:--|
| inner | 只傳回具有兩個查詢相符值的記錄。 |
| outer | 傳回兩個查詢的所有記錄。  |
| left  | 傳回左查詢的所有記錄，以及傳回右查詢的相符記錄。 |


- 聯結目前不支援下列查詢：包含 **IN**關鍵字、**Measure** 命令或 (如果目標為右側查詢的欄位) **Extend** 命令。
- 您目前只可以在一個聯結中包含單一欄位。
- 單一搜尋可能不包含一個以上的聯結。

**語法**

```
<left-query> | JOIN <join-type> <left-query-field-name> (<right-query>) <right-query-field-name>
```

**範例**

若要說明不同的聯結類型，請考慮聯結從稱為 MyBackup_CL 的自訂記錄收集的資料類型與每部電腦的活動訊號。  這些資料類型具有下列資料。

`Type = MyBackup_CL`

| TimeGenerated | 電腦 | LastBackupStatus |
|:---|:---|:---|
| 4/20/2017 01:26:32.137 AM | srv01.contoso.com | 成功 |
| 4/20/2017 02:13:12.381 AM | srv02.contoso.com | 成功 |
| 4/20/2017 02:13:12.381 AM | srv03.contoso.com | 失敗 |

`Type = Hearbeat` (只會顯示部分的欄位)

| TimeGenerated | 電腦 | ComputerIP |
|:---|:---|:---|
| 4/21/2017 12:01:34.482 PM | srv01.contoso.com | 10.10.100.1 |
| 4/21/2017 12:02:21.916 PM | srv02.contoso.com | 10.10.100.2 |
| 4/21/2017 12:01:47.373 PM | srv04.contoso.com | 10.10.100.4 |

#### <a name="inner-join"></a>inner join

`Type=MyBackup_CL | join inner Computer (Type=Heartbeat) Computer`

當電腦欄位符合兩個資料類型時，傳回下列記錄。

| 電腦| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| srv01.contoso.com | 4/20/2017 01:26:32.137 AM | 成功 | 4/21/2017 12:01:34.482 PM | 10.10.100.1 | Heartbeat |
| srv02.contoso.com | 4/20/2017 02:13:12.381 AM | 成功 | 4/21/2017 12:02:21.916 PM | 10.10.100.2 | Heartbeat |


#### <a name="outer-join"></a>outer join

`Type=MyBackup_CL | join outer Computer (Type=Heartbeat) Computer`

傳回兩個資料類型的下列記錄。

| 電腦| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| srv01.contoso.com | 4/20/2017 01:26:32.137 AM | 成功  | 4/21/2017 12:01:34.482 PM | 10.10.100.1 | Heartbeat |
| srv02.contoso.com | 4/20/2017 02:14:12.381 AM | 成功  | 4/21/2017 12:02:21.916 PM | 10.10.100.2 | Heartbeat |
| srv03.contoso.com | 4/20/2017 01:33:35.974 AM | 失敗  | 4/21/2017 12:01:47.373 PM | | |
| srv04.contoso.com |                           |          | 4/21/2017 12:01:47.373 PM | 10.10.100.2 | Heartbeat |



#### <a name="left-join"></a>left join

`Type=MyBackup_CL | join left Computer (Type=Heartbeat) Computer`

從 MyBackup_CL 傳回下列記錄，以及 Heartbeat 的任何相符欄位。

| 電腦| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| srv01.contoso.com | 4/20/2017 01:26:32.137 AM | 成功 | 4/21/2017 12:01:34.482 PM | 10.10.100.1 | Heartbeat |
| srv02.contoso.com | 4/20/2017 02:13:12.381 AM | 成功 | 4/21/2017 12:02:21.916 PM | 10.10.100.2 | Heartbeat |
| srv03.contoso.com | 4/20/2017 02:13:12.381 AM | 失敗 | | | |


### <a name="extend"></a>Extend
可讓您在查詢中建立執行階段欄位。 請注意，執行階段欄位不能使用量值命令執行彙總。

**範例 1**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
顯示 SQL 評估建議的加權建議分數。

**範例 2**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
顯示計數器值，單位為 KB，而非位元組。

**範例 3**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
調整 WireData TotalBytes 的值，讓所有結果都落在 0 與 100 之間。

**範例 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
```
將小於 50% 的效能計數器值標記為 LOW，而將其他值標記為 HIGH。

**範例 5**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | Extend product(CounterValue,60) as DWPerMin| measure max(DWPerMin) by InstanceName Interval 1HOUR
```
計算您電腦上每個磁碟每分鐘磁碟寫入的最大值。

**支援的函式**

| 函式 | 說明 | 語法範例 |
| --- | --- | --- |
| abs |傳回所指定數值或函式的絕對值。 |`abs(x)` <br> `abs(-5)` |
| acos |傳回值或函式的反餘弦值。 |`acos(x)` |
| 和 |只有在其所有運算元都評估為 true 時，才會傳回 true 值。 |`and(not(exists(popularity)),exists(price))` |
| asin |傳回值或函式的反正弦值。 |`asin(x)` |
| atan |傳回值或函式的反正切值。 |`atan(x)` |
| atan2 |傳回矩形座標 x、y 轉換為極座標所產生的角度。 |`atan2(x,y)` |
| cbrt |立方根。 |`cbrt(x)` |
| ceil |無條件進位到整數。 |`ceil(x)`  <br> `ceil(5.6)`：傳回 6 |
| cos |傳回角度的餘弦值。 |`cos(x)` |
| cosh |傳回角度的雙曲餘弦值。 |`cosh(x)` |
| def |預設值的縮寫。 傳回欄位 "field" 的值。 如果欄位不存在，則會傳回所指定的預設值，並產生第一個值，其中：`exists()==true`。 |`def(rating,5)`。 此 def() 函式傳回分級，或如果在文件中未指定分級，則傳回 5。 <br> `def(myfield, 1.0)` 相當於 `if(exists(myfield),myfield,1.0)`。 |
| 度 |將弧度轉換為度。 |`deg(x)` |
| div |`div(x,y)` 除以 x 乘以 y。 |`div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| dist |傳回 n 維度空間中兩個向量之間的距離 (點)。 在次方中，使用 ValueSource 執行個體加上二以上的值，並計算兩個向量之間的距離。 每個 ValueSource 都必須是數字。 必須傳入偶數的 ValueSource 執行個體，而且此方法假設前半部分代表第一個向量，而後半部分代表第二個向量。 |`dist(2, x, y, 0, 0)`：計算每個文件的 (0,0) 和 (x,y) 之間的 Euclidean 距離。 <br> `dist(1, x, y, 0, 0)`：計算每個文件的 (0,0) 和 (x,y) 之間的 Manhattan (計程車) 距離。 <br> `dist(2,,x,y,z,0,0,0)`：每個文件的 (0,0,0) 和 (x,y,z) 之間的 Euclidean 距離。<br>`dist(1,x,y,z,e,f,g)`：(x,y,z) 和 (e,f,g) 之間的 Manhattan 距離，其中每個字母是欄位名稱。 |
| exists |如果有欄位的任何成員，請傳回 TRUE。 |`exists(author)`：如果任何文件在 "author" 欄位中有值，則傳回 TRUE。<br>`exists(query(price:5.00))`：如果 "price" 符合 "5.00" 則傳回 TRUE。 |
| exp |傳回歐拉數字乘冪 x。 |`exp(x)` |
| floor |無條件捨去到整數。 |`floor(x)`  <br> `floor(5.6)`：傳回 5 |
| hypo |傳回 sqrt(sum(pow(x,2),pow(y,2)))，而不需要中繼溢位或反向溢位。 |`hypo(x,y)`  <br> ` |
| if |啟用條件式函式查詢。 在 `if(test,value1,value2)` 中：test 是或參照可傳回邏輯值 (TRUE 或 FALSE) 的邏輯值或運算式。 `value1` 是函式在 test 產生 TRUE 時所傳回的值。 `value2` 是函式在 test 產生 FALSE 時所傳回的值。 運算式可以是輸出布林值的任何函式。 也可以是傳回數值的函式，在此情況下，值 0 會解譯為 false 或傳回字串，在此情況下，空字串會解譯為 false。 |`if(termfreq(cat,'electronics'),popularity,42)`：這個函式會檢查每個文件，確認 cat 欄位中是否包含 "electronics" 這個字。 如果是的話，會傳回 popularity 欄位的值。 否則會傳回值 42。 |
| linear |實作 `m*x+c`，其中 m 和 c 是常數，而 x 是任意函式。 這相當於 `sum(product(m,x),c)`，但較具效率，因為它實作為單一函式。 |`linear(x,m,c) linear(x,2,4)` 傳回 `2*x+4` |
| ln |傳回所指定函式的自然對數。 |`ln(x)` |
| log |傳回所指定函式的對數底數 10。 |`log(x)   log(sum(x,100))` |
| map |將輸入函式 x 的任何落在 min 與 max (含) 之間的值對應到指定的目標。 min 和 max 引數必須是常數。 target 和 default 引數可以是常數或函式。 如果值 x 未落在 min 與 max 之間，則會傳回值 x，或者，如果指定為第 5 個引數，則會傳回預設值。 |`map(x,min,max,target) map(x,0,0,1)`：將任何 0 的值變更為 1。 這可用於處理預設值 0 的值。<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)`：變更 0 和 100 到 1 之間的任何值，所有其他值為 -1。<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))`：變更介於 0 和 100 到 x+599 之間的任何值和所有其他值，成為欄位文字中詞彙 'solr' 的頻率。 |
| max |傳回多個巢狀函式或常數的最大數值 (指定為引數：`max(x,y,...)`。 max 函式也適用於將某個指定常數上的另一個函式或欄位「降到最小值」  使用 `field(myfield,max)` 語法來選取單一多值欄位的最大值。 |`max(myfield,myotherfield,0)` |
| Min |傳回常數之多個巢狀函式的最小數值 (指定為引數︰`min(x,y,...)`。 min 函式也適用於使用常數提供函式的「上限」 使用 `field(myfield,min)` 語法來選取單一多值欄位的最小值。 |`min(myfield,myotherfield,0)` |
| mod |計算函式 x 乘以函式 y 的模數。 |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))` |
| ms |傳回其引數之間差異的毫秒。 日期相對於 Unix 或 POSIX 時間新紀元：1970 年 1 月 1 日午夜 UTC。 引數可以是索引 TrieDateField 的名稱，或根據常數日期或 NOW 的日期算術。 `ms()` 相當於 `ms(NOW)`，epoch 以來毫秒數的數字。 `ms(a)` 傳回引數所代表之新紀元後的毫秒數。 `ms(a,b)` 傳回 b 之前發生的毫秒數，也就是 `a - b`。 |`ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| 否 |所包裝函式的邏輯負值。 |`not(exists(author))`：當 `exists(author)` 為 false 時才為 TRUE。 |
| 或 |邏輯分離。 |`or(value1,value2)`：如果 value1 或 value2 為 true，則為 TRUE。 |
| pow |將指定的基底提升為指定的次方。 `pow(x,y)` 將 x 提升為次方 y。 |`pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)`：與 sqrt 相同。 |
| product |傳回多個數值或函式 (指定為逗號分隔的清單) 的乘積。 `mul(...)` 也可以作為這個函式的別名。 |`product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip |執行 `recip(x,m,a,b)` 實作 `a/(m*x+b)` 的倒數函式，其中 m、a 和 b 是常數，而 x 是任何任意複雜函式。 a 與 b 相等而且 x>=0 時，此函式的最大值為 1，會在 x 增加時減少。 同時增加 a 和 b 的值，可將整個函式移到曲線最平緩的部分。 x 為 `rord(datefield)`時，這些屬性可以將這個設為提升較新文件的理想函式。 |`recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| rad |將度數轉換為弧度。 |`rad(x)` |
| rint |四捨五入為最接近的整數。 |`rint(x)`  <br> `rint(5.6)`：傳回 6 |
| sin |傳回角度的正弦值。 |`sin(x)` |
| sinh |傳回角度的雙曲正弦值。 |`sinh(x)` |
| 級別 |調整函式 x 的值，讓它們落在指定的 minTarget 與 maxTarget (含) 之間。 目前的實作會周遊所有函式值以取得最小值和最大值，讓它可以取得正確的小數位數。 目前的實作無法區別何時刪除文件或文件是否沒有值。 在這些情況下，它會使用 0.0 值。 這表示，如果值通常都大於 0.0，其中一個還是可以將 0.0 當成要對應來源的最小值。 在這些情況下，適當的 `map()` 函式可以用做將 0.0 變更為實際範圍內之值的因應措施，如這裡所示：`scale(map(x,0,0,5),1,2)` |`scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)`：調整 x 值，讓所有值都在 1 與 2 (含) 之間。 |
| sqrt |傳回所指定數值或函式的平方根。 |`sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist |計算兩個字串之間的距離。 使用 Lucene 拼字檢查程式 StringDistance 介面並支援該套件中可用的所有實作。 也允許應用程式透過 Solr 的資源載入功能插入專屬的實作。 strdist 採用 `(string1, string2, distance measure)`。 距離量值的可能值如下︰<ul><li>jw: Jaro-Winkler</li><li>編輯︰Levenstein 或編輯距離</li><li>ngram：NGramDistance，如果指定，也可以選擇性地傳入 ngram 大小。 預設值為 2。</li><li>FQN：StringDistance 介面實作的完整類別名稱。 必須要有無引數建構函式。</li></ul> |`strdist("SOLR",id,edit)` |
| sub |從 `sub(x,y)`傳回 x-y。 |`sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| Sum |傳回多個數值或函式 (指定為逗號分隔的清單) 的總和。 `add(...)` 可以作為這個函式的別名。 |`sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)` |
| termfreq |傳回在該文件欄位中字詞出現的次數。 |termfreq(text,'memory') |
| tan |傳回角度的正切值。 |`tan(x)` |
| tanh |傳回角度的雙曲正切值。 |`tanh(x)` |

## <a name="search-field-and-facet-reference"></a>搜尋欄位和 Facet 參考 (英文)
當您使用記錄搜尋來尋找資料時，結果會顯示各種欄位和 Facet。 資訊的某些部分可能不太清楚。 使用下列資訊來協助了解結果。

| 欄位 | 搜尋類型 | 說明 |
| --- | --- | --- |
| TenantId |全部 |用來分割資料。 |
| TimeGenerated |全部 |用來驅動時間軸，timeselectors (在搜尋和其他螢幕中)。 它代表資料片段的產生 (通常在代理程式上) 時間。 時間以 ISO 格式表示，且一律為 UTC。 如果類型是根據現有檢測 (也就是，記錄中的事件)，這通常是記錄項目/列/記錄的實際記錄時間。 針對某些透過管理組件或在雲端 (例如，建議或警示) 所產生的其他類型，時間代表不同的東西。 這是收集到含某種組態之快照集資料的這個新部分的時間，或根據它產生建議/警示的時間。 |
| EventID |Event |Windows 事件記錄中的 EventID。 |
| EventLog |Event |Windows 在其中記錄事件的事件記錄。 |
| EventLevelName |Event |重大/警告/資訊/成功 |
| EventLevel |Event |重大/警告/資訊/成功的數值 (對於更容易/更適合閱讀的查詢，請改用 EventLevelName)。 |
| SourceSystem |全部 |資料來自何處 (根據服務的附加模式)。 範例包括 Microsoft System Center Operations Manager 和 Azure 儲存體。 |
| ObjectName |PerfHourly |Windows 效能物件名稱。 |
| InstanceName |PerfHourly |Windows 效能計數器執行個體名稱。 |
| CounteName |PerfHourly |Windows 效能計數器名稱。 |
| ObjectDisplayName |PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty |Operations Manager 中效能集合規則設為目標之物件的顯示名稱。 也可以是 Operational Insights 探索到之物件的顯示名稱，或針對其產生警示之物件的顯示名稱。 |
| RootObjectName |PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty |Operations Manager 中效能集合規則設為目標之物件的父系的父系 (在雙主控關聯性中) 的顯示名稱。 也可以是 Operational Insights 探索到之物件的顯示名稱，或針對其產生警示之物件的顯示名稱。 |
| 電腦 |大部分的類型 |資料所屬的電腦名稱。 |
| DeviceName |ProtectionStatus |資料所屬的電腦名稱 (如同 "Computer")。 |
| DetectionId |ProtectionStatus | |
| ThreatStatusRank |ProtectionStatus |威脅狀態排名是威脅狀態的數字表示。 與 HTTP 回應碼類似，排名在數字之間會有間距 (這是無威脅為 150 而不是 100 或 0 的原因)，以留下空間來新增狀態。 針對威脅狀態和防護狀態的彙總，目的是要顯示電腦在選取時間間隔中所經歷的最差狀態。 數字可排名不同的狀態，以查看最高數字的記錄。 |
| ThreatStatus |ProtectionStatus |ThreatStatus 的描述，與 ThreatStatusRank 1:1 對應。 |
| TypeofProtection |ProtectionStatus |在電腦中偵測到的反惡意程式碼產品：無、Microsoft 惡意程式碼移除工具、Forefront 等等。 |
| ScanDate |ProtectionStatus | |
| SourceHealthServiceId |ProtectionStatus、RequiredUpdate |這台電腦之代理程式的 HealthService 識別碼。 |
| HealthServiceId |大部分的類型 |這台電腦之代理程式的 HealthService 識別碼。 |
| ManagementGroupName |大部分的類型 |Operations Manager 附加代理程式的管理群組名稱。 否則會是 null/空白。 |
| ObjectType |ConfigurationObject |由 Log Analytics 組態評估所探索到之這個物件的類型 (例如 Operations Manager 管理組件的類型/類別)。 |
| UpdateTitle |RequiredUpdate |找到未安裝的更新名稱。 |
| PublishDate |RequiredUpdate |更新何時在 Microsoft Update 上發佈。 |
| 伺服器 |RequiredUpdate |資料所屬的電腦名稱 (如同 "Computer")。 |
| 產品 |RequiredUpdate |更新適用的產品。 |
| UpdateClassification |RequiredUpdate |更新類型 (例如，更新彙總套件、Service Pack)。 |
| KBID |RequiredUpdate |描述此最佳做法或更新的 KB 文章識別碼。 |
| WorkflowName |ConfigurationAlert |產生警示之規則或監視器的名稱。 |
| 嚴重性 |ConfigurationAlert |警示的嚴重性。 |
| 優先順序 |ConfigurationAlert |警示的優先順序。 |
| IsMonitorAlert |ConfigurationAlert |此警示由監視器 (true) 或規則 (false) 所產生？ |
| AlertParameters |ConfigurationAlert |使用 Log Analytics 警示之參數的 XML。 |
| Context |ConfigurationAlert |使用 Log Analytics 警示之內容的 XML。 |
| 工作負載 |ConfigurationAlert |警示所參考的技術或工作負載。 |
| AdvisorWorkload |建議 |建議所參考的技術或工作負載。 |
| 說明 |ConfigurationAlert |警示描述 (簡短)。 |
| DaysSinceLastUpdate |UpdateAgent |此代理程式在幾天前 (相對於記錄的 TimeGenerated) 從 Windows Server Update Service (WSUS) 或 Microsoft Update 安裝了任何更新？ |
| DaysSinceLastUpdateBucket |UpdateAgent |根據 DaysSinceLastUpdate，多久以前的時間值區分類是上一次從 WSUS/Microsoft Update 安裝任何更新的電腦。 |
| AutomaticUpdateEnabled |UpdateAgent |自動更新檢查是在此代理程式上啟用或停用的嗎？ |
| AutomaticUpdateValue |UpdateAgent |自動更新檢查會設為自動下載和安裝、只下載，或只檢查嗎？ |
| WindowsUpdateAgentVersion |UpdateAgent |Microsoft Update 代理程式的版本號碼。 |
| WSUSServer |UpdateAgent |此更新代理程式的目標是哪個 WSUS 伺服器？ |
| OSVersion |UpdateAgent |此更新代理程式在其上執行的作業系統版本。 |
| 名稱 |建議，ConfigurationObjectProperty |來自 Log Analytics 組態評估的建議名稱/標題或屬性名稱。 |
| 值 |ConfigurationObjectProperty |來自 Log Analytics 組態評估的屬性值。 |
| KBLink |建議 |描述此最佳做法或更新的 KB 文章 URL。 |
| RecommendationStatus |建議 |建議為一些類型，其記錄會更新，而不只是新增至搜尋索引。 此狀態會變更建議是否為作用中/開啟，或者 Log Analytics 是否偵測到狀態已解決。 |
| RenderedDescription |Event |Windows 事件的呈現描述 (具有填入參數的重複使用文字)。 |
| ParameterXml |Event |使用 Windows 事件之資料區段中參數的 XML (如同在事件檢視器中所見)。 |
| EventData |Event |使用 Windows 事件之完整資料區段的 XML (如同在事件檢視器中所見)。 |
| 來源 |Event |產生事件的事件記錄來源。 |
| EventCategory |Event |事件的類別，直接來自 Windows 事件記錄。 |
| UserName |Event |Windows 事件的使用者名稱 (通常是 NT AUTHORITY\LOCALSYSTEM)。 |
| SampleValue |PerfHourly |效能計數器每小時彙總的平均值。 |
| Min |PerfHourly |效能計數器每小時彙總的每小時間隔內最小值。 |
| max |PerfHourly |效能計數器每小時彙總的每小時間隔內最大值。 |
| Percentile95 |PerfHourly |效能計數器每小時彙總的每小時間隔內第 95 個百分位數。 |
| SampleCount |PerfHourly |多少未經處理的效能計數器範例用來產生此每小時彙總記錄。 |
| Threat |ProtectionStatus |找到的惡意程式碼名稱。 |
| StorageAccount |W3CIISLog |從其中讀取記錄的 Azure 儲存體帳戶。 |
| AzureDeploymentID |W3CIISLog |記錄所屬之雲端服務的 Azure 部署識別碼。 |
| 角色 |W3CIISLog |記錄所屬之 Azure 雲端服務的角色。 |
| RoleInstance |W3CIISLog |記錄所屬之 Azure 角色的 RoleInstance。 |
| sSiteName |W3CIISLog |記錄所屬的 IIS 網站 (metabase 標記法)；在原始記錄中的 s-sitename 欄位。 |
| sComputerName |W3CIISLog |原始記錄中的 s-computername 欄位。 |
| sIP |W3CIISLog |HTTP 要求被定址的目標伺服器 IP 位址。 原始記錄中的 s-ip 欄位。 |
| csMethod |W3CIISLog |用戶端在 HTTP 要求中使用的 HTTP 方法 (例如，GET/POST)。 原始記錄中的 cs-method。 |
| cIP |W3CIISLog |HTTP 要求的來源用戶端 IP 位址。 原始記錄中的 c-ip 欄位。 |
| csUserAgent |W3CIISLog |由用戶端宣告的 HTTP 使用者代理程式 (瀏覽器或其他方式)。 原始記錄中的 cs-user-agent。 |
| scStatus |W3CIISLog |由伺服器傳回給用戶端的 HTTP 狀態碼 (例如，200/403/500)。 原始記錄中的 cs-status。 |
| TimeTaken |W3CIISLog |完成要求所花費的時間 (以毫秒為單位)。 原始記錄中的 timetaken 欄位。 |
| csUriStem |W3CIISLog |要求的相對 URI (沒有主機位址，也就是 /search) 的要求。 原始記錄中的 cs-uristem 欄位。 |
| csUriQuery |W3CIISLog |URI 查詢。 只有 ASP 頁面等動態頁面才需要 URI 查詢，所以此欄位通常包含靜態網頁的連字號。 |
| sPort |W3CIISLog |傳送 HTTP 要求的目標伺服器連接埠 (也是 IIS 接聽的目標，因為 IIS 會挑選該連接埠)。 |
| csUserName |W3CIISLog |已驗證的使用者名稱，如果要求已通過驗證且不匿名。 |
| csVersion |W3CIISLog |使用於要求中的 HTTP 通訊協定版本 (例如，HTTP/1.1)。 |
| csCookie |W3CIISLog |Cookie 資訊。 |
| csReferer |W3CIISLog |使用者上次造訪的網站。 這個網站提供目前網站的連結。 |
| csHost |W3CIISLog |要求的主機標頭 (例如，www.mysite.com)。 |
| scSubStatus |W3CIISLog |子狀態錯誤碼。 |
| scWin32Status |W3CIISLog |Windows 狀態碼。 |
| csBytes |W3CIISLog |在要求中從用戶端傳送到伺服器的位元組。 |
| scBytes |W3CIISLog |在回應中從伺服器傳回給用戶端的位元組。 |
| ConfigChangeType |ConfigurationChange |變更的類別 (例如，WindowsServices/Software)。 |
| ChangeCategory |ConfigurationChange |變更的類別 (已修改/已新增/已移除)。 |
| SoftwareType |ConfigurationChange |軟體的類型 (更新/應用程式)。 |
| SoftwareName |ConfigurationChange |軟體的名稱 (僅適用於軟體變更)。 |
| 發佈者 |ConfigurationChange |發佈軟體的供應商 (僅適用於軟體變更)。 |
| SvcChangeType |ConfigurationChange |已套用在 Windows 服務的變更類型 (State/StartupType/Path/ServiceAccount)。 這僅適用於 Windows 服務變更。 |
| SvcDisplayName |ConfigurationChange |顯示已變更的服務名稱。 |
| SvcName |ConfigurationChange |已變更的服務名稱。 |
| SvcState |ConfigurationChange |要求的新 (目前) 狀態。 |
| SvcPreviousState |ConfigurationChange |服務的上一個已知狀態 (僅適用於服務狀態變更時)。 |
| SvcStartupType |ConfigurationChange |服務啟動類型。 |
| SvcPreviousStartupType |ConfigurationChange |上一個服務啟動類型 (僅適用於服務啟動類型變更時)。 |
| SvcAccount |ConfigurationChange |服務帳戶。 |
| SvcPreviousAccount |ConfigurationChange |先前的服務帳戶 (僅適用於服務帳戶變更時)。 |
| SvcPath |ConfigurationChange |Windows 服務的可執行檔路徑。 |
| SvcPreviousPath |ConfigurationChange |Windows 服務先前的可執行檔路徑 (僅適用於其變更時)。 |
| SvcDescription |ConfigurationChange |服務的描述。 |
| 上一步 |ConfigurationChange |此軟體先前的狀態 (已安裝/未安裝/上一個版本)。 |
| Current |ConfigurationChange |此軟體最新的狀態 (已安裝/未安裝/上一個版本)。 |

## <a name="next-steps"></a>後續步驟
如需記錄搜尋的其他資訊：

* 熟悉 [記錄搜尋](log-analytics-log-searches.md) 以檢視方案所收集的詳細資訊。
* 使用 [Log Analytics 中的自訂欄位](log-analytics-custom-fields.md)來延伸記錄搜尋。
