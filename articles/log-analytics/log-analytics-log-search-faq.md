---
title: "Log Analytics 新的記錄搜尋常見問題集 | Microsoft Docs"
description: "本文提供將 Log Analytics 升級為新的查詢語言的相關常見問題集。"
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
ms.date: 08/08/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 2de9a87390da11a034c6cebaa37d4cc89edf3cad
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---

# <a name="log-analytics-new-log-search-frequently-asked-questions"></a>Log Analytics 新的記錄搜尋常見問題集

本文提供[將 Log Analytics 升級為新的查詢語言](log-analytics-log-search-upgrade.md)的相關常見問題集。



## <a name="upgrade-process"></a>升級程序

### <a name="i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>我有數個工作區。 可以同時升級所有工作區嗎？  
答：否。  一次只能將升級套用至單一工作區。 目前無法一次升級多個工作區。 請注意，升級工作區的其他使用者也會受到影響。  

### <a name="will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>如果我升級，我的工作區中收集的現有記錄資料會被修改嗎？  
編號 您的工作區搜尋可用的記錄資料不會受到升級影響。 已儲存的搜尋、警示和檢視會自動轉換成新的搜尋語言。  

### <a name="what-happens-if-i-dont-upgrade-my-workspace"></a>如果不升級我的工作區會發生什麼事？  
接下來幾個月內舊版記錄搜尋會被取代。 屆時未升級的工作區將會自動升級。

### <a name="i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>我未選擇要升級，但是我的工作區仍然升級！ 發生什麼情形？  
此工作區的其他管理員可能已升級工作區。 請注意，當新的語言正式運作時，所有工作區便會自動升級。  

### <a name="i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>我不小心升級，但是現在需要取消並且將所有項目還原回來。 我該怎麼辦？！  
沒問題。  我們會在升級時之前建立工作區的快照集，以便您可以將它還原。 請注意，在您升級之後儲存的搜尋、警示或檢視會遺失。  若要還原您的工作區環境，請依照[我是否可以在升級之後返回？](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade)中的程序執行。



## <a name="log-searches"></a>記錄檔搜尋

### <a name="i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>我已在升級工作區之外儲存搜尋。 是否可以自動將它們轉換成新的搜尋語言？
您可以使用記錄搜尋分頁中的語言轉換器工具，轉換每個項目。  沒有任何方法可以在未升級工作區的情況下，自動轉換多個搜尋。


## <a name="alerts"></a>Alerts

### <a name="i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>我有大量的警示規則。 需要在升級之後以新的語言再次建立嗎？  
否，警示規則會在升級期間自動轉換成新的搜尋語言。  

## <a name="power-bi"></a>Power BI

### <a name="does-anything-change-with-powerbi-integration"></a>PowerBI 整合有任何變更嗎？
是。  一旦升級您的工作區，將 Log Analytics 資料匯出至 Power BI 的程序就無法再運作。  您在升級之前建立的任何現有排程將會變成停用。  升級之後，Azure Log Analytics 會使用與 Application Insights 相同的平台，您使用如同[將 Application Insights 查詢匯出至 Power BI 的程序](../application-insights/app-insights-export-power-bi.md#export-analytics-queries)的相同程序，將 Log Analytics 查詢匯出至 Power BI。

## <a name="dashboards"></a>儀表板

### <a name="can-i-still-use-dashboards"></a>仍然可以使用儀表板嗎？
您可以繼續使用在升級工作區之前建立的儀表板，但您無法編輯這些儀表板或建立新的儀表板。  您可以繼續使用[檢視表設計工具](log-analytics-view-designer.md)建立和編輯檢視。 

## <a name="next-steps"></a>後續步驟

- 深入了解[將您的工作區升級為新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)。

