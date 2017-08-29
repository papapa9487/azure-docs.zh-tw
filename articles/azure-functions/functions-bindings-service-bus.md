---
title: "Azure Functions 服務匯流排觸發程序和繫結 | Microsoft Docs"
description: "瞭解如何在 Azure Functions 中使用「Azure 服務匯流排」觸發程序和繫結。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 58b6d5c6ef40891e56ea4811f5e778286bdb8bc3
ms.contentlocale: zh-tw
ms.lasthandoff: 07/13/2017

---
# <a name="azure-functions-service-bus-bindings"></a>Azure Functions 服務匯流排繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

此文章說明如何在 Azure Functions 中設定及使用 Azure 服務匯流排繫結。 

Azure Functions 支援服務匯流排佇列和主題的觸發程序和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>服務匯流排觸發程序
使用服務匯流排觸發程序來回應來自服務匯流排佇列或主題的訊息。 

服務匯流排佇列和主題觸發程序是由 function.json 之 `bindings` 陣列中的 JSON 物件定義的:

* *佇列*觸發程序︰

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* *主題*觸發程序︰

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

請注意：

* 針對 `connection`，請[在您的函數應用程式中建立應用程式設定](functions-how-to-use-azure-function-app-settings.md)，其中包含您「服務匯流排」命名空間的連接字串，然後在觸發程序的 `connection` 屬性中指定應用程式設定的名稱。 您會遵循[取得管理認證](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)中顯示的步驟來取得連接字串。
  連接字串必須是用於服務匯流排命名空間，而不限於特定佇列或主題。
  如果您將 `connection` 保留空白，觸發程序會假設預設的服務匯流排連接字串已在名為 `AzureWebJobsServiceBus` 的應用程式設定中指定。
* 針對 `accessRights`，可用值為 `manage` 和 `listen`。 預設值是 `manage`，這表示 `connection` 已具備**管理**權限。 如果您使用沒有**管理**權限的連接字串，請將 `accessRights` 設定為 `listen`。 否則，Functions 執行階段在嘗試執行需要管理權限的作業時可能會失敗。

## <a name="trigger-behavior"></a>觸發程序行為
* **單一執行緒** - Functions 執行階段預設會並行處理多個訊息。 若要指示執行階段一次只處理一個佇列或主題訊息，請在 *host.json* 檔案中將 `serviceBus.maxConcurrentCalls` 設定為 1。 
  如需有關 *host.json* 的資訊，請參閱[資料夾結構](functions-reference.md#folder-structure)和 [host.json](https://github .com/Azure/azure-webjobs-sdk-script/wiki/host.json) \(英文\)。
* **有害訊息處理** - 服務匯流排會自行處理無法在 Azure Functions 組態或程式碼中控制或設定的有害訊息。 
* **PeekLock behavior** - Functions 執行階段會在 [`PeekLock` 模式](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)下接收訊息，並在函式順利完成時，於訊息上呼叫 `Complete`，或是在函式失敗時呼叫 `Abandon`。 
  如果函數執行時間較 `PeekLock` 逾時還長，即會自動更新鎖定。

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>觸發程序使用方式
本節說明如何在您的函數程式碼中使用「服務匯流排」觸發程序。 

在 C# 和 F# 中，服務匯流排觸發程序訊息可以還原序列化為下列任何一種輸入類型︰

* `string` - 適用於字串訊息
* `byte[]` - 適用於二進位資料
* 任何[物件](https://msdn.microsoft.com/library/system.object.aspx) - 適用於 JSON 序列化的資料。
  如果您宣告自訂輸入類型 (例如 `CustomType`)，Azure Functions 會嘗試將 JSON 資料還原序列化為您指定的類型。
* `BrokeredMessage` - 利用 [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx) 方法提供您還原序列化的訊息。

在 Node.js 中，服務匯流排觸發程序訊息會以字串或 JSON 物件的形式傳遞至函式。

<a name="triggersample"></a>

## <a name="trigger-sample"></a>觸發程序範例
假設您有下列 function.json︰

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

請參閱可處理服務匯流排佇列訊息的特定語言範例。

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C# 中的觸發程序範例 #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F# 中的觸發程序範例 #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js 中的觸發程序範例

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>服務匯流排輸出繫結
函式的服務匯流排佇列和主題輸出會使用 function.json 之 `bindings` 陣列中的下列 JSON 物件︰

* *佇列*輸出︰

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* *主題*輸出︰

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

請注意：

* 針對 `connection`，請[在您的函數應用程式中建立應用程式設定](functions-how-to-use-azure-function-app-settings.md)，其中包含您「服務匯流排」命名空間的連接字串，然後在輸出繫結的 `connection` 屬性中指定應用程式設定的名稱。 您會遵循[取得管理認證](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)中顯示的步驟來取得連接字串。
  連接字串必須是用於服務匯流排命名空間，而不限於特定佇列或主題。
  如果您將 `connection` 保留空白，輸出繫結會假設預設的服務匯流排連接字串已在名為 `AzureWebJobsServiceBus` 的應用程式設定中指定。
* 針對 `accessRights`，可用值為 `manage` 和 `listen`。 預設值是 `manage`，這表示 `connection` 已具備**管理**權限。 如果您使用沒有**管理**權限的連接字串，請將 `accessRights` 設定為 `listen`。 否則，Functions 執行階段在嘗試執行需要管理權限的作業時可能會失敗。

<a name="outputusage"></a>

## <a name="output-usage"></a>輸出使用方式
在 C# 和 F# 中，Azure Functions 可以從下列任何類型建立服務匯流排佇列訊息：

* 任何[物件](https://msdn.microsoft.com/library/system.object.aspx) - 參數定義看起來像 `out T paramName` (C#)。
  Functions 會將物件還原序列化為 JSON 訊息。 當函式結束時，如果輸出值是 null，Functions 會使用 Null 物件建立訊息。
* `string` - 參數定義看起來像 `out string paraName` (C#)。 如果參數值為非 Null，則函式結束時，Functions 會建立一則訊息。
* `byte[]` - 參數定義看起來像 `out byte[] paraName` (C#)。 如果參數值為非 Null，則函式結束時，Functions 會建立一則訊息。
* `BrokeredMessage` - 參數定義看起來像 `out BrokeredMessage paraName` (C#)。 如果參數值為非 Null，則函式結束時，Functions 會建立一則訊息。

若要在 C# 函式中建立多個訊息，您可以使用 `ICollector<T>` 或 `IAsyncCollector<T>`。 當您呼叫 `Add` 方法時，就會建立一則訊息。

在 Node.js 中，您可以指派字串、位元組陣列或 Javascript 物件 (還原序列化為 JSON) 給 `context.binding.<paramName>`。

<a name="outputsample"></a>

## <a name="output-sample"></a>輸出範例
假設您有下列 function.json，則會定義服務匯流排佇列輸出︰

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

請參閱可傳送訊息至服務匯流排佇列的特定語言範例。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C# 中的輸出範例 #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

或者，若要建立多個訊息：

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F# 中的輸出範例 #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js 中的輸出範例

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

或者，若要建立多個訊息：

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


