---
title: "Microsoft Azure 中的計量概觀 | Microsoft Docs"
description: "Microsoft Azure 中的度量及其用法概觀"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 405ec51c-0946-4ec9-b535-60f65c4a5bd1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: johnkem
ms.openlocfilehash: eb519aab87c13e8836bf1d41992812762f0cd737
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Microsoft Azure 中的度量概觀
本文章說明何謂 Microsoft Azure 中的度量、其優點，以及如何開始使用它們。  

## <a name="what-are-metrics"></a>何謂度量？
Azure 監視器可讓您取用遙測來查看您 Azure 工作負載的效能與健全狀況。 Azure 遙測資料最重要的類型是由大多數 Azure 資源所發出的度量 (也稱為效能計數器)。 Azure 監視器提供數種方式可設定及取用這些度量進行監視與疑難排解。

## <a name="what-can-you-do-with-metrics"></a>度量能讓您做什麼？
度量是遙測的寶貴來源，可讓您執行下列工作：

* **追蹤資源** (例如 VM、網站或邏輯應用程式) 的效能，方法是在入口網站圖表上繪製其度量，並將該圖表釘選到儀表板。
* **取得問題的通知**，該問題在度量超出特定的閾值時會影響資源的效能。
* **設定自動化動作**，例如自動調整資源，或在度量超出特定的閾值時觸發 Runbook。
* **執行進階分析**或報告您資源的效能或使用量趨勢。
* **封存**您資源的效能或健全狀況歷程記錄以用於**相容性或稽核**。

## <a name="what-are-the-characteristics-of-metrics"></a>度量有哪些特性？
度量具有下列特性︰

* 所有度量皆有**一分鐘的頻率**。 您每分鐘會從您的資源收到度量值，讓您可幾乎即時地看到資源的狀態和健全狀況。
* 度量**可立即使用**。 您不需要選擇加入或設定其他診斷。
* 您可以針對每個度量存取 **30 天的歷程記錄** 。 您可以快速查看最近和每個月的資源效能或健全狀況趨勢。
* 某些計量可能有成對的名稱和數值屬性，稱為**維度**。 這些可讓您以更有意義的方式進一步分類和探索計量。

您也可以：

* 設定當度量超出您設定的閾值時，**會傳送通知或採取自動化動作的度量警示規則**。 自動調整是一種特殊的自動化動作，可讓您相應放大資源來符合您網站或計算資源的連入要求或負載。 您可以設定自動調整設定規則，根據超出閾值的度量相應縮小或放大。

* **路由**所有度量至 Application Insights 或 Log Analytics (OMS)，以啟用即時分析、搜尋及自訂來自您資源的度量資料警示。 您也可以串流度量到事件中樞，可讓您將它們路由至 Azure 串流分析或自訂應用程式，以進行近乎即時的分析。 您需要設定使用診斷設定的事件中樞串流。

* **將度量封存至儲存體**來延長保留時間或將度量用於離線報告。 當您設定資源的診斷設定時，可以將度量路由至 Azure Blob 儲存體。

* 當您選取資源並在圖表上繪製度量時，透過 Azure 入口網站輕鬆探索、存取及**檢視所有度量**。

* 透過新的 Azure 監視器 REST API **取用**度量。

* 使用 PowerShell Cmdlet 或跨平台 REST API **查詢**度量。

  ![Azure 監視器中的度量路由](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>透過入口網站存取度量
以下是如何使用 Azure 入口網站建立度量圖表的快速逐步解說。

### <a name="to-view-metrics-after-creating-a-resource"></a>建立資源之後檢視度量
1. 開啟 Azure 入口網站。
2. 建立 Azure App Service 網站。
3. 建立網站之後，移至網站的 [概觀] 刀鋒視窗。
4. 您可以用 [監視] 圖格的形式檢視新度量。 您可以接著編輯圖格並選取更多度量。

   ![Azure 監視器中的資源度量](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>在單一位置存取所有度量
1. 開啟 Azure 入口網站。
2. 瀏覽至新的 [監視] 索引標籤，然後選取其下方的 [度量] 選項。
3. 從下拉式清單選取您的訂用帳戶、資源群組和資源的名稱。
4. 檢視可用的度量清單。 然後選取您感興趣的度量並繪製它。
5. 您可以按一下右上角的 [釘選]，將它釘選至儀表板。

   ![在 Azure 監視器中的單一位置存取所有度量](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> 您可以從 VM (以 Azure Resource Manager 為基礎) 存取主機層級的度量和虛擬機器擴展集，而不需要任何額外的診斷設定。 這些新的主機層級度量可供 Windows 和 Linux 執行個體使用。 這些度量不會與您在 VM 或虛擬機器擴展集上開啟 Azure 診斷時具有存取權的客體 OS 層級度量混淆。 若要深入了解如何設定診斷，請參閱[什麼是 Microsoft Azure 診斷](../azure-diagnostics.md)。
>
>

Azure 監視器的預覽版本也可讓使用者體驗新的計量圖表。 這項體驗可讓使用者在一個圖表上重疊多個資源的計量。 透過這項新的計量圖表體驗，使用者也可以繪製、分類和篩選多維度計量。 若要深入了解，請按一下[這裡](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>透過 REST API 存取度量
可以透過 Azure 監視器 API 存取 Azure 度量。 有兩個 API 可協助您探索及存取度量：

* 使用 [Azure 監視器計量定義 REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions) 來存取可供服務使用的計量清單和任何維度。
* 使用 [Azure 監視器計量 REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metrics) 來分類、篩選和存取實際的計量資料。

> [!NOTE]
> 本文章透過 Azure 資源的 [新度量 API](https://docs.microsoft.com/en-us/rest/api/monitor/) 涵蓋度量。 新計量定義和計量 API 的 API 版本為 2017-05-01-preview。 可以使用 API 版本 2014-04-01 存取舊版的度量定義和度量。
>
>

如需使用 Azure 監視器 REST API 的更詳細逐步解說，請參閱 [Azure 監視器 REST API 逐步解說](monitoring-rest-api-walkthrough.md)。

## <a name="export-metrics"></a>匯出度量
您可以移至 [監視] 索引標籤下的 [診斷設定] 刀鋒視窗，並檢視度量的匯出選項。 針對本文中先前所述的使用案例，您可以選取要傳送至 Blob 儲存體、Azure 事件中樞或 OMS 的度量 (和診斷記錄檔)。

 ![在 Azure 監視器中匯出度量選項](./media/monitoring-overview-metrics/MetricsOverview3.png)

您可以透過 Resource Manager 範本、[PowerShell](insights-powershell-samples.md)、[Azure CLI](insights-cli-samples.md) 或 [REST API](https://msdn.microsoft.com/library/dn931943.aspx) 來進行設定。

## <a name="take-action-on-metrics"></a>對度量採取動作
若要收到通知或對度量資料採取自動化的動作，您可以設定警示規則或 [自動調整] 設定。

### <a name="configure-alert-rules"></a>設定警示規則
您可以設定度量的警示規則。 這些警示規則可以檢查度量是否已超過某個臨界值， Azure 監視器提供兩個計量警示功能。

計量警示：可透過電子郵件通知您，或引發可用來執行任何自訂指令碼的 Webhook。 您也可以使用 Webhook 來設定第三方產品整合。

 ![Azure 監視器中的度量和警示規則](./media/monitoring-overview-metrics/MetricsOverview4.png)

近呼即時警示 (預覽)：能夠監視資源的多個計量和閾值，然後透過[動作群組](/monitoring-action-groups.md)通知您。 深入了解[近乎即時計量警示](https://aka.ms/azuremonitor/near-real-time-alerts)。


### <a name="autoscale-your-azure-resources"></a>自動調整您的 Azure 資源
某些 Azure 資源可支援相應放大或縮小多個執行個體，以便處理您的工作負載。 自動調整可套用至 App Service (Web Apps)、虛擬機器擴展集和傳統 Azure 雲端服務。 您可以設定自動調整規則，以在影響您工作負載的特定度量超出您所指定的閾值時相應放大或縮小。 如需詳細資訊，請參閱 [自動調整的概觀](monitoring-overview-autoscale.md)。

 ![Azure 監視器中的度量和自動調整](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>了解支援的服務和度量
您可以在 [Azure 監視器度量--每個資源類型支援的度量](monitoring-supported-metrics.md)檢視所有支援服務及其度量的詳細清單。

## <a name="next-steps"></a>後續步驟
請參閱這整篇文章的連結。 此外，請了解：  

* [自動調整規模的常用計量](insights-autoscale-common-metrics.md)
* [如何建立警示規則](insights-alerts-portal.md)
* [使用 Log Analytics 分析來自 Azure 儲存體的記錄](../log-analytics/log-analytics-azure-storage.md)
