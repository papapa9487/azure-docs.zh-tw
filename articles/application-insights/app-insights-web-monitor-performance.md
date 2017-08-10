---
title: "使用 Application Insights 來監視應用程式的健康情況和流量"
description: "開始使用 Application Insights。 分析內部部署或 Microsoft Azure 應用程式的使用情況、可用性和效能。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/25/2015
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 69ead621c179bf49f17ed3274be4b625fc587556
ms.contentlocale: zh-tw
ms.lasthandoff: 07/11/2017

---
# <a name="monitor-performance-in-web-applications"></a>監視 Web 應用程式的效能


確認應用程式的運作狀況良好，以及迅速找出任何失敗。 [Application Insights][start] 能指出任何效能問題和例外狀況，以及協助您尋找及診斷根本原因。

Application Insights 可以監視 Java 和 ASP.NET Web 應用程式與服務、WCF 服務。 這些服務可以裝載在內部部署、虛擬機器上，或作為 Microsoft Azure 網站。 

用戶端方面，Application Insights 可從網頁及各種裝置 (包括 iOS、Android 和 Windows 市集應用程式) 上取得遙測。

>[!Note]
> 我們讓尋找您的 Web 應用程式中緩慢執行分頁有新的體驗。 如果您沒有它的存取權，藉由使用[預覽刀鋒視窗](app-insights-previews.md)以設定預覽選項來啟用它。 在[使用互動式效能調查尋找及修正效能瓶頸](#Find-and-fix-performance-bottlenecks-with-an-interactive-Performance-investigation)中深入了解這個新體驗。

## <a name="setup"></a>設定效能監視
如果您尚未將 Application Insights 新增至專案中 (亦即專案沒有 ApplicationInsights.config)，請選擇以下任一種方法來開始使用：

* [ASP.NET Web 應用程式](app-insights-asp-net.md)
  * [加入例外狀況監視](app-insights-asp-net-exceptions.md)
  * [加入相依性監視](app-insights-monitor-performance-live-website-now.md)
* [J2EE Web 應用程式](app-insights-java-get-started.md)
  * [加入相依性監視](app-insights-java-agent.md)

## <a name="view"></a>探索效能度量
在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您為應用程式設定的 Application Insights 資源。 概觀刀鋒視窗會顯示基本的效能資料：

按一下任一圖表可查看詳細資料，也能查看較長期的結果。 例如，按一下 [要求] 磚，然後選取時間範圍：

![按一下詳細資料的方式來選取時間範圍](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

按一下圖表以選擇它要顯示的度量，或是加入新圖表並選取其度量：

![按一下圖形以選擇度量](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **取消核取所有度量** 可查看所有可供選擇的項目。 度量分為多個群組；當您選取群組的任何成員時，只有該群組的其他成員會出現。

## <a name="metrics"></a>這具有哪些意義？ 效能磚和報告
您可以取用多種效能計量。 我們從預設顯示在應用程式分頁中的度量開始討論。

### <a name="requests"></a>要求
在指定期間內接收到的 HTTP 要求數量。 將此度量與其他報告中的結果比較，可了解應用程式在各種負載下的行為。

HTTP 要求包括分頁、資料及映像的所有 GET 或 POST 要求。

按一下磚可取得特定 URL 的計數。

### <a name="average-response-time"></a>平均回應時間
測量從 Web 要求進入應用程式到傳回回應之間的時間。

資料點會指出移動平均值。 在有許多要求的情況下，可能會有一些要求偏離平均值，但在圖表中沒有顯著的高低起伏。

找尋異常的高峰。 一般來說，當要求增加時，回應時間也會上升。 如果出現不相稱的上升跡象，表示應用程式可能遭遇到資源限制 (例如，應用程式使用之服務的 CPU 或容量)。

按一下圖格可取得特定 URL 的時間。

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>最慢的要求
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

顯示可能需要進行效能調整的要求。

### <a name="failed-requests"></a>失敗的要求
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

丟出無法攔截之例外狀況的要求計數。

按一下磚可查看特定失敗的詳細資料；選取個別要求可查看要求的詳細資料。 

系統只會針對各項檢查保留一個具代表性的失敗範例。

### <a name="other-metrics"></a>其他度量
若要查看其他可顯示的度量，請按一下圖表，然後取消選取所有度量以查看可供使用的完整集合。 按一下 (i) 可查看各項度量的定義。

![取消選取所有度量以查看完整的集合](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

選取任何計量將會停用其他計量，使其無法顯示在同一個圖表中。

## <a name="set-alerts"></a>設定警示
若要在任何度量有不尋常的值時收到電子郵件通知，請加入警示。 您可以選擇將電子郵件傳送給帳戶管理員，或傳送給特定的電子郵件地址。

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

設定其他屬性之前的資源。 如果您想要設定效能或使用度量的相關警示，請勿選擇 webtest 資源。

請小心注意系統要求您輸入臨界值時所使用的單位。

*I don't see the Add Alert button.* 我沒有看到 [新增警示] 按鈕 - 您只擁有此群組帳戶的唯讀權限嗎？ 請洽詢帳戶管理員。

## <a name="diagnosis"></a>診斷問題
以下是幾個尋找及診斷效能問題的訣竅：

* 設定 [Web 測試][availability]，以在網站故障、回應異常或過於緩慢時收到警示。 
* 比較要求計數與其他度量，了解失敗或回應過慢的情況是否與負載有關。
* 在程式碼中[插入及搜尋追蹤陳述式][diagnostic]有助於找出問題所在。
* 使用[即時計量資料流][livestream]監視作業中的 Web 應用程式。
* 使用[快照集偵錯工具][snapshot]擷取 .Net 應用程式的狀態。

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-performance-investigation"></a>使用互動式效能調查尋找及修正效能瓶頸

您可以使用新的 Application Insights 互動式效能調查，以找出減緩整體效能的 Web 應用程式區域。 您可以快速地找出減緩速度的特定分頁，並且使用[分析工具](app-insights-profiler.md)以查看這些分頁之間是否有相互關聯。

### <a name="create-a-list-of-slow-performing-pages"></a>建立一份執行緩慢分頁的清單 

找出效能問題的第一個步驟是取得緩慢回應分頁的清單。 下列螢幕擷取畫面示範如何使用 [效能] 刀鋒視窗取得一份要進一步調查的潛在分頁清單。 您可以從這個分頁快速地查看應用程式的回應時間在大約下午 6:00 時以及再次於大約下午 10 點時減緩。 您也可以看到 GET 客戶/詳細資料作業有一些長時間執行的作業，回應時間中間值為 507.05 毫秒。 

![Application Insights 互動式效能](./media/app-insights-web-monitor-performance/performance1.png)

### <a name="drill-down-on-specific-pages"></a>向下鑽研特定分頁

您有了應用程式效能的快照集之後，您可以取得更多特定執行緩慢作業的詳細資料。 按一下清單中的任何作業以查看詳細資料，如下所示。 您可以從圖表中查看效能是否以相依性為基礎。 您也可以查看有多少使用者遇到各種不同的回應時間。 

![Application Insights 作業刀鋒視窗](./media/app-insights-web-monitor-performance/performance5.png)

### <a name="drill-down-on-a-specific-time-period"></a>在特定時間週期向下鑽研

您識別要調查的時間點之後，更進一步向下鑽研以查看可能導致效能減緩的特定作業。 當您按一下特定時間點時，會取得分頁的詳細資料，如下所示。 在以下範例中，您可以看到針對指定時間週期列出的作業，以及伺服器回應碼和作業持續時間。 如果您需要將此資訊傳送給開發小組，您也有開啟 TFS 工作項目的 URL。

![Application Insights 時間配量](./media/app-insights-web-monitor-performance/performance2.png)

### <a name="drill-down-on-a-specific-operation"></a>在特定作業向下鑽研

您識別要調查的時間點之後，更進一步向下鑽研以查看可能導致效能減緩的特定作業。 從清單按一下作業，以查看作業的詳細資料，如下所示。 在此範例中，您可以看到作業失敗，且 Application Insights 提供應用程式擲回之例外狀況的詳細資料。 同樣地，您可以從這個刀鋒視窗輕鬆地建立 TFS 工作項目。

![Application Insights 作業刀鋒視窗](./media/app-insights-web-monitor-performance/performance3.png)


## <a name="next"></a>接續步驟
[Web 測試][availability] - 定期從全世界傳送 Web 要求給應用程式。

[擷取及搜尋診斷追蹤][diagnostic] - 插入追蹤呼叫並詳查結果，以便找出問題所在。

[流量追蹤][usage] - 了解使用者使用應用程式的情況。

[疑難排解][qna] - 以及問答集



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md




