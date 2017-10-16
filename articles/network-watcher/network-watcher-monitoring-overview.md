---
title: "Azure 網路監看員簡介 | Microsoft Docs"
description: "本頁概述用於監視並以視覺化方式呈現 Azure 中網路連線資源的網路監看員服務"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: eecb20d4a53478471c238018d8fbd5a5f9cb79d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-network-monitoring-overview"></a>Azure 網路監視概觀

客戶可藉由安排和組合各種個別的網路資源 (例如 VNet、ExpressRoute、應用程式閘道、負載平衡器等等)，在 Azure 中建置端對端網路。 每個網路資源都可進行監視。 我們將這種監視稱為資源層級監視。

端對端網路可能會有複雜的組態和資源互動，而產生需要透過網路監看員來進行案例式監視的複雜案例。

本文將探討案例和資源層級的監視。 Azure 中的網路監視功能相當完善，主要涵蓋類別有二種︰

* [**網路監看員**](#network-watcher) - 網路監看員的功能隨附了案例式監視。 這項服務包括封包擷取、下一個躍點、IP 流量驗證、安全性群組檢視、NSG 流量記錄。 案例層級監視可提供端對端的網路資源檢視，而非個別的網路資源監視。
* [**資源監視**](#network-resource-level-monitoring) - 資源層級監視由診斷記錄、計量、疑難排解和資源健全狀況這四個功能所組成。 這些功能全是建置在網路資源層級。

## <a name="network-watcher"></a>網路監看員

網路監看員是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。

網路監看員目前具有下列功能︰

* **[拓撲](network-watcher-topology-overview.md)** - 提供網路層級檢視，顯示資源群組中的網路資源之間的各種相互連線和關聯。
* **[變動的封包擷取](network-watcher-packet-capture-overview.md)** - 擷取進出虛擬機器的封包資料。 進階篩選選項和微調控制項 (例如能夠設定時間和大小限制) 可讓您靈活擷取資料。 封包資料可以 .cap 格式儲存在 Blob 存放區或本機磁碟上。
* **[IP 流量驗證](network-watcher-ip-flow-verify-overview.md)** - 根據流量資訊 5-tuple 封包參數 (目的地 IP、來源 IP、目的地連接埠、來源連接埠和通訊協定) 檢查是否允許或拒絕封包。 如果封包遭到安全性群組拒絕，則會傳回拒絕封包的規則和群組。
* **[下一個躍點](network-watcher-next-hop-overview.md)** - 決定在 Azure 網路網狀架構中路由傳送之封包的下一個躍點，讓您得以診斷任何設定錯誤的使用者定義路由。
* **[安全性群組檢視](network-watcher-security-group-view-overview.md)** - 取得套用至 VM 的有效和已套用安全性規則。
* **[NSG 流量記錄](network-watcher-nsg-flow-logging-overview.md)** - 網路安全性群組的流量記錄可讓您擷取群組中的安全性規則所允許或拒絕之流量的相關記錄。 流量是由 5-tuple 資訊加以定義，這些資訊分別是來源 IP、目的地 IP、來源連接埠、目的地連接埠和通訊協定。
* **[虛擬網路閘道和連線疑難排解](network-watcher-troubleshoot-manage-rest.md)** - 提供針對虛擬網路閘道和連線進行疑難排解的能力。
* **[網路訂用帳戶限制](#network-subscription-limits)** - 可讓您檢視網路資源使用量與限制。
* **[設定診斷記錄](#diagnostic-logs)** - 提供單一窗格以供啟用或停用資源群組中網路資源的診斷記錄。
* **[連線 (預覽)](network-watcher-connectivity-overview.md)** - 確認是否可以建立從虛擬機器到指定端點的直接 TCP 連線。

### <a name="role-based-access-control-rbac-in-network-watcher"></a>網路監看員的角色型存取控制 (RBAC)

網路監看員使用 [Azure 角色型存取控制 (RBAC) 模型](../active-directory/role-based-access-control-what-is.md)。 網路監看員需要下列權限。 請務必確定用來起始網路監看員 API 或從入口網站使用網路監看員的角色具有所需存取權。

|資源| 權限|
|---|---| 
|Microsoft.Storage/ |讀取|
|Microsoft.Authorization/| 讀取| 
|Microsoft.Resources/subscriptions/resourceGroups/| 讀取|
|Microsoft.Storage/storageAccounts/listServiceSas/ | 動作|
|Microsoft.Storage/storageAccounts/listAccountSas/ |動作|
|Microsoft.Storage/storageAccounts/listKeys/ | 動作|
|Microsoft.Compute/virtualMachines/ |讀取|
|Microsoft.Compute/virtualMachines/ |寫入|
|Microsoft.Compute/virtualMachineScaleSets/ |讀取|
|Microsoft.Compute/virtualMachineScaleSets/ |寫入|
|Microsoft.Network/networkWatchers/packetCaptures/ |讀取|
|Microsoft.Network/networkWatchers/packetCaptures/| 寫入|
|Microsoft.Network/networkWatchers/packetCaptures/| 刪除|
|Microsoft.Network/networkWatchers/ |寫入 |
|Microsoft.Network/networkWatchers/| 讀取 |
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/ | *|

### <a name="network-subscription-limits"></a>網路訂用帳戶限制

網路訂用帳戶限制可為您提供區域中，訂用帳戶的每個網路資源使用量詳細資料與可用資源數上限的對照情況。

![網路訂用帳戶限制][nsl]

## <a name="network-resource-level-monitoring"></a>網路資源層級監視

資源層級監視可使用下列功能︰

### <a name="audit-log"></a>稽核記錄檔

做為網路組態一部分所執行的作業會記錄下來。 這些記錄可在 Azure 入口網站中檢視，或使用 Power BI 之類的 Microsoft 工具或協力廠商工具來擷取。 稽核記錄可透過入口網站、PowerShell、CLI 和 Rest API 來取得。 如需稽核記錄的詳細資訊，請參閱[使用 Resource Manager 來稽核作業](../resource-group-audit.md)

針對所有網路資源所進行的作業都會有稽核記錄。

### <a name="metrics"></a>度量

計量是在一段時間內所收集到的效能測量數據和計數器。 計量目前適用於應用程式閘道。 計量可用來根據臨界值觸發警示。 請參閱[應用程式閘道診斷](../application-gateway/application-gateway-diagnostics.md)，以檢視如何使用計量來建立警示。

![計量檢視][metrics]

### <a name="diagnostic-logs"></a>診斷記錄檔

網路資源會定期和自發地建立事件，並記錄到儲存體帳戶、傳送到事件中樞或 Log Analytics。 這些記錄可讓您深入了解資源的健全狀況。 您可以在 Power BI 和 Log Analytics 等工具中檢視這些記錄。 若要了解如何檢視診斷記錄，請瀏覽 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)。

診斷記錄適用於[負載平衡器](../load-balancer/load-balancer-monitor-log.md)、[網路安全性群組](../virtual-network/virtual-network-nsg-manage-log.md)、路由和[應用程式閘道](../application-gateway/application-gateway-diagnostics.md)。

網路監看員提供診斷記錄檢視。 此檢視包含所有支援診斷記錄的網路資源。 從這個檢視中，您可以方便且快速地啟用和停用網路資源。

![logs][logs]

### <a name="troubleshooting"></a>疑難排解

網路資源現已提供 [疑難排解] 刀鋒視窗，這項入口網站體驗可診斷與個別資源相關聯的常見問題。 這項體驗適用於下列網路資源：ExpressRoute、VPN 閘道、應用程式閘道、網路安全性記錄、路由、DNS、負載平衡器和流量管理員。 若要深入了解資源層級的疑難排解，請瀏覽[使用 Azure 疑難排解來診斷和解決問題](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/)

![疑難排解資訊][TS]

### <a name="resource-health"></a>資源健康情況

系統會定期提供網路資源的健康情況。 這類資源包括 VPN 閘道與 VPN 通道。 資源健全狀況可在 Azure 入口網站上取得。 若要深入了解資源健全狀況，請瀏覽[資源健全狀況概觀](../resource-health/resource-health-overview.md)

## <a name="next-steps"></a>後續步驟

在了解網路監看員之後，您可以接著學習︰

在 VM 上執行封包擷取，請瀏覽 [Azure 入口網站中的變動封包擷取](network-watcher-packet-capture-manage-portal.md)

使用[警示觸發的封包擷取](network-watcher-alert-triggered-packet-capture.md)執行主動監視和診斷。

使用開放原始碼工具，透過[使用 Wireshark 分析封包擷取](network-watcher-deep-packet-inspection.md)來偵測安全性弱點。

了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md)。

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png











