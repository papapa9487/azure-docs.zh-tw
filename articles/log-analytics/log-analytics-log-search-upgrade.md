---
title: "將 Azure Log Analytics 升級為新的記錄搜尋 | Microsoft Docs"
description: "新的 Log Analytics 查詢語言快要完成了，您可以參加公開預覽。  本文說明新語言的優點以及如何轉換您的工作區。"
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
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 8cde51313f683fbf96a89c8335eb75348b05dee6
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---

# <a name="upgrade-your-azure-log-analytics-workspace-to-new-log-search"></a>將您的 Azure Log Analytics 工作區升級為新的記錄搜尋

> [!NOTE]
> 升級為新的 Log Analytics 查詢語言目前是選擇性的，讓您有時間[掌握新語言](https://docs.loganalytics.io/learn/tutorials/getting_started_with_queries.html)。  

新的 Log Analytics 查詢語言在此，您必須升級工作區以利用它。  本文說明新語言的優點以及如何轉換您的工作區。  如果您沒有選擇立即升級，則您的工作區將會如同往常一般繼續運作，但是將會在日後自動轉換。  設定該日期時，您會有足夠的時間和通知。

這篇文章提供新的語言和升級程序的詳細資料。

## <a name="why-the-new-language"></a>為什麼要使用新的語言？
我們了解任何轉換都會有陣痛期，而且我們變更查詢語言不是為了好玩。  這項變更將會為 Log Analytics 客戶帶來顯著的價值有幾個原因。

- **簡單但功能強大。** 新的語言更易於了解，而且類似 SQL，然而其建構比舊版語言更像自然語言。 
- **完整的管線語言。**  新的語言具有比舊版語言更廣泛的管線功能。  幾乎任何輸出都可以管線傳送至另一個命令，以建立可能比先前更複雜的查詢。
- **搜尋時間欄位擷取。**  新的語言支援比舊版語言更進階的執行階段導出欄位。  您可以針對擴充欄位使用複雜的計算，然後針對其他命令 (包括聯結和彙總) 使用導出欄位。
- **進階聯結。**  新的語言提供比舊版語言更進階的聯結，包括在多個欄位上聯結資料表、使用內部和外部聯結，以及在擴充欄位上聯結的能力。
- **日期/時間函式。**  新的語言比舊版語言具備更進階的日期/時間函式。
- **智慧分析。**  新的語言具有進階演算法，以評估資料集中的模式，並比較不同的資料集。
- **進階分析入口網站。**  進階分析入口網站提供 Log Analytics 入口網站中無法使用的分析功能，包括查詢的多行編輯、其他視覺效果，以及進階診斷。
- **與其他應用程式的一致性。**  新的語言和進階分析入口網站已用於 Application Insights 中的分析。  針對 Log Analytics 實作它，在 Azure 服務之間提供一致性。
- **與 Power BI 的更佳整合。** 使用新語言的查詢可以匯出至 Power BI Desktop，因此您可以利用其豐富的資料轉換功能。
- **更多功能。** 請參閱 [Azure Log Analytics 查詢語言](https://docs.loganalytics.io/index.html)網站，以取得新語言的完整詳細資料和教學課程。


## <a name="when-can-i-upgrade"></a>何時可以升級？
升級將在所有 Azure 區域推出，因此某些區域可能會比其他區域更快取得。  當您看到工作區頂端的紫色橫幅邀請您升級時，您就會知道何時可以升級工作區。 

![升級 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

## <a name="what-happens-when-i-upgrade"></a>升級時會發生什麼事？
當您轉換工作區時，您使用檢視設計工具建立的任何已儲存搜尋、警示規則及檢視，都會自動轉換成新的語言。  包含在解決方案中的搜尋不會自動轉換，而是會在您開啟它們時即時轉換。  這是完全透明的。

## <a name="can-i-go-back-after-i-upgrade"></a>升級之後可以回復嗎？
當您升級時，會進行工作區的完整備份，包含所有已儲存搜尋、警示規則和檢視的快照集。  這可讓您在稍後想要時，還原舊的工作區。

若要還原舊版工作區，請移至工作區中的 [設定]，然後移至 [升級摘要]。  然後，您可以選取 [還原舊版工作區] 的選項。  

![還原舊版](media/log-analytics-log-search-upgrade/restore-legacy-b.png)

## <a name="how-do-i-perform-the-upgrade"></a>如何執行升級？
當您在入口網站頂端看到紫色橫幅時，就可以升級您的工作區。  

1.  藉由按一下指出**深入了解及升級**的紫色橫幅，開始升級程序。<br>![升級 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>
2.  閱讀升級資訊分頁上有關升級的其他資訊。<br>![升級 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>
3.  按一下 [立即升級] 以開始升級。<br>![升級 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>右上角的通知方塊會顯示狀態。<br>![升級 5](media/log-analytics-log-search-upgrade/upgrade-05.png)
4.  這樣就大功告成了！  前往 [記錄搜尋] 分頁以查看升級的工作區。<br>![升級 6](media/log-analytics-log-search-upgrade/upgrade-06.png)<br>

如果您遇到造成升級失敗的問題，您可以移至[討論論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)並張貼您的問題，或者從 Azure 入口網站[建立支援要求](../azure-supportability/how-to-create-azure-support-request.md)。

## <a name="how-do-i-learn-the-new-language"></a>如何了解新的語言？
因為新的語言會由多個服務使用，所以我們為新的語言建立了[用來裝載文件的外部網站](https://docs.loganalytics.io/)。  這包括教學課程、範例和完整參考，可協助您加速前進。 您可以在[開始使用查詢](https://docs.loganalytics.io/learn/tutorial_getting_started_with_queries.html)逐步進行新語言的教學課程，並且在 [Log Analytics 查詢語言](https://docs.loganalytics.io/queryLanguage/query_language.html)存取語言參考。  

如果您已熟悉舊版 Log Analytics 查詢語言，則您可以使用語言轉換器，這個工具新增至您的工作區作為升級的一部分。

只要輸入舊版查詢，然後按一下 [轉換] 即可看到翻譯的版本。  接著，您可以按一下搜尋按鈕，執行搜尋或複製並貼上轉換的查詢，以在例如警示規則的其他地方使用。
  
![語言轉換器](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>後續步驟
- 請參閱[新語言的教學課程](https://docs.loganalytics.io/learn/tutorial_getting_started_with_queries.html)。
- 逐步解說以新的查詢語言[使用記錄搜尋入口網站的教學課程](log-analytics-log-search-log-search-portal.md)。
- 取得新的[進階分析入口網站](https://docs.loganalytics.io/learn/tutorial_getting_started_with_analytics_portal.html)的簡介。
