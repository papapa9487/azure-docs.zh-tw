---
title: "Azure Functions 的 Azure 佇列儲存體繫結"
description: "了解如何在 Azure Functions 中使用 Azure 佇列儲存體觸發程序和輸出繫結。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: glenga
ms.openlocfilehash: 0aae58fa52f9f7f64b08e1701b7688a90c56e6ed
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Functions 的 Azure 佇列儲存體繫結

本文說明如何在 Azure Functions 中使用 Azure 佇列儲存體繫結。 Azure Functions 支援適用於佇列的觸發程序和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>觸發程序

在佇列上收到新項目時，可使用佇列觸發程序以啟動函式。 佇列訊息會當成函式輸入提供。

## <a name="trigger---example"></a>觸發程序 - 範例

請參閱特定語言的範例：

* [先行編譯 C#](#trigger---c-example)
* [C# 指令碼](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>觸發程序 - C# 範例

下列範例所示範的[先行編譯 C#](functions-dotnet-class-library.md) 程式碼會輪詢 `myqueue-items` 佇列，並在每次處理佇列項目時寫入記錄。

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>觸發程序 - C# 指令碼範例

下列範例所示範的是使用繫結之 *function.json* 檔案和 [C# 指令碼](functions-reference-csharp.md)中的 Blob 觸發程序繫結。 此函式會輪詢 `myqueue-items` 佇列，並在每次處理佇列項目時寫入記錄。

以下是 *function.json* 檔案：

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[設定](#trigger---configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

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

[使用方式](#trigger---usage)章節會說明 `myQueueItem` (由 function.json 中的`name` 屬性命名)。  [訊息中繼資料區段](#trigger---message-metadata)會說明所有其他顯示的變數。

### <a name="trigger---javascript-example"></a>觸發程序 - JavaScript 範例

下列範例示範的是使用繫結之 *function.json* 檔案，以及 [JavaScript 函式](functions-reference-node.md)中的 Blob 觸發程序繫結。 此函式會輪詢 `myqueue-items` 佇列，並在每次處理佇列項目時寫入記錄。

以下是 *function.json* 檔案：

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[設定](#trigger---configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

[使用方式](#trigger---usage)章節會說明 `myQueueItem` (由 function.json 中的`name` 屬性命名)。  [訊息中繼資料區段](#trigger---message-metadata)會說明所有其他顯示的變數。

## <a name="trigger---attributes"></a>觸發程序 - 屬性
 
對於[先行編譯 C#](functions-dotnet-class-library.md) 函數，請使用下列屬性以設定佇列觸發程序：

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)，定義於 NuGet 封裝 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中

  屬性的建構函式會採用佇列名稱進行監視，如下列範例所示：

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  {
      ...
  }
  ```

  您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  {
      ....
  }
  ```
 
  如需完整範例，請參閱[觸發程序 - 先行編譯 C# 範例](#trigger---c-example)。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)，定義於 NuGet 封裝 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中

  提供另一種方式來指定要使用的儲存體帳戶。 建構函式採用的是內含儲存體連接字串的應用程式設定名稱。 屬性可以套用在參數、方法或類別層級。 下列範例所示範的是類別層級與方法層級：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

要使用的儲存體帳戶按以下順序決定：

* `QueueTrigger` 屬性的 `Connection` 內容。
* `StorageAccount` 屬性套用至與 `QueueTrigger` 屬性相同的參數。
* `StorageAccount` 屬性套用至該函式。
* `StorageAccount` 屬性套用至該類別。
* 「AzureWebJobsStorage」應用程式設定。

## <a name="trigger---configuration"></a>觸發程式 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `QueueTrigger` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a| 必須設為 `queueTrigger`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction**| n/a | 僅限在 *function.json* 檔案中。 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a |代表函式程式碼中佇列的變數名稱。  | 
|**queueName** | **QueueName**| 要輪詢的佇列名稱。 | 
|**連接** | **連接** |應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為「MyStorage」，則函式執行階段會尋找名稱為「AzureWebJobsMyStorage」的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>觸發程序 - 使用方式
 
在 C# 和 C# 指令碼中，使用方法參數 (例如 `Stream paramName`) 來存取 Blob 資料。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 您可以繫結至下列任何類型：

* POCO 物件 - Functions 執行階段會將 JSON 承載還原序列化為 POCO 物件。 
* `string`
* `byte[]`
* [CloudQueueMessage]

在 JavaScript 中，使用 `context.bindings.<name>` 存取佇列項目承載。 如果承載為 JSON，則會將已序列化的承載還原為物件。

## <a name="trigger---message-metadata"></a>觸發程序 - 訊息中繼資料

佇列觸發程序提供數個中繼資料屬性。 這些屬性可作為其他繫結中繫結運算式的一部分或程式碼中的參數使用。 這些值的語意與 [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) 相同。

|屬性|類型|說明|
|--------|----|-----------|
|`QueueTrigger`|`string`|佇列承載 (如果為有效字串)。 如果佇列承載為字串，`QueueTrigger` 具有相同於 *function.json* 中由 `name` 屬性命名之變數的值。|
|`DequeueCount`|`int`|此訊息已從佇列清除的次數。|
|`ExpirationTime`|`DateTimeOffset?`|訊息到期時間。|
|`Id`|`string`|佇列訊息識別碼。|
|`InsertionTime`|`DateTimeOffset?`|訊息新增至佇列的時間。|
|`NextVisibleTime`|`DateTimeOffset?`|下次顯示訊息的時間。|
|`PopReceipt`|`string`|訊息的離開通知。|

## <a name="trigger---poison-messages"></a>觸發程序 - 有害訊息

當佇列觸發程序函數失敗時，Azure Functions 會針對指定的佇列訊息重試該函數最多五次，包括第一次嘗試。 如果五次嘗試全都失敗，Functions 執行階段會將訊息新增至名為 *&lt;originalqueuename>-poison* 的佇列。 您可以撰寫函數，透過記錄或傳送通知表示需要手動處理，來處理有害佇列中的訊息。

若要手動處理有害訊息，請檢查佇列訊息的 [dequeueCount](#trigger---message-metadata)。

## <a name="trigger---hostjson-properties"></a>觸發程序 - host.json 屬性

[host.json](functions-host-json.md#queues) 檔案包含控制佇列觸發程序行為的設定。

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>輸出

使用 Azure 佇列儲存體輸出繫結，將訊息寫入佇列。

## <a name="output---example"></a>輸出 - 範例

請參閱特定語言的範例：

* [先行編譯 C#](#output---c-example)
* [C# 指令碼](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>輸出 - C# 範例

下列範例所示範的[先行編譯 C#](functions-dotnet-class-library.md) 程式碼會為每個收到的 HTTP 要求建立佇列訊息。

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>輸出 - C# 指令碼範例

下列範例所示範的是使用繫結之 *function.json* 檔案和 [C# 指令碼](functions-reference-csharp.md)中的 Blob 觸發程序繫結。 此函式會針對每個收到的 HTTP 要求，使用 POCO 承載建立佇列項目。

以下是 *function.json* 檔案：

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

[設定](#output---configuration)章節會說明這些屬性。

以下是建立單一佇列訊息的 C# 指令碼程式碼：

```cs
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

您可以使用 `ICollector` 或 `IAsyncCollector` 參數一次傳送多個訊息。 以下 C# 指令碼程式碼會傳送多個訊息，一個使用 HTTP 要求資料，一個使用硬式編碼值：

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItem, 
    TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>輸出 - JavaScript 範例

下列範例示範的是使用繫結之 *function.json* 檔案，以及 [JavaScript 函式](functions-reference-node.md)中的 Blob 觸發程序繫結。 此函式會針對每個收到的 HTTP 要求建立佇列項目。

以下是 *function.json* 檔案：

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

[設定](#output---configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

您可以定義 `myQueueItem` 輸出繫結的訊息陣列，藉此一次傳送多個訊息。 下列 JavaScript 程式碼會對每個接收到的 HTTP 要求，使用硬式編碼值傳送兩個佇列訊息。

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes"></a>輸出 - 屬性
 
對於[先行編譯 C#](functions-dotnet-class-library.md) 函式，會使用 [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs)，其定義於 NuGet 封裝 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中。

該屬性會套用至 `out` 參數或函式的傳回值。 該屬性的建構函式會採用佇列名稱，如下列範例所示：

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items, Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

如需完整範例，請參閱[輸出 - 先行編譯 C# 範例](#output---c-example)。

您可以使用 `StorageAccount` 屬性來指定類別、方法或參數層級的儲存體帳戶。 如需詳細資訊，請參閱[觸發程序 - 屬性](#trigger---attributes-for-precompiled-c)。

## <a name="output---configuration"></a>輸出 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `Queue` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `queue`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `out`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中佇列的變數名稱。 設為 `$return` 以參考函式傳回值。| 
|**queueName** |**QueueName** | 佇列的名稱。 | 
|**連接** | **連接** |應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為「MyStorage」，則函式執行階段會尋找名稱為「AzureWebJobsMyStorage」的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>輸出 - 使用方式
 
在 C# 和 C# 指令碼中，藉由使用方法參數 (例如 `out T paramName`) 寫入單一佇列訊息。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 您可以使用方法傳回類型，而不是 `out` 參數，而且 `T` 可以是下列類型之一：

* 可序列化為 JSON 的 POCO
* `string`
* `byte[]`
* [CloudQueueMessage] 

在 C# 和 C# 指令碼中，藉由使用下列其中一個類型，寫入多個佇列訊息： 

* `ICollector<T>` 或 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

在 JavaScript 函式中，使用 `context.bindings.<name>` 存取輸出佇列訊息。 對於佇列項目承載，可使用字串或 JSON 可序列化物件。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至使用佇列儲存體觸發程序的快速入門](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [移至使用佇列儲存體輸出繫結的教學課程](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage