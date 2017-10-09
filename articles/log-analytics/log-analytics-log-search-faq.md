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
ms.date: 09/26/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 0ced7a128003402f74b847cc71e1c3ed21982651
ms.contentlocale: zh-tw
ms.lasthandoff: 09/26/2017

---

# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Log Analytics 新記錄搜尋常見問題集與已知問題

本文包含[將 Log Analytics 升級為新的查詢語言](log-analytics-log-search-upgrade.md)的相關常見問題集和已知問題。  您應該先閱讀整份文件，然後再決定是否升級您的工作區。


## <a name="alerts"></a>Alerts

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>問題：我有大量的警示規則。 需要在升級之後以新的語言再次建立嗎？  
否，警示規則會在升級期間自動轉換成新的搜尋語言。  


## <a name="computer-groups"></a>電腦群組

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>問題：嘗試使用電腦群組發生錯誤。  其語法是否已變更？
是，升級您的工作區時，使用電腦群組的語法已變更。  如需詳細資訊，請參閱 [Log Analytics 記錄檔搜尋中的電腦群組](log-analytics-computer-groups.md)。

### <a name="known-issue-groups-imported-from-active-directory"></a>已知問題：從 Active Directory 匯入的群組
您目前無法建立使用從 Active Directory 匯入之電腦群組的查詢。  作為修正此問題的因應措施，請使用匯入的 Active Directory 群組建立新的電腦群組，然後在您的查詢中使用新群組。

建立包含已匯入 Active Directory 群組之新電腦群組的範例查詢如下所示：

    ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "AD Group Name" and TimeGenerated >= ago(24h) | distinct Computer


## <a name="dashboards"></a>儀表板

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>問題：是否仍然可以在升級的工作區中使用儀表板？
您可以繼續使用在升級工作區之前新增至 [我的儀表板] 的圖格，但您無法編輯這些圖格或建立新的圖格。  您可以使用[檢視設計工具](log-analytics-view-designer.md)繼續建立並編輯檢視，也可以在 Azure 入口網站中建立儀表板。


## <a name="log-searches"></a>記錄檔搜尋

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>問題：我已在升級工作區之外儲存搜尋。 是否可以自動將它們轉換成新的搜尋語言？
您可以使用記錄搜尋分頁中的語言轉換器工具，轉換每個項目。  沒有任何方法可以在未升級工作區的情況下，自動轉換多個搜尋。

### <a name="question-why-are-my-query-results-not-sorted"></a>問題： 為什麼我的查詢結果未排序？
根據預設，使用新的查詢語言不會排序結果。  使用 [sort 運算子](https://go.microsoft.com/fwlink/?linkid=856079)，依照一或多個屬性來排序結果。

### <a name="question-where-did-minify-go-after-i-upgraded"></a>問題：升級之後，「縮短」功能到哪裡去了？
「縮短」是一種功能，可為您的搜尋結果提供摘要檢視。  升級之後，[縮短] 選項不會再出現在記錄搜尋入口網站中。  您可以使用 [reduce](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) 或 [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster)，搭配新的搜尋語言，取得類似的功能。 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()


### <a name="known-issue-search-results-in-a-list-may-include-properties-with-no-data"></a>已知問題：清單中的搜尋結果可能包含沒有資料的屬性
清單中的記錄搜尋結果可能會顯示沒有資料的屬性。  升級之前，不會包含這些屬性。  未來將修正此問題，以避免顯示空白屬性。

### <a name="known-issue-selecting-a-value-in-a-chart-doesnt-display-detailed-results"></a>已知問題：選取圖表中的值不會顯示詳細結果
升級之前，當您選取圖表中的值，會傳回符合所選取值的詳細記錄清單。  升級之後，只會傳回單一的摘要行。  目前正在調查此問題。

## <a name="log-search-api"></a>記錄檔搜尋 API

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>問題：升級後，記錄搜尋 API 是否也會更新？
在升級工作區時，舊版[記錄搜尋 API](log-analytics-log-search-api.md) 將不再有用。  如需新 API 的詳細資訊，請參閱 [Azure Log Analytics REST API](https://dev.loganalytics.io/)。


## <a name="portals"></a>入口網站

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>問題：我應該使用新的進階 Analytics 入口網站或繼續使用記錄搜尋入口網站？
您可以在 [Azure Log Analytics 中用於建立和編輯記錄查詢的入口網站](log-analytics-log-search-portals.md)看到兩個入口網站的比較。  兩者各有不同的優點，因此您可以針對需求選擇最適合的入口網站。  通常會在進階 Analytics 入口網站中寫入查詢，並將它們貼到其他地方，例如檢視設計工具。  這麼做時，您應該閱讀[需考量的問題](log-analytics-log-search-portals.md#advanced-analytics-portal)。


## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>問題：PowerBI 整合有任何變更嗎？
是。  一旦升級您的工作區，將 Log Analytics 資料匯出至 Power BI 的程序就無法再運作。  您在升級之前建立的任何現有排程將會變成停用。  升級之後，Azure Log Analytics 會使用與 Application Insights 相同的平台，您使用如同[將 Application Insights 查詢匯出至 Power BI 的程序](../application-insights/app-insights-export-power-bi.md#export-analytics-queries)的相同程序，將 Log Analytics 查詢匯出至 Power BI。

### <a name="known-issue-power-bi-request-size-limit"></a>已知問題：Power BI 要求大小限制
目前匯出 Log Analytics 查詢至 Power BI 的大小限制為 8 MB。  此限制即將放寬。


##<a name="powershell-cmdlets"></a>PowerShell Cmdlet

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>問題：升級後，記錄搜尋 PowerShell Cmdlet 是否也會更新？
[Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) 尚未升級至新的搜尋語言。  即使在升級工作區之後，請繼續搭配使用舊版查詢語言與此 Cmdlet。  更新後，Cmdlet 之說明文件也將更新。


## <a name="resource-manager-templates"></a>Resource Manager 範本

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>問題：我可以使用 Resource Manager 範本建立升級工作區嗎？
是。  您必須使用 2017-03-15-preview 的 API 版本，並包含範本中的**功能**區段，如以下範例所示。

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>解決方案

### <a name="question-will-my-solutions-continue-to-work"></a>問題：我的解決方案能繼續運作嗎？
所有解決方案將會在升級的工作區中繼續運作，如果其轉換為新的查詢語言，將改善其效能。  本節中所述之現有解決方案目前已知存在問題。

### <a name="known-issue-capacity-and-performance-solution"></a>已知問題：容量與效能解決方案
[容量與效能] [](log-analytics-capacity.md)檢視中的某些部分可能是空白的。  近期內將提供此問題的修正。

### <a name="known-issue-application-insights-connector"></a>已知問題：Application Insights Connector
已升級工作區目前不支援 [Application Insights Connector 解決方案](log-analytics-app-insights-connector.md)中的檢視方塊。  此問題的修正目前正在進行分析。

### <a name="known-issue-backup-solution"></a>已知問題：備份解決方案
備份解決方案不會收集升級工作區中的資料。 可搭配升級工作區使用的新備份解決方案很快就會公佈。

## <a name="upgrade-process"></a>升級程序

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>問題：我有數個工作區。 可以同時升級所有工作區嗎？  
否。  一次只能將升級套用至單一工作區。 目前無法一次升級多個工作區。 請注意，升級工作區的其他使用者也會受到影響。  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>問題：如果我升級，我的工作區中收集的現有記錄資料會被修改嗎？  
否。 您的工作區搜尋可用的記錄資料不會受到升級影響。 已儲存的搜尋、警示和檢視會自動轉換成新的搜尋語言。  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>問題：如果不升級我的工作區會發生什麼事？  
接下來幾個月內舊版記錄搜尋會被取代。 屆時未升級的工作區將會自動升級。

### <a name="question-i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>問題：我並未選擇要升級，但是我的工作區仍然升級了！ 發生什麼情形？  
此工作區的其他管理員可能已升級工作區。 請注意，當新的語言正式運作時，所有工作區便會自動升級。  

### <a name="question-i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>問題：我不小心升級了，現在需要取消並且將所有項目還原回來。 我該怎麼辦？！  
沒問題。  我們會在升級時之前建立工作區的快照集，以便您可以將它還原。 請注意，在您升級之後儲存的搜尋、警示或檢視會遺失。  若要還原您的工作區環境，請依照[我是否可以在升級之後還原？](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade)中的程序執行


## <a name="views"></a>Views

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>問題：如何使用檢視設計工具建立新的檢視？
升級之前，您可以從主要儀表板上的圖格，使用檢視設計工具建立新的檢視。  升級您的工作區時，會移除此圖格。  您可以按一下左側功能表中綠色的 + 按鈕，以在 OMS 入口網站中使用檢視設計工具建立新的檢視。

### <a name="known-issue-see-all-option-for-line-charts-in-views-doesnt-result-in-a-line-chart"></a>已知問題：折線圖的檢視全部選項不會產生折線圖
當您在視圖中折線圖部分的底部按一下 [檢視全部] 選項，會出現資料表。  升級之前，會出現折線圖。  目前正在分析此問題以進行可能的修改。


## <a name="next-steps"></a>後續步驟

- 深入了解[將您的工作區升級為新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)。

