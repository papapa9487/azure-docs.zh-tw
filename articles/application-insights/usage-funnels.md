---
title: "Azure Application Insights 漏斗圖"
description: "了解如何使用漏斗圖來探索客戶與您的應用程式互動的方式。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: 0396c59d9d95ab71f0af04029d87afbb6e47dc35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>使用 Application Insights 漏斗圖來探索客戶如何使用您的應用程式

了解客戶體驗對於您的企業非常重要。 如果您的應用程式牽涉到多個階段，您必須了解大部分客戶是否進行了整個流程，或是在某個時間點結束流程。 在 Web 應用程式中通過一系列步驟的過程便稱為「漏斗圖」。 您可以使用 Application Insights 漏斗圖來深入了解使用者，並監視各步驟的轉換率。 

## <a name="create-your-funnel"></a>建立您的漏斗圖
建立您的漏斗圖之前，您需要決定要獲得回答的問題。 例如，您可能想要知道有多少使用者檢視首頁、檢視客戶設定檔和建立票證。 在此範例中，Fabrikam Fiber 公司的擁有者想要知道已成功建立客戶票證的客戶百分比。

以下是他們用來建立漏斗圖所採取的步驟。

1. 按一下 [漏斗圖] 工具上的 [新增] 按鈕。
1. 在 [時間範圍] 下拉式清單中，選取 [過去 90 天] 作為時間範圍。 選取 [我的漏斗圖] 或 [共用的漏斗圖]
1. 從 [步驟 1] 下拉式清單選取 [索引] 事件。 
1. 從 [步驟 2] 下拉式清單選取 [客戶] 事件。
1. 從 [步驟 3] 下拉式清單選取 [建立] 事件。
1. 新增漏斗圖的名稱，然後按一下 [儲存]。

下圖示範 [漏斗圖] 工具產生的資料。 Fabrikam 擁有者可以從這裡看到在過去 90 天期間，54.3% 的客戶已造訪首頁、建立客戶票證。 他們也會看到 2700 個客戶從首頁來到索引，這可能表示重新整理問題。


![具有資料的漏斗圖工具](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnel-features"></a>漏斗圖功能
1. 如果您的應用程式進行取樣，您會看到取樣橫幅。 按一下橫幅會開啟內容窗格，指示要如何關閉取樣。 
2. 您可以將漏斗圖匯出至 [Power BI](app-insights-export-power-bi.md)。
3. 按一下右邊的步驟以深入了解。 
4. 歷程記錄轉換會顯示過去 90 天的轉換。 
5. 從漏斗圖移至使用者工具，以更加了解您的使用者。 每個步驟都會給予您已策劃的使用者篩選。 

## <a name="next-steps"></a>後續步驟
  * [使用量概觀](app-insights-usage-overview.md)
  * [使用者、工作階段和事件](app-insights-usage-segmentation.md)
  * [保留](app-insights-usage-retention.md)
  * [活頁簿](app-insights-usage-workbooks.md)
  * [新增使用者內容](app-insights-usage-send-user-context.md)
  * [匯出至 Power BI](app-insights-export-power-bi.md)

