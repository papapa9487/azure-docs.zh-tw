---
title: "Azure Application Insights 快照集偵錯工具疑難排解指南 | Microsoft Docs"
description: "關於 Azure Application Insights 快照集偵錯工具的常見問題集。"
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 
ms.service: application-insights
ms.workload: 
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 5d6a2fe4c3ee373172f5324b6c7a39e4f94f4652
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>快照集偵錯工具：疑難排解指南

Application Insights 快照集偵錯工具可讓您從即時 Web 應用程式中自動收集偵錯快照集。 快照集會顯示擲回例外狀況時原始程式碼和變數的狀態。 若是讓 Application Insights 快照集偵錯工具正常運作有困難，本文將逐步示範偵錯工具如何運作，並提供常見疑難排解情況的解決方案。 

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Application Insights 快照集偵錯工具如何運作

Application Insights 快照集偵錯工具是 Application Insights 遙測管道 (ITelemetryProcessor 的執行個體) 的一部分，快照集收集器會監視程式碼中擲回的例外狀況 (AppDomain.FirstChanceException) 以及 Application Insights 例外狀況遙測管道所追蹤的例外狀況。 一旦您成功將快照集收集器加入專案中，且其偵測到 Application Insights 遙測管道中有一個例外狀況，便會傳送在自訂資料中名為「AppInsightsSnapshotCollectorLogs」和「SnapshotCollectorEnabled」的 Application Insights 自訂事件。 同時，會啟動名為「MinidumpUploader.exe」的程序，將所收集的快照集料檔案上傳至 Application Insights。  「MinidumpUploader.exe」程序啟動時，將會傳送名為「UploaderStart」的自訂事件。 在先前的步驟後，快照集收集器便會開始其正常的監視行為。

快照集收集器監視 Application Insights 例外狀況遙測資料時，會使用設定中定義的參數 (例如ThresholdForSnapshotting、MaximumSnapshotsRequired、MaximumCollectionPlanSize、ProblemCounterResetInterval) 來判斷何時要收集快照。 符合所有規則時，收集器就會要求下一個擲回相同位置的例外狀況快照。 同時，將會傳送自訂資料中名為「AppInsightsSnapshotCollectorLogs」和「RequestSnapshots」的自訂事件。 由於編譯器會將「版本」程式碼最佳化，因此在收集的快照集中可能無法顯示區域變數。 要求快照集時，快照集收集器會嘗試針對擲回例外狀況的方法取消最佳化。 在此期間，將會傳送在自訂資料中名為「AppInsightsSnapshotCollectorLogs」和「ProductionBreakpointsDeOptimizeMethod」的 Application Insights 自訂事件。  收集了下一個例外狀況的快照集後，將可使用區域變數。 收集了快照集後，會將程式碼重新最佳化以確保效能。 

> [!NOTE]
> 取消最佳化需要安裝 Application Insights 網站延伸模組。

要求特定例外狀況的快照集時，快照集收集器會開始監視應用程式的例外狀況處理管道 (AppDomain.FirstChanceException)。 當例外狀況再次發生時，快照集收集器會啟動快照 (自訂資料中名為「AppInsightsSnapshotCollectorLogs」和「SnapshotStart」的 Application Insights 自訂事件)。 接著就會製作執行中程序的陰影複製 (將會複製分頁表)。 這通常需要 10 到 20 毫秒。 在此之後，將會傳送自訂資料中名為「AppInsightsSnapshotCollectorLogs」和「SnapshotStop」的 Application Insights 自訂事件。 建立分支的程序時，總分頁記憶體增加的量與執行中應用程式的分頁記憶體量相同 (工作集則小得多)。 您的應用程式處理序正常執行時，陰影複製的處理序記憶體會傾印到磁碟並上傳到 Application Insights。 上傳快照集之後，將會傳送名為「UploadSnapshotFinish」的 Application Insights 自訂事件。

## <a name="is-the-snapshot-collector-working-properly"></a>快照集收集器是否正常運作？

### <a name="how-to-find-snapshot-collector-logs"></a>如何找到快照集收集器記錄檔
若已安裝[快照集收集器 NuGet 套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) 1.1.0 版或更新版本，則快照集收集器記錄檔會傳送到您的 Application Insight 帳戶。 請確定 *ProvideAnonymousTelemetry* 未設定為 False (此值預設為 True)。

* 在 Azure 入口網站中瀏覽到您的 Application Insights 資源。
* 在 [概觀] 區段中按一下 [搜尋]。
* 在搜尋方塊中輸入下列字串：
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* 注意︰若有需要，請變更 [時間範圍]。

![搜尋快照集收集器記錄檔的螢幕擷取畫面](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>檢查快照集收集器記錄檔
搜尋快照集收集器記錄檔時，目標時間範圍內應該會有「UploadSnapshotFinish」事件。 若仍未看到用以開啟快照集的 [開啟偵錯快照集] 按鈕，請傳送含有 Application Insights 檢測金鑰的電子郵件給 snapshothelp@microsoft.com。

![檢查快照集收集器記錄檔的螢幕擷取畫面](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>我找不到要開啟的快照集
若下列步驟無法協助您解決問題，請傳送含有 Application Insights 檢測金鑰的電子郵件給 snapshothelp@microsoft.com。

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>步驟 1：確定您的應用程式有傳送遙測資料和例外狀況資料至 Application Insights
瀏覽到 Application Insights 資源，檢查是否有資料從您的應用程式傳送過來。

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>步驟 2：確定快照集收集器已正確新增到應用程式的 Application Insights 遙測管道中
若您可以在「如何找到快照集收集器記錄檔」步驟中找到記錄檔，代表快照集收集器已正確加入您的專案中，您便可以忽略這個步驟。

若沒有任何快照集收集器記錄檔，請確認下列各項目：
* 針對傳統 ASP.NET 應用程式，請檢查 *ApplicationInsights.config* 檔案中的此資料行 *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">*。

* 針對 ASP.NET Core 應用程式，請確定具有 *SnapshotCollectorTelemetryProcessor* 的 *ITelemetryProcessorFactory* 已新增到 *IServiceCollection* 服務中。

* 也請檢查您是否在已發佈的應用程式中使用正確的檢測金鑰。

* 快照集收集器在同一個應用程式中不支援多個檢測金鑰，而會將快照集傳送至所觀察到第一個例外狀況的檢測金鑰。

* 若您在程式碼中以手動方式設定了 *InstrmentationKey*，請從 *ApplicationInsights.config* 更新 *InstrumentationKey* 元素。

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>步驟 3：確定已啟動小型傾印上傳程式
在快照集收集器記錄檔中搜尋 *UploaderStart* (在搜尋方塊中輸入 UploaderStart)。 應該會有快照集收集器監視到第一個例外狀況時的事件。 若沒有此事件，請檢查其他記錄檔以取得詳細資料。 解決此問題其中一種可能的方式，是重新啟動您的應用程式。

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>步驟 4：確定快照集收集器已表示其收集快照的意圖
在快照集收集器記錄檔中搜尋 *RequestSnapshots* (在搜尋方塊中輸入 ```RequestSnapshots```)。  若沒有任何快照，請檢查您的設定，例如 *ThresholdForSnapshotting* 表示開始收集快照集之前，發生特定例外狀況的數目上限。

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>步驟 5：確定快照集未因記憶體保護而停用
為了確保您的應用程式效能，只會在記憶體緩衝區夠大時才會擷取快照集。 在快照集收集器記錄檔中搜尋「CannotSnapshotDueToMemoryUsage」。 在事件的自訂資料中，會有詳細的原因。 若您的應用程式是在 Azure Web 應用程式中執行，可能會受到嚴格限制。 因為符合某些記憶體規則時，Azure Web 應用程式會重新啟動您的應用程式。 您可以嘗試將服務方案相應增加為具有更多記憶體的機器，來解決此問題。

### <a name="step-6-make-sure-snapshots-were-captured"></a>步驟 6：確定已擷取快照集
在快照集收集器記錄檔中搜尋 ```RequestSnapshots```。  若沒有任何快照，請檢查您的設定，例如 ```ThresholdForSnapshotting``` 表示開始收集快照集之前，發生特定例外狀況的數目上限。

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>步驟 7：確定已正確上傳快照集
在快照集收集器記錄檔中搜尋 ```UploadSnapshotFinish```。  若沒有此事件，請傳送含有 Application Insights 檢測金鑰的電子郵件給 snapshothelp@microsoft.com。 若此事件存在，請開啟其中一個記錄檔，並複製自訂資料中的「SnapshotId」值。 然後搜尋該值。 這會找到與快照集對應的例外狀況。 然後按一下例外狀況並開啟偵錯快照集。 (若沒有對應的例外狀況，可能是因為高容量而取樣了例外狀況遙測資料，請嘗試另一個 snapshotId。)

![尋找 SnapshotId 的螢幕擷取畫面](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![開啟例外狀況的螢幕擷取畫面](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![開啟偵錯快照集的螢幕擷取畫面](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>快照集檢視區域變數不完整

遺漏了某些區域變數。 若您的應用程式正在執行版本程式碼，編譯器會對某些變數進行最佳化，使其消失。 例如：

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

如果您的情況有所不同，可能是 Bug。 請傳送含有 Application Insights 檢測金鑰的電子郵件給 snapshothelp@microsoft.com，並附上程式碼片段。

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>快照集檢視：無法取得區域變數或引數的值
請確定已安裝 [Application Insights 網站延伸模組](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/)。 若問題持續發生，請傳送含有 Application Insights 檢測金鑰的電子郵件給 snapshothelp@microsoft.com。
