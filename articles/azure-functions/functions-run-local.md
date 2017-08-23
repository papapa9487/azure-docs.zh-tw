---
title: "開發 Azure 函數並在本機執行 | Microsoft Docs"
description: "在於 Azure Functions 上執行 Azure 函式之前，先了解如何撰寫 Azure 函式並在本機電腦上進行測試。"
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 07/12/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 07ad15c61bd4b3912dfa2f629218deebdebd6dc8
ms.contentlocale: zh-tw
ms.lasthandoff: 07/14/2017

---
# <a name="code-and-test-azure-functions-locally"></a>撰寫 Azure 函式並在本機進行測試

雖然「Azure 入口網站」[]有提供開發及測試 Azure Functions 的完整工具集，有許多開發人員仍偏好本機開發體驗。 Azure Functions 可讓您輕鬆使用最喜愛的程式碼編輯器及本機開發工具，在本機電腦上開發並測試您的函式。 您的函式可以透過 Azure 中的事件觸發，您也可以在本機電腦上對 C# 和 JavaScript 函式進行偵錯。 

如果您是 Visual Studio C# 開發人員，Azure Functions 也能[與 Visual Studio 2017 整合](functions-develop-vs.md)。

## <a name="install-the-azure-functions-core-tools"></a>安裝 Azure Functions Core Tools

Azure Functions Core Tools 是 Azure Functions 執行階段的本機版本，可讓您在本機 Windows 電腦上執行。 它不是模擬器。 它與在 Azure 中提供 Functions 的執行階段是相同的執行階段。

[Azure Functions Core Tools] 是以 npm 套件的形式提供。 您必須先[安裝 NodeJS](https://docs.npmjs.com/getting-started/installing-node) \(英文\)，它將會包含 npm。  

>[!NOTE]
>目前 Azure Functions Core Tools 套件只能安裝在 Windows 電腦上。 此限制是因為 Functions 主機的暫時性限制所造成。

[Azure Functions Core Tools]新增了下列命令別名：
* **func**
* **azfun**
* **azurefunctions**

這些別名都可以用來取代本主題範例中所顯示的 `func`。

## <a name="create-a-local-functions-project"></a>建立本機的 Functions 專案

在本機執行時，Functions 專案是具有 host.json 和 local.settings.json 檔案的目錄。 此目錄相當於 Azure 中的函式應用程式。 若要深入了解 Azure Functions 的資料夾結構，請參閱 [Azure Functions 的開發人員指南](functions-reference.md#folder-structure)。

在命令提示字元中，執行下列命令：

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

若要選擇不建立 Git 存放庫，請使用 `--no-source-control [-n]` 選項。

<a name="local-settings"></a>

## <a name="local-settings-file"></a>本機設定檔

local.settings.json 檔案會儲存應用程式設定、連接字串和 Azure Functions Core Tools 的設定。 其結構如下：

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>", 
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
| **值** | 於本機執行時使用的應用程式設定集合。 請將您的應用程式設定新增至此物件。  |
| **AzureWebJobsStorage** | 設定針對由 Azure Functions 執行階段於內部使用之 Azure 儲存體帳戶的連接字串。 該儲存體帳戶支援您函式的觸發程序。 所有函式都必須設定此儲存體帳戶連接字串 (由 HTTP 觸發的函式除外)。  |
| **AzureWebJobsDashboard** | 設定針對用來儲存函式記錄之 Azure 儲存體帳戶的連接字串。 此選擇性值能使記錄可在入口網站中存取。|
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

若要設定連接字串的值，您可以執行下列其中一項：
* 從 [Azure 儲存體總管](http://storageexplorer.com/) \(英文\) 輸入連接字串。
* 使用下列其中一個命令：

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure functionapp storage fetch-connection-string <StorageAccountName>
    ```
    這兩個命令都需要先登入 Azure。

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

您也可以使用 `func run <FunctionName>` 直接叫用函式，並為函式提供輸入資料。 此命令類似於使用 Azure 入口網站中的 [測試] 索引標籤執行函式。 這個命令會啟動整個 Functions 主機。

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

`publish` 命令會將 Functions 專案目錄的內容上傳。 如果您在本機將檔案刪除，`publish` 命令並不會從 Azure 刪除它們。 您可以使用「Azure 入口網站」[]中的 [Kudu 工具](functions-how-to-use-azure-function-app-settings.md#kudu)來刪除 Azure 中的檔案。

## <a name="next-steps"></a>後續步驟

Azure Functions Core Tools 是[開放原始碼且裝載於 GitHub 上](https://github.com/azure/azure-functions-cli)。  
若要提出錯誤或功能要求，[請開啟 GitHub 問題](https://github.com/azure/azure-functions-cli/issues)。 

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure 入口網站]: https://portal.azure.com 

