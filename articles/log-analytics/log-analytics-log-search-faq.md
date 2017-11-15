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
ms.date: 11/02/2017
ms.author: bwren
ms.openlocfilehash: 1ec815a12cea98228dd4b7ac7361fe5e3554b5d3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Log Analytics 新記錄搜尋常見問題集與已知問題

本文包含[將 Log Analytics 升級為新的查詢語言](log-analytics-log-search-upgrade.md)的相關常見問題集和已知問題。  您應該先閱讀整份文件，然後再決定是否升級您的工作區。


## <a name="alerts"></a>Alerts

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>問題：我有大量的警示規則。 需要在升級之後以新的語言再次建立嗎？  
否，警示規則會在升級期間自動轉換成新的搜尋語言。  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>問題：我有包含 Webhook 和 Runbook 動作的警示規則。 當我升級時，這些動作是否可繼續運作？

否，Webhook 和 Runbook 動作中有一些變更，您可能需要針對處理承載的方式進行一些變更。 我們進行這些變更是為了將各種輸出格式標準化，以及縮減承載的大小。 如需有關這些格式的詳細資料，請參閱[將動作新增至 Log Analytics 中的警示規則](log-analytics-alerts-actions.md)。


## <a name="computer-groups"></a>電腦群組

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>問題：嘗試使用電腦群組發生錯誤。  其語法是否已變更？
是，升級您的工作區時，使用電腦群組的語法已變更。  如需詳細資訊，請參閱 [Log Analytics 記錄檔搜尋中的電腦群組](log-analytics-computer-groups.md)。


## <a name="dashboards"></a>儀表板

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>問題：是否仍然可以在升級的工作區中使用儀表板？
隨著升級，我們將開始淘汰 [我的儀表板]的程序。  您可以繼續使用在升級工作區之前新增至儀表板的所有圖格，但無法編輯這些圖格或新增新的圖格。  您可以使用[檢視表設計工具](log-analytics-view-designer.md) (此工具提供更豐富的功能集) 來繼續建立和編輯檢視，也可以在 Azure 入口網站中建立儀表板。


## <a name="log-searches"></a>記錄檔搜尋

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>問題：我已在升級工作區之外儲存搜尋。 是否可以自動將它們轉換成新的搜尋語言？
您可以使用記錄搜尋分頁中的語言轉換器工具，轉換每個項目。  沒有任何方法可以在未升級工作區的情況下，自動轉換多個搜尋。

### <a name="question-why-are-my-query-results-not-sorted"></a>問題： 為什麼我的查詢結果未排序？
根據預設，使用新的查詢語言不會排序結果。  使用 [sort 運算子](https://go.microsoft.com/fwlink/?linkid=856079)，依照一或多個屬性來排序結果。

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>問題：升級之後，計量檢視到哪裡去了？
計量檢視會將來自記錄搜尋的效能資料以圖形方式顯示。  升級後，即不再提供此檢視。  您可以使用 [render 運算子](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator)，來設定時間圖中查詢的輸出格式。

### <a name="question-where-did-minify-go-after-i-upgraded"></a>問題：升級之後，「縮短」功能到哪裡去了？
「縮短」是一種功能，可為您的搜尋結果提供摘要檢視。  升級之後，[縮短] 選項不會再出現在記錄搜尋入口網站中。  您可以使用 [reduce](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) 或 [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster)，搭配新的搜尋語言，取得類似的功能。 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()



## <a name="log-search-api"></a>記錄檔搜尋 API

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>問題：升級後，記錄搜尋 API 是否也會更新？
在升級工作區時，舊版[記錄搜尋 API](log-analytics-log-search-api.md) 將不再有用。  如需新 API 的詳細資訊，請參閱 [Azure Log Analytics REST API](https://dev.loganalytics.io/)。


## <a name="portals"></a>入口網站

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>問題：我應該使用新的進階 Analytics 入口網站或繼續使用記錄搜尋入口網站？
您可以在 [Azure Log Analytics 中用於建立和編輯記錄查詢的入口網站](log-analytics-log-search-portals.md)看到兩個入口網站的比較。  兩者各有不同的優點，因此您可以針對需求選擇最適合的入口網站。  通常會在進階 Analytics 入口網站中寫入查詢，並將它們貼到其他地方，例如檢視設計工具。  這麼做時，您應該閱讀[需考量的問題](log-analytics-log-search-portals.md#advanced-analytics-portal)。


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>問題：升級之後，我在嘗試執行查詢時收到錯誤，並在我的檢視中看到錯誤。

您的瀏覽器需要存取下列位址，才能在升級之後執行 Log Analytics 查詢。  如果您的瀏覽器要透過防火牆存取 Azure 入口網站，則必須啟用這些位址的存取。

| Uri | IP | 連接埠 |
|:---|:---|:---|
| portal.loganalytics.io | 動態 | 80,443 |
| api.loganalytics.io    | 動態 | 80,443 |
| docs.loganalytics.io   | 動態 | 80,443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>問題：PowerBI 整合有任何變更嗎？
是。  一旦升級您的工作區，將 Log Analytics 資料匯出至 Power BI 的程序就無法再運作。  您在升級之前建立的任何現有排程將會變成停用。  

升級之後，Azure Log Analytics 會使用與 Application Insights 相同的平台，您使用如同[將 Application Insights 查詢匯出至 Power BI 的程序](../application-insights/app-insights-export-power-bi.md#export-analytics-queries)的相同程序，將 Log Analytics 查詢匯出至 Power BI。  匯出至 Power BI 現在會直接呼叫 API 端點。 這可讓您取得最多 500,000 個資料列或 64,000,000 個位元組的資料、匯出長時間查詢，以及自訂查詢逾時 (預設逾時為 3 分鐘，且最大逾時為 10 分鐘)。

## <a name="powershell-cmdlets"></a>PowerShell Cmdlet

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>問題：升級後，記錄搜尋 PowerShell Cmdlet 是否也會更新？
完成所有工作區的升級時，將會取代 [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults)。  使用 [Invoke-LogAnalyticsQuery Cmdlet](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets)，在已升級工作區中執行記錄搜尋。




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

### <a name="known-issue-perspectives-in-application-insights-connector"></a>已知問題：Application Insights Connector 中的檢視方塊
Application Insights Connector 解決方案不再支援 [Application Insights Connector 解決方案](log-analytics-app-insights-connector.md)中的檢視方塊。  您可以使用檢視設計工具建立包含 Application Insights 資料的自訂檢視。

### <a name="known-issue-backup-solution"></a>已知問題：備份解決方案
如果在升級工作區之前已安裝備份解決方案，則可能無法收集資料。 解除安裝解決方案，然後再安裝最新的版本。  新版本的解決方案不支援傳統的備份保存庫，因此您也必須升級為復原服務保存庫，才能繼續使用該解決方案。

## <a name="upgrade-process"></a>升級程序

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>問題：我有數個工作區。 可以同時升級所有工作區嗎？  
否。  一次只能將升級套用至單一工作區。 目前無法一次升級多個工作區。 請注意，升級工作區的其他使用者也會受到影響。  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>問題：如果我升級，我的工作區中收集的現有記錄資料會被修改嗎？  
否。 您的工作區搜尋可用的記錄資料不會受到升級影響。 已儲存的搜尋、警示和檢視會自動轉換成新的搜尋語言。  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>問題：如果不升級我的工作區會發生什麼事？  
接下來幾個月內舊版記錄搜尋會被取代。 屆時未升級的工作區將會自動升級。

### <a name="question-can-i-revert-back-after-i-upgrade"></a>問題：我可以在升級之後還原嗎？
公開上市之前，您可以在升級之後還原工作區。  新的語言現在已可公開上市，在我們開始淘汰舊版平台時已移除這項功能。



## <a name="views"></a>Views

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>問題：如何使用檢視設計工具建立新的檢視？
升級之前，您可以從主要儀表板上的圖格，使用檢視設計工具建立新的檢視。  升級您的工作區時，會移除此圖格。  您可以按一下左側功能表中綠色的 + 按鈕，以在 OMS 入口網站中使用檢視設計工具建立新的檢視。


## <a name="next-steps"></a>後續步驟

- 深入了解[將您的工作區升級為新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)。
