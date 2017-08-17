---
title: "管理 Azure Application Insights 的價格和資料量 | Microsoft Docs"
description: "在 Application Insights 中管理遙測量和監視成本。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 311cee724fc77256748153b5167d2a38ccba9775
ms.contentlocale: zh-tw
ms.lasthandoff: 06/09/2017

---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>管理 Application Insights 的價格和資料量


[Azure Application Insights][start] 的價格是根據每個應用程式的資料量而定。 開發期間或小型應用程式的低使用量可能是免費的，因為每個月有 1 GB 的遙測資料額度。

每項 Application Insights 資源的收費方式採個別服務計價，並計入您 Azure 訂用帳戶的帳單。

價格方案有兩個。 預設方案稱為「基本」。 您可以選擇「企業」方案，該方案有每日費用，但可使用某些特定功能，例如[連續匯出](app-insights-export-telemetry.md)。

如果您對 Application Insights 的定價有疑問，歡迎隨時在[論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights)中張貼問題。 

## <a name="the-price-plans"></a>價格方案

如需您的貨幣的目前價格，請參閱[Application Insights 價格頁面][pricing]。

### <a name="basic-plan"></a>基本方案

建立新的 Application Insights 資源時，基本方案是預設方案，應可滿足大部分客戶的需要。

* 基本方案是依照您的資料量收費︰Application Insights 接收的遙測位元組數。 資料量是測量 Application Insights 從應用程式收到的未壓縮 JSON 資料套件的大小。
對於[匯入分析的表格式資料](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-import)，資料磁碟區會測量為傳送至 Application Insights 之檔案的解壓縮大小。  
* 每個應用程式的第一個 1 GB 是免費的，因此如果只是實驗或開發，您可能不需要付費。
* [即時計量串流](app-insights-live-stream.md)資料不會計入價格用途。
* 在基本方案中，可以依照每一 GB 額外付費使用[連續匯出](app-insights-export-telemetry.md)。

### <a name="enterprise-plan"></a>企業方案

* 在企業方案中，您的應用程式可以使用 Application Insights 的所有功能。 [連續匯出](app-insights-export-telemetry.md)和 

[Log Analytics 連接器](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409)在企業方案中為免費，無需任何額外的費用。
* 在企業方案中，是依照傳送所有應用程式遙測的每一節點付費。 
 * 「節點」是裝載應用程式的實體或虛擬伺服器機器，或是平台即服務 (PaaS) 角色執行個體。
 * 開發用電腦、用戶端瀏覽器、行動裝置不算節點。
 * 如果您的應用程式有數個會傳送遙測的元件，例如 Web 服務和後端背景工作，會將它們分開計算。
 * [即時計量資料流](app-insights-live-stream.md)資料不會針對價格目的進行計算。* 橫跨訂用帳戶，您的費用是針對每個節點，而不是每個應用程式。 如果您有五個節點傳送 12 個應用程式的遙測，則是以五個節點計算收費。
* 雖然費用是按月報價，您的收費僅適用於一個節點從一個應用程式傳送遙測的任何小時。 每小時的費用是月費報價 / 744 (一個月 31 天的小時數)。
* 每個節點 (資料粒度為小時) 偵測每日 200 MB 的資料量配置。 未使用的資料配置不會帶到隔天。
 * 如果您選擇企業價格方案，每個訂用帳戶會根據傳送遙測資料至該訂用帳戶中 Application Insights 資源的節點數，得到每日資料額度。 因此，如果您有 5 個節點全天候傳送資料，您會有合起來 1GB 的資料額度，套用至該訂用帳戶中的所有 Application Insights 資源。 由於內含資料會在所有節點之間共用，因此特定節點所傳送的資料是否超過其他節點並無妨。 如果某一天，Application Insights 資源收到超過此訂用帳戶的每日資料配置，則會按 GB 收取資料超量費用。 
 * 每日資料額度的計算方式，是每個節點在當天傳送遙測資料的時數 (使用 UTC) 除以 24 再乘以 200 MB。 因此，如果您有 4 個節點在一天 24 個小時的 15 個小時內傳送遙測資料，則當天內含資料量會是 ((4 x 15) / 24) x 200 MB = 500 MB。 以資料超量每一 GB 的價格是 2.30 美元來說，如果節點那一天會傳送 1 GB 的資料，費用會是 1.15 美元。
 * 請注意，「企業」方案的每日額度不能與已選擇「基本」方案的應用程式共用，且未使用的額度也不能帶到隔天。 
* 以下是判斷不同節點計數的一些範例︰
| 案例                               | 每日節點總數 |
|:---------------------------------------|:----------------:|
| 1 個應用程式使用 3 個 Azure App Service 執行個體和 1 個虛擬伺服器 | 4 |
| 3 個應用程式在 2 個 VM 上執行，而這些應用程式的 Application Insights 資源位於相同的訂用帳戶中，且採用「企業」方案 | 2 | 
| 4 個應用程式，其 Application Insights 資源位於相同訂用帳戶中。 在 16 小時的離峰時間裡每個應用程式執行 2 個執行個體，在 8 小時的尖峰時間裡執行 4 個執行個體。 | 13.33 | 
| 雲端服務有 1 個背景工作角色和 1 個 Web 角色，各執行 2 個執行個體 | 4 | 
| 5 個節的 Service Fabric 叢集執行 50 個微服務，每個微服務執行 3 個執行個體 | 5|

* 節點的精確計算行為取決於您的應用程式使用哪個 Application Insights SDK。 
  * 若是 SDK 2.2 及之後的版本，Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 或 [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 會將每個應用程式主機當做節點報告，例如實體伺服器與 VM 主機的電腦名稱 (在雲端服務的案例中則是執行個體名稱)。  唯一的例外是只使用 [.NET Core](https://dotnet.github.io/) 和 Application Insights Core SDK 的應用程式，此情況下，所有主機只會報告一個節點，因為不知道主機名稱。 
  * 對於較早版本的 SDK，[Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 的行為就和較新版的 SDK 一樣，不過不論實際應用程式主機的數目是多少，[Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 都只會報告一個節點。 
  * 請注意，如果您的應用程式使用 SDK 將 roleInstance 設為某個自訂值，依預設會用相同的值來判斷節點數目。 
  * 如果您使用新的 SDK 版本，搭配從用戶端電腦或行動裝置執行的應用程式，則節點數目可能會傳回很大的數字 (來自大量的用戶端電腦或行動裝置)。 

### <a name="multi-step-web-tests"></a>多重步驟 Web 測試

[多步驟 Web 測試](app-insights-monitor-web-app-availability.md#multi-step-web-tests)會有一項額外的費用。 這是指執行連續動作的 Web 測試。 

單一頁面的 ping 測試沒有另外的費用。 來自 ping 測試和多步驟測試的遙測，會與來自您應用程式的其他遙測一起計費。
 
## <a name="operations-management-suite-subscription-entitlement"></a>Operations Management Suite 訂用帳戶權利

在[最近宣布](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)，購買 Microsoft Operations Management Suite E1 和 E2 的客戶可以取得 Application Insights Enterprise 作為額外的元件，不需其他成本。 具體來說，Operations Management Suite E1 和 E2 的每個單位包含 1 個 Application Insights Enterprise 方案節點的權利。 如先前所述，每個 Application Insights 節點包含每天最多 200 MB 的內嵌資料 (獨立於 Log Analytics 資料擷取)，具有 90 天資料保留期，不需其他費用。 

> [!NOTE]
> 若要確保您取得此權利，您的 Application Insights 資源必須在 Enterprise 定價方案中。 此權利僅適用作為節點，因此基本方案中的 Application Insights 資源不會帶來任何效益。 請注意，此權利將不會顯示在 [功能 + 定價] 刀鋒視窗上顯示的估計成本。 
>
 
## <a name="review-pricing-plans-and-estimate-costs"></a>檢閱定價方案和估計成本

Applicaition Insights 可讓您輕鬆地了解可用的定價方案，以及根據最近的使用模式可能的成本。 從開啟 Azure 入口網站之 Application Insights 資源中的 [功能 + 定價] 刀鋒視窗開始：

![選擇價格。](./media/app-insights-pricing/01-pricing.png)

**a.** 檢閱當月的資料量。 這包括從您的伺服器和用戶端應用程式，以及從可用性測試接收並保留的所有資料 (在任何[取樣](app-insights-sampling.md)之後)。

**b.** 進行[多步驟 Web 測試](app-insights-monitor-web-app-availability.md#multi-step-web-tests)需另外收費。 (這不包括簡單的可用性測試，其已包含在資料量費用中。)

**c.** 啟用企業方案。

**d.** 逐一點選資料管理選項，以檢視上個月的資料量，並設定每日上限或設定擷取取樣。

Application Insights 費用會加到您的 Azure 帳單中。 您可以在 Azure 入口網站中的 [帳務] 區段中，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看 Azure 帳單的詳細資料。 

![選擇側邊功能表中的 [帳務]。](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>資料速率
有三種方式可限制您傳送的資料量︰

* **取樣︰**這個機制可用來減少您的伺服器和用戶端應用程式所傳送的遙測量，計量的扭曲程度最小。 這是您調整資料量的主要工具。 深入了解[取樣功能](app-insights-sampling.md)。 
* **每日上限：**從 Azure 入口網站建立 Application Insights 資源時，會將此設為每日 500 GB。 從 Visual Studio 建立 Application Insights 資源時的預設值很小 (只有 32.3 MB/天)，僅旨在協助測試。 在此情況下，用意在於使用者在將應用程式部署至實際執行環境之前，會提高每日的上限。 除非您針對高流量的應用程式要求更高的最大值，否則每日的最高上限是 500 GB。 設定每日的上限時請留意，您的目標應該是**絕不達到每日上限**，因為這樣一來您將會遺失每日剩餘項目的資料，且無法監視您的應用程式。 若要變更，請從 [資料量管理] 刀鋒視窗連結來使用 [每日用量上限] 刀鋒視窗 (如下所示)。 請注意，有些訂用帳戶類型的信用額度無法用於 Application Insights。 如果訂用帳戶內含消費限制，則 [每日上限] 刀鋒視窗中會有指示，說明如何將它移除，並讓每日上限提高至每天 32.3 MB。  
* **節流：**這會將資料速率限制在每秒 32,000 個事件，平均超過 1 分鐘。 


如果應用程式超過節流速率會發生什麼事？

* 系統會每分鐘評估應用程式傳送的資料量。 如果每秒速率超過每分鐘平均值，伺服器會拒絕部分要求。 SDK 會緩衝處理資料，然後嘗試重新傳送，大量傳播幾分鐘。 如果應用程式始終以高於節流速率的方式傳送資料，有些資料會遭到捨棄  (ASP.NET、Java 和 JavaScript SDK 會嘗試以此方式重新傳送，其他 SDK 則可能直接捨棄節流的資料)。當節流發生時，您會看到警告您這種情況已發生的通知。

如何知道應用程式正在傳送多少資料？

* 開啟 [資料量管理] 刀鋒視窗，查看每日資料量的圖表。 
* 或在 [計量瀏覽器] 中，新增新的圖表，然後選取 [資料點數量] 做為其計量。 切換群組，並依 [資料類型] 分組。

## <a name="to-reduce-your-data-rate"></a>降低資料速率
以下是您可進行以減少資料量的一些事項︰

* 使用 [取樣](app-insights-sampling.md)。 這項技術可減少資料率而不會曲解您的計量，且不會中斷在 [搜尋] 中於相關項目之間瀏覽的能力。 在伺服器應用程式，它會自動運作。
* [限制可回報的 Ajax 呼叫次數](app-insights-javascript.md#detailed-configuration) (在每個頁面檢視中) 或關閉 Ajax 報告功能。
* 藉由 [編輯 ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)來關閉您不需要的集合模組。 例如，您可能會決定效能計數器或相依性資料是不必要的。
* 分割您的遙測，以區隔檢測金鑰。 
* 預先彙總度量。 如果您在應用程式中呼叫 TrackMetric，您可以使用接受批次測量之平均及標準差計算的多載來減少流量。 您也可以使用 [預先彙總套件](https://www.myget.org/gallery/applicationinsights-sdk-labs)。

## <a name="managing-the-maximum-daily-data-volume"></a>管理最大每日資料量

您可以使用每日用量上限來限制收集的資料量，但如果達到該上限，您便會失去當天接下來所有來自應用程式的遙測。 我們「不建議」讓您的應用程式達到每日上限，因為在達到該上限後，您將無法追蹤應用程式的健康狀態和效能。 

請改為使用[取樣](app-insights-sampling.md)來將資料量調整到您所需的程度，並只將每日上限做為「最後的手段」，以防您的應用程式開始未預期地傳送大量遙測。 

若要變更每日上限，請在您 Application Insights 的 [設定] 區段中依序按一下 [資料量管理]、[每日上限]。

![調整每日遙測資料量上限](./media/app-insights-pricing/daily-cap.png) 

## <a name="sampling"></a>取樣
[取樣](app-insights-sampling.md) 可以降低將遙測傳送到您 App 的速率，同時仍保留在診斷搜尋時尋找相關事件的功能，並保有正確的事件計數。 

取樣可有效減少費用並維持在每月配額內。 取樣演算法會保留遙測的相關項目，例如：使用「搜尋」時，可以找到與特定例外狀況相關的要求。 此演算法也會保留正確的計數，方便您在計量瀏覽器中查看正確的要求率、例外狀況率，以及其他計數等數值。

取樣有數種形式。

* [調適性取樣](app-insights-sampling.md) 是 ASP.NET SDK 的預設值，它能自動針對應用程式所傳送的遙測量進行調整。 這會自動在 Web 應用程式中的 SDK 運作，所以能降低網路上的遙測流量。 
*  是替代方法，它會在遙測從應用程式進入 Application Insights 服務時運作。 它並不會影響從應用程式傳送的遙測大小，但可減少服務保留的大小。 您可以用它來減少瀏覽器和其他 SDK 遙測所用的配額。

若要設定擷取取樣，請在 [價格] 刀鋒視窗中設定控制項：

![在 [配額和價格] 刀鋒視窗中，按一下 [範例] 磚，然後選取取樣分數。](./media/app-insights-pricing/04.png)

> [!WARNING]
> [資料取樣] 刀鋒視窗只會控制擷取取樣的值。 它並不會反映應用程式中的 Application Insights SDK 所套用的取樣率。 如果已在 SDK 進行連入遙測的取樣，則不適用擷取取樣。
> 

若要找出實際的取樣率 (不論是否已套用)，請使用如下所示的 [分析查詢](app-insights-analytics.md) ︰

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

在每筆保留的記錄中， `itemCount` 表示它所代表的原始記錄筆數，其等於 1 + 先前捨棄的記錄筆數。 


## <a name="automation"></a>自動化

您可以使用 Azure 資源管理撰寫指令碼，以設定價格方案。 [了解作法](app-insights-powershell.md#price)。

## <a name="limits-summary"></a>限制摘要
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>後續步驟

* [取樣](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/


