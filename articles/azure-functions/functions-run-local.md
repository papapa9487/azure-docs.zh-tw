---
title: "開發 Azure 函數並在本機執行 | Microsoft Docs"
description: "在於 Azure Functions 上執行 Azure 函式之前，先了解如何撰寫 Azure 函式並在本機電腦上進行測試。"
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/25/2017
ms.author: glenga
ms.openlocfilehash: b6ab081311822abd9c0a24b4cc241291bf56af68
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2017
---
# <a name="code-and-test-azure-functions-locally"></a>撰寫 Azure Functions 並在本機進行測試

雖然 [Azure 入口網站] 有提供開發及測試 Azure Functions 的完整工具集，有許多開發人員仍偏好本機開發體驗。 Azure Functions 可讓您輕鬆使用最喜愛的程式碼編輯器及本機開發工具，在本機電腦上開發並測試您的函式。 您的函式可以透過 Azure 中的事件觸發，您也可以在本機電腦上對 C# 和 JavaScript 函式進行偵錯。 

如果您是 Visual Studio C# 開發人員，Azure Functions 也能[與 Visual Studio 2017 整合](functions-develop-vs.md)。

## <a name="install-the-azure-functions-core-tools"></a>安裝 Azure Functions Core Tools

[Azure Functions Core Tools] 是 Azure Functions 執行階段的本機版本，可讓您在本機開發電腦上執行。 它不是模擬器。 它與在 Azure 中提供 Functions 的執行階段是相同的執行階段。 Azure Functions Core Tools 有兩個版本，一個版本適用於 1.x 版的執行階段，一個版本則適用於 2.x 版。 這兩個版本都會提供為 [npm 套件](https://docs.npmjs.com/getting-started/what-is-npm)。

>[!NOTE]  
> 您必須[安裝 NodeJS](https://docs.npmjs.com/getting-started/installing-node) (內含 npm)，再安裝任一版本。 針對 2.x 版的工具，只支援 Node.js 8.5 和更新版本。 

### <a name="version-1x-runtime"></a>1.x 版執行階段

工具的原始版本會使用 Functions 1.x 執行階段。 這個版本使用 .NET Framework，並且只有在 Windows 電腦上才予以支援。 使用下列命令來安裝 1.x 版工具：

```bash
npm install -g azure-functions-core-tools
```

### <a name="version-2x-runtime"></a>2.x 版執行階段

工具的 2.x 版會使用以 .NET Core 為建置基礎的 Azure Functions 執行階段 2.x。 .NET Core 2.x 支援的所有平台都支援這個版本。 進行跨平台開發以及需要 Functions 執行階段 2.x 時，請使用這個版本。 

>[!IMPORTANT]   
> 安裝 Azure Functions Core Tools 之前，請[安裝 .NET Core 2.0](https://www.microsoft.com/net/core)。  
>
> Azure Functions 執行階段 2.0 為預覽版本，而且目前並未支援所有 Azure Functions 功能。 如需詳細資訊，請參閱 [Azure Functions 執行階段 2.0 已知問題](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)。 

 使用下列命令來安裝 2.0 版工具：

```bash
npm install -g azure-functions-core-tools@core
```

安裝在 Ubuntu 上時，請使用 `sudo`，如下所示：

```bash
sudo npm install -g azure-functions-core-tools@core
```

安裝在 macOS 和 Linux 上時，您可能需要包括 `unsafe-perm` 旗標，如下所示：

```bash
sudo npm install -g azure-functions-core-tools@core --unsafe-perm true
```

## <a name="run-azure-functions-core-tools"></a>執行 Azure Functions Core Tools
 
Azure Functions Core Tools 新增下列命令別名：
* **func**
* **azfun**
* **azurefunctions**

在範例中顯示 `func` 時，可以使用下列任何別名。

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>建立本機的 Functions 專案

在本機執行時，Functions 專案是具有 [host.json](functions-host-json.md) 和 [local.settings.json](#local-settings) 檔案的目錄。 此目錄相當於 Azure 中的函式應用程式。 若要深入了解 Azure Functions 的資料夾結構，請參閱 [Azure Functions 的開發人員指南](functions-reference.md#folder-structure)。

在終端機視窗或命令提示字元中，執行下列命令來建立專案和本機 Git 存放庫：

```
func init MyFunctionProj
```

輸出看起來會像下列範例：

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

若要建立不含本機 Git 存放庫的專案，請使用 `--no-source-control [-n]` 選項。

<a name="local-settings"></a>

## <a name="local-settings-file"></a>本機設定檔

local.settings.json 檔案會儲存應用程式設定、連接字串和 Azure Functions Core Tools 的設定。 其結構如下：

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| 設定      | 說明                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | 設定為 **true** 時，所有的值都會使用本機電腦金鑰加密。 需搭配 `func settings` 命令使用。 預設值為 **false**。 |
| **值** | 於本機執行時使用的應用程式設定集合。 **AzureWebJobsStorage** 和 **AzureWebJobsDashboard** 是範例；如需完整清單，請參閱[應用程式設定參考](functions-app-settings.md)。  |
| **Host** | 此區段中的設定能自訂於本機執行的 Functions 主機處理序。 | 
| **LocalHttpPort** | 設定於執行本機 Functions 主機 (`func host start` 和 `func run`) 時所使用的預設連接埠。 `--port` 命令列選項的優先順序高於此值。 |
| **CORS** | 定義針對[跨來源資源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 所允許的來源。 來源是以不含空格的逗號分隔清單提供。 支援萬用字元值 (**\***)，它能允許來自任何來源的要求。 |
| **ConnectionStrings** | 包含函式的資料庫連接字串。 此物件中的連接字串會新增至具有 **System.Data.SqlClient** 提供者類型的環境。  | 

大部分的觸發程序和繫結都有 **Connection** 屬性，會對應至環境變數或應用程式設定的名稱。 針對每個連線屬性，都必須在 local.settings.json 檔案中定義應用程式設定。 

這些設定也可以在您的程式碼中讀取為環境變數。 在 C# 中，請使用 [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) \(機器翻譯\) 或 [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx) \(機器翻譯\)。 在 JavaScript 中，使用 `process.env`。 指定為系統環境變數之設定的優先順序，將會高於 local.settings.json 檔案中的值。 

local.settings.json 檔案中的值，只會由於本機執行的 Functions 工具使用。 根據預設，在專案發佈至 Azure 時，這些設定將不會自動移轉。 請在[發佈時](#publish)使用 `--publish-local-settings` 參數，以確保這些設定會新增至 Azure 中的函式應用程式。

沒有針對 **AzureWebJobsStorage** 設定有效的儲存體連接字串時，系統會顯示下列錯誤訊息：  

>在 local.settings.json 中遺失 AzureWebJobsStorage 的值。 這對 HTTP 以外的所有觸發程序是必要的。 您可以執行 'func azure functionary fetch-app-settings <functionAppName>' 或在 local.settings.json 中指定連接字串。
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>進行應用程式設定

若要設定連接字串的值，您可以執行下列其中一個選項：
* 從 [Azure 儲存體總管](http://storageexplorer.com/) \(英文\) 輸入連接字串。
* 使用下列其中一個命令：

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    這兩個命令都需要先登入 Azure。

<a name="create-func"></a>
## <a name="create-a-function"></a>建立函式

若要建立函式，請執行下列命令：

```
func new
``` 
`func new` 支援下列選擇性引數︰

| 引數     | 說明                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | 範本程式語言，例如 C#、F# 或 JavaScript。 |
| **`--template -t`** | 範本名稱。 |
| **`--name -n`** | 函式名稱。 |

例如，若要建立 JavaScript HTTP 觸發程序，請執行：

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

若要建立佇列所觸發的函式，請執行：

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>在本機執行函式

若要執行 Functions 專案，請執行 Functions 主機。 主機可允許專案中所有函式的觸發程序：

```
func host start
```

`func host start` 支援下列選項：

| 選項     | 說明                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | 要接聽的本機連接埠。 預設值：7071。 |
| **`--debug <type>`** | 選項為 `VSCode` 和 `VS`： |
| **`--cors`** | 以逗號分隔的 CORS 來源清單，不含空格。 |
| **`--nodeDebugPort -n`** | 要使用的節點偵錯工具連接埠。 預設值：Launch.json 中的值或 5858。 |
| **`--debugLevel -d`** | 主控台追蹤層級 (off、verbose、info、warning 或 error)。 預設：info。|
| **`--timeout -t`** | Functions 主機要啟動的逾時 (以秒為單位)。 預設值：20 秒。|
| **`--useHttps`** | 繫結至 https://localhost:{port} 而不是 http://localhost:{port}。 根據預設，此選項會在您的電腦上建立受信任的憑證。|
| **`--pause-on-error`** | 暫停以在結束處理程序之前取得其他輸入。 當您從整合式開發環境 (IDE) 啟動 Azure Functions Core Tools 時很有用。|

Functions 主機啟動時，它會輸出 HTTP 觸發函式的 URL：

```
Found the following functions:
Host.Functions.MyHttpTrigger

ob host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>在 VS Code 或 Visual Studio 中進行偵錯

若要連結偵錯工具，請傳遞 `--debug` 引數。 若要偵錯 JavaScript 函式，請使用 Visual Studio Code。 對於 C# 函式，請使用 Visual Studio。

若要偵錯 C# 函式，請使用 `--debug vs`。 您也可以使用 [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) \(英文\)。 

若要啟動主機並設定 JavaScript 偵錯，請執行：

```
func host start --debug vscode
```

然後，在 Visual Studio Code 的 [偵錯] 檢視中，選取 [連結至 Azure Functions]。 您可以附加中斷點、檢查變數及逐步執行程式碼。

![使用 Visual Studio Code 進行 JavaScript 偵錯](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>將測試資料傳遞至函式

若要在本機測試您的函式，您要使用 HTTP 要求在本機伺服器上[啟動 Functions 主機](#start)並呼叫端點。 您呼叫的端點取決於函式的類型。 

>[!NOTE]  
> 本主題中的範例使用 cURL 工具，從終端機或命令提示字元傳送 HTTP 要求。 您可以使用您選擇的工具，將 HTTP 要求傳送至本機伺服器。 以 Linux 為基礎的系統預設可以使用 cURL 工具。 在 Windows 上，您必須先下載並安裝 [cURL 工具 (英文)](https://curl.haxx.se/)。

如需測試函式的更多一般資訊，請參閱[在 Azure Functions 中測試程式碼的策略](functions-test-a-function.md)。

#### <a name="http-and-webhook-triggered-functions"></a>HTTP 和 Webhook 觸發的函式

您要呼叫下列端點，以在本機執行 HTTP 和 Webhook 觸發的函式：

    http://localhost:{port}/api/{function_name}

請務必使用 Functions 主機接聽的相同伺服器名稱和連接埠。 啟動 Function 主機時，您會在產生的輸出中看到下列內容。 您可以使用觸發程序支援的任何 HTTP 方法呼叫此 URL。 

下列 cURL 命令從 GET 要求在查詢字串中傳遞 _name_ 參數，觸發 `MyHttpTrigger` 快速入門函式。 

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
下列範例與從 POST 要求在要求本文中傳遞 _name_ 所呼叫的函式相同：

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

請注意，您可以從瀏覽器在查詢字串中傳遞資料來進行 GET 要求。 對於所有其他 HTTP 方法，您必須使用 cURL、Fiddler、Postman 或類似的 HTTP 測試工具。  

#### <a name="non-http-triggered-functions"></a>非 HTTP 觸發函式
對於 HTTP 觸發程序和 Webhook 以外的所有函式類型，您可以呼叫管理端點在本機測試函式。 在本機伺服器上呼叫此端點會觸發函式。 您可以選擇性傳遞測試資料到執行程序。 這項功能類似於 Azure 入口網站中的 [測試] 索引標籤。  

您可以使用 HTTP POST 要求，呼叫下列系統管理員端點來觸發非 HTTP 函式：

    http://localhost:{port}/admin/functions/{function_name}

若要將測試資料傳遞至函式的管理員端點，您必須在 POST 要求訊息的本文中提供資料。 訊息本文必須具備下列 JSON 格式：

```JSON
{
    "input": "<trigger_input>"
}
```` 
`<trigger_input>` 值包含函式預期格式的資料。 下列 cURL 範例是 POST 到 `QueueTriggerJS` 函式。 在此情況下，輸入是一個相當於在佇列中預期找到的訊息字串。      

```
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>使用版本 1.x 中的 `func run` 命令

>[!IMPORTANT]  
> 工具的版本 2.x 不支援 `func run` 命令。 如需詳細資訊，請參閱[如何設定 Azure Functions 執行階段版本目標](functions-versions.md)主題。

您也可以使用 `func run <FunctionName>` 直接叫用函式，並為函式提供輸入資料。 此命令類似於使用 Azure 入口網站中的 [測試] 索引標籤執行函式。 

`func run` 支援下列選項：

| 選項     | 說明                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | 內嵌內容。 |
| **`--debug -d`** | 在執行函式之前，請先將偵錯工具附加到主機處理序。|
| **`--timeout -t`** | 本機 Functions 主機就緒前的等候時間 (以秒為單位)。|
| **`--file -f`** | 要用來作為內容的檔案名稱。|
| **`--no-interactive`** | 不會提示輸入。 適用於自動化情節。|

例如，若要呼叫 HTTP 觸發的函式並傳遞內容的內文，請執行下列命令：

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>發佈至 Azure

若要將 Functions 專案發佈至 Azure 中的函式應用程式，請使用 `publish` 命令：

```
func azure functionapp publish <FunctionAppName>
```

您可以使用下列選項：

| 選項     | 說明                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  將 local.settings.json 中的設定發佈至 Azure，若設定已經存在，則提示進行覆寫。|
| **`--overwrite-settings -y`** | 必須與 `-i` 搭配使用。 使用本機值在 Azure 中覆寫 AppSettings (如果不同)。 預設值為提示。|

此命令會發行至 Azure 中的現有函式應用程式。 訂用帳戶中沒有 `<FunctionAppName>` 時，會發生錯誤。 若要了解如何使用 Azure CLI 從命令提示字元或終端機視窗建立函式應用程式，請參閱[建立無伺服器也可執行的函式應用程式](./scripts/functions-cli-create-serverless.md)。

`publish` 命令會將 Functions 專案目錄的內容上傳。 如果您在本機將檔案刪除，`publish` 命令並不會從 Azure 刪除它們。 您可以使用 [Azure 入口網站] 中的 [Kudu 工具](functions-how-to-use-azure-function-app-settings.md#kudu)來刪除 Azure 中的檔案。  

>[!IMPORTANT]  
> 當您在 Azure 中建立函式應用程式時，預設會使用 1.x 版的 Function 執行階段。 若要讓函式應用程式使用 2.x 版的執行階段，請新增應用程式設定 `FUNCTIONS_EXTENSION_VERSION=beta`。  
使用下列 Azure CLI 程式碼，將這個設定新增至函式應用程式： 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>後續步驟

Azure Functions Core Tools 是[開放原始碼且裝載於 GitHub 上](https://github.com/azure/azure-functions-cli)。  
若要提出錯誤或功能要求，[請開啟 GitHub 問題](https://github.com/azure/azure-functions-cli/issues)。 

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure 入口網站]: https://portal.azure.com 
