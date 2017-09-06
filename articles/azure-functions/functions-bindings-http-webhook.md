---
title: "Azure Functions HTTP 和 Webhook 繫結 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 HTTP 和 Webhook 觸發程序與繫結。"
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, webhook, 動態計算, 無伺服器架構, HTTP, API, REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: mahender
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: cac0f437cee86aa933763e5133ac1a0e892ffb52
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-http-and-webhook-bindings"></a>Azure Functions HTTP 和 Webhook 繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

這篇文章說明如何在 Azure Functions 中設定並使用 HTTP 觸發程序和繫結。
利用這些資訊，您就可以使用 Azure Functions 建置無伺服器 API 並回應 Webhook。

Azure Functions 提供下列繫結：
- [HTTP 觸發程序](#httptrigger)可讓您透過 HTTP 要求叫用函式。 您可以將它自訂來回應 [Webhook](#hooktrigger)。
- [HTTP 輸出繫結](#output)可讓您回應要求。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

<a name="httptrigger"></a>

## <a name="http-trigger"></a>HTTP 觸發程序
HTTP 觸發程序會執行您的函式，以回應 HTTP 要求。 您可以自訂它來回應特定的 URL 或一組 HTTP 方法。 也可以設定 HTTP 觸發程序來回應 Webhook。 

如果使用 Functions 入口網站，您也可以使用預先製作的範本立即開始。 選取 [新函式] 然後從 [案例] 下拉式清單選擇 [API 與 Webhook]。 選取其中一個範本，然後按一下 [建立]。

根據預設，HTTP 觸發程序會以 HTTP 200 OK 的狀態碼和空白主體來回應要求。 若要修改回應，請設定 [HTTP 輸出繫結](#output)

### <a name="configuring-an-http-trigger"></a>設定 HTTP 觸發程序
HTTP 觸發程序是藉由 function.json 的 `bindings` 陣列中之 JSON 物件來定義，如下列範例所示：

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "get" ],
    "route": "values/{id}"
},
```
繫結支援下列屬性：

|屬性  |說明  |
|---------|---------|
| **name** | 必要項目 - 函式程式碼中用於要求或要求主體的變數名稱。 請參閱[從程式碼使用 HTTP 觸發程序](#httptriggerusage)。 |
| **type** | 必要項目 - 必須設定為 `httpTrigger`。 |
| **direction** | 必要項目 - 必須設定為 `in`。 |
| **authLevel** | 會判斷要求中必須存在哪些金鑰 (若有的話) 才能叫用函式。 值可以是下列值其中之一： <ul><li><code>anonymous</code>&mdash;不需要 API 金鑰。</li><li><code>function</code>&mdash;需要函式專屬的 API 金鑰。 如果沒有提供任何值，此為預設值。</li><li><code>admin</code>&mdash;需要主要金鑰。</li></ul> 如需詳細資訊，請參閱[使用金鑰](#keys)。 |
| **methods** | 函式將回應的 HTTP 方法陣列。 如果未指定，函式將會回應所有的 HTTP 方法。 請參閱[自訂 HTTP 端點](#url)。 |
| **route** | 會定義路由範本，從而控制函式所要回應的要求 URL。 如果沒有提供任何值，預設值為 `<functionname>`。 如需詳細資訊，請參閱[自訂 HTTP 端點](#url)。 |
| **webHookType** | 會設定 HTTP 觸發程序作為指定提供者的 Webhook 接收器。 使用這項設定時，請勿使用 _methods_ 屬性。 值可以是下列值其中之一：<ul><li><code>genericJson</code>&mdash;一般用途的 Webhook 端點，不需要特定提供者的邏輯。</li><li><code>github</code>&mdash;函式會回應 GitHub Webhook。 使用這個值時，請勿使用 _authLevel_ 屬性。</li><li><code>slack</code>&mdash;函式會回應 Slack Webhook。 使用這個值時，請勿使用 _authLevel_ 屬性。</li></ul> 如需詳細資訊，請參閱[回應 Webhook](#hooktrigger)。 |

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>從程式碼使用 HTTP 觸發程序
針對 C# 和 F# 函式，您可以將觸發程序輸入的類型宣告為 `HttpRequestMessage` 或自訂 .NET 類型。 如果您選擇 `HttpRequestMessage`，就會取得要求物件的完整存取權。 對於自訂的 .NET 類型，函式會嘗試剖析 JSON 要求主體來設定物件屬性。 

對於 Node.js 函式，Functions 執行階段提供要求主體而非要求物件。 如需詳細資訊，請參閱 [HTTP 觸發程序範例](#httptriggersample)。


<a name="output"></a>
## <a name="http-response-output-binding"></a>HTTP 回應輸出繫結
使用 HTTP 輸出繫結來回應 HTTP 要求傳送者。 此繫結需要 HTTP 觸發程序，並可讓您自訂與觸發程序要求相關聯的回應。 如果沒有提供 HTTP 輸出繫結，HTTP 觸發程序將會傳回 HTTP 200 OK 和空白主體。 

### <a name="configuring-an-http-output-binding"></a>設定 HTTP 輸出繫結
HTTP 輸出繫結是藉由 function.json 的 `bindings` 陣列中之 JSON 物件來定義，如下列範例所示：

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
繫結支援下列必要的屬性：

|屬性  |說明  |
|---------|---------|
|**name** | 函式程式碼中用於回應的變數名稱。 請參閱[從程式碼使用 HTTP 輸出繫結](#outputusage)。 |
| **type** |必須設為 `http`。 |
| **direction** | 必須設為 `out`。 |

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>從程式碼使用 HTTP 輸出繫結
您可以使用輸出參數來回應 http 或 Webhook 呼叫端。 您也可以使用語言標準回應模式。 如需範例回應，請參閱 [HTTP 觸發程序範例](#httptriggersample)和 [Webhook 觸發程序範例](#hooktriggersample)。


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>回應 Webhook
含有 _webHookType_ 屬性的 HTTP 觸發程序將會設定為回應 [Webhook](https://en.wikipedia.org/wiki/Webhook)。 基本組態會使用 "genericJson" 設定。 這會將要求限制為只有那些使用 HTTP POST 和包含 `application/json` 內容類型的要求。

觸發程序可另外針對特定的 Webhook 提供者進行調整，例如 [GitHub](https://developer.github.com/webhooks/) 或 [Slack](https://api.slack.com/outgoing-webhooks)。 當您指定提供者時，Functions 執行階段就可為您處理提供者的驗證邏輯。  

### <a name="configuring-github-as-a-webhook-provider"></a>將 GitHub 設定為 Webhook 提供者
若要回應 GitHub Webhook，請先建立含有 HTTP 觸發程序的函式，然後將 **webHookType** 屬性設定為 `github`。 接著將其 [URL](#url) 和 [API 金鑰](#keys) 複製到您 GitHub 存放庫的 [新增 Webhook] 頁面。 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

如需範例，請參閱[建立由 GitHub Webhook 所觸發的函式](functions-create-github-webhook-triggered-function.md)。

### <a name="configuring-slack-as-a-webhook-provider"></a>將 Slack 設定為 Webhook 提供者
Slack webhook 會為您產生權杖，而不是由您指定，因此您必須使用 Slack 的權杖來設定函式專屬的金鑰。 請參閱[使用金鑰](#keys)。

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>自訂 HTTP 端點
根據預設，當您為 HTTP 觸發程序或 WebHook 建立函式時，將可藉由下列形式的路由來定址該函式：

    http://<yourapp>.azurewebsites.net/api/<funcname> 

您可以在 HTTP 觸發程序的輸入繫結上使用選擇性的 `route` 屬性來自訂此路由。 舉例來說，下列 *function.json* 檔案定義了 HTTP 觸發程序的 `route` 屬性：

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

在使用此設定的情況下，現在便可使用下列路由來定址該函式，而不需使用原始路由。

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

這可讓該函式程式碼在位址中支援兩個參數，即 _category_ 和 _id_。您可以將任何 [Web API 路由條件約束](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)與您的參數搭配使用。 下列 C# 函式程式碼會同時利用這兩個參數。

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

以下是使用相同路由參數的 Node.js 函式程式碼。

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

所有函式路由預設前面都會加上 *api*。 您也可以在 *host.json* 檔案中使用 `http.routePrefix` 屬性來自訂或移除前置詞。 下列範例會在 *host.json* 檔案中使用空字串作為前置詞來移除 *api* 路由前置詞。

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

如需有關如何更新您函數 *host.json* 檔案的詳細資訊，請參閱[如何更新函數應用程式檔案](functions-reference.md#fileupdate)。 

如需有關您可以在 *host.json* 檔案中設定之其他屬性的資訊，請參閱 [host.json 參考](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)。


<a name="keys"></a>
## <a name="working-with-keys"></a>使用金鑰
HTTP 觸發程序可讓您使用金鑰來提高安全性。 標準 HTTP 觸發程序可以使用這些金鑰作為 API 金鑰，要求金鑰必須存在於要求上。 Webhook 可以以多種方式使用金鑰授權要求，視提供者支援的方式而定。

金鑰會當作您函數應用程式的一部分儲存於 Azure 中，並在加密後靜置。 若要檢視您的金鑰，請建立新的金鑰或將金鑰輪替為新的值，瀏覽至入口網站中您的其中一個函式，然後選取 [管理]。 

金鑰類型有兩種：
- **主機金鑰**：這些金鑰由函數應用程式中所有的函式共用。 當做為 API 金鑰使用時，這些金鑰會允許存取函數應用程式中的任何函式。
- **函式金鑰**：這些金鑰僅適用於據以定義它們的特定函式。 當做為 API 金鑰使用時，這些金鑰僅允許存取該函式。

每個金鑰均為具名以供參考，並且在函式和主機層級有一預設金鑰 (名稱為 "default")。 「主要金鑰」是預設的主機金鑰，名稱為 "_master"，它是針對每個函式應用程式所定義。 無法撤銷此金鑰。 它會提供執行階段 API 的系統管理存取權。 在繫結 JSON 中使用 `"authLevel": "admin"` 會要求此金鑰必須存在於要求上；任何其他金鑰將會導致授權失敗。

> [!IMPORTANT]  
> 由於主要金鑰會授與提高的權限，因此您不應該與第三方共用此金鑰，或是將它散發到原生用戶端應用程式。 當您選擇管理授權層級時，請務必謹慎。

### <a name="api-key-authorization"></a>API 金鑰授權
根據預設，HTTP 觸發程序會在 HTTP 要求中要求 API 金鑰。 因此您的 HTTP 要求通常看起來會像這樣：

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

金鑰可包含在名為 `code` 的查詢字串變數中 (如上所述)，或是包含在 `x-functions-key` HTTP 標頭中。 金鑰的值可以是針對函式定義的任何函式金鑰，或是任何主機金鑰。

您可以允許匿名要求，這不需要金鑰。 您也可以要求使用主要金鑰。 您可以使用繫結 JSON 中的 `authLevel` 屬性來變更預設授權層級。 如需詳細資訊，請參閱 [HTTP 觸發程序](#httptrigger)。

### <a name="keys-and-webhooks"></a>金鑰和 Webhook
Webhook 授權是由 Webhook 接收器元件 (HTTP 觸發程序的一部分) 處理，處理機制則會以 Webhook 類型作為基礎而有所不同。 不過，每個機制都依賴金鑰。 根據預設，將會使用名稱為 "default" 的函式金鑰。 如需使用不同的金鑰，請設定 Webhook 提供者以下列其中一種方式將金鑰名稱隨著要求一起傳送：

- **查詢字串**：提供者會在 `clientid` 查詢字串參數中傳遞金鑰名稱，例如 `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`。
- **要求標頭**︰提供者在 `x-functions-clientid` 標頭中傳遞金鑰名稱。

> [!NOTE]
> 函式金鑰的優先順序高於主機金鑰。 當您使用相同的名稱來定義兩個金鑰時，一律會使用函式金鑰。


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>HTTP 觸發程序範例
假設您 function.json 的 `bindings` 陣列中有下列 HTTP 觸發程序：

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

請參閱在查詢字串或 HTTP 要求主體中尋找 `name` 參數的語言特定範例。

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.js](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>C# 中的 HTTP 觸發程序範例 #
```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

您也繫結至 .NET 物件，而不是 **HttpRequestMessage**。 會從要求主體建立這個物件，並剖析成 JSON。 同樣地，類型可以傳遞至 HTTP 回應輸出繫結，並加以傳回作為狀態碼 200 的回應主體。

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>F# 中的 HTTP 觸發程序範例 #
```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

您需要 `project.json` 檔案，以使用 NuGet 來參考 `FSharp.Interop.Dynamic` 和 `Dynamitey` 組件，如下所示：

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

這會使用 NuGet 來擷取相依性，並在指令碼中加以參考。

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>Node.JS 中的 HTTP 觸發程序範例
```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```
     
<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>Webhook 範例
假設您 function.json 的`bindings` 陣列中有下列 Webhook 觸發程序︰

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

請參閱會記錄 GitHub 問題註解的語言特定範例。

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.js](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>C# 中的 Webhook 範例 #
```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>F# 中的 Webhook 範例 #
```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>Node.JS 中的 Webhook 範例
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


