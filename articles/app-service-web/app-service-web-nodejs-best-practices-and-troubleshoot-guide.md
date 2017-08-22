---
title: "Azure Web Apps 上節點應用程式的最佳作法和疑難排解指南"
description: "了解 Azure Web Apps 上節點應用程式的最佳作法和疑難排解步驟。"
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: 
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 06/06/2016
ms.author: ranjithr
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 656c5b3e363bd2eaa7550ffc673606d26f7d06de
ms.contentlocale: zh-tw
ms.lasthandoff: 06/07/2017

---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Azure Web Apps 上節點應用程式的最佳作法和疑難排解指南
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

在本文中，您將了解在 Azure Web Apps 上執行之[節點應用程式](app-service-web-get-started-nodejs.md)的最佳作法和疑難排解步驟 (透過 [iisnode](https://github.com/azure/iisnode))。

> [!WARNING]
> 請小心在您的生產網站上使用疑難排解步驟。 建議在非生產安裝 (例如您的預備位置) 上對應用程式進行疑難排解，而當問題修正後，請切換您的預備位置與生產位置。
> 
> 

## <a name="iisnode-configuration"></a>IISNODE 組態
此 [結構描述檔案](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) 會顯示可針對 iisnode 設定的所有設定。 適合用於您的應用程式的一些設定如下︰

* nodeProcessCountPerApplication
  
    此設定會控制每個 IIS 應用程式啟動的節點處理序數目。 預設值為 1。 您可以將此值設為 0，啟動與您的 VM 核心計數一樣多的 node.exe 。 大部分應用程式的建議值為 0，以便您利用電腦上的所有核心。 Node.exe 為單一執行緒，因此一個 node.exe 最多會耗用 1 個核心，若要讓節點應用程式發揮最大效能，您會可以利用所有的核心。
* nodeProcessCommandLine
  
    此設定會控制 node.exe 的路徑。 您可以設定此值以指向您的 node.exe 版本。
* maxConcurrentRequestsPerProcess
  
    此設定會控制 iisnode 傳送給每個 node.exe 的並行要求數目上限。 在 Azure Web Apps 上，此設定的預設值為 [無限]。 您不必擔心這項設定。 在 Azure Web Apps 外部，預設值為 1024。 您可以視您的應用程式取得的要求數目以及您的應用程式處理每個要求的速度而定，進行此設定。
* maxNamedPipeConnectionRetry
  
    此設定會控制 iisnode 將在具名管道上重試連線，以將要求傳送至 node.exe 的次數上限。 此設定結合 namedPipeConnectionRetryDelay 一起使用，可決定 iisnode 內每個要求的總逾時。 在 Azure Web Apps 上預設值為 200。 總逾時 (秒) = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
* namedPipeConnectionRetryDelay
  
    此設定控制 iisnode 在每次重試透過具名管道將要求傳送到 node.exe 之間所要等待的時間量 (毫秒)。 預設值為 250 毫秒。
    總逾時 (秒) = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
  
    在 Azure Web Apps 上，iisnode 的總逾時預設為 200 \* 250 毫秒 = 50 秒。
* logDirectory
  
    此設定會控制 iisnode 記錄 stdout/stderr 的目錄。 預設值是相對於主要指令碼目錄 (主要 server.js 所在的目錄) 的 iisnode
* debuggerExtensionDll
  
    此設定會控制 iisnode 在進行節點應用程式偵錯時，將使用的節點偵測器版本。 iisnode-inspector-0.7.3.dll 和 iisnode-inspector.dll 目前是此設定僅有的 2 個有效值。 預設值為 iisnode-inspector-0.7.3.dll。 iisnode-inspector-0.7.3.dll 版本會使用 node-inspector-0.7.3 並使用 Websocket，所以您必須啟用您的 Azure Web App 上的 Websocket，才能使用這個版本。 如需有關如何設定 iisnode 以使用新的節點偵測器的詳細資訊，請參閱 <http://www.ranjithr.com/?p=98>。
* flushResponse
  
    IIS 的預設行為是在排清之前或直到回應結束時 (取決於何者較早出現)，將回應資料緩衝至 4MB。 iisnode 提供組態設定來覆寫這個行為︰若要在 iisnode 從 node.exe 收到回應實體內文的片段時就立刻排清，您需要將 web.config 中的 iisnode/@flushResponse 屬性設定為 'true'︰
  
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```
  
    啟用排清每個回應主體內文片段會增加效能負荷，而降低 ~5% 的系統輸送量 (從 v0.1.13 起)，所以最好讓此設定的範圍僅限於需要回應串流的端點 (例如在 web.config 中使用 <location> 元素)
  
    除此之外，對於串流應用程式，您也必須將 iisnode 處理常式的 responseBufferLimit 設定為 0。
  
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```
* watchedFiles
  
    這是以分號分隔的檔案清單，系統將監看其變更。 檔案變更會導致應用程式回收。 每個項目都包含選擇性目錄名稱，再加上相對於主要應用程式進入點所在目錄的必要檔案名稱。 只有檔案名稱部分可以使用萬用字元。 預設值為 “\*.js;web.config”
* recycleSignalEnabled
  
    預設值為 false。 若已啟用，節點應用程式可以連接至具名管道 (環境變數 IISNODE\_CONTROL\_PIPE) 並傳送「回收」訊息。 這會導致正常回收 w3wp。
* idlePageOutTimePeriod
  
    預設值為 0，這表示已停用此功能。 若設定為大於 0 的值，iisnode 會每隔 'idlePageOutTimePeriod' 毫秒將其所有的子處理序移出分頁。 若要了解移出分頁的意思，請參閱此 [文件](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx)。 對於耗用大量記憶體和偶爾想要將記憶體移出至磁碟以釋放一些 RAM 的應用程式，此設定很有用。

> [!WARNING]
> 在生產應用程式上啟用下列組態設定時，請格外小心。 建議不要在實際生產應用程式上啟用它們。
> 
> 

* debugHeaderEnabled
  
    預設值為 False。 如果設為 true，iisnode 會將 HTTP 回應標頭 iisnode-debug 加入至它所傳送的每個 HTTP 回應，而 iisnode-debug 標頭值是 URL。 查看 URL 片段即可搜集個別的診斷資訊，但在瀏覽器中開啟 URL 可達成更好的視覺效果。
* loggingEnabled
  
    此設定會控制 iisnode 記錄 stdout 和 stderr 的功能。 Iisnode 將從它所啟動的節點處理序擷取 stdout/stderr，並寫入至 'logDirectory' 設定中指定的目錄。 一旦啟用，您的應用程式會將記錄檔寫入至檔案系統，而視應用程式所完成的記錄量而定，可能會影響效能。
* devErrorsEnabled
  
    預設值為 false。 若設為 true，iisnode 會在瀏覽器上顯示 HTTP 狀態碼和 Win32 錯誤碼。 在偵錯特定類型的問題時，Win32 程式碼很有幫助。
* debuggingEnabled (請勿在實際生產網站上啟用)
  
    此設定會控制偵錯功能。 Iisnode 會與節點偵測器整合。 藉由啟用此設定，即可啟用節點應用程式的偵錯功能。 啟用此設定後，iisnode 會在對節點應用程式進行第一個偵錯要求時，在 'debuggerVirtualDir' 目錄中配置所需的節點偵測器檔案。 您可以將要求傳送至 http://yoursite/server.js/debug，以載入節點偵測器。 您可以使用 'debuggerPathSegment' 設定來控制偵錯 URL 區段。 根據預設，debuggerPathSegment=’debug’。 您可以將此值設定為 GUID，所以其他人更難以發現。
  
    如需偵錯詳細資訊，請查看此 [連結](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) 。

## <a name="scenarios-and-recommendationstroubleshooting"></a>案例和建議/疑難排解
### <a name="my-node-application-is-making-too-many-outbound-calls"></a>我的節點應用程式進行太多的輸出呼叫。
許多應用程式想要在其定期作業中進行輸出連線。 例如，當要求傳入時，節點應用程式會想連絡別處的 REST API，並取得一些資訊來處理要求。 您想要在進行 http 或 https 呼叫時使用保持連線代理程式。 例如，您可以在進行這些輸出呼叫時，使用 agentkeepalive 模組做為您的保持連線代理程式。 這可確保在您的 Azure Web App VM 上重複使用通訊端，並減少為每個輸出要求建立新通訊端的額外負荷。 此外，這可確保您使用較少的通訊端來進行許多輸出要求，因此您不會超過每個 VM 配置的 maxSockets。 對於 Azure Web Apps 的建議是將 agentKeepAlive maxSockets 值設為每個 VM 總計有 160 個通訊端。 這表示如果您有 4 個 node.exe 在 VM 上執行，您可以將每個 node.exe 的 agentKeepAlive maxSockets 設定為 40，也就是每個 VM 總計有 160 個。

範例 [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) 設定：

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

這個範例假設您有 4 個 node.exe 在 VM 上執行。 如果您有不同數目的 node.exe 在 VM 上執行，您必須據此修改 maxSockets 設定。

### <a name="my-node-application-is-consuming-too-much-cpu"></a>我的節點應用程式目前耗用太多 CPU。
您可能會在您的入口網站上取得 Azure Web Apps 建議的高 cpu 耗用量。 您也可以設定監視器以監看某些 [度量](web-sites-monitor.md)。 在 [Azure 入口網站儀表板](../application-insights/app-insights-web-monitor-performance.md)上檢查 CPU 使用量時，請檢查 CPU 的 MAX 值，您才不會錯過尖峰值。
在您認為應用程式耗用太多 CPU，但您無法解釋的情況下，您必須剖析節點應用程式。

### 
#### <a name="profiling-your-node-application-on-azure-webapps-with-v8-profiler"></a>在 Azure Web Apps 上使用 V8 分析工具剖析節點應用程式
例如，假設您擁有想要剖析的 hello world 應用程式，如下所示︰

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

請移至您的 scm 網站 https://yoursite.scm.azurewebsites.net/DebugConsole

您會看到命令提示字元，如下所示。 進入 site/wwwroot 目錄

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

執行 “npm install v8-profiler” 命令

這應會在 node\_modules 目錄與其所有的相依項目之下安裝 v8 分析工具。
現在，編輯 server.js 以剖析您的應用程式。

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

上述變更將剖析 WriteConsoleLog 函式，然後將設定檔輸出寫入至您的網站 wwwroot 下的 'profile.cpuprofile' 檔案。 將要求傳送至您的應用程式。 您會在您的網站 wwwroot 下看到所建立的 'profile.cpuprofile' 檔案。

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

下載此檔案，您必須使用 Chrome F12 工具來開啟此檔案。 在 Chrome 上按 F12，然後按一下 [設定檔索引標籤]。 按一下 [載入] 按鈕。 選取您剛下載的 profile.cpuprofile 檔案。 按一下您剛下載的設定檔

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

您會看到 WriteConsoleLog 函式已耗用 95%的時間，如下所示。 這也會顯示造成此問題的確切行號和原始程式檔。

### <a name="my-node-application-is-consuming-too-much-memory"></a>我的節點應用程式目前耗用太多記憶體。
您可能會在您的入口網站上取得 Azure Web Apps 建議的高記憶體耗用量。 您也可以設定監視器以監看某些 [度量](web-sites-monitor.md)。 在 [Azure 入口網站儀表板](../application-insights/app-insights-web-monitor-performance.md)上檢查記憶體使用量時，請檢查記憶體的 MAX 值，您才不會錯過尖峰值。

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>node.js 的流失偵測和堆積區分
您可以使用 [node-memwatch](https://github.com/lloyd/node-memwatch) 協助找出記憶體流失。
如同 v8 分析工具，您可以安裝 memwatch 並編輯您的程式碼來擷取和區分堆積，以找出您應用程式中的記憶體流失。

### <a name="my-nodeexes-are-getting-killed-randomly"></a>我的 node.exe 會隨機終止
發生這種情況有幾個原因︰

1. 您的應用程式擲回未攔截的例外狀況 - 請檢查 d:\\home\\LogFiles\\Application\\logging-errors.txt file 檔案，以取得所擲回例外狀況的詳細資料。 這個檔案有堆疊追蹤，所以您可以據此修正您的應用程式。
2. 您的應用程式耗用太多記憶體，近而讓其他處理序無法開始執行。 如果 VM 記憶體總數接近 100%，則處理序管理員會終止 node.exe，讓其他處理序有機會執行一些工作。 若要修正此問題，請確定您的應用程式並未流失記憶體，或者如果您的應用程式真的需要使用大量記憶體，請相應增加為更多 RAM 的較大 VM。

### <a name="my-node-application-does-not-start"></a>我的節點應用程式並未啟動
如果您的應用程式在啟動時傳回 500 錯誤，可能有幾個原因︰

1. Node.exe 未出現在正確的位置。 檢查 nodeProcessCommandLine 設定。
2. 主要指令碼檔案未出現在正確的位置。 檢查 web.config，並確定處理常式區段中的主要指令碼檔案名稱符合主要指令碼檔案。
3. Web.config 組態不正確 – 檢查設定名稱/值。
4. 冷啟動 – 您的應用程式花太長時間啟動。 如果您的應用程式所花的時間大於 (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 秒，則 iisnode 會傳回 500 錯誤。 增加這些設定的值，以符合您的應用程式開始時間，可防止 iisnode 逾時並傳回 500 錯誤。

### <a name="my-node-application-crashed"></a>我的節點應用程式毀損
您的應用程式擲回未攔截的例外狀況 - 請檢查 d:\\home\\LogFiles\\Application\\logging-errors.txt file 檔案，以取得所擲回例外狀況的詳細資料。 這個檔案有堆疊追蹤，所以您可以據此修正您的應用程式。

### <a name="my-node-application-takes-too-much-time-to-startup-cold-start"></a>我的節點應用程式花太多時間啟動 (冷啟動)
最常見原因的是應用程式在 node\_modules 中有很多檔案，而且應用程式嘗試在啟動期間載入大多數的檔案。 根據預設，因為您的檔案位於 Azure Web Apps 的網路共用上，所以載入太多檔案可能需要一些時間。
讓載入速度變快的解決方式如下︰

1. 使用 npm3 來安裝您的模組，確定您有扁平相依性結構，並沒有重複的相依性。
2. 試著延遲載入您的 node\_modules，而不要在啟動時載入所有的模組。 這表示對 require('module') 的呼叫應該在嘗試使用模組的函式中有實際需要時進行。
3. Azure Web Apps 會提供稱為本機快取的功能。 這項功能會將您的內容從網路共用複製到 VM 上的本機磁碟。 因為這些檔案都在本機，node\_modules 的載入時間快很多。 - 本 [文件](../app-service/app-service-local-cache.md) 詳細說明如何使用本機快取。

## <a name="iisnode-http-status-and-substatus"></a>IISNODE http 狀態和子狀態
此 [原始程式檔](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) 會列出 iisnode 可在發生錯誤時傳回的所有可能狀態/子狀態組合。

為您的應用程式啟用 FREB 以查看 win32 錯誤碼 (基於效能考量，請確定只在非生產網站上啟用 FREB)。

| Http 狀態 | Http 子狀態 | 可能的原因？ |
| --- | --- | --- |
| 500 |1000 |將要求分派至 IISNODE 時發生一些問題 – 檢查 node.exe 是否已啟動。 Node.exe 可能在啟動時損毀。 檢查 web.config 組態是否有錯誤。 |
| 500 |1001 |- Win32Error 0x2 - 應用程式並未回應 URL。 檢查 URL 重寫規則，或您的快速應用程式是否已定義正確的路由。 - Win32Error 0x6d – 具名管道忙碌中 – Node.exe 不接受要求，因為管道忙碌中。 檢查高 CPU 使用量。 - 其他錯誤 – 檢查 node.exe 是否毀損。 |
| 500 |1002 |Node.exe 損毀 – 檢查 d:\\home\\LogFiles\\logging-errors.txt 中的堆疊追蹤。 |
| 500 |1003 |管道組態問題 – 您不應該看見此問題，但如果看見，則表示具名管道組態不正確。 |
| 500 |1004-1018 |傳送要求或處理 node.exe 的相關回應時發生一些錯誤。 檢查 node.exe 是否損毀。 檢查 d:\\home\\LogFiles\\logging-errors.txt 中的堆疊追蹤。 |
| 503 |1000 |記憶體不足，無法配置更多具名管道連線。 檢查您的應用程式為何使用這麼多的記憶體。 檢查 maxConcurrentRequestsPerProcess 設定值。 如果此值並非無限，而且您有許多要求，請增加此值來避免這個錯誤。 |
| 503 |1001 |無法將要求分派至 node.exe，因為應用程式正在回收處理。 回收應用程式之後，應該會正常提供要求。 |
| 503 |1002 |檢查 win32 錯誤碼的實際原因 – 無法將要求分派至 node.exe。 |
| 503 |1003 |具名管道太忙 – 檢查節點是否正耗用大量的 CPU |

NODE.exe 內有名為 NODE\_PENDING\_PIPE\_INSTANCES 的設定。 根據預設，此值在 Azure Web Apps 外部為 4。 這表示該 node.exe 在具名管道上一次只能接受 4 個要求。 在 Azure Web Apps，此值設定為 5000，這個值應足以滿足大部分在 Azure Web Apps 上執行的節點應用程式。 您不應該在 Azure Web Apps 上看見 503.1003，因為我們 NODE\_PENDING\_PIPE\_INSTANCES 的值較高。  |

## <a name="more-resources"></a>其他資源
請遵循下列連結以深入了解 Azure App Service 上的 node.js 應用程式。

* [在 Azure App Service 中開始使用 Node.js Web 應用程式](app-service-web-get-started-nodejs.md)
* [如何在 Azure App Service 中偵錯 Node.js Web 應用程式](web-sites-nodejs-debug.md)
* [使用 Node.js 模組與 Azure 應用程式搭配](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps：Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js 開發人員中心](../nodejs-use-node-modules-azure-apps.md)
* [探索神秘無比的 Kudu 偵錯主控台](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)


