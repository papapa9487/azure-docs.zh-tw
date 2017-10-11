---
title: "服務對應解決方案自學示範 | Microsoft Docs"
description: "服務對應是 Operations Management Suite (OMS) 中的一個解決方案，可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。  這是一個自學示範，會逐步解說如何使用服務對應來識別及診斷 Web 應用程式中的模擬問題。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: c3548d24c74f8ad865b22d6af3490d0b5cc77a84
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="operations-management-suite-oms-self-paced-demo---service-map"></a>Operations Management Suite (OMS) 自學示範 - 服務對應
這是一個自學示範，會逐步解說如何使用 Operations Management Suite (OMS) 中的[服務對應解決方案](operations-management-suite-service-map.md)來找出及診斷 Web 應用程式中的模擬問題。  服務對應可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。  它也會合併其他 OMS 服務所收集的資料，協助您分析效能和找出問題。  您也會使用 [Log Analytics 中的記錄搜尋](../log-analytics/log-analytics-log-searches.md)，向下切入收集的資料以便找出根本問題。


## <a name="scenario-description"></a>案例描述
您剛收到一則通知：ACME 客戶入口網站應用程式發生效能問題。  您所擁有的唯一資訊就是這些問題大約在今天上午 4:00 (太平洋標準時間) 開始出現。  除了一組 Web 伺服器以外，您並未完全確定入口網站相依的所有元件。  

## <a name="components-and-features-used"></a>使用的元件和功能
- [服務對應解決方案](operations-management-suite-service-map.md)
- [Log Analytics 記錄搜尋](../log-analytics/log-analytics-log-searches.md)


## <a name="walk-through"></a>逐步解說

### <a name="1-connect-to-the-oms-experience-center"></a>1.連線至 OMS 體驗中心
此逐步解說會使用 [Operations Management Suite 體驗中心](https://experience.mms.microsoft.com/)，提供包含範例資料的完整 OMS 環境。 首先遵循此連結，提供您的資訊，然後選取**深入解析與分析**案例。


### <a name="2-start-service-map"></a>2.啟動服務對應
按一下 [服務對應] 圖格，以啟動服務對應解決方案。

![服務對應圖格](media/operations-management-suite-walkthrough-servicemap/tile.png)

服務對應主控台隨即顯示。  左窗格中有您的環境中已安裝服務對應代理程式的電腦清單。  您將從這份清單中選取您想要檢視的電腦。

![電腦清單](media/operations-management-suite-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3.檢視電腦
我們知道，Web 伺服器稱為 AcmeWFE001 和 AcmeWFE002，所以這似乎是合理的起點。  按一下 [AcmeWFE001]。  這會顯示 AcmeWFE001 與其所有相依性的對應。  您可以查看哪些程序正在選取的電腦上執行，以及與其通訊的外部服務。

![Web 伺服器](media/operations-management-suite-walkthrough-servicemap/web-server.png)

我們很擔心 Web 應用程式的效能，所以按一下 [AcmeAppPool (IIS 應用程式集區)] 程序。  這會顯示此程序的詳細資料，並醒目提示其相依性。  

![應用程式集區](media/operations-management-suite-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4.變更時間範圍

我們聽到問題是在上午 4:00 開始出現，所以讓我們看看該時間發生什麼狀況。 按一下 [時間範圍]，將時間變更為太平洋標準時間上午 4:00 (保留目前的日期並針對當地時區進行調整) 並持續 20 分鐘的時間。

![時間選擇器](./media/operations-management-suite-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5.檢視警示

我們現在看到 **acmetomcat** 相依性顯示警示，因此這是可能的問題。  按一下 **acmetomcat** 中的警示圖示，以顯示警示詳細資料。  我們可以看到 CPU 使用率有重大警示並可將其展開，以取得更詳細的資料。  這可能是造成效能變慢的原因。 

![警示](./media/operations-management-suite-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6.檢視效能

讓我們仔細看看 **acmetomcat**。  按一下右上方的 **acmetomcat**，然後選取 [載入伺服器對應] 以顯示這台電腦的詳細資料和相依性。 然後我們可以進一步查看這些效能計數器，以確認我們的懷疑。  選取 [效能] 索引標籤，以顯示 [Log Analytics 在時間範圍內所收集的效能計數器](../log-analytics/log-analytics-data-sources-performance-counters.md)。  我們可以看到，我們的處理器和記憶體出現週期性尖峰。

![效能](./media/operations-management-suite-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7.檢視變更追蹤
我們來看看是否可以找出可能造成此高使用率的原因。  按一下 [摘要] 索引標籤。  這會提供 OMS 從電腦收集到的資訊，例如失敗的連線、重大警示，以及軟體變更。  具有最新有趣資訊的區段應該已經展開，而您可以展開其他區段來檢查其中包含的資訊。


如果 [變更追蹤] 尚未開啟，則將它展開。  這會顯示[變更追蹤解決方案](../log-analytics/log-analytics-change-tracking.md)所收集的資訊。  看起來，已在這段期間範圍內進行軟體變更。  按一下 [軟體] 以取得詳細資料。  備份程序已在上午 4:00 後新增至電腦，因此這似乎耗用過多資源的罪魁禍首。

![變更追蹤](./media/operations-management-suite-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8.在記錄搜尋中檢視詳細資料
我們可以查看在 Log Analytics 存放庫中收集的詳細效能資訊，進一步確認這點。  再次按一下 [警示] 索引標籤，然後按一下其中一個 [高 CPU] 警示。  按一下 [在記錄搜尋中顯示]。  這會開啟 [記錄搜尋] 視窗，您可以在該視窗中對存放庫中儲存的任何資料執行[記錄搜尋](../log-analytics/log-analytics-log-searches.md)。  服務對應已填入查詢中，以供我們擷取感興趣的警示。  

![記錄搜尋](./media/operations-management-suite-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9.開啟已儲存的搜尋
我們看看是否可以取得更多有關產生此警示之效能集合的詳細資料，並確認我們懷疑的問題是由該備份程序所造成。  將時間範圍變更為 [6 小時]。  然後按一下 [我的最愛] 並向下捲動至針對 [服務對應] 儲存的搜尋。  這些是我們特別為此分析所建立的查詢。  按一下 [acmetomcat CPU 的前 5 個處理程序]。

![儲存的搜尋](./media/operations-management-suite-walkthrough-servicemap/saved-search.png)


此查詢會傳回在 **acmetomcat** 上耗用處理器的前 5 個處理程序。  您可以檢查此查詢，以取得用於記錄搜尋的查詢語言簡介。  如果您對其他電腦上的處理程序感興趣，您可以修改查詢來擷取該資訊。

在此情況下，我們可以看到備份程序始終耗用大約 60% 的應用程式伺服器 CPU。  這個新程序顯然是效能問題的原因。  我們的解決方法顯然會是移除應用程式伺服器中的這個新備份軟體。  我們實際可以運用 Azure 自動化所管理的期望狀態組態 (DSC) 來定義原則，以確保此程序永遠不會在這些重要系統上執行。


## <a name="summary-points"></a>摘要點
- 即使您不知道應用程式的所有伺服器和相依性，[服務對應](operations-management-suite-service-map.md)也會提供整個應用程式的檢視。
- 服務對應會呈現其他 OMS 解決方案所收集的資料，協助您找出您的應用程式與其基礎結構的問題。
- [記錄搜尋](../log-analytics/log-analytics-log-searches.md)可讓您向下切入 Log Analytics 存放庫中收集的特定資料。    

## <a name="next-steps"></a>後續步驟
- 深入了解[服務對應](operations-management-suite-service-map.md)。
- [部署和設定](operations-management-suite-service-map-configure.md)服務對應。
- 了解服務對應所需的 [Log Analytics](../log-analytics/log-analytics-overview.md)，其可儲存代理程式所儲存的作業資料。