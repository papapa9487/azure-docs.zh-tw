---
title: "Azure Application Insights 漏斗圖"
description: "了解如何使用漏斗圖來探索客戶與您的應用程式互動的方式。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 2b25ffd844507074a8467b1135b1331feb9d3ed7
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---

# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>使用 Application Insights 漏斗圖來探索客戶如何使用您的應用程式

了解客戶體驗對於您的企業非常重要。 如果您的應用程式牽涉到多個階段，您必須了解大部分客戶是否進行了整個流程，或是在某個時間點結束流程。 在 Web 應用程式中通過一系列步驟的過程便稱為「漏斗圖」。 您可以使用 Application Insights 漏斗圖來深入了解使用者，並監視各步驟的轉換率。 

## <a name="get-started-with-the-funnels-blade"></a>開始使用 [漏斗圖] 刀鋒視窗
了解漏斗圖最簡單的方法是透過範例逐步執行。 下圖示範電子商務企業的擁有者會採取的步驟，以了解其客戶如何與 Web 應用程式互動。  

### <a name="create-your-funnel"></a>建立您的漏斗圖
建立您的漏斗圖之前，您需要決定要獲得回答的問題。 例如，您可能想知道有多少檢視首頁的客戶在廣告上按一下。 在此範例中，Fabrikam Fiber 公司的擁有者想知道上個月中，客戶將項目加入購物車後進行購買的百分比為何。

以下是他們用來建立漏斗圖所採取的步驟。

1. 按一下 [漏斗圖] 刀鋒視窗上的 [新增] 按鈕。
1. 在 [時間範圍] 下拉式清單中，選取 [上個月] 作為時間範圍。 
1. 從 [步驟 1] 下拉式清單選取 [產品頁面] 活動。 
1. 從 [步驟 2] 下拉式清單選取 [加入至購物車] 活動。
1. 從 [步驟 3] 下拉式清單選取 [按一下購買] 活動。
1. 新增漏斗圖的名稱，然後按一下 [儲存]。

下圖示範 [漏斗圖] 刀鋒視窗產生的資料。 在這裡，Fabrikam 的擁有者可看到上星期中，將項目加入購物車的客戶有 22.7% 完成了購買。 他們也會看到有 1% 的客戶在瀏覽產品頁面之前按了一下廣告，而 20% 的客戶在完成購買之後就登出。


![含有資料的 [漏斗圖] 刀鋒視窗](./media/app-insights-understand-usage-patterns/funnel1.png)

## <a name="next-steps"></a>後續步驟
  * [使用量概觀](app-insights-usage-overview.md)
  * [使用者、工作階段和事件](app-insights-usage-segmentation.md)
  * [保留](app-insights-usage-retention.md)
  * [活頁簿](app-insights-usage-workbooks.md)
  * [新增使用者內容](app-insights-usage-send-user-context.md)

