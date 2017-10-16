---
title: "在 Azure Application Insights 中使用使用者流程分析使用者瀏覽模式 | Microsoft docs"
description: "分析使用者如何在 Web 應用程式的分頁和功能之間進行瀏覽。"
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 08/02/2017
ms.author: cfreeman
ms.openlocfilehash: d17ed3dff08f00a1d6a2108608e42b29f95fbd84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>在 Application Insights 中使用使用者流程分析使用者瀏覽模式

![Application Insights 使用者流程工具](./media/app-insights-usage-flows/flows.png)

「使用者流程」工具會視覺化使用者如何在網站的分頁和功能之間進行瀏覽。 適合用來回答問題，例如：
* 使用者如何離開網站上的分頁？
* 使用者在網站上的分頁按了什麼？
* 使用者最常從您的網站變換的位置是哪裡？
* 有使用者一再重複相同動作的位置嗎？

「使用者流程」工具會從初始分頁檢視或您指定的事件啟動。 指定此分頁檢視或自訂事件，「使用者流程」會顯示在工作階段期間使用者隨後立即傳送、兩個步驟之後傳送，依此類推的分頁檢視和自訂事件。 不同粗細的線條顯示每個路徑被使用者追蹤的次數。 特殊「工作階段已結束」節點顯示在上一個節點之後有多少使用者未傳送任何分頁檢視或自訂事件，醒目提示使用者可能離開網站的位置。



> [!NOTE]
> 您的 Application Insights 資源必須包含分頁檢視或自訂事件，以便使用「使用者流程」工具。 [了解如何設定應用程式以使用 Application Insights JavaScript SDK 自動收集分頁檢視](app-insights-javascript.md)。
> 
> 

## <a name="start-by-choosing-an-initial-page-view-or-custom-event"></a>從選擇初始分頁檢視或自訂事件開始

![為使用者流程選擇初始事件](./media/app-insights-usage-flows/flows-initial-event.png)

若要開始使用「使用者流程」工具回答問題，請選擇初始分頁檢視或自訂事件作為視覺效果的起點：
1. 按一下「使用者之後要怎麼做...？」標題 中的連結，或者按一下 [編輯] 按鈕。 
2. 從 [初始事件] 下拉式清單中選取分頁檢視或自訂事件。
3. 按一下 [建立圖形]。

視覺效果的 [步驟 1] 資料行會顯示使用者最常在初始事件之後做什麼，由上到下依序為最常至最少。 [步驟 2] 和後續的資料行會顯示使用者之後的動作，建立使用者瀏覽您的網站一路下來的圖片。

根據預設，「使用者流程」工具會從您的網站隨機取樣僅過去 24 小時的分頁檢視和自訂事件。 您可以增加時間範圍，並變更在 [編輯] 功能表中隨機取樣的效能和精確度平衡。

如果某些分頁檢視和自訂事件與您不相關，在您想要隱藏的節點上按一下 [X]。 一旦您已選取想要隱藏的節點，按一下視覺效果下方的 [建立圖形] 按鈕。 若要查看隱藏的所有節點，請按一下 [編輯] 按鈕，然後查看 [排除事件] 區段。

如果分頁檢視或自訂事件遺失，您預期會在視覺效果上看到：
* 在 [編輯] 功能表中檢查 [排除事件] 區段。
* 使用 [編輯] 功能表中的 [詳細資料層級] 控制項，在視覺效果中包含較不常見的事件。
* 如果使用者不常傳送您預期的分頁檢視或自訂事件，請嘗試增加 [編輯] 功能表中視覺效果的時間範圍。
* 請確定您預期的分頁檢視或自訂事件設定為由網站原始程式碼中的 Application Insights SDK 收集。 [深入了解收集自訂事件。](app-insights-api-custom-events-metrics.md)

如果您想要在視覺效果中看到更多步驟，請使用 [編輯] 功能表中的 [步驟數目] 滑桿。

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>造訪分頁或功能之後，使用者的去向以及他們按了什麼？

![使用「使用者流程」以了解使用者點擊的位置](./media/app-insights-usage-flows/flows-one-step.png)

如果您的初始事件是分頁檢視，視覺效果的第一個資料行 ([步驟 1]) 是了解使用者造訪分頁之後隨即做了什麼的快速方法。 請嘗試在「使用者流程」視覺效果旁邊的視窗中開啟您的網站。 比較您預期使用者與分頁的互動方式與 [步驟 1] 資料行中的事件清單。 通常，看起來對於小組微不足道之分頁的 UI 元素，會是分頁上的最常用元素之一。 它是為網站設計增強功能的絕佳起點。

如果您的初始事件是自訂事件，第一個資料行會顯示使用者在執行該動作之後做了什麼。 如同分頁檢視，請考慮觀察到的使用者行為是否符合您的小組目標和期望。 舉例來說，如果您的選取初始事件是「將項目新增至購物車」，則查看「前往結帳」和「已完成採購」是否隨即出現在視覺效果中。 如果使用者行為與您的預期大不相同，請使用視覺效果以了解使用者如何「受困」於網站的目前設計。

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>使用者最常從您的網站變換的位置是哪裡？

查看出現在視覺效果上層資料行中的「工作階段已結束」節點，特別是流程的早期。 這表示許多使用者可能在遵循分頁和 UI 互動的前面路徑之後，從您的網站變換。 有時候變換是預期的 - 例如，在電子商務網站上完成購買之後 - 但是變換通常是設計問題、效能不佳，或您的網站可以改善之其他問題的徵兆。

請注意，「工作階段已結束」節點只會根據這個 Application Insights 資源所收集的遙測。 如果 Application Insights 未收到特定使用者互動的遙測，使用者仍然可以在「使用者流程」工具指出工作階段已結束之後，使用那些方式與網站互動。

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>有使用者一再重複相同動作的位置嗎？

尋找許多使用者跨視覺效果的後續步驟重複的分頁檢視或自訂事件。 這通常表示使用者會在您的網站上執行重複的動作。 如果您發現重複，請考慮變更網站的設計或新增功能以減少重複。 例如，如果您發現使用者在資料表元素的每個資料列上執行重複動作，則新增大量編輯功能。

## <a name="common-questions"></a>常見問題

### <a name="why-do-steps-appear-disconnected"></a>為什麼步驟顯示中斷連線？

![具有已中斷連線步驟的使用者流程](./media/app-insights-usage-flows/flows-disconnected.png)

如果「使用者流程」視覺效果中的步驟 (資料行) 已中斷連線，則表示使用者在步驟之間所採取的路徑都不夠頻繁而無法顯示。 若要在視覺效果上顯示較不頻繁的事件，讓步驟顯示連線，請調整 [編輯] 功能表中的 [詳細資料層級] 滑桿。

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>初始事件代表事件第一次出現在工作階段中，或是出現在工作階段中的任一次？

視覺效果的初始事件只代表使用者第一次在工作期間傳送該分頁檢視或自訂事件。 如果使用者可以在工作階段中傳送初始事件多次，則 [步驟 1] 資料行只會顯示使用者在初始事件  第一個執行個體 (而非所有執行個體) 之後的行為方式。

## <a name="next-steps"></a>後續步驟

* [使用量概觀](app-insights-usage-overview.md)
* [使用者、工作階段和事件](app-insights-usage-segmentation.md)
* [保留](app-insights-usage-retention.md)
* [將自訂事件新增至您的應用程式](app-insights-api-custom-events-metrics.md)