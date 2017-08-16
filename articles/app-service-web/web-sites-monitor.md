---
title: "Azure App Service 中的 Node.js API 應用程式 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站來監視 Azure App Service 中的 Web 應用程式。"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: byvinyal
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 25d3776920d683fffedcd8ac6ed0e84dfe875974
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>作法：監視 Azure App Service 中的應用程式
[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 在 [Azure 入口網站](https://portal.azure.com)中提供內建監視功能。
這包括檢閱應用程式及 App Service 方案的**配額**和**計量**、設定**警示**，甚至是根據這些計量自動**調整**的功能。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>了解配額和計量
### <a name="quotas"></a>配額
App Service 中裝載的應用程式都必須遵守其可用資源的某些「限制」  。 限制是由定義與應用程式相關聯的 **App Service 方案** 所定義。

如果應用程式裝載於**免費**或**共用**方案中，則應用程式可用資源的限制是由**配額**所定義。

如果應用程式裝載於**基本**、**標準**或**進階**方案中，則其可用資源的限制是由 **App Service 方案**的**大小** (小、中、大) 和**執行個體計數** (1、2、3 …) 所設定。

**免費**或**共用**應用程式的**配額**如下︰

* **CPU (短期)**
  * 此應用程式在 5 分鐘間隔內允許的 CPU 數量。 此配額會每 5 分鐘重設一次。
* **CPU (天)**
  * 此應用程式在 1 天內允許的 CPU 總量。 此配額會每隔 24 小時在午夜 (UTC) 重設一次。
* **記憶體**
  * 此應用程式允許的記憶體總量。
* **頻寬**
  * 此應用程式在 1 天內允許的連出頻寬總量。
    此配額會每隔 24 小時在午夜 (UTC) 重設一次。
* **Filesystem**
  * 允許的儲存體總量。

唯一適用於**基本**、**標準**和**進階**方案上裝載之應用程式的配額是 **Filesystem**。

這裡可以找到有關不同 App Service SKU 可用的特定配額、限制和功能的詳細資訊︰ [Azure 訂用帳戶服務限制](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>強制配額
如果應用程式的使用量超過 **CPU (短期)**、**CPU (天)** 或**頻寬**配額，則應用程式將會停止，直到重設配額為止。 在此期間，所有連入要求都會導致 **HTTP 403**。
![][http403]

如果超出應用程式 **記憶體** 配額，則會重新啟動應用程式。

如果超出 **Filesystem** 配額，則所有寫入作業都會失敗，這包括寫入記錄檔。

升級您的 App Service 方案，即可在應用程式中增加或移除配額。

### <a name="metrics"></a>度量
**計量** 提供有關應用程式或 App Service 方案行為的資訊。

若為 **應用程式**，可用的計量如下︰

* **平均回應時間**
  * 應用程式處理要求所花費的平均時間 (以毫秒為單位)。
* **平均記憶體工作集**
  * 應用程式使用的平均記憶體數量 (MiB)。
* **CPU 時間**
  * 應用程式所耗用的 CPU 數量 (以秒為單位)。 如需此計量的詳細資訊，請參閱︰[CPU 時間與 CPU 百分比](#cpu-time-vs-cpu-percentage)
* **資料輸入**
  * 應用程式所耗用的連入頻寬量 (MiB)。
* **資料輸出**
  * 應用程式所耗用的連出頻寬量 (MiB)。
* **Http 2xx**
  * 導致 http 狀態碼 >= 200 但 < 300 的要求計數。
* **Http 3xx**
  * 導致 http 狀態碼 >= 300 但 < 400 的要求計數。
* **Http 401**
  * 導致 HTTP 401 狀態碼的要求計數。
* **Http 403**
  * 導致 HTTP 403 狀態碼的要求計數。
* **Http 404**
  * 導致 HTTP 404 狀態碼的要求計數。
* **Http 406**
  * 導致 HTTP 406 狀態碼的要求計數。
* **Http 4xx**
  * 導致 http 狀態碼 >= 400 但 < 500 的要求計數。
* **Http 伺服器錯誤**
  * 導致 http 狀態碼 >= 500 但 < 600 的要求計數。
* **記憶體工作集**
  * 應用程式目前使用的記憶體數量 (MiB)。
* **要求**
  * 要求總數 (不論其導致的 HTTP 狀態碼為何)。

若為 **App Service 方案**，可用的計量如下︰

> [!NOTE]
> App Service 方案計量只適用於**基本**、**標準**和**進階** SKU 中的方案。
> 
> 

* **CPU 百分比**
  * 方案的所有執行個體使用的平均 CPU。
* **記憶體百分比**
  * 方案的所有執行個體使用的平均記憶體。
* **資料輸入**
  * 方案的所有執行個體使用的平均連入頻寬。
* **資料輸出**
  * 方案的所有執行個體使用的平均連出頻寬。
* **磁碟佇列長度**
  * 儲存體上已排入佇列的平均讀取和寫入要求數目。 磁碟佇列長度很大表示應用程式可能因為過度的磁碟 I/O 而變慢。
* **Http 佇列長度**
  * 必須在履行前排入佇列的平均 HTTP 要求數目。 HTTP 佇列長度很大或不斷增加是方案負載過重的徵兆。

### <a name="cpu-time-vs-cpu-percentage"></a>CPU 時間與 CPU 百分比
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

有 2 個計量可反映 CPU 使用量。 **CPU 時間**與 **CPU 百分比**

**CPU 時間**很適合用於**免費**或**共用**方案中裝載的應用程式，因為其中有一個配額是以應用程式使用的 CPU 分鐘數來定義。

另一方面，**CPU 百分比**很適合用於**基本**、**標準**和**進階**方案中裝載的應用程式，因為它們可以相應放大，而此計量會清楚指出所有執行個體的整體使用量。

## <a name="metrics-granularity-and-retention-policy"></a>計量資料粒度和保留原則
服務會記錄和彙總應用程式和 App Service 方案的計量，其資料粒度和保留原則如下︰

* **分鐘**資料粒度度量會保留 **48 小時**
* **小時**資料粒度度量會保留 **30 天**
* **天**資料粒度度量會保留 **90 天**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>在 Azure 入口網站中監視配額和計量。
您可以在 [Azure 入口網站](https://portal.azure.com)中，檢閱會影響應用程式的不同**配額**和**計量**狀態。

在 [設定] > **配額** 之下可以找到 ![][quotas]
**配額**。 UX 可讓您檢閱：(1) 配額名稱，(2) 其重設的間隔，(3) 以及其目前的限制和 (4) 目前的值。

![][metrics]
**計量** 可以直接從資源刀鋒視窗存取。 您也可以自訂圖表：(1) **按一下**圖表，然後選取 (2) [編輯圖表]。
您可以從這裡變更要顯示的 (3) [時間範圍]、(4) [圖表類型] 和 (5) [計量]。  

您可以在此進一步了解計量︰[監視服務計量](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)。

## <a name="alerts-and-autoscale"></a>警示和自動調整
應用程式或 App Service 方案的計量可以連接到警示，若要深入了解這點，請參閱[接收警示通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

基本、標準或進階 App Service 方案中裝載的 App Service 應用程式支援 **自動調整**。 這可讓您設定相關規則來監視 App Service 方案計量，而且可以增加或減少執行個體計數，並視需要提供其他資源，或在應用程式過度佈建時節省金錢。 您可以在此進一步了解自動調整︰[如何調整](../monitoring-and-diagnostics/insights-how-to-scale.md)以及這裡的[Azure 監視器自動調整的最佳做法](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

> [!NOTE]
> 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](https://azure.microsoft.com/try/app-service/)，即可在 App Service 中立即建立短期入門 Web 應用程式。 不需要信用卡；沒有承諾。
> 
> 

## <a name="whats-changed"></a>變更的項目
* 如需從網站變更為 App Service 的指南，請參閱： [Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png

