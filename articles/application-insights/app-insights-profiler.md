---
title: "使用 Application Insights 來分析 Azure 上的即時 Web 應用程式 | Microsoft Docs"
description: "使用低資源使用量的分析工具來找出 Web 伺服器程式碼中的最忙碌路徑。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: ddfed2be315ae261e9c3015aa21d0b44405d6109
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="profiling-live-azure-web-apps-with-application-insights"></a>使用 Application Insights 來分析即時 Azure Web Apps

Application Insights 的這項功能在應用程式服務為 GA，在 Compute 為預覽中。

使用 [Azure Application Insights](app-insights-overview.md) 的分析工具來了解即時 Web 應用程式中的每個方法各使用了多少時間。 此工具會顯示應用程式所提供之即時要求的詳細設定檔，並醒目提示使用最多時間的「最忙碌路徑」。 它會自動選取具有不同回應時間的範例。 分析工具會使用各種技術來盡量減少系統負荷。

分析工具目前適用於在 Azure App Services (至少必須是基本定價層) 上執行的 ASP.NET Web 應用程式 

<a id="installation"></a>
## <a name="enable-the-profiler"></a>啟用分析工具

在程式碼中[安裝 Application Insights](app-insights-asp-net.md)。 如果已安裝，請確定您擁有的是最新版本  (若要進行安裝，請在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [管理 NuGet 套件]。 選取 [更新] 並更新所有套件)。重新部署應用程式。

您使用的是 ASP.NET Core 嗎？[請查看這裡](#aspnetcore)。

在 [https://portal.azure.com](https://portal.azure.com) 中，為您的 Web 應用程式開啟 Application Insights 資源。 開啟 [效能] 然後按一下 [啟用 Application Insights 分析工具...]。

![按一下 [啟用程式碼分析工具] 橫幅][enable-profiler-banner]

或者，您隨時可以按一下 [設定] 來檢視狀態、啟用或停用分析工具。

![在 [效能] 刀鋒視窗中，按一下 [設定]][performance-blade]

使用 Application Insights 設定的 Web Apps 會列在 [設定] 刀鋒視窗。 請視需要依照指示來安裝分析工具代理程式。 如果尚未使用 Application Insights 設定任何 Web 應用程式，請按一下 [新增連結的應用程式]。

使用 [設定] 刀鋒視窗中的 [啟用分析工具] 或 [停用分析工具] 按鈕來控制所有連結的 Web Apps 之分析工具。



![設定刀鋒視窗][linked app services]

## <a name="disable-the-profiler"></a>停用分析工具
若要停止或重新啟動個別 App Service 執行個體的分析工具，您可以在 [App Service 資源] 的 [Web 作業] 中找到它。 若要刪除它，請在 [擴充功能] 底下查看。

![停用 web 作業的分析工具][disable-profiler-webjob]

我們建議您將 Web Apps 上的分析工具啟用，儘速找出任何的效能問題。

如果您使用 WebDeploy 來部署 Web 應用程式的變更，請確定您已排除 **App_Data** 資料夾，以免系統在部署期間刪除它。 否則，當您下次將 Web 應用程式部署至 Azure 時，系統就會刪除分析工具擴充功能的檔案。

### <a name="using-profiler-with-azure-vms-and-compute-resources-preview"></a>使用分析工具搭配 Azure VM 和 Compute 資源 (預覽)

當您[在執行階段啟用 Azure App Service 的 Application Insights](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights) 時，分析工具會自動提供使用。 (如果已啟用資源的 Application Insights，您可能需要透過 [設定] 精靈更新為最新版本。)

現提供 [Azure Compute 資源的分析工具預覽版本](https://go.microsoft.com/fwlink/?linkid=848155)。


## <a name="limitations"></a>限制

預設資料保留期為 5 天。 每日擷取最多 10 GB。

分析工具服務不會收取費用。 Web 應用程式必須至少在應用程式服務的基本層中託管。

## <a name="overhead-and-sampling-algorithm"></a>額外負荷和取樣演算法

Profiler 會在裝載已啟用分析工具之應用程式的每個虛擬機器上每小時隨機執行 2 分鐘，以擷取追蹤。 當 Profiler 執行時，它會增加 5-15% 的 CPU 額外負荷至伺服器。
可用於裝載應用程式的伺服器越多，Profiler 對整體應用程式效能的影響越小。 這是因為取樣演算法導致 Profiler 在任何給定時間只在 5% 的伺服上執行，並且有更多的伺服器可用於 Web 要求以抵消具有 Profiler 額外負荷的伺服器。


## <a name="viewing-profiler-data"></a>檢視分析工具的資料

開啟 [效能] 刀鋒視窗，並向下捲動至作業清單。




![Application Insights 的 [效能] 刀鋒視窗 [範例] 資料行][performance-blade-examples]

資料表中有下列資料行：

* **計數** - 這些要求在刀鋒視窗之時間範圍內的數目。
* **中位數** - 應用程式回應要求時一般會使用的時間長度。 在所有的回應中，有一半會快過此時間。
* **第 95 個百分位數** - 95% 的回應會快過此時間。 如果此數字和中位數差異極大，則表示應用程式可能有間歇性問題  (或者，可能的解釋為有某者設計功能導致此結果，例如快取)。
* **分析工具追蹤** - 一個圖示，用來指出分析工具已擷取這項作業的堆疊追蹤。

按一下 [檢視] 按鈕以開啟追蹤總管。 此總管會顯示分析工具已擷取的數個範例，並依回應時間來分類。

如果您使用 [預覽效能] 刀鋒視窗，請移至右下角的 [採取的動作] 區段來要檢視分析工具追蹤。 按一下 [分析工具追蹤] 按鈕。

![Application Insights [效能] 刀鋒視窗預覽分析工具追蹤][performance-blade-v2-examples]

選取某個範例就會顯示要求在執行時所用時間的程式碼層級細目。

![Application Insights 追蹤總管][trace-explorer]

[顯示最忙碌路徑] 會開啟最大的分葉節點，或者，至少也會是接近最大的節點。 在大部分情況下，這個節點會很接近效能瓶頸。



* **標籤**︰函式或事件的名稱。 樹狀結構會混合顯示程式碼和發生的事件 (例如 SQL 和 http 事件)。 最上層事件代表要求整體的持續時間。
* **經過**︰作業開始和結束之間的時間間隔。
* **何時**︰顯示函式/事件相對於其他函式的執行時間。

## <a name="how-to-read-performance-data"></a>如何讀取效能資料

Microsoft 服務分析工具會合併使用取樣方法和檢測功能來分析應用程式的效能。
當系統正在進行詳細的收集作業時，服務分析工具會對每個機器之 CPU 的指令指標進行取樣，頻率為每毫秒一次。
每此取樣都會擷取目前執行之執行緒的完整呼叫堆疊，以便獲得詳細而實用的資訊，來了解該執行緒在高低兩個抽象層執行了哪些作業。 服務分析工具也會收集其他事件 (例如內容切換的事件，TPL 事件，以及執行緒集區事件) 來追蹤活動的關聯性和原因。

時間表檢視中所顯示的呼叫堆疊是上述取樣和檢測的結果。 每個範例都會擷取執行緒的完整呼叫堆疊，因此範例中會包含 .NET Framework 的程式碼，以及您參考的其他架構。

### <a id="jitnewobj"></a>物件配置 (`clr!JIT\_New or clr!JIT\_Newarr1`)
`clr!JIT\_New and clr!JIT\_Newarr1` 是 .NET Framework 內的 Helper 函式，可從 Managed 堆積來配置記憶體。 系統在配置物件時會叫用 `clr!JIT\_New`。 系統在配置物件陣列時則會叫用 `clr!JIT\_Newarr1`。 這兩個函式的速度通常很快，應該不會花上多少時間。 如果您在時間表中看到 `clr!JIT\_New` 或 `clr!JIT\_Newarr1` 花了很久的時間，就表示程式碼可能配置了許多物件，並耗用了大量記憶體。

### <a id="theprestub"></a>載入程式碼 (`clr!ThePreStub`)
`clr!ThePreStub` 是 .NET Framework 內的 Helper 函式，可讓程式碼準備好進行第一次的執行。 這通常包括但不限於 JIT (Just In Time) 編譯。 在程序的存留期內，每個 C# 方法最多只應該會叫用 `clr!ThePreStub` 一次。

如果您看到 `clr!ThePreStub` 花了很久的時間來處理要求，則表示該要求是第一個執行該方法的要求，因此 .NET Framework 執行階段要花很多時間來載入該方法。 您可以考慮使用準備程序來執行程式碼的這個部分，再讓使用者存取該程式碼，或考慮在您的組件上執行 NGen。

### <a id="lockcontention"></a>鎖定爭用 (`clr!JITutil\_MonContention` 或 `clr!JITutil\_MonEnterWorker`)
`clr!JITutil\_MonContention` 或 `clr!JITutil\_MonEnterWorker` 表示目前的執行緒正在等待系統釋放鎖定。 系統在執行 C# 鎖定陳述式、叫用 Monitor.Enter 方法或以 MethodImplOptions.Synchronized 屬性叫用方法時，常會出現此情形。 當執行緒 A 取得鎖定但還未將其釋放之前，執行緒 B 就嘗試取得相同鎖定，系統一般就會發生鎖定爭用現象。

### <a id="ngencold"></a>載入程式碼 (`[COLD]`)
如果方法名稱包含 `[COLD]` (例如 `mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined`)，這表示 .NET Framework 執行階段所執行的程式碼尚未由<a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">特性指引最佳化</a>進行首次最佳化。 每個方法最多只應該會在程序的存留期內顯示一次這個名稱。

如果某個要求在載入程式碼時花了很久的時間，就表示該要求是第一個在方法中執行這未最佳化部分的要求。 您可以考慮使用準備程序來執行程式碼的這個部分，再讓使用者存取該程式碼。

### <a id="httpclientsend"></a>傳送 HTTP 要求
`HttpClient.Send` 之類的方法表示程式碼正在等候 HTTP 要求完成。

### <a id="sqlcommand"></a>資料庫作業
SqlCommand.Execute 之類的方法表示程式碼正在等候系統資料庫作業完成。

### <a id="await"></a>等候 (`AWAIT\_TIME`)
`AWAIT\_TIME` 表示程式碼正在等候另一個工作完成。 這通常會發生在 C# 'await' 陳述式。 當程式碼執行 C# 'await' 時，執行緒會回溯控制權並將控制權交還給執行緒集區，而且系統不會將任何執行緒封鎖起來以等候 'await' 完成。 不過，就邏輯上來說，系統其實會將執行 await 的執行緒「封鎖」以等候作業完成。 `AWAIT\_TIME` 表示為了等候工作完成而封鎖的時間。

### <a id="block"></a> 封鎖的時間
`BLOCKED_TIME` 表示程式碼在等候另一項資源變成可用狀態，例如，等候同步處理物件、等候執行緒變成可用狀態，或等候要求完成。

### <a id="cpu"></a>CPU 時間
CPU 正忙於執行指令。

### <a id="disk"></a>磁碟時間
應用程式正在執行磁碟作業。

### <a id="network"></a>網路時間
應用程式正在執行網路作業。

### <a id="when"></a>何時資料行
這個視覺化資料行會顯示針對節點所收集的 INCLUSIVE 樣本如何隨著時間變化。 要求的整個範圍會分成 32 個時間值區，而該節點的 INCLUSIVE 樣本則會累積到這 32 個值區。 接著，每個值區會以長條來表示，而長條的高度則代表換算後的值。 若節點標記為 `CPU_TIME` 或 `BLOCKED_TIME`，或是明顯與耗用資源 (CPU、磁碟、執行緒) 有關，長條就表示節點在該值區的一段時間內耗用了其中一項資源。 若您耗用多項資源，這些計量會大於 100%。 例如，通常來說，如果您在某個期間內使用兩個 CPU，計量會是 200%。


## <a id="troubleshooting"></a>疑難排解

### <a name="too-many-active-profiling-sessions"></a>太多個使用中分析工作階段

目前，您最多可以在執行於相同服務方案的 4 個 Azure Web 應用程式與部署位置上啟用分析工具。 如果您看到分析工具 Web 作業報告太多使用中分析工作階段，您需要將某些 Web 應用程式移至不同的服務方案。

### <a name="how-can-i-know-whether-application-insights-profiler-is-running"></a>如何知道 Application Insights 分析工具是否有執行？

在 Web 應用程式中，分析工具會以連續性 Web 作業的形式來執行。 您可以在 https://portal.azure.com 中開啟 Web 應用程式資源，然後檢查 [Webjob] 刀鋒視窗中的「ApplicationInsightsProfiler」狀態。 如果它沒有執行，請開啟 [記錄] 以進一步了解。

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>為什麼分析工具明明已在執行，我卻找不到任何堆疊範例呢？

您可以檢查以下幾點。

1. 確定 Web App Service 方案服務方案至少是基本層。
2. 確定 Web 應用程式至少已啟用 Application Insights SDK 2.2 Beta 版。
3. 確定 Web 應用程式的 APPINSIGHTS_INSTRUMENTATIONKEY 設定所擁有的檢測金鑰和 Application Insights SDK 所使用的金鑰相同。
4. 確定 Web 應用程式是在 .Net Framework 4.6 上執行。
5. 如果 Web 應用程式是 ASP.NET Core 應用程式，也請檢查[所需的相依性](#aspnetcore)。

在分析工具啟動之後，系統會有一段準備時間，分析工具會利用這段不長的時間積極收集幾項效能追蹤資料。 之後每隔一小時，分析工具會再花兩分鐘的時間收集效能追蹤資料。  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>我之前使用的是 Azure Service Profiler。 它有什麼改變？  

當您啟用 Application Insights Profiler 時，Azure Service Profiler 代理程式就會停用。

### <a id="double-counting"></a>平行執行緒重複計算

在某些情況下，堆疊檢視器的總時間計量會比要求實際的持續時間還多。

當某個要求有兩個以上的關聯執行緒以平行方式運作時，就會發生這種情形。 於是，執行緒的總時間就會超過實際經過的時間。 執行緒經常要等候另一個執行緒完成。 檢視器會嘗試偵測這個狀況，並省略不重要的等候，但在過程中，它會寧可顯示過多資訊，以免省略了可能重要的資訊。  

當您在追蹤資料內看到平行執行緒，您必須判斷哪些執行緒正在等候，以便判斷出要求的關鍵路徑。 在大部分情況下，快速進入等候狀態的執行緒就只是在等候其他執行緒。 請專注在其他執行緒上，並略過等候中執行緒的時間。

### <a id="issue-loading-trace-in-viewer"></a>沒有分析資料

1. 如果您嘗試檢視的資料已存在好幾週，請試著限縮時間篩選器，然後再試一次。

2. 確認系統未阻止 Proxy 或防火牆存取 https://gateway.azureserviceprofiler.net。

3. 檢查您在應用程式中使用的 Application Insights 檢測金鑰和您已啟用分析功能的 Application Insights 資源相同。 金鑰通常位於 ApplicationInsights.config 中，但您也可以在 web.config 或 app.config 中找到。

### <a name="error-report-in-the-profiling-viewer"></a>分析檢視器的錯誤報表

您可以從入口網站提出支援票證。 請納入錯誤訊息內的相互關聯識別碼。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>部署錯誤，不是空目錄 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

如果您要將 Web 應用程式重新部署為具有已啟用 Profiler 的應用程式服務資源，您可能會遇到類似下列的錯誤：不是空目錄 'D:\\home\\site\\wwwroot\\App_Data\\jobs' 如果您是從指令碼或在 VSTS 部署管線上執行 Web Deploy，將會發生此錯誤。
解決此問題的方法是將以下其他部署參數新增至 Web Deploy 工作中：

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

這會刪除 App Insights Profiler 使用的資料夾，並解除封鎖重新部署程序。 它不會影響目前正在執行的 Profiler 執行個體。


## <a name="manual-installation"></a>手動安裝

當您設定分析工具時，系統會對 Web 應用程式的設定進行下列更新。 如果您的環境需要，您能以手動方式自行執行它們，例如，如果應用程式在 Azure App Service Environment (ASE) 中執行︰

1. 在 Web 應用程式控制刀鋒視窗中，開啟 [設定]。
2. 將 [.Net Framework 版本] 設定為 [v4.6]。
3. 將 [一律開啟] 設定為 [開啟]。
4. 新增應用程式設定 "__APPINSIGHTS_INSTRUMENTATIONKEY__"，並將其值設定為 SDK 所使用的同一個檢測金鑰。
5. 開啟 [進階工具]。
6. 按一下 [執行] 以開啟 Kudu 網站。
7. 在 Kudu 網站中，選取 [網站擴充功能]。
8. 從資源庫安裝 "__Application Insights__"。
9. 重新啟動 Web 應用程式。

## <a id="aspnetcore"></a>ASP.NET Core 支援

ASP.NET Core 應用程式需要安裝 Microsoft.ApplicationInsights.AspNetCore Nuget 套件 2.1.0-beta6 或更新版本才能搭配分析工具使用。 在 2017/6/27 之後，我們將不再支援較低版本。

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

