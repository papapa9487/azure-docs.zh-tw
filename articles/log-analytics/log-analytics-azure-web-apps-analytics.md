---
title: "檢視 Azure Web Apps 分析資料 | Microsoft Docs"
description: "您可以使用 Azure Web Apps 分析解決方案在所有 Azure Web 應用程式資源之間收集不同的計量，以深入了解 Azure Web 應用程式。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: banders
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 9ef26d4b6bfd92925a70b7bbdf8979e287c73445
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---

# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>檢視所有 Azure Web 應用程式資源之間的計量分析資料

![Web Apps 符號](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  
Azure Web Apps 分析 (預覽) 解決方案會收集所有 Azure Web 應用程式資源之間的不同計量，以深入了解 [Azure Web Apps](../app-service/app-service-web-overview.md)。 透過此解決方案，您可以分析與搜尋 Web 應用程式資源計量資料。

使用此解決方案，您可以檢視：

- 回應時間最長的 Web Apps 排行榜
- 整個 Web Apps 間的要求數，包括成功與失敗的要求
- 傳入和傳出流量最高的 Web Apps 排行榜
- CPU 與記憶體使用率最高的服務方案排行榜
- Azure Web Apps 活動記錄作業

## <a name="connected-sources"></a>連接的來源

不同於大部分其他 Log Analytics 解決方案，代理程式不會收集 Azure Web Apps 的資料。 解決方案使用的所有資料直接來自於 Azure。

| 連接的來源 | 支援 | 說明 |
| --- | --- | --- |
| [Windows 代理程式](log-analytics-windows-agents.md) | 否 | 解決方案不會收集來自 Windows 代理程式的資訊。 |
| [Linux 代理程式](log-analytics-linux-agents.md) | 否 | 解決方案不會收集來自 Linux 代理程式的資訊。 |
| [SCOM 管理群組](log-analytics-om-agents.md) | 否 | 解決方案不會收集來自連線 SCOM 管理群組的代理程式之中的資訊。 |
| [Azure 儲存體帳戶](log-analytics-azure-storage.md) | 否 | 解決方案不會收集來自 Azure 儲存體的資訊。 |

## <a name="prerequisites"></a>必要條件

- 若要存取 Azure Web 應用程式資源計量資訊，您必須有 Azure 訂用帳戶。

## <a name="configuration"></a>組態

執行下列步驟為您的工作區設定 Azure Web Apps 分析解決方案。

1. 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) 或使用[從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)中所述的程序，啟用 Azure Web Apps 分析解決方案。
2. [使用 PowerShell 允許 Azure 資源計量記錄至 OMS (Enable Azure resource metrics logging to OMS using PowerShell)](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell)。

Azure Web Apps 分析解決方案會從 Azure 收集兩組計量：

- Azure Web Apps 計量
  - 平均記憶體工作集
  - 平均回應時間
  - 已接收/傳送的位元組
  - CPU 時間
  - 要求
  - 記憶體工作集
  - Httpxxx
- App Service 方案計量
  - 已接收/傳送的位元組
  - CPU 百分比
  - 磁碟佇列長度
  - Http 佇列長度
  - 記憶體百分比

如果您使用的是專用的服務方案，則只會收集 App Service 方案計量。 這不適用於免費或共用的 App Service 方案。

如果您使用 OMS 入口網站新增解決方案，會看見下列圖格。 您必須[使用 PowerShell 允許 Azure 資源計量記錄至 OMS (enable Azure resource metrics logging to OMS using PowerShell)](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell)。

![執行評估通知](./media/log-analytics-azure-web-apps-analytics/performing-assessment.png)

在您設定解決方案後，資料應會在 15 分鐘內開始流向您的工作區。

## <a name="using-the-solution"></a>使用解決方案

當您將 Azure Web Apps 分析解決方案新增至工作區時，[Azure Web Apps 分析] 圖格會新增至 [概觀] 儀表板。 此圖格會顯示該解決方案在您的 Azure 訂用帳戶中可存取的 Azure Web Apps 數目。

![Azure Web Apps 分析圖格](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>檢視 Azure Web Apps 分析資訊

按一下 [Azure Web Apps 分析] 圖格開啟 [Azure Web Apps 分析] 儀表板。 此儀表板包含下表中的刀鋒視窗。 每個刀鋒視窗會針對所指定的範圍與時間範圍，列出最多 10 個符合該刀鋒視窗準則的項目。 您可以按一下刀鋒視窗底部的 [查看全部]，或按一下刀鋒視窗標頭，以執行記錄搜尋來傳回所有記錄。

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| 資料欄 | 說明 |
| --- | --- |
| Azure Web Apps |   |
| Web Apps 要求趨勢 | 針對您已選取的日期範圍顯示 Web Apps 要求趨勢的折線圖，並顯示前十名 Web 要求清單。 按一下折線圖可執行記錄搜尋，以搜尋 <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* (MetricName=Requests OR MetricName=Http*) &#124; measure avg(Average) by MetricName interval 1HOUR</code> <br>按一下 Web 要求項目可執行記錄搜尋，以搜尋所要求的 Web 要求計量趨勢。 |
| Web Apps 回應時間 | 針對您所選取的日期範圍，顯示 Web Apps 回應時間的折線圖。 另外也顯示前十名 Web Apps 回應時間清單。 按一下該圖表可執行記錄搜尋，以搜尋 <code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* MetricName="AverageResponseTime" &#124; measure avg(Average) by Resource interval 1HOUR</code><br> 按一下某個 Web 應用程式可執行記錄搜尋，以傳回該 Web 應用程式的回應時間。 |
| Web Apps 流量 | 顯示 Web Apps 流量 (MB) 的折線圖，並列出 Web Apps 流量排行榜。 按一下該圖表可執行記錄搜尋，以搜尋 <code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"*  MetricName=BytesSent OR BytesReceived &#124; measure sum(Average) by Resource interval 1HOUR</code><br> 它會顯示過去一分鐘有流量的所有 Web Apps。 按一下某個 Web 應用程式可執行記錄搜尋，以顯示針對該 Web 應用程式收到與傳送的位元組數。 |
| Azure App Service 方案 |   |
| CPU 使用率 &gt; 80% 的 App Service 方案 | 顯示 CPU 使用率大於 80% 的 App Service 方案總數，並依 CPU 使用率列出前 10 名 App Service 方案。 按一下總數區可執行記錄搜尋，以搜尋 <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=CpuPercentage &#124; measure Avg(Average) by Resource</code><br> 它會顯示 App Service 方案清單及其平均 CPU 使用率。 按一下某個 App Service 方案可執行記錄搜尋，以顯示平均 CPU 使用率。 |
| 記憶體使用率 &gt; 80% 的 App Service 方案 | 顯示記憶體使用率大於 80% 的 App Service 方案總數，並依記憶體使用率列出前 10 名 App Service 方案。 按一下總數區可執行記錄搜尋，以搜尋 <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=MemoryPercentage &#124; measure Avg(Average) by Resource</code><br> 它會顯示 App Service 方案清單及其平均記憶體使用率。 按一下某個 App Service 方案可執行記錄搜尋，以顯示其平均記憶體使用率。 |
| Azure Web Apps 活動記錄 |   |
| Azure Web Apps 活動稽核 | 顯示有[活動記錄](log-analytics-activity.md)的 Web Apps 總數，並列出前 10 名的活動記錄作業。 按一下總數區可執行記錄搜尋，以搜尋 <code>Type=AzureActivity ResourceProvider= "Azure Web Sites" &#124; measure count() by OperationName</code><br> 它會顯示活動記錄作業清單。 按一下活動記錄作業可記錄搜尋，以列出作業記錄。 |



### <a name="azure-web-apps"></a>Azure Web Apps 

在儀表板中，您可以向下鑽研以深入了解您的 Web Apps 計量。 這第一組刀鋒視窗會顯示 Web Apps 要求的趨勢、錯誤數目 (例如 HTTP404)、流量，以及一段時間的平均回應時間。 它也會顯示不同 Web Apps 的這些計量細目。

![Azure Web Apps 刀鋒視窗](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

顯示該資料的主要原因是讓您可以識別回應時間最長的 Web 應用程式，並調查找出根本原因。 此外也會套用臨界值限制，幫助您更容易找出有問題之處。

- 紅色的 Web Apps 其回應時間超過 1 秒。
- 橘色的 Web Apps 其回應時間超過 0.7 秒，不到 1 秒。
- 綠色的 Web Apps 其回應時間不到 0.7 秒。

在下列記錄搜尋範例影像中，您可以看到 *anugup3* Web 應用程式的回應時間遠多於其他的 Web 應用程式。

![記錄搜尋範例](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>App Service 方案

如果您使用專用的服務方案，則亦可收集您 App Service 方案的計量。 在這個檢視中，您會看到 CPU 或記憶體使用率高 (&gt; 80%) 的 App Service 方案。 此外，也會顯示記憶體或 CPU 使用率高的應用程式服務排行榜。 同樣地會套用臨界值限制，來幫助您更容易找出有問題的應用程式。

- 紅色的 App Service 方案其 CPU/記憶體使用率高於 80%。
- 橘色的 App Service 方案其 CPU/記憶體使用率高於 60%，低於 80%。
- 綠色的 App Service 方案其 CPU/記憶體使用率低於 60%。

![Azure App Service 方案刀鋒視窗](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Azure Web Apps 記錄搜尋

**熱門 Azure Web Apps 搜尋查詢清單**顯示 Web Apps 的所有相關活動記錄，讓您深入了解在您 Web Apps 資源上執行的作業。 此外也會列出所有相關的作業，以及其發生的次數。

您可以從使用任何的記錄搜尋查詢開始，來輕鬆建立警示。 例如，您可能會想要在計量平均回應時間超過 1 秒時即建立警示。

## <a name="next-steps"></a>後續步驟

- 建立特定計量的[警示](log-analytics-alerts-creating.md)。
- 使用[記錄搜尋](log-analytics-log-searches.md)檢視活動記錄的詳細資訊。

