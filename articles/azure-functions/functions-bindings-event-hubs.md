---
title: "Azure Functions 事件中樞繫結 | Microsoft Docs"
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
ms.date: 06/20/2017
ms.author: wesmc
ms.openlocfilehash: 85eb6985ef3579b1b2313db3ce5f91c3471da72f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-event-hubs-bindings"></a>Azure Functions 事件中樞繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何針對 Azure Functions 設定及使用 [Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)繫結。
Azure Functions 支援事件中樞的觸發程序和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

如果您對 Azure 事件中樞並不熟悉，請參閱[事件中樞概述](../event-hubs/event-hubs-what-is-event-hubs.md)。

<a name="trigger"></a>

## <a name="event-hub-trigger"></a>事件中樞觸發程序
使用事件中樞觸發程序將回應傳送至事件中樞事件資料流。 您必須具有事件中樞的讀取存取權，才能設定觸發程序。

事件中樞函式觸發程序會使用 function.json 之 `bindings` 陣列中的下列 JSON 物件︰

```json
{
    "type": "eventHubTrigger",
    "name": "<Name of trigger parameter in function signature>",
    "direction": "in",
    "path": "<Name of the event hub>",
    "consumerGroup": "Consumer group to use - see below",
    "connection": "<Name of app setting with connection string - see below>"
}
```

`consumerGroup` 是選擇性屬性，可設定用來訂閱中樞內事件的[取用者群組](../event-hubs/event-hubs-features.md#event-consumers)。 如果省略，則會使用 `$Default` 取用者群組。  
`connection` 必須是應用程式設定的名稱，包含事件中樞命名空間的連接字串。
按一下*命名空間*的 [連接資訊] 按鈕 (而不是事件中樞本身)，來複製此連接字串。 此連接字串至少必須具備讀取權限，才能啟動觸發程序。

可以在 host.json 檔案中提供[其他設定](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)來進一步微調事件中樞觸發程序。  

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>觸發程序使用方式
事件中樞觸發程序函式觸發時，觸發它的訊息會以字串形式傳遞至函式。

<a name="triggersample"></a>

## <a name="trigger-sample"></a>觸發程序範例
假設您的 function.json 之 `bindings` 陣列中有下列事件中樞觸發程序︰

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

請參閱記錄事件中樞的觸發程序的訊息本文的語言特定範例。

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C# 中的觸發程序範例 #

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

您也能以 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 物件的形式接收事件，該物件能為您提供事件中繼資料的存取權。

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

若要以批次接收事件，請將方法簽章變更為 `string[]` 或 `EventData[]`。

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F# 中的觸發程序範例 #

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js 中的觸發程序範例

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

<a name="output"></a>

## <a name="event-hubs-output-binding"></a>事件中樞輸出繫結
使用事件中樞輸出繫結將事件寫入事件中樞事件資料流。 您必須具備事件中樞的傳送權限，才能將事件寫入其中。

輸出繫結會使用 function.json `bindings` 陣列中的下列 JSON 物件︰

```json
{
    "type": "eventHub",
    "name": "<Name of output parameter in function signature>",
    "path": "<Name of event hub>",
    "connection": "<Name of app setting with connection string - see below>"
    "direction": "out"
}
```

`connection` 必須是應用程式設定的名稱，包含事件中樞命名空間的連接字串。
按一下*命名空間*的 [連接資訊] 按鈕 (而不是事件中樞本身)，來複製此連接字串。 此連接字串必須具有傳送權限，才能將訊息傳送至事件資料流。

## <a name="output-usage"></a>輸出使用方式
本節說明如何在您的函式程式碼中使用「事件中樞」輸出繫結。

您可以使用下列參數類型，將訊息輸出至已設定的事件中樞︰

* `out string`
* `ICollector<string>` (輸出多個訊息)
* `IAsyncCollector<string>` (`ICollector<T>` 的非同步版本)

<a name="outputsample"></a>

## <a name="output-sample"></a>輸出範例
假設您的 function.json 之 `bindings` 陣列中有下列事件中樞輸出繫結︰

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

請參閱會將事件寫入事件資料流的特定語言範例。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C# 中的輸出範例 #

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

或者，若要建立多個訊息：

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F# 中的輸出範例 #

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

<a name="outnodejs"></a>

### <a name="output-sample-for-nodejs"></a>Node.js 的輸出範例

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

或者，若要傳送多個訊息，

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

## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
