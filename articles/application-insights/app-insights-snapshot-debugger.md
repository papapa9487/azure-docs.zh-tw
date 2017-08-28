---
title: ".NET 應用程式的 Azure Application Insights 快照集偵錯工具 | Microsoft Docs"
description: "在生產環境 .NET 應用程式中擲回例外狀況時，會自動收集偵錯快照集"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: bb6c93557ea26bed721315dc82da917e4727b5f9
ms.contentlocale: zh-tw
ms.lasthandoff: 08/17/2017

---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET 應用程式中的例外狀況偵錯快照集

發生例外狀況時，您可以自動從即時 Web 應用程式收集偵錯快照集。 快照集會顯示擲回例外狀況時原始程式碼和變數的狀態。 [Application Insights](app-insights-overview.md) 中的快照集偵錯工具 (預覽) 會監視 web 應用程式的例外狀況遙測。 它會收集前幾個擲回例外狀況的快照集，讓您取得診斷生產環境中問題所需的資訊。 將[快照集收集器 NuGet 套件](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)納入您的應用程式，並選擇性地設定 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中的集合參數。 快照集會顯示在 Application Insights 入口網站中的[例外狀況](app-insights-asp-net-exceptions.md)。

您可以檢視入口網站中的偵錯快照集，以查看呼叫堆疊並檢查每個呼叫堆疊框架的變數。 若要使用原始程式碼取得更強大的偵錯體驗，可[下載 Visual Studio 的快照集偵錯工具擴充功能](https://aka.ms/snapshotdebugger)，以 Visual Studio 2017 Enterprise 開啟快照集。

快照集集合適用於：
* 執行 .NET Framework 4.5 或更新版本的 .NET Framework 和 ASP.NET 應用程式。
* 在 Windows 上執行的 .NET Core 2.0 和 ASP.NET Core 2.0 應用程式。

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>設定 ASP.NET 應用程式的快照集集合

1. 如果您尚未這麼做，請[在 Web 應用程式中啟用 Application Insights](app-insights-asp-net.md)。

2. 將 [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件納入您的應用程式。 

3. 檢閱套件已新增至 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 的預設選項：

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in one minute. -->
        <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. 快照集只會收集向 Application Insights 回報的例外狀況。 在某些情況下 (例如，舊版的 .NET 平台)，您可能需要[設定例外狀況集合](app-insights-asp-net-exceptions.md#exceptions)，才能在入口網站中查看快照集的例外狀況。


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>設定 ASP.NET Core 2.0 應用程式的快照集集合

1. 如果您尚未這麼做，請[在 ASP.NET Core Web 應用程式中啟用 Application Insights](app-insights-asp-net-core.md)。

2. 將 [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件納入您的應用程式。

3. 修改您應用程式的 `Startup` 類別中的`ConfigureServices` 方法，以新增快照集收集器的遙測處理器。 您應新增的程式碼取決於參考的 Microsoft.ApplicationInsights.ASPNETCore NuGet 套件版本。

   對於 Microsoft.ApplicationInsights.AspNetCore 2.1.0，新增：
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       // This method is called by the runtime. Use it to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddSingleton<Func<ITelemetryProcessor, ITelemetryProcessor>>(next => new SnapshotCollectorTelemetryProcessor(next));
           // TODO: Add any other services your application needs here.
       }
   }
   ```

   對於 Microsoft.ApplicationInsights.AspNetCore 2.1.1，新增：
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           public ITelemetryProcessor Create(ITelemetryProcessor next) =>
               new SnapshotCollectorTelemetryProcessor(next);
       }

       // This method is called by the runtime. Use it to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
            services.AddSingleton<ITelemetryProcessorFactory>(new SnapshotCollectorTelemetryProcessorFactory());
           // TODO: Add any other services your application needs here.
       }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>設定其他 .NET 應用程式的快照集集合

1. 如果您的應用程式尚未使用 Application Insights 檢測，請從[啟用 Application Insights 及設定檢測金鑰](app-insights-windows-desktop.md)著手。

2. 在您的應用程式中新增 [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件。

3. 快照集只會收集向 Application Insights 回報的例外狀況。 您可能需要修改程式碼才能回報例外狀況。 例外狀況處理程式碼取決於應用程式的結構，但有一個範例如下：
    ```C#
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```
    
## <a name="grant-permissions"></a>授與權限

Azure 訂用帳戶的擁有者可以檢查快照集。 其他使用者必須由擁有者授與權限。

若要授與權限，請指派 `Application Insights Snapshot Debugger` 角色給要檢查快照集的使用者。 這個角色可以由目標 Application Insights 資源或其資源群組或訂用帳戶的訂用帳戶擁有者，指派給個別使用者或群組。

1. 開啟 [存取控制] (IAM) 刀鋒視窗。
1. 按一下 [+新增] 按鈕。
1. 從 [角色] 下拉式清單中選取 Application Insights 快照集偵錯工具。
1. 搜尋並輸入要新增的使用者名稱。
1. 按一下 [儲存] 按鈕，將使用者新增至角色。


[!IMPORTANT]
    快照集可能會在變數和參數值中包含個人和其他機密資訊。

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Application Insights 入口網站中的偵錯快照集

如果快照集可用於指定的例外狀況或問題識別碼，則 Application Insights 入口網站中的[例外狀況](app-insights-asp-net-exceptions.md)會出現 [開啟偵錯快照集] 按鈕。

![例外狀況的 [開啟偵錯快照集] 按鈕](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

在 [偵錯快照集] 檢視中，您會看到呼叫堆疊和變數窗格。 當您在 [呼叫堆疊] 窗格中選取呼叫堆疊的框架時，您可以檢視 [變數] 窗格中的本機變數和該函式呼叫的參數。

![檢視入口網站中的偵錯快照集](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

快照集可能包含機密資訊，依預設為不可檢視。 若要檢視快照集，您必須有指派給您的 `Application Insights Snapshot Debugger` 角色。

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Visual Studio 2017 Enterprise 的偵錯快照集
1. 按一下 [下載快照集] 按鈕，下載可利用 Visual Studio 2017 Enterprise 開啟的 `.diagsession` 檔案。 

2. 若要開啟 `.diagsession` 檔案，您必須先[下載並安裝 Visual Studio 的快照集偵錯工具擴充功能](https://aka.ms/snapshotdebugger)。

3. 開啟快照集檔案之後，Visual Studio 中的 [小型傾印偵錯] 分頁隨即出現。 按一下 [偵錯 Managed 程式碼] 以開始偵錯快照集。 快照集會開啟至擲回例外狀況的程式碼行，您可將程序的目前狀態進行偵錯。

    ![檢視 Visual Studio 中的偵錯快照集](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

下載的快照集會包含您 Web 應用程式伺服器上找到的任何符號檔。 若要建立快照集資料與原始程式碼的關聯，就需要這些符號檔。 對於 App Service 應用程式，當您發佈 Web 應用程式時請務必啟用符號部署。

## <a name="how-snapshots-work"></a>快照集運作方式

當您的應用程式啟動時，會建立個別的快照集上傳者程序，可針對快照集要求監視應用程式。 要求快照集時，會在大約 10 到 20 毫秒內製作執行程序的陰影副本。 接著會將陰影程序進行分析，且在主要程序繼續執行並將流量提供給使用者時，會建立快照集。 接著，會將快照集與檢視快照集所需的任何相關符號 (.pdb) 檔案一起上傳至 Application Insights。

## <a name="current-limitations"></a>目前的限制

### <a name="publish-symbols"></a>發佈符號
快照集偵錯工具需要符號檔出現在生產環境伺服器上，才可將變數解碼並提供 Visual Studio 中的偵錯體驗。 15.2 版 Visual Studio 2017 發佈至 App Service 時，預設會發佈版本組建的符號。 在舊版中，您必須將下列這一行新增至發佈設定檔 `.pubxml` 檔案，才會在發行模式中將符號發佈：

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

針對 Azure Compute 和其他類型，請確定符號檔案與主要應用程式 .dll 位於相同資料夾 (通常為 `wwwroot/bin`)，或可在目前的路徑使用。

### <a name="optimized-builds"></a>最佳化的組建
在某些情況下，由於建置程序期間所套用的最佳化，使版本組建無法檢視本機變數。

## <a name="troubleshooting"></a>疑難排解

這些提示可協助您針對快照集偵錯工具的問題進行疑難排解。

### <a name="verify-the-instrumentation-key"></a>驗證檢測金鑰

確定您在已發佈的應用程式中使用正確的檢測金鑰。 通常，Application Insights 會從 ApplicationInsights.config 檔案讀取檢測金鑰。 確認此值與您在入口網站中看到之 Application Insights 資源的檢測金鑰相同。

### <a name="check-the-uploader-logs"></a>請檢查上傳程式記錄檔

建立快照集之後，磁碟上會建立小型傾印檔案 (.dmp)。 個別的上載程序會採用該小型傾印檔案，並將它 (以及任何相關聯的 PDB) 上傳至 Application Insights 快照集偵錯工具儲存體。 成功上傳小型傾印之後，它就會從磁碟中刪除。 小型傾印上傳程式的記錄檔會保留在磁碟上。 在 App Service 環境中，您可以在 `D:\Home\LogFiles\Uploader_*.log` 中找到這些記錄。 使用 App Service 的 Kudu 管理網站來尋找這些記錄檔。

1. 在 Azure 入口網站中開啟您的 App Service 應用程式。

2. 選取 [進階工具] 刀鋒視窗，或搜尋 [Kudu]。
3. 按一下 [執行]。
4. 在 [偵錯主控台] 下拉式清單方塊中，選取 [CMD]。
5. 按一下 [LogFiles]。

您應會看到至少有一個檔案的名稱開頭為 `Uploader_` 且副檔名為 `.log`。 按一下適當的圖示，以下載任何記錄檔，或在瀏覽器中開啟它們。
檔案名稱包含電腦名稱。 如果 App Service 執行個體裝載於一部以上的電腦，每部電腦會有個別的記錄檔。 當上傳程式偵測到新的小型傾印檔案時，該檔案會記錄在記錄檔中。 以下是成功上傳的範例︰

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

在上述範例中，檢測金鑰為 `c12a605e73c44346a984e00000000000`。 這個值應該符合您應用程式的檢測金鑰。
小型傾印會與識別碼為 `139e411a23934dc0b9ea08a626db16c5` 的快照集相關聯。 您稍後可以使用這個識別碼，在 Application Insights Analytics 中找出相關聯的例外狀況遙測。

上載程式約每隔 15 分鐘掃描一次新的 PDB。 以下是範例：

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

若為「未」裝載於 App Service 中的應用程式，上傳程式記錄位於與小型傾印相同的資料夾中：`%TEMP%\Dumps\<ikey>` (其中 `<ikey>` 是您的檢測金鑰)。

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>使用 Application Insights 搜尋來尋找快照集例外狀況的

建立快照集後，擲回中的例外狀況會以快照集識別碼標記。 向 Application Insights 回報例外狀況遙測後，快照集識別碼會納入為自訂屬性。 使用 Application Insights 中的 [搜尋] 刀鋒視窗，您可以找到具有 `ai.snapshot.id` 自訂屬性的所有遙測。

1. 在 Azure 入口網站中瀏覽至您的 Application Insights 資源。
2. 按一下 [搜尋] 。
3. 在 [搜尋] 文字方塊中輸入 `ai.snapshot.id`，然後按 Enter 鍵。

![在入口網站中使用快照集識別碼搜尋遙測](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

如果此搜尋未傳回任何結果，則不會針對所選時間範圍中您的應用程式向 Application Insights 回報任何快照集。

若要搜尋從上傳程式記錄檔中的特定快照集識別碼，請在 [搜尋] 方塊中輸入該識別碼。 如果您找不到已上傳快照集的遙測，請遵循下列步驟：

1. 請驗證檢測金鑰，仔細檢查您查看的是正確的 Application Insights 資源。

2. 使用上傳程式記錄中的時間戳記，調整搜尋的時間範圍篩選條件以涵蓋該時間範圍。

如果仍未看到具有該快照集識別碼的例外狀況，則未向 Application Insights 回報此例外狀況遙測。 如果您的應用程式在採用快照集之後，但回報例外狀況遙測之前損毀，可能會發生這種情況。 在此情況下，檢查 `Diagnose and solve problems` 之下的 App Service 記錄，查看是否發生非預期的重新啟動或未處理的例外狀況。

## <a name="next-steps"></a>後續步驟

* [在您的程式碼中設定 Snappoint](https://azure.microsoft.com/blog/snapshot-debugger-for-azure/) 以取得快照集，而不需等待例外狀況。
* [診斷 Web Apps 中的例外狀況](app-insights-asp-net-exceptions.md)說明如何讓 Application Insights 看見更多的例外狀況。 
* [智慧型偵測](app-insights-proactive-diagnostics.md)會自動探索效能異常。

