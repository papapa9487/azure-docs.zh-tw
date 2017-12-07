---
title: "在 Azure Functions 中使用觸發程序和繫結"
description: "了解如何在 Azure Functions 中使用觸發程序和繫結，將您的程式碼執行連接到線上事件和雲端服務。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: e3413c9e1055ca9198dae4a467bcf47372ad4ecb
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 觸發程序和繫結概念
Azure Functions 可讓您撰寫程式碼，以透過「觸發程序」和「繫結」回應 Azure 和其他服務中的事件。 此文章是適用於所有支援之程式設計語言的觸發程序和繫結的概念性概觀。 這裡描述所有繫結的通用功能。

## <a name="overview"></a>概觀

觸發程序和繫結是定義叫用函數的方式與其處理之資料的宣告式方法。 「觸發程序」會定義叫用函數的方式。 一個函數只能恰有一個觸發程序。 觸發程序具有相關聯的資料，它通常是觸發函數的承載。

輸入和輸出「繫結」提供從您的程式碼內連線到資料的宣告式方法。 類似於觸發程序，您需要在函數組態中指定連接字串和其他屬性。 繫結是選擇性的，而且一個函數可以有多個輸入和輸出繫結。 

使用觸發程序和繫結，您可以撰寫較泛型的程式碼，而不是以硬式編碼的方式撰寫要互動之服務的詳細資訊。 來自服務的資料會直接成為函數程式碼的輸入值。 若要將資料輸出到其他服務 (例如在 Azure 表格儲存體中建立新的資料列)，請使用方法的傳回值。 或者，如果您需要輸出多個值，請使用協助程式物件。 觸發程序和繫結有 **name** 屬性，它是您在程式碼中用來存取繫結的識別項。

您可以在 Azure Functions 入口網站的 [整合] 索引標籤中設定觸發程序和繫結。 實際上，UI 會修改函數目錄中名稱為 *function.json* 的檔案。 您可以變更為 [進階編輯器] 以編輯這個檔案。

## <a name="supported-bindings"></a>支援的繫結

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

如需哪些繫結為預覽狀態或已核准可用於實際執行環境的資訊，請參閱[支援的語言](supported-languages.md)。

## <a name="example-queue-trigger-and-table-output-binding"></a>範例︰佇列觸發程序和資料表輸出繫結

假設您想要每當新訊息出現在 Azure 佇列儲存體時，就在 Azure 表格儲存體寫入新的資料列。 此案例可以使用 Azure 佇列觸發程序和 Azure 資料表儲存體輸出繫結來實作。 

Azure 佇列儲存體觸發程序需要 [整合] 索引標籤中的下列資訊：

* 包含 Azure 佇列儲存體的 Azure 儲存體帳戶連接字串之應用程式設定的名稱
* 佇列名稱
* 程式碼中讀取佇列訊息內容所需的識別項，例如 `order`。

若要寫入 Azure 表格儲存體，可以使用包含下列詳細資料的輸出繫結：

* 包含 Azure 資料表儲存體的 Azure 儲存體帳戶連接字串之應用程式設定的名稱
* 資料表名稱
* 程式碼中建立輸出項目的識別項，或來自函數的傳回值。

繫結會使用儲存在應用程式設定的連接字串值，來強制執行最佳做法，也就是 function.json 不包含服務密碼，而是只包含應用程式設定的名稱。

然後，在程式碼中使用提供的識別項來與 Azure 儲存體整合。

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

以下是對應上述程式碼的 *function.json*。 請注意，無論函數實作的語言為何，仍可以使用相同的設定。

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```
若要在 Azure 入口網站中檢視及編輯 *function.json* 的內容，請按一下函數的 [整合] 索引標籤上的 [進階編輯器] 選項。

如需程式碼範例及整合 Azure 儲存體的詳細資訊，請參閱 [Azure 儲存體的 Azure Functions 觸發程序和繫結](functions-bindings-storage.md)。

## <a name="binding-direction"></a>繫結方向

所有觸發程序和繫結在 function.json 檔案中都具有 `direction` 屬性：

- 對於觸發程序，方向一律為 `in`
- 輸入和輸出繫結使用 `in` 和 `out`
- 某些繫結支援特殊方向 `inout`。 如果您使用 `inout`，則 [整合] 索引標籤中只有 [進階編輯器] 可供使用。

## <a name="using-the-function-return-type-to-return-a-single-output"></a>使用函數傳回類型來傳回單一輸出

上述範例顯示如何使用函數傳回值，來提供輸出值給繫結，方法是使用特殊名稱參數 `$return` (這只支援有傳回值的語言，例如 C#、JavaScript 和 F#)。如果函數有多個輸出繫結，請只將 `$return` 用於其中一個輸出繫結。 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

下列範例顯示如何在 C#、JavaScript 和 F# 中搭配輸出繫結使用傳回類型。

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>繫結 dataType 屬性

在.NET 中會使用類型定義輸入資料的資料類型。 例如，使用 `string` 繫結至要以二進位格式和自訂類型讀取以還原序列化為 POCO 物件之佇列觸發程序和位元組陣列的文字。

對於 JavaScript 等具有動態類型的語言，則會在繫結定義中使用 `dataType` 屬性。 例如，若要以二進位格式讀取 HTTP 要求的內容，請使用類別 `binary`：

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` 也另具有 `stream` 和 `string` 兩種選項。

## <a name="resolving-app-settings"></a>解析應用程式設定
為了遵循最佳做法，祕密和連接字串應使用應用程式設定來管理，而不是使用組態檔。 這會限制對這些祕密的存取，並保護儲存在公用原始檔控制存放庫的 *function.json*。

當您想要根據環境來變更設定時，應用程式設定也很有用。 例如，在測試環境中，您可能會想要監視不同佇列或 Blob 儲存體容器。

只要某個值是以百分比符號括住 (例如 `%MyAppSetting%`)，就會解析應用程式設定。 請注意，觸發程序和繫結的 `connection` 屬性是特殊案例，且會自動將值解析為應用程式設定。 

下列範例是使用應用程式設定 `%input-queue-name%` 來定義要觸發之佇列的 Azure 佇列儲存體觸發程序。

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

## <a name="trigger-metadata-properties"></a>觸發程序中繼資料屬性

除了觸發程序提供的資料承載 (例如觸發函數的佇列訊息) 之外，許多觸發程序都提供額外的中繼資料值。 這些值可以在 C# 和 F# 中作為輸入參數使用，或在 JavaScript 中做為 `context.bindings` 物件上的屬性使用。 

例如，Azure 儲存體佇列觸發程序支援下列屬性：

* QueueTrigger - 如果是有效字串，便觸發訊息內容
* DequeueCount
* ExpirationTime
* 識別碼
* InsertionTime
* NextVisibleTime
* PopReceipt

在對應的參考主題中，會描述每個觸發程序之中繼資料屬性的詳細資料。 您也可以在入口網站的 [整合] 索引標籤中，繫結設定區域之下的 [文件] 區段取得文件。  

例如，因為 Blob 觸發程序會有一些延遲，所以您可以使用佇列觸發程序來執行您的函數 (請參閱 [Blob 儲存體觸發程序](functions-bindings-storage-blob.md#trigger))。 佇列訊息會包含要在其上觸發的 Blob 檔案名稱。 使用 `queueTrigger` 中繼資料屬性，您只要在設定中就能指定此行為，而不需在程式碼中指定。

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

您也可以將來自觸發程序的中繼資料屬性用於其他繫結中的「繫結運算式」，如下節所述。

## <a name="binding-expressions-and-patterns"></a>繫結運算式和模式

觸發程序和繫結其中一個最強大的功能就是「繫結運算式」。 在繫結中，您可以定義模式運算式，並將它用於其他繫結或您的程式碼。 觸發程序中繼資料也可以用於繫結運算式，如上一節中的範例所示。

例如，當您想要調整特定 Blob 儲存體容器中的影像大小時，就如同 [新函數] 頁面中的 [Image Resizer] \(影像大小重新調整器) 範本。 移至 [新函數] -> [語言 C#] -> [案例範例] -> [ImageResizer-CSharp]。 

以下是 *function.json* 定義：

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

請注意，`filename` 參數用於 Blob 觸發程序定義以及 Blob 輸出繫結。 這個參數也可以用於函數程式碼。

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->


### <a name="random-guids"></a>隨機 GUID
Azure Functions 透過 `{rand-guid}` 繫結運算式，提供在繫結中產生 GUID 的便利語法。 下列範例使用此語法產生唯一的 Blob 名稱： 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>目前時間

您可以使用繫結運算式 `DateTime`，系統會將其解析為 `DateTime.UtcNow`。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>在繫結運算式中繫結到自訂輸入屬性

繫結運算式也可以參考在其觸發程序承載中定義的屬性。 例如，您可能想要從在 Webhook 中提供的檔案名稱動態地繫結到 Blob 儲存體檔案。

例如，下列 *function.json* 使用來自觸發程序承載且稱為 `BlobName` 的屬性：

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

若要在 C# 和 F# 中完成此動作，您必須定義一個 POCO，以定義將在觸發程序承載中還原序列化的欄位。

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

在 JavaScript 中，會自動執行 JSON 還原序列化，且您可以直接使用該屬性。

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

## <a name="configuring-binding-data-at-runtime"></a>在執行階段設定繫結資料

在 C# 和其他 .NET 語言中，您可以使用相對於 function.json 中宣告式繫結的命令式繫結模式。 當繫結參數需要在執行階段而不是設計階段中計算時，命令式繫結非常有用。 若要深入了解，請參閱 C# 開發人員參考中的[在執行階段透過命令式繫結進行繫結](functions-reference-csharp.md#imperative-bindings)。

## <a name="next-steps"></a>後續步驟
如需特定繫結的詳細資訊，請參閱下列文章：

- [HTTP 和 Webhook](functions-bindings-http-webhook.md)
- [計時器](functions-bindings-timer.md)
- [佇列儲存體](functions-bindings-storage-queue.md)
- [Blob 儲存體](functions-bindings-storage-blob.md)
- [資料表儲存體](functions-bindings-storage-table.md)
- [事件中樞](functions-bindings-event-hubs.md)
- [服務匯流排](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-documentdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [通知中樞](functions-bindings-notification-hubs.md)
- [行動應用程式](functions-bindings-mobile-apps.md)
- [外部檔案](functions-bindings-external-file.md)
