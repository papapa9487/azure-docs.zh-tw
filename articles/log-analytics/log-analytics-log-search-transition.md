---
title: "Azure Log Analytics 查詢語言功能提要 | Microsoft Docs"
description: "如果您已熟悉舊版語言，本文會提供轉換為 Log Analytics 新查詢語言的協助。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 10b7f3ad23d9c5451bc7ff82b8927c260230f6da
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---

# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>轉換為 Azure Log Analytics 新的查詢語言

> [!NOTE]
> 您可以在[將 Azure Log Analytics 工作區升級為新的記錄搜尋](log-analytics-log-search-upgrade.md)中，深入了解新的 Log Analytics 查詢語言，並且取得升級工作區的程序。

如果您已熟悉舊版語言，本文會提供轉換為 Log Analytics 新查詢語言的協助。

## <a name="language-converter"></a>語言轉換器

如果您已熟悉舊版 Log Analytics 查詢語言，以新的語言建立相同查詢的最簡單方式是使用「語言轉換器」，它已在您的工作區轉換時安裝在記錄搜尋入口網站中。  使用轉換器很簡單，就如同在頂端文字方塊中輸入舊版查詢，然後按一下 [轉換] 一樣。  您可以按一下搜尋按鈕以執行查詢，或複製並貼上它以在其他地方使用。

![語言轉換器](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="cheat-sheet"></a>功能提要

下表提供 Azure Log Analytics 中新的和舊版查詢語言之間，對於對等命令之各種通用查詢的比較。

| 說明 | 舊版 | new |
|:--|:--|:--|
| 搜尋所有資料表      | 錯誤 | 搜尋 "error" (不區分大小寫) |
| 從資料表選取資料 | Type=Event |  Event |
|                        | Type=Event &#124; select Source, EventLog, EventID | Event &#124; project Source, EventLog, EventID |
|                        | Type=Event &#124; top 100 | Event &#124; take 100 |
| 字串比較      | Type=Event Computer=srv01.contoso.com   | Event &#124; where Computer == "srv01.contoso.com" |
|                        | Type=Event Computer=contains("contoso") | Event &#124; where Computer contains "contoso" (不區分大小寫)<br>Event &#124; where Computer contains_cs "Contoso" (區分大小寫) |
|                        | Type=Event Computer=RegEx("@contoso@")  | Event &#124; where Computer matches regex ".*contoso*" |
| 日期比較        | Type=Event TimeGenerated > NOW-1DAYS | Event &#124; where TimeGenerated > ago(1d) |
|                        | Type=Event TimeGenerated>2017-05-01 TimeGenerated<2017-05-31 | Event &#124; where TimeGenerated between (datetime(2017-05-01) .. datetime(2017-05-31)) |
| 布林值比較     | Type=Heartbeat IsGatewayInstalled=false  | Heartbeat | where IsGatewayInstalled == false |
| 排序                   | Type=Event &#124; sort Computer asc, EventLog desc, EventLevelName asc | Event \| sort by Computer asc, EventLog desc, EventLevelName asc |
| Distinct               | Type=Event &#124; dedup Computer \| 選取電腦 | Event &#124; summarize by Computer, EventLog |
| 擴充資料行         | Type=Perf CounterName="% Processor Time" &#124; EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION | Perf &#124; where CounterName == "% Processor Time" \| extend Utilization = iff(CounterValue > 50, "HIGH", "LOW") |
| 彙總            | Type=Event &#124; measure count() as Count by Computer | Event &#124; summarize Count = count() by Computer |
|                                | Type=Perf ObjectName=Processor CounterName="% Processor Time" &#124; measure avg(CounterValue) by Computer interval 5minute | Perf &#124; where ObjectName=="Processor" and CounterName=="% Processor Time" &#124; summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min) |
| 限制的彙總 | Type=Event &#124; measure count() by Computer &#124; top 10 | Event &#124; summarize AggregatedValue = count() by Computer &#124; limit 10 |
| Union                  | Type=Event or Type=Syslog | union Event, Syslog |
| Join                   | Type=NetworkMonitoring &#124; join inner AgentIP (Type=Heartbeat) ComputerIP | NetworkMonitoring &#124; join kind=inner (search Type == "Heartbeat") on $left.AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>後續步驟
- 請參閱使用新的查詢語言[撰寫查詢的教學課程](https://go.microsoft.com/fwlink/?linkid=856078)。
- 請參閱[查詢語言參考](https://go.microsoft.com/fwlink/?linkid=856079)以取得新的查詢語言的所有命令、運算子和函式的詳細資訊。  

