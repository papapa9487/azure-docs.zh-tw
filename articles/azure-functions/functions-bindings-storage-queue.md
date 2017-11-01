---
title: "Azure Functions 佇列儲存體繫結 | Microsoft Docs"
description: "瞭解如何在 Azure Functions 中使用「Azure 儲存體」觸發程序和繫結。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.openlocfilehash: b68ce106ceb25d19ee0bbde287891d553a448560
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="azure-functions-queue-storage-bindings"></a>Azure Functions 佇列儲存體繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何在 Azure Functions 中為 Azure 佇列儲存體繫結進行設定及撰寫程式碼。 Azure Functions 支援適用於 Azure 佇列的觸發程序和輸出繫結。 如需所有繫結中可用的功能，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>佇列儲存體觸發程序
Azure 佇列儲存體觸發程序可讓您監視佇列儲存體的新訊息，並對它們做出回應。 

使用 Functions 入口網站中的 [整合] 索引標籤定義佇列觸發程序。 該入口網站會在 *function.json* 的 **bindings** 區段中建立下列定義：

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* `connection` 屬性必須包含應用程式設定的名稱，其中包含儲存體連接字串。 在 Azure 入口網站中，當您選取儲存體帳戶時，[整合] 索引標籤中的標準編輯器可設定此應用程式設定。

您可以在 [host.json 檔案](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)中提供其他設定，進一步微調佇列儲存體觸發程序。 例如，您可以變更 host.json 中的佇列輪詢間隔。

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>使用佇列觸發程序
在 Node.js 函式中，使用 `context.bindings.<name>` 存取佇列資料。


在 .NET 函式中，使用方法參數 (例如`CloudQueueMessage paramName`) 存取佇列承載。 其中，`paramName` 是您在[觸發程序設定](#trigger)中指定的值。 佇列訊息可以還原序列化為下列任何一種類型︰

* POCO 物件。 佇列承載為 JSON 物件時使用。 Functions 執行階段會將承載還原序列化為 POCO 物件。 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>佇列觸發程序中繼資料
佇列觸發程序提供數個中繼資料屬性。 這些屬性可作為其他繫結中繫結運算式的一部分或程式碼中的參數使用。 這些值的語意與 [`CloudQueueMessage`] 相同。

* **QueueTrigger** - 佇列承載 (如果為有效字串)
* **DequeueCount** - 鍵入 `int`。 此訊息已從佇列清除的次數。
* **ExpirationTime** - 鍵入 `DateTimeOffset?`。 訊息到期時間。
* **Id** - 鍵入 `string`。 佇列訊息識別碼。
* **InsertionTime** - 鍵入 `DateTimeOffset?`。 訊息新增至佇列的時間。
* **NextVisibleTime** - 鍵入 `DateTimeOffset?`。 下次顯示訊息的時間。
* **PopReceipt** - 鍵入 `string`。 訊息的離開通知。

請參閱[觸發程序範例](#triggersample)以了解如何使用佇列中繼資料。

<a name="triggersample"></a>

## <a name="trigger-sample"></a>觸發程序範例
假設您有下列 function.json，定義了佇列觸發程序：

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

請參閱可擷取及記錄佇列中繼資料的特定語言範例。

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C# 中的觸發程序範例 #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js 中的觸發程序範例

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>處理有害的佇列訊息
當佇列觸發程序函數失敗時，Azure Functions 會針對指定的佇列訊息重試該函數最多五次，包括第一次嘗試。 如果五次嘗試全都失敗，Functions 執行階段會將訊息新增至名為 &lt;原始佇列名稱>-poison 的佇列儲存體。 您可以撰寫函數，透過記錄或傳送通知表示需要手動處理，來處理有害佇列中的訊息。 

若要手動處理有害訊息，請檢查佇列訊息的 `dequeueCount` (請參閱[佇列觸發程序中繼資料](#meta))。

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>佇列儲存體輸出繫結
Azure 佇列儲存體輸出繫結可讓您將訊息寫入佇列。 

使用 Functions 入口網站中的 [整合] 索引標籤定義佇列輸出繫結。 該入口網站會在 *function.json* 的 **bindings** 區段中建立下列定義：

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* `connection` 屬性必須包含應用程式設定的名稱，其中包含儲存體連接字串。 在 Azure 入口網站中，當您選取儲存體帳戶時，[整合] 索引標籤中的標準編輯器可設定此應用程式設定。

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>使用佇列輸出繫結
在 Node.js 函數中，您會使用 `context.bindings.<name>` 存取輸出佇列。

在 .NET 函式中，您可以輸出至下列任何類型。 如果有型別參數 `T`，`T` 必須是其中一個支援的輸出類型，例如 `string` 或 POCO。

* `out T` (序列化為 JSON)
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

您也可以使用方法傳回型別作為輸出繫結。

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>佇列輸出範例
下列 *function.json* 定義 HTTP 觸發程序與佇列輸出的繫結：

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

請參閱透過傳入 HTTP 承載輸出佇列訊息的特定語言範例。

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>C# 中的佇列輸出範例 #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

若要傳送多個訊息，請使用 `ICollector`：

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Node.js 中的佇列輸出範例

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

或者，若要傳送多個訊息，

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>後續步驟

如需使用佇列儲存體觸發程序和繫結的函式範例，請參閱[建立由 Azure 佇列儲存體觸發的函式](functions-create-storage-queue-triggered-function.md)。

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

[`CloudQueueMessage`]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
