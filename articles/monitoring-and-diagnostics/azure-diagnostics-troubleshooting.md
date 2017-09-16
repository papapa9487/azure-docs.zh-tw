---
title: "針對 Azure 診斷進行疑難排解 | Microsoft Docs"
description: "針對在 Azure 虛擬機器、Service Fabric 或 雲端服務中使用 Azure 診斷時出現的問題進行疑難排解。"
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/12/2017
ms.author: robb
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: a0cb529836b14df71e83616f4f625a002c535b7b
ms.contentlocale: zh-tw
ms.lasthandoff: 08/25/2017

---
# <a name="azure-diagnostics-troubleshooting"></a>Azure 診斷疑難排解
有關使用 Azure 診斷的疑難排解資訊。 如需有關 Azure 診斷的詳細資訊，請參閱 [Azure 診斷概觀](azure-diagnostics.md)。

## <a name="logical-components"></a>邏輯元件
**診斷外掛程式啟動器 (DiagnosticsPluginLauncher.exe)**：啟動 Azure 診斷擴充功能。 可作為進入點程序。

**診斷外掛程式 (DiagnosticsPlugin.exe)**：上述啟動器啟動的主要程序，而且該程序會設定監視代理程式，啟動代理程式，以及管理其存留期。 

**監視代理程式 (MonAgent\*.exe 處理程序)**：這些處理程序會執行大量工作；也就是監視、收集及傳輸診斷資料。  

## <a name="logartifact-paths"></a>記錄檔/構件路徑
以下是一些重要記錄檔和構件的路徑。 我們會持續在文件的其餘部分參考這些項目：
### <a name="cloud-services"></a>雲端服務
| 構件 | 路徑 |
| --- | --- |
| **Azure 診斷組態檔** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **記錄檔** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **診斷資料的本機存放區** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **監視代理程式組態檔** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure 診斷擴充功能套件** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **記錄集合公用程式路徑** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost 記錄檔** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>虛擬機器
| 構件 | 路徑 |
| --- | --- |
| **Azure 診斷組態檔** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **記錄檔** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Logs\ |
| **診斷資料的本機存放區** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **監視代理程式組態檔** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **狀態檔案** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Azure 診斷擴充功能套件** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **記錄集合公用程式路徑** | C:\WindowsAzure\Packages |
| **MonAgentHost 記錄檔** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-show-in-azure-portal"></a>計量資料不會出現在 Azure 入口網站
Azure 診斷會提供一些計量資料，這些資料可以在 Azure 入口網站中顯示。 如果您在入口網站中看不到這些資料，請檢查診斷儲存體帳戶 -> WADMetrics\* 資料表，以查看是否有對應計量記錄。 在這裡，資料表的 PartitionKey 是虛擬機器或虛擬機器擴展集的資源識別碼，而 RowKey 是計量名稱，也就是效能計數器名稱。

如果資源識別碼不正確，請檢查 [診斷設定] -> [計量] -> [資源識別碼]，以查看資源識別碼是否正確設定。

如果沒有特定計量的資料，請檢查 [診斷設定] -> [PerformanceCounter]，以查看是否包含計量 (效能計數器)。 我們預設會啟用下列計數器。
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET Applications(__Total__)\Requests/Sec
- \ASP.NET Applications(__Total__)\Errors Total/Sec
- \ASP.NET\Requests Queued
- \ASP.NET\Requests Rejected
- \Processor(w3wp)\% Processor Time
- \Process(w3wp)\Private Bytes
- \Process(WaIISHost)\% Processor Time
- \Process(WaIISHost)\Private Bytes
- \Process(WaWorkerHost)\% Processor Time
- \Process(WaWorkerHost)\Private Bytes
- \Memory\Page Faults/sec
- \.NET CLR Memory(_Global_)\% Time in GC
- \LogicalDisk(C:)\Disk Write Bytes/sec
- \LogicalDisk(C:)\Disk Read Bytes/sec
- \LogicalDisk(D:)\Disk Write Bytes/sec
- \LogicalDisk(D:)\Disk Read Bytes/sec

如果設定正確，但是您仍然看不到計量資料，請遵循以下指導方針以進行進一步的調查。


## <a name="azure-diagnostics-is-not-starting"></a>Azure 診斷未啟動
從上述提供的記錄檔位置查看 **DiagnosticsPluginLauncher.log** 和 **DiagnosticsPlugin.log** 檔案，以取得診斷為何無法啟動的相關資訊。 

如果這些記錄指出 `Monitoring Agent not reporting success after launch`，則表示啟動 MonAgentHost.exe 失敗。 在針對上節中的 `MonAgentHost log file` 指示的位置，查看其記錄。

記錄檔的最後一行包含結束代碼。  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
如果您發現**負數**的結束代碼，請參閱[參考](#references)中的[結束代碼表格](#azure-diagnostics-plugin-exit-codes)。

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>診斷資料未記錄至 Azure 儲存體
判斷是否沒有顯示任何資料，或只有部分資料未顯示。

### <a name="diagnostics-infrastructure-logs"></a>診斷基礎結構記錄檔
診斷基礎結構記錄檔是 Azure 診斷記錄任何所發生錯誤的所在位置。 確定您已經在設定中啟用 ([作法？](#how-to-check-diagnostics-extension-configuration)) 診斷基礎結構記錄的擷取功能，並快速尋找設定的儲存體帳戶中，於 `DiagnosticInfrastructureLogsTable` 資料表中顯示的任何相關錯誤。

### <a name="no-data-is-showing-up"></a>未顯示任何資料
完全遺漏事件資料的最常見原因是不正確地定義儲存體帳戶資訊。

解決方法：更正診斷組態，並重新安裝診斷。

如果已正確設定儲存體帳戶，請從遠端桌面連接到電腦，並確定 DiagnosticsPlugin.exe 和 MonAgentCore.exe 正在執行中。 如果它們並未執行，請依照 [Azure 診斷未啟動](#azure-diagnostics-is-not-starting)中的說明進行作業。 如果程序正在執行，請跳至[是否正在本機擷取資料](#is-data-getting-captured-locally)，並從那裡開始依照本指南進行作業。

### <a name="part-of-the-data-is-missing"></a>遺漏部分資料
如果您正在取得某些資料，但沒有取得其他資料。 這表示未正確設定資料收集/傳輸管線。 請依照這裡的子小節縮小問題範圍以找出問題：
#### <a name="is-collection-configured"></a>是否已設定集合： 
診斷組態包含指定要收集特定資料類型的部分。 [檢閱您的設定](#how-to-check-diagnostics-extension-configuration)以確定您尋找的資料不是未設定要收集的資料。
#### <a name="is-the-host-generating-data"></a>主機是否正在產生資料：
- **效能計數器**：開啟 Perfmon 並檢查計數器。
- **追蹤記錄檔**：從遠端桌面連接到 VM，並將 TextWriterTraceListener 新增至應用程式的組態檔。  請參閱 http://msdn.microsoft.com/library/sk36c28t.aspx 設定文字接聽程式。  確定 `<trace>` 元素具有 `<trace autoflush="true">`。<br />
如果您沒有看到系統正在產生追蹤記錄檔，請依照[關於遺漏追蹤記錄檔的詳細資訊](#more-about-trace-logs-missing)中的說明進行作業。
- **ETW 追蹤**：從遠端桌面連接到 VM 並安裝 PerfView。  在 PerfView 中執行 [File] \(檔案\) -> [User Command] \(使用者命令\) -> Listen etwprovder1,etwprovider2,etc。請注意，Listen 命令區分大小寫，而且在 ETW 提供者清單中，以逗號區隔的 ETW 提供者之間不能有空格。  如果命令執行失敗，您可以按一下 Perfview 工具右下方的 [Log] \(記錄\) 按鈕，即可查看已嘗試執行哪些動作與執行結果。  假設輸入正確，就會顯示一個新的視窗，並且您會在幾秒鐘內開始看到 ETW 追蹤。
- **事件記錄檔**：從遠端桌面連接到 VM。 開啟 `Event Viewer` 並確認事件存在。
#### <a name="is-data-getting-captured-locally"></a>是否正在本機擷取資料：
接下來請確定正在本機擷取資料。
資料儲存在[診斷資料的本機存放區](#log-artifacts-path)的本機 `*.tsf` 檔案中。 不同的 `.tsf` 檔案會收集不同類型的記錄。 名稱類似於 Azure 儲存體中的資料表名稱。 例如，`PerformanceCountersTable.tsf`中會收集 `Performance Counters`，`WindowsEventLogsTable.tsf` 中會收集事件記錄檔。 請使用[本機記錄檔擷取](#local-log-extraction)一節中的指示開啟本機收集檔案，並確定您看到系統在磁碟上收集那些檔案。

如果您沒有看到系統在本機收集記錄，且已經確認主機正在產生資料，可能是設定有問題。 請仔細檢閱適當區段的設定。 請一併檢閱針對 MonitoringAgent [MaConfig.xml](#log-artifacts-path) 產生的設定，並確定有描述相關記錄來源的部分區段，以及在 Azure 診斷組態和監視代理程式組態之間轉譯時，沒有造成遺失。
#### <a name="is-data-getting-transferred"></a>是否正在傳輸資料：
如果您已經確認正在本機擷取資料，但仍然沒有在儲存體帳戶中看到資料： 
- 首先且最重要的是，請確定您已經提供正確的儲存體帳戶，且您未變換所提供儲存體帳戶的金鑰等項目。 針對雲端服務，我們發現使用者有時候並未更新他們的 `useDevelopmentStorage=true`。
- 如果提供的儲存體帳戶正確。 請確定您的網路沒有受到不允許元件連線公用儲存體端點的限制。 那樣做的其中一個方法是從遠端桌面連接到電腦，然後嘗試自行將一些內容寫入到相同的儲存體帳戶。
- 最後，您可以嘗試及查看監視代理程式報告了哪些失敗項目。 監視代理程式會將其記錄檔寫入位於[診斷資料的本機存放區](#log-artifacts-path)的 `maeventtable.tsf` 中。 請依照[本機記錄檔擷取](#local-log-extraction)一節中的指示開啟此檔案，然後嘗試找出是否有指出讀取本機檔案或寫入儲存體失敗的 `errors`。

### <a name="capturing--archiving-logs"></a>擷取/封存記錄檔
您依序執行了上述步驟，但無法找出問題所在，因此正在考慮連絡支援人員。 他們可能會要求您做的第一件事是收集電腦的記錄。 您可以先自行執行該作業以節省時間。 請執行位於[記錄集合公用程式路徑](#log-artifacts-path)的 `CollectGuestLogs.exe` 公用程式，它將會產生一個在同一資料夾中包含所有相關 Azure 記錄的壓縮檔。

## <a name="diagnostics-data-tables-not-found"></a>找不到診斷資料資料表
Azure 儲存體中的資料表保存了使用以下代碼命名的 ETW 事件：

```C#
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

下列是一個範例：

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
產生的 4 個資料表如下︰

| Event | 資料表名稱 |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

## <a name="references"></a>參考

### <a name="how-to-check-diagnostics-extension-configuration"></a>如何檢查診斷延伸模組組態
檢查延伸模組組態最容易的方式是，瀏覽至 http://resources.azure.com ，接著瀏覽至 Azure 診斷擴充功能 (IaaSDiagnostics / PaaDiagnostics) 有問題的虛擬機器或雲端服務。

或者，從遠端桌面連接到電腦，然後查看[這裡](#log-artifacts-path)的適當小節中描述的 Azure 診斷組態檔。

在任一情況下，都可以搜尋 **Microsoft.Azure.Diagnostics**，然後搜尋 **xmlCfg** 或 **WadCfg** 欄位。 

在使用虛擬機器的情況下，如果出現 WadCfg 欄位，表示組態在 JSON 中。 如果出現 xmlCfg 欄位，表示組態在 XML 中且以 base64 編碼。 您必須將它[解碼](http://www.bing.com/search?q=base64+decoder)，才能查看診斷載入的 XML。

針對雲端服務角色，如果是從磁碟挑選設定，該資料會以 base64 編碼，因此需要[將它解碼](http://www.bing.com/search?q=base64+decoder)，才能查看診斷載入的 XML。

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure 診斷外掛程式結束代碼
外掛程式會傳回下列結束代碼：

| 結束代碼 | 說明 |
| --- | --- |
| 0 |成功。 |
| -1 |一般錯誤。 |
| -2 |無法載入 rcf 檔。<p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。 |
| -3 |無法載入診斷組態檔。<p><p>解決方法：因組態檔未通過結構描述驗證而造成。 解決方法是提供以結構描述編譯的組態檔。 |
| -4 |診斷之監視代理程式的另一個執行個體已在使用本機資源目錄。<p><p>解決方法：為 **LocalResourceDirectory** 指定其他值。 |
| -6 |客體代理程式外掛程式啟動器嘗試使用無效的命令列來啟動診斷。<p><p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。 |
| -10 |診斷外掛程式結束並發生未處理的例外狀況。 |
| -11 |客體代理程式無法建立負責啟動及監視監視代理程式的處理序。<p><p>解決方法：確認有足夠的系統資源，可啟動新的處理序。<p> |
| -101 |呼叫診斷外掛程式時的引數無效。<p><p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。 |
| -102 |外掛程式處理序無法對本身進行初始化。<p><p>解決方法：確認有足夠的系統資源，可啟動新的處理序。 |
| -103 |外掛程式處理序無法對本身進行初始化。 具體而言，其無法建立記錄器物件。<p><p>解決方法：確認有足夠的系統資源，可啟動新的處理序。 |
| -104 |無法載入客體代理程式提供的 rcf 檔。<p><p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。 |
| -105 |診斷外掛程式無法開啟診斷組態檔。<p><p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用診斷外掛程式時才會發生。 |
| -106 |無法讀取診斷組態檔。<p><p>解決方法：因組態檔未通過結構描述驗證而造成。 因此，解決方法是提供以結構描述編譯的組態檔。 請參閱[如何檢查診斷延伸模組組態](#how-to-check-diagnostics-extension-configuration)。 |
| -107 |傳遞給監視代理程式的資源目錄無效。<p><p>此內部錯誤應只有當不正確地在 VM 上以手動方式叫用監視代理程式時才會發生。</p> |
| -108 |無法將診斷組態檔轉換成監視代理程式組態檔。<p><p>此內部錯誤應只有當使用無效的組態檔以手動方式叫用診斷外掛程式時才會發生。 |
| -110 |一般診斷組態錯誤。<p><p>此內部錯誤應只有當使用無效的組態檔以手動方式叫用診斷外掛程式時才會發生。 |
| -111 |無法啟動監視代理程式。<p><p>解決方法：確認有足夠的系統資源可用。 |
| -112 |一般錯誤 |

### <a name="local-log-extraction"></a>本機記錄檔擷取
監視代理程式會以 `.tsf` 檔案的方式收集記錄和構件。 您無法讀取 `.tsf` 檔案，但可以將它轉換成 `.csv`，如下所示： 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
將會在同一路徑中建立一個名為 `<relevantLogFile>.csv` 的新檔案，作為相對應的 `.tsf` 檔案。

**注意**：您只需要針對主要的 tsf 檔案 (例如 PerformanceCountersTable.tsf) 執行此公用程式。 系統會自動處理隨附的檔案 (例如，PerformanceCountersTables_\*\*001.tsf、PerformanceCountersTables_\*\*002.tsf 等等)。

### <a name="more-about-trace-logs-missing"></a>關於遺漏追蹤記錄檔的詳細資訊

**注意：**除非您已經在於 IaaS VM 中執行的應用程式上設定 DiagnosticsMonitorTraceListener，否則這幾乎僅適用於雲端服務。 

- 請確定已在 web.config 或 app.config 中設定 DiagnosticMonitorTraceListener。雲端服務專案中會預設設定這個項目，但某些客戶認為這會導致診斷不會收集追蹤陳述式。 
- 如果未從 OnStart 或 Run 方法寫入記錄檔，請確定 app.config 中有 DiagnosticMonitorTraceListener。依照預設，它是位於 web.config 之中，但那僅適用於在 w3wp.exe 內執行的程式碼；所以您需要它位於 app.config 中，才能擷取在 WaIISHost.exe 中執行的追蹤。
- 請確定您使用的是 Diagnostics.Trace.TraceXXX，而不是 Diagnostics.Debug.WriteXXX。  偵錯陳述式將從發行組建中移除。
- 請確定已編譯的程式碼確實具有 Diagnostics.Trace 行 (使用 Reflector、ildasm 或 ILSpy 來驗證)。  Diagnostics.Trace 命令已從編譯的二進位檔中移除，除非您使用 TRACE 條件式編譯的符號。  如果使用 msbuild 建置專案，這就會是一個常見的問題。

## <a name="known-issues-and-mitigations"></a>已知問題與緩解方式
以下是已知問題和緩解方式的清單：

**1. .NET 4.5 相依性：**

WAD 在 .NET 4.5 Framework 或更新版本上具有執行階段相依性。 在撰寫這部分的內容時，所有針對雲端服務佈建的電腦，以及所有官方 Azure 虛擬機器基本映像，都已安裝 .NET 4.5 或更新版本。 但是，仍可能會發生您嘗試在沒有安裝 .NET 4.5 或更新版本的電腦上執行 WAD 的情況。 當您從舊映像或快照集建立電腦，或攜帶自己的自訂磁碟時，就可能會發生這個問題。

執行 DiagnosticsPluginLauncher.exe 時，這通常會以結束代碼 **255** 來表示。 失敗原因為未處理的例外狀況： 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**緩解方式：**在電腦上安裝 .NET 4.5 或更新版本。

**2.儲存體中有效能計數器資料，但入口網站中沒有顯示**

虛擬機器入口網站體驗預設會顯示特定效能計數器。 如果您沒有看到那些計數器，但您知道系統已經產生資料，因為儲存體中有資料。 勾選：
- 儲存體中的資料是否具有英文計數器名稱。 如果計數器名稱不是英文，入口網站計量圖表將無法辨識該計數器。
- 如果您在效能計數器名稱中使用萬用字元 (\*)，入口網站將無法關聯設定和收集的計數器。

**緩解方式**：針對系統帳戶，將電腦的語言變更為英文。 [控制台] -> [地區] -> [系統管理] -> [複製設定] -> 取消選取 [歡迎畫面及系統帳戶]，就能讓自訂語言不會套用至系統帳戶。 如果想要入口網站成為主要的使用體驗網站，也請確定沒有使用萬用字元。

