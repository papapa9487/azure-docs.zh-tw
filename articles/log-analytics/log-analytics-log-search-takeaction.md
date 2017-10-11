---
title: "Log Analytics 中由使用者初始化的 Azure 自動化 Runbook 動作 | Microsoft Docs"
description: "本文說明如何視需要從 Log Analytics 搜尋結果執行自動化 Runbook。"
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: ff938697add98f3d21b4971175432335ee2e39ba
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>從 Log Analytics 記錄搜尋結果利用自動化 Runbook 採取動作

從 Azure Log Analytics 中的記錄搜尋結果，您現在可以選取 [採取動作] 執行自動化 Runbook。  Runbook 可用來修復問題或採取其他動作，例如收集疑難排解資訊、傳送電子郵件，或建立服務要求。 

## <a name="components-and-features-used"></a>使用的元件和功能
* [Azure 自動化帳戶](../automation/automation-offering-get-started.md)
* [Log Analytics 工作區](../log-analytics/log-analytics-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>從記錄搜尋初始化 Runbook

若要對事件採取動作，並從記錄搜尋結果初始化 Runbook，首先請建立記錄搜尋，然後就可以從結果中視需要叫用 Runbook。  您可以利用 Azure 或 [OMS 入口網站](../log-analytics/log-analytics-log-searches.md)中的記錄搜尋功能達到此目的。  在此範例中，我們會透過這項功能的基本示範，從 Azure 入口網站執行記錄搜尋。

1. 在 Azure 入口網站的 [中樞] 功能表上按一下 [更多服務]，然後選取 [Log Analytics]。  
2. 在 [Log Analytics] 記錄搜尋上，選取您的 Log Analytics 工作區，然後在工作區刀鋒視窗上選取 [記錄搜尋]。  
3. 在 [記錄搜尋] 刀鋒視窗中，執行記錄搜尋。  
4. 從記錄搜尋結果中，按一下其中一個欄位左邊的省略符號，然後從快顯視窗中，選取 [對...採取動作]。<br><br> ![從搜尋結果中選取採取動作](./media/log-analytics-log-search-takeaction/log-search-takeaction-menuoption.png) 
5. 從 [採取動作] 刀鋒視窗中，選取 [執行 Runbook]，然後在 [執行 Runbook] 刀鋒視窗中，您可以選取要執行的 Runbook。  您可以在連結至記錄 Log Analytics 工作區的自動化帳戶中選取任何 Runbook。  請注意：

    * Runbook 是依標籤來組織。
    * 從搜尋結果的欄位中，可直接選取 Runbook 輸入參數值。  出現的下拉式清單會顯示結果中所有可供選取的欄位。  
    * 您選擇執行 Runbook 的地方，可以是您在有問題的電腦上已安裝的[混合式 Runbook 背景工作角色](../automation/automation-hybrid-runbook-worker.md)，前提是您有對應的混合式 Runbook 背景工作群組，而且這台電腦是唯一成員。  如果混合式背景工作群組的名稱符合記錄搜尋結果中的電腦名稱，則會自動選取該群組。    

6. 按一下 [執行] 之後，Runbook 作業刀鋒視窗會開啟，讓您檢閱作業的狀態。   

如果您選取的 Runbook 已設定為[從 Log Analytics 警示呼叫](../automation/automation-invoke-runbook-from-omsla-alert.md)，它會有一個 **Object** 類型的輸入參數，稱為 **WebhookData**。  如果是必要的輸入參數，您需要將搜尋結果傳遞給 Runbook，它才能將 JSON 格式的字串轉換成物件類型，讓您篩選要在 Runbook 活動中參考的特定項目。  作法是從下拉式清單中選取 [搜尋結果 (Object)]。<br><br> ![選取 Webhook 資料物件給 Runbook 參數](media/log-analytics-log-search-takeaction/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>後續步驟

* 檢閱 [Log Analytics 記錄檔搜尋參考資料](log-analytics-search-reference.md) ，以檢視 Log Analytics 中提供的所有搜尋欄位和 Facet。
* 若要了解如何自動叫用自動化 Runbook，請檢閱[從 OMS Log Analytics 警示呼叫 Azure 自動化 Runbook](../automation/automation-invoke-runbook-from-omsla-alert.md)。  