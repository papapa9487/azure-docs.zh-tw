---
title: "Azure 監視器概觀 | Microsoft Docs"
description: "Azure 監視器會收集用於警示、webhook、自動調整，以及自動化的統計資料。 文章也會列出其他 Microsoft 監視選項。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: robb
ms.custom: mvc
ms.openlocfilehash: 8de1eca5a3e52533e05d93cfe30de612e3d0c648
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="overview-of-azure-monitor"></a>Azure 監視器的概觀
本文提供 Microsoft Azure 中 Azure 監視器服務的概觀。 它會討論 Azure 監視器所執行的作業，並提供關於如何使用 Azure 監視器之其他資訊的指標。  如果您偏好影片介紹，請參閱本文最後的＜後續步驟＞連結。 

## <a name="azure-monitor-and-microsofts-other-monitoring-products"></a>Azure 監視器及 Microsoft 的其他監視產品
Azure 監視器可針對 Microsoft Azure 中的大多數服務提供基本等級的基礎結構計量與記錄。 尚未將其資料放入 Azure 監視器的 Azure 服務，未來會將其資料放入。

Microsoft 隨附額外的產品和服務，可針對同時擁有內部部署安裝的開發人員、DevOps 及 IT Ops 提供其他監視功能。 如需概觀及了解這些不同產品和服務如何一起運作的相關資訊，請參閱 [Microsoft Azure 中的監視](monitoring-overview.md)。

## <a name="portal-overview-page"></a>入口網站概觀頁面

Azure 監視器具有登陸頁面，可協助使用者： 
- 了解 Azure 所提供的監視功能。
- 探索、設定及開始使用 Azure 的平台與進階監視功能。

當 Azure 監視器服務發行時，登陸概觀頁面處於預覽狀態。 

該頁面試是瀏覽的起點 (包括開始使用)。 它顯示來自不同服務的精心策劃問題，並可讓使用者深入瀏覽。
 
![非計算資源的監視與診斷模型](./media/monitoring-overview-azure-monitor/monitor-overview-ux2.png)

當您開啟該頁面時，您可以選取您有讀取存取權的訂用帳戶。 針對選取的訂用帳戶，您可以看到：

- **已觸發的警示與警示來源** - 此表格顯示摘要計數、警示來源，以及警示在選取的時間範圍內觸發的次數。 它同時適用於計量與活動記錄警示。
- **活動記錄錯誤** - 若您的任何 Azure 資源記錄具有嚴重性錯誤層級的事件，您可以檢視高階計數並點擊到活動記錄頁面以調查每個事件。
- **Azure 服務健康情況** - 您可以看到「服務健康情況」服務問題、已規劃的維護事件與健康情況建議的計數。 當 Azure 基礎結構的問題影響您的服務時，Azure 服務健康情況將提供個人化資訊。  如需詳細資訊，請參閱 [Azure 服務健康情況](../service-health/service-health-overview.md)。  
- **Application Insights** - 查看目前訂用帳戶中每個 AppInsights 資源的 KPI。 KPI 已針對伺服器端應用程式 (包括 ASP.NET Web 應用程式、Java、Node 與一般應用程式類型) 監視最佳化。 KPI 包括要求速率、回應期間、失敗率與可用性百分比的計量。 

若您尚未開始使用 Log Analytics 或 Application Insights，或您尚未在目前訂用帳戶中設定任何 Azure 警示，該頁面提供連結協助您上手。



## <a name="azure-monitor-sources---compute-subset"></a>Azure 監視器資源 - 計算子集

![非計算資源的監視與診斷模型](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-compute_v6.png)

這裡的計算服務包括 
- 雲端服務 
- 虛擬機器 
- 虛擬機器擴展集 
- Service Fabric

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>應用程式 - 診斷記錄檔、應用程式記錄檔及計量
應用程式能以計算模型在客體 OS 上執行。 它們會發出自己的記錄檔和計量集合。 Azure 監視器依賴 Azure 診斷擴充功能 (Windows 或 Linux) 來收集大多數應用程式等級的計量和記錄。 類型包括

* 效能計數器
* 應用程式記錄檔
* Windows 事件記錄檔
* .NET 事件來源
* IIS 記錄檔
* 以資訊清單為基礎的 ETW
* 損毀傾印
* 客戶錯誤記錄檔

如果沒有診斷擴充功能，就只能使用一些像是 CPU 使用量的計量。 

### <a name="host-and-guest-vm-metrics"></a>主機和客體 VM 計量
先前所列的計算資源具有可互動的專用主機 VM 和客體 OS。 主機 VM 和客體 OS 等同於 Hyper-V Hypervisor 模型中的根 VM 和客體 VM。 您可以同時收集這兩者的計量。 您也可以在客體 OS 上收集診斷記錄。   

### <a name="activity-log"></a>活動記錄檔
您可以搜尋活動記錄檔 (之前稱為「作業記錄」或「稽核記錄」)，以取得 Azure 基礎結構所見之資源的相關資訊。 此記錄檔包含資源的建立或終結時間等資訊。  如需詳細資訊，請參閱[活動記錄概觀](monitoring-overview-activity-logs.md)。 

## <a name="azure-monitor-sources---everything-else"></a>Azure 監視器來源 - 其他項目

![計算資源的監視與診斷模型](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-non-compute_v6.png)


### <a name="resource---metrics-and-diagnostics-logs"></a>資源 - 計量和診斷記錄檔
可收集的計量和診斷記錄視資源類型而異。 例如，Web Apps 會提供磁碟 IO 和百分比 CPU 的統計資料。 服務匯流排佇列沒有那些計量，而是改為提供佇列大小和訊息輸送量等計量。 [支援的計量](monitoring-supported-metrics.md)中提供可針對每個資源收集的計量清單。 

### <a name="host-and-guest-vm-metrics"></a>主機和客體 VM 計量
您的資源和特定主機或客體 VM 之間不一定具有 1:1 的對應，因此沒有計量可供使用。

### <a name="activity-log"></a>活動記錄檔
活動記錄與計算資源一樣。  

## <a name="uses-for-monitoring-data"></a>監視資料的用途
一旦您收集資料，就可以在 Azure 監視器中使用它來執行下列動作。

### <a name="route"></a>路由
您可以將監視資料串流到其他位置。 

範例包括：

- 傳送到 Application Insights，讓您可以使用其更豐富的視覺效果與分析工具。
- 傳送到事件中樞，讓您可以路由傳送到協力廠商工具。 

### <a name="store-and-archive"></a>儲存與封存
某些監視資料已經在 Azure 監視器中儲存一段時間且可供使用。 
- 計量會儲存 30 天。 
- 活動記錄項目會儲存 90 天。 
- 診斷記錄完全不會儲存。 

如果您想要儲存資料的時間長度超過上面所列的時間週期，您可以使用 Azure 儲存體。 監視資料會根據您設定的保留原則，保留在您的儲存體帳戶中。 您需要支付資料在 Azure 儲存體中所佔用的空間。 

以下為使用此資料的幾個方式：

- 資料寫入後，您可以讓其他位於 Azure 之內或之外的工具讀取該資料並加以處理。
- 您可以於本機將該資料下載到本機封存，或是在雲端變更您的保留原則，以延長資料的保留時間。  
- 您可以基於封存目的，將該資料無限期地保留在 Azure 儲存體中。 

### <a name="query"></a>查詢
您可以使用 Azure 監視器 REST API、跨平台命令列介面 (CLI) 命令、PowerShell Cmdlet 或 .NET SDK 來存取系統或是 Azure 儲存體中的資料

範例包括：

* 針對您所撰寫的自訂監視應用程式取得資料
* 建立自訂查詢並將該資料傳送到協力廠商應用程式。

### <a name="visualize"></a>視覺化
以圖形和圖表視覺化您的監視資料，協助您透過比查看資料本身更快的速度找出趨勢。  

幾個視覺化方法包括︰

* 使用 Azure 入口網站
* 將資料路由至 Azure Application Insights
* 將資料路由至 Microsoft PowerBI
* 將資料路由至協力廠商視覺化工具，無論是透過即時串流，或是讓該工具讀取 Azure 儲存體中的封存


### <a name="automate"></a>自動化
您可以使用監視資料來觸發警示或甚至觸發整個程序。 範例包括：

* 使用資料來根據應用程式負載，自動向上或向下調整計算執行個體。
* 在某個計量超過預先定義的臨界值時傳送電子郵件。
* 呼叫 Web URL (webhook) 以在 Azure 之外的系統中執行動作
* 在 Azure 自動化中啟動 Runbook 以執行各種工作

## <a name="methods-of-accessing-azure-monitor"></a>存取 Azure 監視器的方法
一般來說，您可以使用下列其中一個方法操作資料追蹤、路由及擷取。 並非所有方法都適用所有動作或資料類型。

* [Azure 入口網站](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [跨平台命令列介面 (CLI)](insights-cli-samples.md)
* [REST API](https://docs.microsoft.com/rest/api/monitor/)
* [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>後續步驟
深入了解
- 僅 Azure 監視器的影片逐步解說位於  
[開始使用 Azure 監視器](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor)。 
- 在[探索 Microsoft Azure 監視和診斷 ](https://channel9.msdn.com/events/Ignite/2016/BRK2234) \(英文\) 和[來自 Ignite 2016 的 Azure 監視器影片](https://myignite.microsoft.com/videos/4977) \(英文\) 可取得影片，該影片說明您可以使用 Azure 監視器的案例
- 在[開始使用 Azure 監視器](monitoring-get-started.md)中完整執行 Azure 監視器介面
- 設定 [Azure 診斷擴充功能](../azure-diagnostics.md)，如果您正在嘗試診斷雲端服務、虛擬機器或 Service Fabric 應用程式中的問題。
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) ，如果您正在嘗試診斷 App Service Web 應用程式中的問題。
- [疑難排解 Azure 儲存體](../storage/common/storage-e2e-troubleshooting.md) 
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 及 [Operations Management Suite](https://www.microsoft.com/oms/)
