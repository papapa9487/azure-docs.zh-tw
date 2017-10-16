---
title: "Azure Functions Blob 儲存體繫結 | Microsoft Docs"
description: "瞭解如何在 Azure Functions 中使用「Azure 儲存體」觸發程序和繫結。"
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: b123578dbac48018f674f85ec923e4c6e65fb9f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Azure Functions Blob 儲存體繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何在 Azure Functions 中設定及使用 Azure Blob 儲存體繫結。 Azure Functions 支援適用於 Azure Blob 儲存體的觸發程序、輸入和輸出繫結。 如需所有繫結中可用的功能，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> 不支援[僅限 blob 儲存體帳戶](../storage/common/storage-create-storage-account.md#blob-storage-accounts)。 Blob 儲存體觸發程序和繫結需要一般用途的儲存體帳戶。 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>Blob 儲存體觸發程序和繫結

使用 Azure Blob 儲存體觸發程序，就會在偵測到新的或更新的 blob 時呼叫您的函式程式碼。 Blob 內容會當成函式輸入提供。

使用 Functions 入口網站中的 [整合] 索引標籤定義 Blob 儲存體觸發程序。 該入口網站會在 *function.json* 的 **bindings** 區段中建立下列定義：

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

使用 `blob` 將 blob 輸入和輸出繫結定義為繫結類型：

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* `path` 屬性支援繫結運算式和篩選參數。 請參閱[名稱模式](#pattern)。
* `connection` 屬性必須包含應用程式設定的名稱，其中包含儲存體連接字串。 在 Azure 入口網站中，當您選取儲存體帳戶時，[整合] 索引標籤中的標準編輯器可設定此應用程式設定。

> [!NOTE]
> 當您在使用情況方案中使用 blob 觸發程序時，函數應用程式進入閒置狀態之後，處理新 blob 最多會有 10 分鐘的延遲。 在函數應用程式開始執行之後，會立即處理 blob。 為了避免發生此初始延遲，請考慮下列做法︰
> - 使用 App Service 方案並啟用「永遠開啟」。
> - 使用其他機制來觸發 blob 處理，像是包含 blob 名稱的佇列訊息。 如需範例，請參閱[具有 blob 輸入繫結的佇列觸發程序](#input-sample)。

<a name="pattern"></a>

### <a name="name-patterns"></a>名稱模式
您可以在 `path` 屬性中，指定可篩選或繫結運算式的 blob 名稱模式。 請參閱[繫結運算式和模式](functions-triggers-bindings.md#binding-expressions-and-patterns)。

例如，若要篩選至以 "original" 字串開頭的 blob，請使用下列定義。 此路徑會在 *input* 容器中尋找名為 *original-Blob1.txt* 的 blob，且函式程式碼中的 `name` 變數值為 `Blob1`。

```json
"path": "input/original-{name}",
```

若要分別繫結至 blob 檔案名稱和副檔名，請使用兩個模式。 此路徑也會尋找名為 *original-Blob1.txt* 的 blob，且函式程式碼中的 `blobname` 和 `blobextension` 變數值為 *original-Blob1* 和 *txt*。

```json
"path": "input/{blobname}.{blobextension}",
```

您可以使用檔案副檔名的固定值來限制 blob 的類型。 例如，若只要在 .png 檔案上觸發，請使用下列模式：

```json
"path": "samples/{name}.png",
```

大括號是名稱模式中的特殊字元。 若要指定名稱中包含大括號的 blob 名稱，您可以使用兩個大括號來逸出大括號。 下列範例會在 *images* 容器中尋找名為 *{20140101}-soundfile.mp3* 的 blob，且函式程式碼中的 `name` 變數值為 *soundfile.mp3*。 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>觸發程序中繼資料

Blob 觸發程序提供數個中繼資料屬性。 這些屬性可作為其他繫結中繫結運算式的一部分或程式碼中的參數使用。 這些值的語意與 [CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) 相同。

- **BlobTrigger**： 輸入 `string`。 觸發的 blob 路徑
- **URI**： 輸入 `System.Uri`。 Blob 的主要位置 URI。
- **屬性**： 輸入 `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`。 Blob 的系統屬性。
- **中繼資料**： 輸入 `IDictionary<string,string>`。 Blob 的使用者定義中繼資料。

<a name="receipts"></a>

### <a name="blob-receipts"></a>Blob 回條
Azure Functions 執行階段可確保不會針對一樣新或更新的 blob 多次呼叫 blob 觸發程序函式。 為了判斷指定的 blob 版本是否已處理過，它會維護 *blob 回條*。

Azure Functions 會將 blob 回條儲存在您函數應用程式 (`AzureWebJobsStorage` 應用程式設定所定義) 的 Azure 儲存體帳戶中名為 *azure-webjobs-hosts*的容器中。 Blob 回條具有下列資訊：

* 已觸發的函數 ("&lt;函數應用程式名稱>.Functions.&lt;函數名稱>"，例如："MyFunctionApp.Functions.CopyBlob")
* 容器名稱
* Blob 類型 ("BlockBlob" 或 "PageBlob")
* Blob 名稱
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

要強制重新處理某個 Blob，可以從 *azure-webjobs-hosts* 容器中手動刪除該 Blob 的 Blob 回條。

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>處理有害的 blob
當指定 blob 的 blob 觸發程序函式失敗時，Azure Functions 預設一共會重試該函式 5 次。 

如果 5 次嘗試全都失敗，Azure Functions 會將訊息新增至名為 *webjobs-blobtrigger-poison* 的儲存體佇列。 適用於有害 Blob 的佇列訊息是一個 JSON 物件，其中包含下列屬性：

* FunctionId (格式為 *&lt;function app name>*.Functions.*&lt;function name>*)
* BlobType ("BlockBlob" 或 "PageBlob")
* ContainerName
* BlobName
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>大型容器的 Blob 輪詢
如果所監看的 blob 容器包含超過 10,000 個 blob，Functions 執行階段會掃描記錄檔以監看新增或變更的 blob。 此程序不是即時。 可能直到建立 Blob 之後數分鐘或更久，才會觸發函數。 此外，[會以「最大努力」建立儲存體記錄](/rest/api/storageservices/About-Storage-Analytics-Logging)。 並不保證會擷取所有的事件。 在某些情況下可能會遺失記錄檔。 如果您需要更快或更可靠的 Blob 處理，請考慮在建立 Blob 時建立[佇列訊息](../storage/queues/storage-dotnet-how-to-use-queues.md)。 然後，使用[佇列觸發程序](functions-bindings-storage-queue.md) (而不是 blob 觸發程序) 處理該 blob。

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>使用 blob 觸發程序和輸入繫結
在 .NET 函式中，使用方法參數 (例如`Stream paramName`) 存取 blob 資料。 其中，`paramName` 是您在[觸發程序設定](#trigger)中指定的值。 在 Node.js 函式中，使用 `context.bindings.<name>` 存取 blob 的輸入資料。

在 .NET 中，您可以繫結至下列清單中的任何類型。 如果用作輸入繫結，其中一些類型需要 *function.json* 中的 `inout` 繫結方向。 標準編輯器不支援此方向，因此您必須使用進階編輯器。

* `TextReader`
* `Stream`
* `ICloudBlob` (需要 "inout" 繫結方向)
* `CloudBlockBlob` (需要 "inout" 繫結方向)
* `CloudPageBlob` (需要 "inout" 繫結方向)
* `CloudAppendBlob` (需要 "inout" 繫結方向)

如果預期為文字 blob，您也可以繫結至 .NET `string` 類型。 由於會將整個 blob 內容載入記憶體中，因此只有在 blob 大小很小時才建議這樣做。 一般而言，最好使用 `Stream` 或 `CloudBlockBlob` 類型。

## <a name="trigger-sample"></a>觸發程序範例
假設您有下列 function.json，定義了 blob 儲存體觸發程序：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

請參閱會將每個新增的 Blob 內容記錄到受監視容器的語言特定範例。

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>C# 中的 Blob 觸發程序範例 #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>Node.js 中的觸發程序範例

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a>
<a name="storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>使用 blob 輸出繫結

在 .NET 函式中，您應該在函式簽章中使用 `out string` 參數，或使用下列清單中的其中一個類型。 在 Node.js 函式中，您使用 `context.bindings.<name>` 存取輸出 blob。

在 .NET 函式中，您可以輸出至下列任何類型：

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>具有 blob 輸入和輸出的佇列觸發程序範例
假設您有下列 function.json，定義了[佇列儲存體觸發程序](functions-bindings-storage-queue.md)、blob 儲存體輸入和 blob 儲存體輸出。 請注意 `queueTrigger` 中繼資料屬性 在 blob 輸入和輸出 `path` 屬性中的使用方式：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

請參閱將輸入 blob 複製到輸出 blob 的語言特定範例。

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="blob-binding-example-in-c"></a>C# 中的 blob 繫結範例 #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>Node.js 中的 blob 繫結範例

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

