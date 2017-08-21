---
title: "Azure Service Fabric 監視和診斷概觀 | Microsoft Docs"
description: "了解如何對 Azure Service Fabric 叢集、應用程式和服務進行監視和診斷。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 88f4a23f89a1c8fd88db1df3a7ff03ae5df64c0f
ms.contentlocale: zh-tw
ms.lasthandoff: 07/20/2017

---

# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>對 Azure Service Fabric 進行監視和診斷

不論任何環境，針對應用程式與服務的開發、測試及部署進行監視與診斷，都極為重要。 如果您要規劃和實作監視和診斷功能，以協助確保應用程式和服務可在本機開發環境或生產環境中如預期般運作，Service Fabric 解決方案就是理想之選。

監視和診斷的主要目標是︰
* 偵測並診斷硬體和基礎結構的問題
* 偵測軟體和應用程式的問題，縮短服務中斷時間
* 了解資源耗用量，以及協助進行磁碟機作業決策
* 最佳化應用程式、服務及基礎結構效能
* 產生商業見解，並找出改善之處


監視與診斷的整體工作流程包含三個步驟：

1. **事件產生**：這包含基礎結構 (叢集)、平台和應用程式/服務層級中的事件 (記錄、追蹤、自訂事件)
2. **事件彙總**：產生的事件必須經過收集與彙總後，才能夠顯示。
3. **分析**︰事件必須經過視覺化並可藉由某種格式來存取，才能視需要進行分析及顯示。

市面上很多產品已涵蓋這三個領域，因此您可以針對每個領域選擇不同的技術。 請務必確認各項技術能夠互相配合，以為叢集提供端對端的監視解決方案。

## <a name="event-generation"></a>事件產生

監視和診斷工作流程的第一個步驟是建立與產生事件和記錄檔。 這些事件、記錄和追蹤會從下列兩個層級產生：平台層 (包括叢集、機器或 Service Fabric 動作) 或應用程式層 (針對已部署到叢集的應用程式與服務的任何新增檢測)。 雖然 Service Fabric 預設會提供部分檢測，但這些層級的每個事件皆可自訂。

深入了解[平台層級事件](service-fabric-diagnostics-event-generation-infra.md)和[應用程式層級事件](service-fabric-diagnostics-event-generation-app.md)，以了解提供項目，以及如何新增其他的檢測。

決定好要使用的記錄提供者之後，您必須確認系統能正確彙總記錄檔並加以儲存。

## <a name="event-aggregation"></a>事件彙總

若要收集應用程式與叢集所產生的記錄檔和事件，我們通常建議使用 [Azure 診斷](service-fabric-diagnostics-event-aggregation-wad.md) (類似於代理程式型記錄檔收集) 或 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (同處理序記錄檔收集)。

對 Service Fabric 服務來說，如果記錄檔來源與目的地的集合不常變更，並且來源與其目的地之間有直接的對應，則使用 Azure 診斷擴充功能來收集應用程式記錄檔是非常適合的選項。 這是因為 Azure 診斷會在 Resource Manager 層進行設定，因此若有大幅設定變更時需更新/重新部署叢集。 此外，若要確保您的記錄檔能更永久儲存在可供各種分析平台存取的位置，這也是最適用的選擇。 這表示，比起採用 EventFlow 這類選項，管線的效率較低。

使用 [EventFlow](https://github.com/Azure/diagnostics-eventflow) 時，您可以讓服務直接將記錄檔傳送給分析和視覺化平台，及/或儲存體。 ILogger、Serilog 等其他程式庫或許可以用於相同的目的，但 EventFlow 的優點在於它是專門針對同處理序記錄檔收集而設計，並且支援 Service Fabric 服務。 這可以帶來下列幾項潛在的優勢：

* 輕鬆設定及部署
    * 診斷資料收集組態只是服務組態的一部分。 可輕鬆將其與應用程式的其餘部分保持始終「同步」。
    * 可輕鬆達成個別應用程式或個別服務的設定。
    * 透過 EventFlow 設定資料目的地時，只要新增適當的 NuGet 套件，並變更 *eventFlowConfig.json* 檔案即可。
* 彈性
    * 只要用戶端程式庫支援目標資料儲存系統，應用程式可以將資料傳送至任何需要的地方。 您可以視需要新增新的目的地。
    * 可以實作複雜的擷取、篩選和資料彙總規則。
* 存取內部應用程式資料與內容
    * 診斷子系統是在應用程式/服務處理序內執行，因此可以輕鬆地隨著內容資訊而擴大追蹤。

值得注意的是，這兩個選項不會互斥，因此雖然使用其中一個選項都可能完成類似的工作，您也可以同時設定兩個選項。 在大部分情況下，合併代理程式與同處理序收集可能得以確保更可靠的監視工作流程。 您可以選擇針對平台層級記錄使用 Azure 診斷延伸模組 (代理程式)，而針對應用程式層級的記錄使用 EventFlow (同處理序收集)。 決定好最適合的方法之後，您就可以開始思考如何顯示及分析資料。

## <a name="event-analysis"></a>事件分析

市面上有多個優異的平台，可進行監視和診斷資料的分析和視覺化作業。 我們建議的兩個平台是 [OMS](service-fabric-diagnostics-event-analysis-oms.md) 和 [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)，因為它們更容易整合 Service Fabric；您也可以考慮[彈性堆疊](https://www.elastic.co/products) (特別是要在離線環境中執行叢集的情況)、[Splunk](https://www.splunk.com/)，或任何您偏好的其他平台。

不論選擇何種平台，重點在於您是否喜歡其中的使用者介面和查詢選項、視覺化資料與建立一目了然之儀表板的功能，以及是否提供可強化監視的其他工具，例如自動化警示。

除了選擇的平台之外，在將 Service Fabric 叢集設為 Azure 資源時，您也可以存取 Azure 立即可用的機器監視功能，這些功能適用於特定的效能和計量監視。

### <a name="azure-monitor"></a>Azure 監視器

您可以使用 [Azure 監視器](../monitoring-and-diagnostics/monitoring-overview.md)，監視構成 Service Fabric 叢集的許多 Azure 資源。 對於[虛擬機器擴展集](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)和個別的[虛擬機器](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)，Azure 入口網站會自動收集並顯示一組計量。 若要檢視收集的資訊，請在 Azure 入口網站中，選取包含 Service Fabric 叢集的資源群組。 接著，選取您想要檢視的虛擬機器擴展集。 在 [監視] 區段中，選取 [計量]，以檢視圖表中的值。

![已收集計量資訊的 Azure 入口網站檢視](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

若要自訂圖表，請依照 [Microsoft Azure 中的計量](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)中的指示。 您也可以根據這些計量建立警示，如[在 Azure 監視器中建立 Azure 服務的警示](../monitoring-and-diagnostics/insights-alerts-portal.md)所述。 您可以使用 Web 攔截來傳送警示給通知服務，如[在 Azure 計量警示上設定 Web 攔截](../monitoring-and-diagnostics/insights-webhooks-alerts.md)所述。 Azure 監視器只支援一個訂用帳戶。 如果您需要監視多個訂用帳戶，或需要其他功能，Microsoft Operations Management Suite 中的 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 已經為內部部署和雲端式基礎結構，提供全面性的 IT 管理解決方案。 您可以將 Azure 監視器的資料直接路由傳送至 Log Analytics，讓您在同一個地方看到整個環境的計量與記錄。

## <a name="next-steps"></a>後續步驟

### <a name="watchdogs"></a>監視程式

監視程式是個別的服務，可以觀察服務之間的健全狀況和負載，並報告健全狀況模型階層中任何項目的健全狀況。 如此可避免在單一服務檢視中無法偵測到的錯誤。 監視程式也非常適合裝載能夠執行修復動作的程式碼，而無需使用者互動 (例如在特定時間間隔，清除儲存體中的記錄檔)。 您可以在[這裡](https://github.com/Azure-Samples/service-fabric-watchdog-service)找到範例監視程式服務實作。

先從了解如何產生[平台層級](service-fabric-diagnostics-event-generation-infra.md)和[應用程式層級](service-fabric-diagnostics-event-generation-app.md)的事件和記錄開始。
