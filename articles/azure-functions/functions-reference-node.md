---
title: "適用於 Azure Functions 的 JavaScript 開發人員參考 | Microsoft Docs"
description: "了解如何使用 JavaScript 開發函式。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構"
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: a20f6db8bbbc9b7936cf102e8cd2ff1b2a995fb2
ms.contentlocale: zh-tw
ms.lasthandoff: 09/29/2017

---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 開發人員指南
> [!div class="op_single_selector"]
> * [C# 指令碼](functions-reference-csharp.md)
> * [F# 指令碼](functions-reference-fsharp.md)
> * [JavaScript](functions-reference-node.md)
> 
> 

Azure Functions 的 JavaScript 體驗能讓您輕鬆地匯出函式，系統會以 `context` 物件的形式傳遞函式，以便與執行階段通訊，以及透過繫結接收和傳送資料。

本文假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)。

## <a name="exporting-a-function"></a>匯出函數
所有 JavaScript 函式都必須透過 `module.exports` 匯出單一 `function`，如此執行階段才能找到函式並執行它。 此函式一定要包含 `context` 物件。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

`direction === "in"` 的繫結會和函式引數一起傳遞，這表示您可以使用 [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) 以動態方式處理新的輸入 (例如，藉由使用 `arguments.length` 來反覆查看您的所有輸入)。 當您只有單一觸發程序而沒有其他輸入時，這項功能十分便利，因為您可以如預期般存取觸發程序資料，而不需要參考 `context` 物件。

引數一律會以它們在 *function.json* 中出現的順序傳遞至函式，即使您未在匯出陳述式中指定它們也一樣。 例如，如果您有 `function(context, a, b)` 並將它變更為 `function(context, a)`，您仍然可以在函式程式碼中藉由參考 `arguments[3]` 來取得 `b` 的值。

所有繫結 (不論方向為何) 也都會在 `context` 物件上傳遞 (請參閱下列指令碼)。 

## <a name="context-object"></a>context 物件
執行階段使用 `context` 物件來將資料傳遞至函式並從中傳出，而且可讓您與執行階段進行通訊。

內容物件一律為函式的第一個參數而且一律必須包含，因為它具有像是 `context.done` 和 `context.log` 的方法，而您必須要有這些方法才能正確地使用執行階段。 您可以任意方式命名物件 (例如 `ctx` 或 `c`)。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>context.bindings 屬性

```
context.bindings
```
傳回包含所有輸入和輸出資料的具名物件。 例如，*function.json* 中的下列繫結定義可讓您從 `context.bindings.myInput` 物件存取佇列的內容。 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

### <a name="contextdone-method"></a>context.done 方法
```
context.done([err],[propertyBag])
```

通知執行階段，您的程式碼已完成。 您必須呼叫 `context.done`，否則執行階段不會知道您的函式已完成，而且執行會逾時。 

`context.done` 方法可讓您執行下列兩個動作：將使用者定義的錯誤傳回執行階段，以及傳回會覆寫 `context.bindings` 物件上屬性之屬性的屬性包。

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>context.log 方法  

```
context.log(message)
```
可讓您寫入預設追蹤層級的資料流主控台記錄。 `context.log` 上有其他可用的記錄方法，可讓您在其他追蹤層級寫入主控台記錄中︰


| 方法                 | 說明                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | 寫入錯誤層級或更低層級的記錄。   |
| **warn(_message_)**    | 寫入警告層級或更低層級的記錄。 |
| **info(_message_)**    | 寫入資訊層級或更低層級的記錄。    |
| **verbose(_message_)** | 寫入詳細資訊層級記錄。           |

下列範例會依警告追蹤層級寫入主控台︰

```javascript
context.log.warn("Something has happened."); 
```
您可以在 host.json 檔案中設定用於記錄的追蹤層級閾值，或將它關閉。  如需如何寫入記錄的詳細資訊，請參閱下一節。

## <a name="binding-data-type"></a>繫結資料類型

若要定義輸入繫結的資料類型，請使用繫結定義中的 `dataType` 屬性。 例如，若要以二進位格式讀取 HTTP 要求的內容，請使用類型 `binary`：

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` 也另具有 `stream` 和 `string` 兩種選項。

## <a name="writing-trace-output-to-the-console"></a>將追蹤輸出寫入主控台中 

在 Functions 中，您可以使用 `context.log` 方法，將追蹤輸出寫入主控台中。 此時，您不能使用 `console.log` 來寫入主控台中。

當您呼叫 `context.log()` 時，會在預設追蹤層級 (也就是「資訊」追蹤層級) 將您的訊息寫入主控台中。 下列程式碼會依資訊追蹤層級寫入主控台中︰

```javascript
context.log({hello: 'world'});  
```

上述程式碼相當於下列程式碼：

```javascript
context.log.info({hello: 'world'});  
```

下列程式碼會依錯誤層級寫入主控台中︰

```javascript
context.log.error("An error has occurred.");  
```

因為 _error_ 是最高追蹤層級，所以只要啟用記錄，這項追蹤就會寫入所有追蹤層級的輸出。  


所有 `context.log` 方法都與 Node.js [util.format 方法 (英文)](https://nodejs.org/api/util.html#util_util_format_format) 支援相同的參數格式。 請考慮下列程式碼，它會使用預設追蹤層級寫入至主控台︰

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

您也能以下列格式撰寫相同的程式碼：

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>設定用於主控台記錄的追蹤層級

Functions 讓您定義寫入至主控台的閾值追蹤層級，這可讓您輕鬆地控制追蹤從函式寫入主控台的方式。 若要設定寫入至主控台之所有追蹤的閾值，請使用 host.json 檔案中的 `tracing.consoleLevel` 屬性。 這個設定會套用到函式應用程式中的所有函式。 下列範例會設定追蹤閾值來啟用詳細資訊記錄︰

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

**consoleLevel** 的值對應至 `context.log` 方法的名稱。 若要停用主控台的所有追蹤記錄，請將 **consoleLevel** 設為 _off_。 如需詳細資訊，請參閱 [host.json 參考](functions-host-json.md)。

## <a name="http-triggers-and-bindings"></a>HTTP 觸發程序和繫結

HTTP 和 Webhook 觸發程序以及 HTTP 輸出繫結會使用要求和回應物件來代表 HTTP 傳訊。  

### <a name="request-object"></a>要求物件

`request` 物件具有下列屬性：

| 屬性      | 說明                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 包含要求本文的物件。               |
| _headers_     | 包含要求標頭的物件。                   |
| _method_      | 要求的 HTTP 方法。                                |
| _originalUrl_ | 要求的 URL。                                        |
| _params_      | 包含要求之路由傳送參數的物件。 |
| _查詢_       | 包含查詢參數的物件。                  |
| _rawBody_     | 字串格式的訊息內文。                           |


### <a name="response-object"></a>回應物件

`response` 物件具有下列屬性：

| 屬性  | 說明                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 包含回應本文的物件。         |
| _headers_ | 包含回應標頭的物件。             |
| _isRaw_   | 表示略過回應的格式。    |
| _狀態_  | 回應的 HTTP 狀態碼。                     |

### <a name="accessing-the-request-and-response"></a>存取要求和回應 

使用 HTTP 觸發程序時，您可以使用三種方式來存取 HTTP 要求和回應物件︰

+ 從具名輸入和輸出繫結。 如此一來，HTTP 觸發程序和繫結的運作方式會與任何其他繫結相同。 下列範例會使用具名 `response` 繫結來設定回應物件： 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ 從 `context` 物件的 `req` 和 `res` 屬性中。 如此一來，您可以使用傳統模式來存取內容物件中的 HTTP 資料，而不需使用完整 `context.bindings.name` 模式。 下列範例示範如何存取 `context` 上的 `req` 和 `res` 物件：

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ 藉由呼叫 `context.done()`。 特殊類型的 HTTP 繫結，會傳回傳遞到 `context.done()` 方法的回應。 下列 HTTP 輸出繫結定義 `$return` 輸出參數︰

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    此輸出繫結需要您在呼叫 `done()` 時提供回應，如下所示：

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Node 版本和套件管理
Node 版本目前鎖定在 `6.5.0`。 我們正在調查加入更多版本並允許設定的支援。

下列步驟可讓您在函式應用程式中納入套件︰ 

1. 移至 `https://<function_app_name>.scm.azurewebsites.net`。

2. 按一下 [偵錯主控台] > [CMD]。

3. 移至 `D:\home\site\wwwroot`，然後將 package.json 檔案拖曳至頁面上半部的 **wwwroot** 資料夾。  
    您也可以使用其他方法將檔案上傳至函數應用程式。 如需詳細資訊，請參閱[如何更新函式應用程式檔案](functions-reference.md#fileupdate)。 

4. 上傳 package.json 檔案之後，請在 **Kudu 遠端執行主控台**中執行 `npm install` 命令。  
    此動作會下載 package.json 檔案中指出的套件，並重新啟動函數應用程式。

安裝您需要的套件之後，請呼叫 `require('packagename')` 將它們匯入您的函式，如下列範例所示：

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

您應該定義函式應用程式根目錄的 `package.json` 檔案。 定義檔案可讓應用程式中的所有函式共用相同的快取套件，以提供最佳效能。 發生版本衝突時，您可以在特定函式的資料夾中新增 `package.json` 檔案來解決它。  

## <a name="environment-variables"></a>環境變數
若要取得環境變數或應用程式設定值，請使用 `process.env`，如下列程式碼範例所示：

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```
## <a name="considerations-for-javascript-functions"></a>JavaScript 函式的考量

當您使用 JavaScript 函式時，請留意下列兩節中的考量事項。

### <a name="choose-single-core-app-service-plans"></a>選擇單一核心 App Service 方案

當您建立使用 App Service 方案的函數應用程式時，建議您選取單一核心方案，而非具有多核心的方案。 目前 Functions 在單一核心 VM 上執行 JavaScript 函式會較有效率，而使用較大的 VM 並不會產生預期的效能改進。 如有必要，您可以新增更多單一核心 VM 執行個體來手動進行相應放大，或者您可以啟用自動規模調整。 如需詳細資訊，請參閱[手動或自動調整執行個體計數規模](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)。    

### <a name="typescript-and-coffeescript-support"></a>TypeScript 和 CoffeeScript 支援
由於目前仍沒有針對透過執行階段自動編譯 TypeScript 或 CoffeeScript 的直接支援，因此需要在部署時期於執行階段之外處理這些支援。 

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱下列資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure Functions C# 開發人員參考](functions-reference-csharp.md)
* [Azure Functions F# 開發人員參考](functions-reference-fsharp.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)


