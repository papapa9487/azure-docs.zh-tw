---
title: "使用 Azure 監視器來監視 API 管理 | Microsoft Docs"
description: "了解如何使用 Azure 監視器來監視 Azure API 管理服務。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: 717e033aa4bbd4dd8ebcc727c3f551aee81770dc
ms.contentlocale: zh-tw
ms.lasthandoff: 08/31/2017

---
# <a name="monitor-api-management-with-azure-monitor"></a>使用 Azure 監視器監視 API 管理
Azure 監視器是一項 Azure 服務，可提供單一來源來讓您監視所有 Azure 資源。 您可以使用 Azure 監視器來視覺化、查詢、路由、封存及針對來自 Azure 資源 (例如 API 管理) 的計量和記錄採取行動。 

下列影片示範如何使用 Azure 監視器來監視 API 管理。 如需 Azure 監視器的詳細資訊，請參閱[開始使用 Azure 監視器]。 


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>度量
API 管理目前可發出五個計量，未來預計會新增更多計量。 這些計量會每分鐘發出一次，讓您近乎即時地了解 API 的狀態和健全狀況。 以下是計量的摘要：
* 閘道要求總數︰該期間內的 API 要求數目。 
* 成功的閘道要求︰收到 HTTP 成功回應碼的 API 要求數目，這些回應碼包括 304、307 和任何小於 301 的代碼 (例如 200)。 
* 失敗的閘道要求︰收到 HTTP 錯誤回應碼的 API 要求數目，這些回應碼包括 400 和任何大於 500 的代碼。
* 未經授權閘道器要求︰收到 401、403 和 429 HTTP 回應碼的 API 要求數目。 
* 其他閘道要求︰所收到的 HTTP 回應碼不屬於上述任何類別 (例如 418) 的 API 要求數目。

您可以在 API 管理服務中存取計量，或在 Azure 監視器中存取所有 Azure 資源的計量。 若要在 API 管理服務中檢視計量︰
1. 開啟 Azure 入口網站。
2. 移至 API 管理服務。
3. 按一下 [計量]。

![[計量] 刀鋒視窗][metrics-blade]

如需如何使用計量的詳細資訊，請參閱[計量概觀]。

## <a name="activity-logs"></a>活動記錄檔
活動記錄可讓您深入了解 API 管理服務上所執行的作業。 此記錄以前稱為「稽核記錄」或「作業記錄」。 您可以使用活動記錄來判斷 API 管理服務上所執行之任何寫入作業 (PUT、POST、DELETE) 的「內容、對象和時間」。 

> [!NOTE]
> 活動記錄不會納入讀取 (GET) 作業，也不會納入透過傳統發行者入口網站或原始管理 API 所執行的作業。

您可以在 API 管理服務中存取活動記錄，或在 Azure 監視器中存取所有 Azure 資源的記錄。 若要在 API 管理服務中檢視活動記錄︰
1. 開啟 Azure 入口網站。
2. 移至 API 管理服務。
3. 按一下 [活動記錄]。

![[活動記錄] 刀鋒視窗][activity-logs-blade]

如需如何使用計量的詳細資訊，請參閱[活動記錄概觀]。

## <a name="alerts"></a>Alerts
您可以進行設定來收到以計量和活動記錄為基礎的警示。 Azure 監視器可讓您將警示設定為在觸發時執行下列動作︰

* 傳送電子郵件通知
* 呼叫 Webhook
* 叫用 Azure 邏輯應用程式

您可以在 API 管理服務或 Azure 監視器中設定警示規則。 若要在 API 管理中進行設定︰ 
1. 開啟 Azure 入口網站。
2. 移至 API 管理服務。
3. 按一下 [警示規則]。

![警示規則刀鋒視窗][alert-rules-blade]

如需有關使用警示的詳細資訊，請參閱[警示概觀]。

## <a name="diagnostic-logs"></a>診斷記錄檔
診斷記錄可提供豐富的作業與錯誤資訊，這些資訊對於稽核和疑難排解用途來說很重要。 診斷記錄與活動記錄不同。 活動記錄可讓您深入了解 Azure 資源上所執行的作業。 診斷記錄能讓您了解資源自行執行的作業。

API 管理目前提供關於個別 API 要求的診斷記錄 (每小時提供一批)，且每個要求項目都有下列結構︰

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

您可以在 API 管理服務中存取診斷記錄，或在 Azure 監視器中存取所有 Azure 資源的記錄。 若要在 API 管理服務中檢視診斷記錄︰
1. 開啟 Azure 入口網站。
2. 移至 API 管理服務。
3. 按一下 [診斷記錄]。

![診斷記錄檔刀鋒視窗][diagnostic-logs-blade]

如需如何使用計量的詳細資訊，請參閱[診斷記錄概觀]。

## <a name="next-step"></a>後續步驟

* [開始使用 Azure 監視器]
* [計量概觀]
* [活動記錄概觀]
* [診斷記錄概觀]
* [警示概觀]

[開始使用 Azure 監視器]: ../monitoring-and-diagnostics/monitoring-get-started.md
[計量概觀]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[活動記錄概觀]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[診斷記錄概觀]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[警示概觀]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png

