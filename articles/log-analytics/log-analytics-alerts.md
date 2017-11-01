---
title: "了解 Azure Log Analytics 中的警示 | Microsoft Docs"
description: "Log Analytics 中的警示會識別您的 OMS 儲存機制中的重要資訊，並可主動通知您相關問題或叫用動作以嘗試更正問題。  本文說明不同種類的警示規則和其定義方式。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2017
ms.author: bwren
ms.openlocfilehash: ee11f64484a66fad06b6536a18f9b3e239fa40d5
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2017
---
# <a name="understanding-alerts-in-log-analytics"></a>了解 Log Analytics 中的警示

Log Analytics 中的警示會識別您的 Log Analytics 儲存機制中的重要資訊。  本文詳述警示規則在 Log Analytics 中的運作方式，並說明不同類型的警示規則之間的差異。

如需建立警示規則的程序，請參閱下列文章：

- 使用 [Azure 入口網站](log-analytics-alerts-creating.md)建立警示規則
- 使用 [Resource Manager 範本](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)建立叢集規則
- 使用 [REST API](log-analytics-api-alerts.md) 建立警示規則


## <a name="alert-rules"></a>警示規則

警示會由自動定期執行記錄搜尋的警示規則所建立。  如果記錄搜尋的結果符合特定準則，則會建立警示的記錄。  此規則接著可以自動執行一或多個動作，以主動通知警示或叫用另一個處理序。  不同類型的警示規則會使用不同邏輯來執行這項分析。

![Log Analytics 警示](media/log-analytics-alerts/overview.png)

警示規則會由下列詳細資料定義：

- **記錄搜尋**。  每次引發警示規則都會執行的查詢。  此查詢所傳回的記錄將是用來判斷是否要建立警示。
- **時間範圍**。  指定查詢的時間範圍。  查詢只會傳回在此目前時間範圍內建立的記錄。  可以是介於 5 分鐘與 24 小時之間的任何值。 例如，如果時間範圍設定為 60 分鐘，則查詢會在下午 1:15 執行，只會傳回在下午 12:15 與下午 1:15 之間建立的記錄。
- **頻率**。  指定應執行查詢的頻率。 可以是介於 5 分鐘與 24 小時之間的任何值。 應等於或小於此時間範圍。  如果值大於時間範圍，則您可能有遺漏記錄的風險。<br>例如，請考慮 30 分鐘的時間範圍，以及 60 分鐘的頻率。  如果在 1:00 執行查詢，它會傳回 12:30 到下午 1:00 之間的記錄。  下一次執行查詢就是 2:00 時，它會傳回 1:30 至 2:00 之間的記錄。  1:00 和 1:30 之間建立的任何記錄一律不會評估。
- **閾值**。  系統會評估記錄搜尋的結果，以判斷是否應該建立警示。  不同類型的警示規則會有不同的閾值。

Log Analytics 中的各個警示規則是兩種類型其中之一。  下列各節會詳細說明這兩個類型。

- **[結果數目](#number-of-results-alert-rules)**。 當記錄搜尋所傳回的數目記錄超過指定數目時，系統會建立單一警示。
- **[計量測量](#metric-measurement-alert-rules)**。  針對記錄搜尋結果中的每個物件，若其值超過指定的閾值，系統就會為其建立警示。

警示規則類型之間的差異如下所示。

- **結果數目**警示規則一律會建立單一警示，而**計量測量**警示規則會為每個超過閾值的物件建立警示。
- **結果數目**警示規則會在閾值超過一次時建立警示。 **計量測量**警示規則會在閾值於特定時間間隔內超過一定次數時建立警示。

## <a name="number-of-results-alert-rules"></a>結果數目警示規則
**結果數目**警示規則會在搜尋查詢所傳回的記錄數目超過指定閾值時建立單一警示。

### <a name="threshold"></a>閾值
**結果數目**警示規則的閾值只會大於或小於特定值。  如果記錄搜尋所傳回的記錄數目符合此準則，則會建立警示。

### <a name="scenarios"></a>案例

#### <a name="events"></a>事件
這種類型的警示規則適用於處理例如 Windows 事件記錄、Syslog 和自訂記錄的事件。  您可能希望在建立特定的錯誤事件時，或是在特定時間範圍內建立多個錯誤事件時建立警示。

若要對單一事件發出警示，請將結果數目設為大於 0 並將頻率與時間範圍設為 5 分鐘。  如此一來，將會每隔 5 分鐘執行一次查詢，並檢查在上次執行查詢後是否發生所建立的單一事件。  較長的頻率可能會延遲收集事件和建立警示的時間。

有些應用程式可能會記錄不一定會產生警示的偶發錯誤。  例如，應用程式可能會重試造成錯誤事件的處理序，而下一次就會成功。  在此情況下，除非在特定時間範圍內建立多個事件，否則您不會想建立警示。  

在某些情況下，您可能想在某個事件不存在時建立警示。  例如，處理序可能會記錄一般事件，表示運作正常。  如果它不在特定的時間範圍內記錄一個事件，則應建立警示。  在此情況下，您會將臨界值設定為**小於 1**。

#### <a name="performance-alerts"></a>效能警示
[效能資料](log-analytics-data-sources-performance-counters.md) 會儲存為 OMS 儲存機制中類似事件的記錄。  如果您要在效能計數器超過特定臨界值時發出警示，則該臨界值應包含在查詢中。

例如，如果您想要在處理器執行超過 90% 時發出警示，您會使用如下的查詢，且警示規則的臨界值**大於 0**。

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90

    

如果您想要針對特定的時間範圍在處理器平均超過 90% 時發出警示，您會利用如下的[測量命令](log-analytics-search-reference.md#commands)來使用查詢，且警示規則的臨界值**大於 0**。

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90

    
>[!NOTE]
> 如果您的工作區尚未升級為新的 [Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則以上查詢會變更如下：`Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90`
> `Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90`


## <a name="metric-measurement-alert-rules"></a>公制度量單位的警示規則

>[!NOTE]
> 計量測量警示規則目前處於公開預覽狀態。

**計量測量**警示規則會針對查詢中其值超過指定閾值的每個物件建立警示。  這些警示規則與**結果數目**警示規則具有下列明顯差異。

#### <a name="log-search"></a>記錄搜尋
您可以對**結果數目**警示規則使用任何查詢，但是對計量測量警示規則使用查詢時則有特定需求。  它必須包含[測量命令](log-analytics-search-reference.md#commands)以將結果群組在特定欄位上。 此命令必須包含下列元素。

- **彙總函式**。  決定要執行的計算，並可能決定要彙總的數值欄位。  例如，**count()** 會在查詢中傳回記錄數目，**avg(CounterValue)** 則會傳回 CounterValue 欄位在一段間隔內的平均值。
- **群組欄位**。  系統會為這個欄位中的每個執行個體建立帶有彙總值的記錄，而且每個執行個體都可產生警示。  例如，如果您想要為每部電腦產生警示，您可以使用**依電腦**。   
- **間隔**。  定義用來彙總資料的時間間隔。  例如，如果您指定 **5 分鐘**，則系統會為群組欄位的每個執行個體建立記錄，而這些執行個體是在針對警示所指定的時間範圍內以每 5 分鐘為間隔進行彙總的。

#### <a name="threshold"></a>閾值
計量測量警示規則的閾值是由彙總值與若干違規所定義。  如果記錄搜尋中的任何資料點超過此值，系統就會將其視為違規。  如果結果中任何物件的違規數目超過指定值，系統舊會為該物件建立警示。

#### <a name="example"></a>範例
假設您想要在任何電腦於過去 30 分鐘內發生三次處理器使用率超過 90% 時收到警示。  您可以建立詳細資料如下的警示規則。  

**查詢：** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
**時間範圍︰**30 分鐘<br>
**警示頻率︰**5 分鐘<br>
**彙總值︰**大於 90<br>
**警示觸發依據︰**違規總數大於 5<br>

查詢會以 5 分鐘為間隔為每部電腦建立平均值。  此查詢會每隔 5 分鐘針對在先前 30 分鐘內所收集的資料來執行。  三部電腦的資料範例如下所示。

![查詢結果範例](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

在此範例中，系統會為 srv02 和 srv03 建立個別警示，因為它們在時間範圍內違反 90% 閾值 3 次。  如果**警示觸發依據︰**變更為**連續**，則系統只會為 srv03 建立警示，因為它違反了 3 個連續取樣的閾值。

## <a name="alert-records"></a>警示記錄
在 Log Analytics 中由警示規則建立的警示記錄，**Type** 為 **Alert**，**SourceSystem** 為 **OMS**。  其屬性如下表所示。

| 屬性 | 說明 |
|:--- |:--- |
| 類型 |*警示* |
| SourceSystem |*OMS* |
| *Object*  | [計量測量警示](#metric-measurement-alert-rules)會有群組欄位的屬性。  例如，如果記錄搜尋針對「電腦」進行群組，則警示記錄會有電腦欄位，並以電腦名稱作為值。
| AlertName |警示的名稱。 |
| AlertSeverity |警示的嚴重性層級。 |
| LinkToSearchResults |連結至 Log Analytics 記錄檔搜尋，而該搜尋會從建立警示的查詢傳回記錄。 |
| 查詢 |已執行的查詢文字。 |
| QueryExecutionEndTime |查詢的時間範圍結尾。 |
| QueryExecutionStartTime |查詢的時間範圍開頭。 |
| ThresholdOperator | 警示規則所使用的運算子。 |
| ThresholdValue | 警示規則所使用的值。 |
| TimeGenerated |建立警示的日期和時間。 |

[警示管理解決方案](log-analytics-solution-alert-management.md)和 [Power BI 匯出](log-analytics-powerbi.md)會建立其他種類的警示記錄。  這些記錄的 **Type** 均為 **Alert**，但依其 **SourceSystem** 區分。


## <a name="next-steps"></a>後續步驟
* 安裝[警示管理解決方案](log-analytics-solution-alert-management.md)，以分析在 Log Analytics 中建立的警示以及從 System Center Operations Manager 收集的警示。
* 深入了解可產生警示的 [記錄檔搜尋](log-analytics-log-searches.md) 。
* 完成[設定 Webhook](log-analytics-alerts-webhooks.md) 搭配警示規則的逐步解說。  
* 了解如何在 [Azure 自動化中撰寫 Runbook](https://azure.microsoft.com/documentation/services/automation) 以補救警示所識別的問題。
