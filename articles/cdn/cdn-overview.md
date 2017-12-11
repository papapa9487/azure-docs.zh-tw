---
title: "Azure CDN 概觀 |Microsoft Docs"
description: "了解何謂 Azure 內容傳遞網路 (CDN)，以及如何使用它透過快取 Blob 和靜態內容來傳遞高頻寬內容。"
services: cdn
documentationcenter: 
author: dksimpson
manager: akucer
editor: 
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/10/2017
ms.author: v-semcev
ms.openlocfilehash: cdcf07b6af2bd915345361c0bda2dcd9abe5486e
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="overview-of-the-azure-content-delivery-network"></a>Azure 內容傳遞網路的概觀

Azure 內容傳遞網路 (CDN) 會在策略性放置的位置上快取靜態 Web 內容，以提供最大輸送量來將內容安全地傳遞給使用者。 CDN 為開發人員提供一套全球解決方案，以在全球實體節點上快取內容來迅速地傳遞高頻寬內容。 

> [!NOTE]
> 本文說明 Azure CDN、其運作方式，以及每項 Azure CDN 產品的功能。 若要跳過這項資訊來檢視如何建立 CDN 端點，請參 [開始使用 Azure CDN](cdn-create-new-endpoint.md)。 若要查看目前的 CDN 節點位置清單，請參閱 [Azure CDN POP 位置](cdn-pop-locations.md)。

使用 CDN 來快取網站資產的優點包括：

* 讓使用者享有更好的效能和改善的使用者經驗，尤其是當使用的應用程式需要反覆存取多次才能載入內容時。
* 進行大幅調整可以更妥善地處理瞬間大量負載 (例如產品上市事件的開始)。
* 分散使用者要求並從 Edge Server 直接提供內容，傳送至原始來源的流量就會減少。

## <a name="how-it-works"></a>運作方式
![CDN 概觀](./media/cdn-overview/cdn-overview.png)

1. 使用者 (Alice) 使用具有特殊網域名稱的 URL (例如 `<endpointname>.azureedge.net`) 要求檔案 (也稱為資產)。 DNS 會將要求路由傳送到最佳的存在點 (POP) 位置，這通常是地理位置最接近使用者的 POP。
2. 如果 POP 中的 Edge Server 在其快取中沒有該檔案，Edge Server 便會從原始來源要求檔案。  原始來源可以是 Azure Web 應用程式、Azure 雲端服務、Azure 儲存體帳戶或任何可公開存取的 Web 伺服器。
3. 原始來源將檔案傳回給 Edge Server，包括描述檔案存留時間 (TTL) 的選擇性 HTTP 標頭。
4. Edge Server 快取檔案，並將檔案傳回給原始要求者 (Alice)。  在 TTL 到期之前，檔案會一直快取在 Edge Server 上。  如果原始來源未指定 TTL，預設 TTL 為 7 天。
5. 其他使用者後來可能會使用相同 URL 要求相同檔案，而且也可能導向至該相同 POP。
6. 如果檔案的 TTL 尚未過期，Edge Server 便會從快取傳回檔案。 此程序會產生更快、更靈敏回應的使用者經驗。

## <a name="azure-cdn-features"></a>Azure CDN 功能
共有三種 Azure CDN 產品︰**來自 Akamai 的 Azure CDN 標準**、**來自 Verizon 的 Azure CDN 標準**和**來自 Verizon 的 Azure CDN 進階**。  下表列出每種產品的可用功能。

|  | 標準 Akamai | 標準 Verizon | 進階 Verizon |
| --- | --- | --- | --- |
| __效能功能和最佳化__ |
| [動態網站加速](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[動態網站加速 - 調整映像壓縮](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only) | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[動態網站加速 - 物件預先擷取](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only) | **&#x2713;**  |  |  |
| [影片串流最佳化](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization) | **&#x2713;**  | \* |  \* |
| [大型檔案最佳化](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization) | **&#x2713;**  | \* |  \* |
| [全域伺服器負載平衡 (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [快速清除](cdn-purge-endpoint.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [資產預先載入](cdn-preload-endpoint.md) | |**&#x2713;** |**&#x2713;** |
| 快取/標頭設定 (使用[快取規則](cdn-caching-rules.md)) |**&#x2713;** |**&#x2713;** | |
| 快取/標頭設定 (使用 [規則引擎](cdn-rules-engine.md)) | | |**&#x2713;** |
| [查詢字串快取](cdn-query-string.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 雙重堆疊 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 支援](cdn-http2.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| __安全性__ |
| CDN 端點的 HTTPS 支援 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [自訂網域 HTTPS](cdn-custom-ssl.md) | |**&#x2713;** |**&#x2713;** |
| [自訂網域名稱支援](cdn-map-content-to-custom-domain.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [地理位置篩選](cdn-restrict-access-by-country.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [權杖驗證](cdn-token-auth.md)|  |  |**&#x2713;**| 
| [DDOS 保護](https://www.us-cert.gov/ncas/tips/ST04-015) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| __分析和報告__ |
| [Azure 診斷記錄](cdn-azure-diagnostic-logs.md) | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [來自 Verizon 的 Core 報告](cdn-analyze-usage-patterns.md) | |**&#x2713;** |**&#x2713;** |
| [來自 Verizon 的自訂報告](cdn-verizon-custom-reports.md) | |**&#x2713;** |**&#x2713;** |
| [進階 HTTP 報告](cdn-advanced-http-reports.md) | | |**&#x2713;** |
| [即時統計資料](cdn-real-time-stats.md) | | |**&#x2713;** |
| [邊緣節點效能](cdn-edge-performance.md) | | |**&#x2713;** |
| [即時警示](cdn-real-time-alerts.md) | | |**&#x2713;** |
| __容易使用__ |
| 很容易與[儲存體](cdn-create-a-storage-account-with-cdn.md)、[雲端服務](cdn-cloud-service-with-cdn.md)、[Web Apps](../app-service/app-service-web-tutorial-content-delivery-network.md) 和[媒體服務](../media-services/media-services-portal-manage-streaming-endpoints.md)等 Azure 服務整合 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| 透過 [REST API](https://msdn.microsoft.com/library/mt634456.aspx)、[.NET](cdn-app-dev-net.md)、[Node.js](cdn-app-dev-node.md) 或 [PowerShell](cdn-manage-powershell.md) 管理。 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [可自訂的、規則式內容傳遞引擎](cdn-rules-engine.md) | | |**&#x2713;** |
| URL 重新導向/重寫 (使用 [規則引擎](cdn-rules-engine.md)) | | |**&#x2713;** |
| 行動裝置規則 (使用 [規則引擎](cdn-rules-engine.md)) | | |**&#x2713;** |

\* Verizon 支援透過一般 Web 傳遞直接傳遞大型檔案和媒體。


> [!TIP]
> 您是否有想要在 Azure CDN 中看到的功能？  [請不吝提供意見](https://feedback.azure.com/forums/169397-cdn)！ 
> 
> 

## <a name="next-steps"></a>後續步驟
若要開始使用 CDN，請參閱[開始使用 Azure CDN](cdn-create-new-endpoint.md)。

如果您是現有的 CDN 客戶，現在可以透過 [Microsoft Azure 入口網站](https://portal.azure.com)或 [PowerShell](cdn-manage-powershell.md) 管理您的 CDN 端點。

若要查看作用中的 CDN，請參閱 [2016 組建會議的影片](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/)。

了解如何透過 [.NET](cdn-app-dev-net.md) 或 [Node.js](cdn-app-dev-node.md) 自動化 Azure CDN。

如需價格資訊，請參閱[內容傳遞網路價格](https://azure.microsoft.com/pricing/details/cdn/)。

