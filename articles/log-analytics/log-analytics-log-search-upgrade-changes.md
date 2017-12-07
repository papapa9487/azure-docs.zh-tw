---
title: "Azure Log Analytics 有哪些改變？ | Microsoft Docs"
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
ms.date: 11/29/2017
ms.author: bwren
ms.openlocfilehash: 017a1da233827f19489a99b234ee9009fd9f6fe3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="whats-changed-in-azure-log-analytics"></a>Azure Log Analytics 有哪些改變？
當您的 Log Analytics 工作區[升級為新的查詢語言](log-analytics-log-search-new.md)時，除了查詢語言本身，還有一些增強功能和改變您應該知道。  本文簡要說明詳細的內容，舊版和升級工作區之間的改變，並提供每一項的詳細資訊連結。 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Whats-changed-in-Azure-Log-Analytics/player]

如需一般問題的答案以及任何已知升級問題的說明，請參閱[新的 Log Analytics 記錄搜尋常見問題集和已知的問題](log-analytics-log-search-faq.md)。  

## <a name="query-language"></a>查詢語言
Log Analytics 升級的主要改變是新的查詢語言，相較於先前的語言有顯著的改良。  

您可以[轉換至 Azure Log Analytics 新的查詢語言](log-analytics-log-search-transition.md)中看到新語言和舊語言的一般作業直接比較。  新語言的完整文件可在 [Azure Log Analytics 查詢語言](https://docs.loganalytics.io)網站取得。


## <a name="computer-groups"></a>電腦群組
建立電腦群組的方法沒有變，但現在您必須為每個群眾提供唯一別名。  電腦群組以記錄搜尋為基礎，必須使用新語言的語法。

在記錄搜尋中使用電腦群組的語法是新語法。  現在，不是使用 $ComputerGroups 函式，而是將電腦群組實作為各有唯一別名的函式。  您可以在記錄搜尋中使用此別名，就和其他函式一樣。  

如需詳細資訊，請參閱 [Log Analytics 記錄檔搜尋中的電腦群組](log-analytics-computer-groups.md)。


## <a name="log-search-portals"></a>記錄搜尋入口網站
除了用記錄搜尋入口網站建立和執行記錄搜尋，您現在可以使用「進階分析」入口網站，其中提供大幅改善的編輯功能。

您可以在 [Azure Log Analytics 中用於建立和編輯記錄查詢的入口網站](log-analytics-log-search-portals.md)看到兩個入口網站的簡單說明。  在 [Analytics 入口網站使用者入門](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal)中有新入口網站的逐步教學課程。

## <a name="alerts"></a>Alerts
在升級後的工作區中，警示的運作方式不變，但它們執行的查詢必須以新的語言撰寫。  任何在升級前便已存在的現有警示規則，將自動轉換成新的語言。  如需詳細資訊，請參閱[了解 Log Analytics 中的警示](log-analytics-alerts.md)。

由警示傳出的 Runbook 和 Webhook 承載的格式已改變。  可至[將動作新增至 Log Analytics 中的警示規則](log-analytics-alerts-actions.md)取得新承載格式的詳細資料。

## <a name="dashboards"></a>儀表板
[儀表板](log-analytics-dashboards.md)正逐漸被取代。  您可以繼續使用在升級工作區之前新增至儀表板的所有圖格，但無法編輯這些圖格或新增新的圖格。  使用 [檢視設計工具] 建立自訂檢視，它能提供比儀表板更豐富的功能。

有關檢視設計工具的詳細資訊，請參閱[在 Log Analytics 中使用檢視設計工具來建立自訂檢視](log-analytics-view-designer.md)。

## <a name="power-bi"></a>Power BI
在升級後的工作區中，將 Log Analytics 資料匯出至 Power BI 的程序已改變，任何您在升級之前建立的現有排程將停用。  

詳細資訊在[將 Log Analytics 資料匯出至 Power BI](log-analytics-powerbi.md)。

## <a name="powershell"></a>PowerShell
從 PowerShell 執行記錄搜尋時，Get AzureRmOperationalInsightsSearchResults 不適用於升級後的工作區。  在升級後的升級工作區中，這項作業要使用 Invoke-LogAnalyticsQuery 來執行。

詳細資訊在 [Azure Log Analytics REST API - PowerShell Cmdlet](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets)。

## <a name="log-search-api"></a>記錄檔搜尋 API
記錄搜尋 REST API 已經改變，任何使用舊版本的解決方案皆需要更新為使用新的 API 版本。   

如需新版本 API 的詳細資訊，可在 [Azure Log Analytics REST API](https://dev.loganalytics.io/) 找到。

## <a name="next-steps"></a>後續步驟

- 如需一般問題的答案以及任何已知升級問題的說明，請參閱[新的 Log Analytics 記錄搜尋常見問題集和已知的問題](log-analytics-log-search-faq.md)。