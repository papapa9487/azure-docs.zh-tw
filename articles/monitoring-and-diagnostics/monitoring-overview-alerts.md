---
title: "Microsoft Azure 和 Azure 監視器中的警示概觀 | Microsoft Docs"
description: "警示可讓您監視 Azure 資源度量、事件或記錄檔，並在您所指定條件符合時收到通知。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: afa863e2a900d4f823b77453d92f034db7d5a93f
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure 中的警示是什麼？
本文說明 Microsoft Azure 中的各種警示來源、這些警示的目的和優點，以及如何開始使用這些警示。 此內容特別適用於 Azure 監視器，但也會提供其他警示服務的指示。 警示是在 Azure 中進行監視的一種方法，可讓您對資料設定條件，並在最近的監視資料符合條件時收到通知。

## <a name="taxonomy-of-azure-alerts"></a>Azure 警示的分類
Azure 使用下列詞彙來描述警示及其功能：
* **警示** - 符合時會啟動之準則 (一或多個規則或條件) 的定義。
* **作用中** - 警示所定義的準則符合時的狀態。
* **已解決** - 警示所定義的準則之前符合但已不再符合時的狀態。
* **通知** - 警示成為作用中時所採取的動作。
* **動作** - 傳送給通知接收者的特定呼叫 (例如以電子郵件傳送位址或張貼到 Webhook URL)。 通知通常可觸發多個動作。

## <a name="alerts-in-different-azure-services"></a>不同 Azure 服務中的警示
警示可跨數個 Azure 監視服務使用。 如需如何及何時使用這些服務的資訊，[請參閱這篇文章](./monitoring-overview.md)。 以下是可跨 Azure 使用之警示類型的細分：

| 服務 | 警示類型 | 支援的服務 | 說明 |
|---|---|---|---|
| Azure 監視器 | [度量警示](./insights-alerts-portal.md) | [Azure 監視器支援的度量](./monitoring-supported-metrics.md) | 當任何平台層級度量符合特定條件時收到通知 (例如 VM 上的 CPU % 在過去 5 分鐘大於 90)。 |
|Azure 監視器 | [近乎即時計量警示 (預覽)](./monitoring-near-real-time-metric-alerts.md)| [Azure 監視器支援的資源](./monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for) | 當一或多個平台層級計量符合指定的條件 (例如過去 5 分鐘 VM 上的 CPU% 超過 90 且 Network In 超過 500 MB)，以比計量警示快的速度接收通知。 |
| Azure 監視器 | [活動記錄警示](./monitoring-activity-log-alerts.md) | Azure Resource Manager 中可用的所有資源類型 | 當 [Azure 活動記錄](./monitoring-overview-activity-logs.md)中有任何新事件符合特定條件時收到通知 (例如當 myProductionResourceGroup 中發生「刪除 VM」作業時，或當新服務健康狀態事件的狀態顯示為「作用中」時)。 |
| Application Insights | [度量警示](../application-insights/app-insights-alerts.md) | 經檢測可傳送資料至 Application Insights 的任何應用程式 | 當任何應用程式層級度量符合特定條件時收到通知 (例如伺服器回應時間大於 2 秒)。 |
| Application Insights | [Web 測試警示](../application-insights/app-insights-monitor-web-app-availability.md) | 經檢測可傳送資料至 Application Insights 的任何網站 | 當網站的可用性或回應能力低於預期時收到通知。 |
| Log Analytics | [Log Analytics 警示](../log-analytics/log-analytics-alerts.md) | 設定為傳送資料至 Log Analytics 的任何服務 | 當 Log Analytics 搜尋查詢高於度量及/或事件資料符合特定準則時收到通知。 |

## <a name="alerts-on-azure-monitor-data"></a>Azure 監視器資料的相關警示
Azure 監視器中可用資料的警示類型有三種：計量警示、近乎即時計量警示 (預覽) 與活動記錄警示。

* **度量警示**：當指定的度量值超出您指派的閾值時會觸發這個警示。 當警示為「已啟動」時 (超出閾值且符合警示條件時)，以及當警示為「已解決」時 (再次超出閾值且不再符合條件時)，警示會產生通知。 Azure 監視器所支援的可用度量清單會不斷成長，如需此清單，請參閱 [Azure 監視器上支援的度量清單](monitoring-supported-metrics.md)。
* **近乎即時計量警示 (預覽)**  - 這些警示類似於計量警示，但在一些方面有差異。 首先，顧名思義，這些警示能近乎即時觸發 (最快可每 1 分鐘觸發一次)。 它們也支援監視多個 (目前支援兩個) 計量。  當警示「啟用」時 (每個計量的閾值同時超過且符合警示條件)，或當警示「解決」時 (當至少有一個計量再次超過閾值且已不符合條件)，警示都會產生通知。

> [!NOTE]
> 近乎即時計量警示目前處於公開預覽狀態。 功能與使用者體驗可能會變更。
>
>

* **活動記錄警示** - 當產生符合您已指派之篩選準則的活動記錄事件時會觸發資料流記錄警示。 這些警示只有「已啟動」這個狀態，因為警示引擎只會將篩選準則套用至任何新的事件。 您可以使用這些警示，在發生新的服務健康狀態事件時，或是在使用者或應用程式於您的訂用帳戶中執行作業時 (例如「刪除虛擬機器」)，收到通知。

針對透過 Azure 監視器提供的診斷記錄資料，建議將資料路由傳送至 Log Analytics 並使用 Log Analytics 警示。 下圖摘要說明 Azure 監視器中的資料來源，以及就概念而言如何發出該資料的警示。

![警示的說明](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-alert"></a>如何收到 Azure 監視器警示的通知？
在過去，Azure 的警示來自不同的服務，各自使用其專屬的內建通知方法。 從現在開始，Azure 監視器提供可重複使用的通知群組，稱為動作群組。 動作群組會指定一組通知接收者 (任何數目的電子郵件地址、電話號碼 (簡訊) 或 Webhook URL)，每當啟動參考此動作群組的警示時，所有接收者都會收到該通知。 這可讓您在許多警示物件之間重複使用一組接收者 (例如您隨時待命的工程師清單)。 目前，只有活動記錄警示可以使用動作群組，但未來將有數個其他 Azure 警示類型也可以使用動作群組。

除了電子郵件地址和簡訊號碼，動作群組的通知支援還包括張貼到 Webhook URL。 如此即可啟用自動化和修復，例如使用：
    - Azure 自動化 Runbook
    - Azure Function
    - Azure 邏輯應用程式
    - 第三方服務

近乎即時計量警示 (預覽) 與活動記錄警示都使用動作群組。

度量警示尚無法使用動作群組。 在個別度量警示上，您可以設定通知：
* 將電子郵件通知傳送至服務管理員、共同管理員或您指定的其他電子郵件。
* 呼叫 webhook，可讓您啟動其他自動化動作。

## <a name="next-steps"></a>後續步驟
使用下列項目取得有關警示規則和設定這些規則的資訊：

* 深入了解[計量](monitoring-overview-metrics.md)
* [透過 Azure 入口網站設定計量警示](insights-alerts-portal.md)
* [透過 PowerShell 設定計量警示](insights-alerts-powershell.md)
* [透過命令列介面 (CLI) 設定計量警示](insights-alerts-command-line-interface.md)
* [透過 Azure 監視器 REST API 設定計量警示](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* 深入了解[活動記錄](monitoring-overview-activity-logs.md)
* [透過 Azure 入口網站設定活動記錄警示](monitoring-activity-log-alerts.md)
* [透過 Resource Manager 設定活動記錄警示](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* 檢閱[活動記錄警示 webhook 結構描述](monitoring-activity-log-alerts-webhook.md)
* 深入了解[近乎即時計量警示](monitoring-near-real-time-metric-alerts.md)
* 深入了解[服務通知](monitoring-service-notifications.md)
* 深入了解[動作群組](monitoring-action-groups.md)

