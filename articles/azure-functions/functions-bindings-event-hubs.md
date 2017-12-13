---
title: "Azure Functions 的 Azure 事件中樞繫結"
description: "了解如何在 Azure Functions 中使用 Azure 事件中樞繫結。"
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: wesmc
ms.openlocfilehash: 5e0ff1b98be73eb5990601ae7c5528e4a7af670b
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Functions 的 Azure 事件中樞繫結

本文說明如何針對 Azure Functions 使用 [Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)繫結。 Azure Functions 支援事件中樞的觸發程序和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>觸發程序

使用事件中樞觸發程序將回應傳送至事件中樞事件資料流。 您必須具有事件中樞的讀取存取權，才能設定觸發程序。

事件中樞觸發程序函式觸發時，觸發它的訊息會以字串形式傳遞至函式。

## <a name="trigger---scaling"></a>觸發程序 - 調整規模

事件中樞所觸發之函式的每個執行個體只能由 1 個 EventProcessorHost (EPH) 執行個體來支援。 事件中樞會確保只有 1 個 EPH 可以在指定的分割區上取得租用。

例如，假設我們開始進行下列設定，並假設事件中樞具有：

1. 10 個分割區。
1. 1000 個平均散佈於所有分割區上的事件 => 每個分割區中有 100 個訊息。

當您的函式首次啟用時，只會有 1 個函式執行個體。 讓我們將這個函式執行個體稱為 Function_0。 Function_0 將具有 1 個會設法在所有 10 個分割區上取得租用的 EPH。 它將會開始讀取分割區 0-9 的事件。 從這裡開始，將發生下列其中一件事：

* **只需要 1 個函式執行個體**：Function_0 能夠在 Azure Functions 的規模調整邏輯開始生效之前完全處理 1000 個。 因此，Function_0 會完全處理 1000 個訊息。

* **新增另 1 個函式執行個體**：Azure Functions 的規模調整邏輯判斷 Function_0 所擁有的訊息數目比它可處理的還多，因此會建立新的執行個體 Function_1。 事件中樞偵測到新的 EPH 執行個體正在嘗試讀取訊息。 事件中樞將開始在 EPH 執行個體之間進行分割區的負載平衡，例如，將分割區 0-4 指派給 Function_0，並將分割區 5-9 指派給 Function_1。 

* **新增另 N 個函式執行個體**：Azure Functions 的規模調整邏輯判斷 Function_0 和 Function_1 所擁有的訊息數目比它們可處理的還多。 它將再次進行規模調整，以建立 Function_2…N，其中 N 大於事件中樞分割區數目。 事件中樞將在 Function_0...9 執行個體之間進行分割區的負載平衡。

Azure Functions 目前規模調整邏輯特有的事實是 N 大於分割區數目。 這樣做是為了確保一律會有 EPH 的執行個體可方便用來在分割區變成可從其他執行個體使用時，快速取得分割區上的鎖定。 使用者只需針對函式執行個體執行時所使用的資源付費，不需要針對這個過度佈建付費。

如果所有函式執行都成功且未發生錯誤，就會將檢查點新增至相關聯的儲存體帳戶。 當檢查點檢查成功時，應該永遠不會再次擷取所有的 1000 個訊息。

## <a name="trigger---example"></a>觸發程序 - 範例

查看特定語言的範例：

* [先行編譯 C#](#trigger---c-example)
* [C# 指令碼](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>觸發程序 - C# 範例

下列範例示範的[先行編譯 C#](functions-dotnet-class-library.md) 程式碼可記錄事件中樞觸發程序的訊息本文。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

若要取得事件中繼資料的存取權，請繫結至 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 物件 (`Microsoft.ServiceBus.Messaging` 需要 `using` 陳述式)。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```
若要以批次方式接收事件，請讓 `string` 或 `EventData` 成為陣列：

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>觸發程序 - C# 指令碼範例

下列範例示範 function.json 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式會記錄事件中樞觸發程序的訊息本文。

以下是 *function.json* 檔案中的繫結資料：

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```
以下是 C# 指令碼程式碼：

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

若要取得事件中繼資料的存取權，請繫結至 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 物件 (`Microsoft.ServiceBus.Messaging` 需要 using 陳述式)。

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

若要以批次方式接收事件，請讓 `string` 或 `EventData` 成為陣列：

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>觸發程序 - F# 範例

下列範例示範 function.json 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [F# 函式](functions-reference-fsharp.md)。 此函式會記錄事件中樞觸發程序的訊息本文。

以下是 *function.json* 檔案中的繫結資料：

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

以下是 F# 程式碼：

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>觸發程序 - JavaScript 範例

下列範例示範 function.json 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式會記錄事件中樞觸發程序的訊息本文。

以下是 *function.json* 檔案中的繫結資料：

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

## <a name="trigger---attributes"></a>觸發程序 - 屬性

對於[先行編譯 C#](functions-dotnet-class-library.md) 函式，使用 [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) 屬性，其定義於 NuGet 套件 [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) 中。

此屬性的建構函式接受事件中樞的名稱、取用者群組的名稱，以及包含連接字串的應用程式設定名稱。 如需這些設定的詳細資訊，請參閱[觸發程序設定](#trigger---configuration)一節。 以下是 `EventHubTriggerAttribute` 屬性範例：

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

如需完整範例，請參閱[觸發程序 - 先行編譯 C# 範例](#trigger---c-example)。

## <a name="trigger---configuration"></a>觸發程式 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `EventHubTrigger` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `eventHubTrigger`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中事件項目的變數名稱。 | 
|**路徑** |**EventHubName** | 事件中樞的名稱。 | 
|**consumerGroup** |**ConsumerGroup** | 選擇性屬性，可設定用來訂閱中樞內事件的[取用者群組](../event-hubs/event-hubs-features.md#event-consumers)。 如果省略，則會使用 `$Default` 取用者群組。 | 
|**連接** |**連接** | 應用程式設定的名稱，其中包含事件中樞命名空間的連接字串。 按一下*命名空間*的 [連接資訊] 按鈕 (而不是事件中樞本身)，來複製此連接字串。 此連接字串至少必須具備讀取權限，才能啟動觸發程序。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---hostjson-properties"></a>觸發程序 - host.json 屬性

[host.json](functions-host-json.md#eventhub) 檔案包含可控制事件中樞觸發程序行為的設定。

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>輸出

使用事件中樞輸出繫結將事件寫入事件串流。 您必須具備事件中樞的傳送權限，才能將事件寫入其中。

## <a name="output---example"></a>輸出 - 範例

請參閱特定語言的範例：

* [先行編譯 C#](#output---c-example)
* [C# 指令碼](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>輸出 - C# 範例

下列範例示範的[先行編譯 C# 函式](functions-dotnet-class-library.md)會將訊息寫入事件中樞，並使用方法傳回值作為輸出：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>輸出 - C# 指令碼範例

下列範例示範 function.json 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式會將訊息寫入事件中樞。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

以下是可建立一則訊息的 C# 指令碼程式碼：

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

以下是可建立多則訊息的 C# 指令碼程式碼：

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>輸出 - F# 範例

下列範例示範 function.json 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [F# 函式](functions-reference-fsharp.md)。 此函式會將訊息寫入事件中樞。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

以下是 F# 程式碼：

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>輸出 - JavaScript 範例

下列範例示範 function.json 檔案中的事件中樞觸發程序繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式會將訊息寫入事件中樞。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

以下是可傳送單一訊息的 JavaScript 程式碼：

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

以下是可傳送多則訊息的 JavaScript 程式碼：

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>輸出 - 屬性

對於[先行編譯 C#](functions-dotnet-class-library.md) 函式，使用 [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) 屬性，其定義於 NuGet 套件 [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) 中。

此屬性的建構函式接受事件中樞的名稱，以及包含連接字串的應用程式設定名稱。 如需這些設定的詳細資訊，請參閱[輸入 - 設定](#output---configuration)一節。 以下是 `EventHub` 屬性範例：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

如需完整範例，請參閱[輸出 - 先行編譯 C# 範例](#output---c-example)。

## <a name="output---configuration"></a>輸出 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `EventHub` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設定為 "eventHub"。 |
|**direction** | n/a | 必須設定為 "out"。 當您在 Azure 入口網站中建立繫結時，會自動設定此參數。 |
|**name** | n/a | 函式程式碼中所使用的變數名稱，代表事件。 | 
|**路徑** |**EventHubName** | 事件中樞的名稱。 | 
|**連接** |**連接** | 應用程式設定的名稱，其中包含事件中樞命名空間的連接字串。 按一下*命名空間*的 [連接資訊] 按鈕 (而不是事件中樞本身)，來複製此連接字串。 此連接字串必須具有傳送權限，才能將訊息傳送至事件資料流。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>輸出 - 使用方式

在 C# 和 C# 指令碼中，使用方法參數 (例如 `out string paramName`) 來傳送訊息。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 若要寫入多則訊息，您可以使用 `ICollector<string>` 或 `IAsyncCollector<string>` 取代 `out string`。

在 JavaScript 中，使用 `context.bindings.<name>` 存取輸出事件。 `<name>` 是 function.json 之 `name` 屬性中指定的值。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
