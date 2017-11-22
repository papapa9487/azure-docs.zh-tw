---
title: "Azure Functions Blob 儲存體繫結"
description: "瞭解如何在 Azure Functions 中使用「Azure Blob 儲存體」觸發程序和繫結。"
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
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: e0c608fe3a80c9d704774e592a1eba383bbdffe8
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Azure Functions Blob 儲存體繫結

本文說明如何在 Azure Functions 中使用 Azure Blob 儲存體繫結。 Azure Functions 支援適用於 Blob 的觸發程序、輸入和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> 不支援[僅限 Blob 的儲存體帳戶](../storage/common/storage-create-storage-account.md#blob-storage-accounts)。 Blob 儲存體觸發程序和繫結需要一般用途的儲存體帳戶。 

## <a name="blob-storage-trigger"></a>Blob 儲存體觸發程序

偵測到新的或已更新的 Blob 時，使用 Blob 儲存體觸發程序啟動函數。 Blob 內容會當成函式輸入提供。

> [!NOTE]
> 當您在使用情況方案中使用 blob 觸發程序時，函數應用程式進入閒置狀態之後，處理新 blob 最多會有 10 分鐘的延遲。 在函數應用程式開始執行之後，會立即處理 blob。 為了避免發生此初始延遲，請考慮下列做法︰
> - 使用 App Service 方案並啟用「永遠開啟」。
> - 使用其他機制來觸發 blob 處理，像是包含 blob 名稱的佇列訊息。 如需範例，請參閱[本文中稍後說明的 Blob 輸入/輸出繫結範例](#input--output---example)。

## <a name="trigger---example"></a>觸發程序 - 範例

請參閱特定語言的範例：

* [先行編譯 C#](#trigger---c-example)
* [C# 指令碼](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>觸發程序 - C# 範例

下列範例示範在 `samples-workitems` 容器中新增或更新 blob 時，寫入記錄的[先行編譯 C#](functions-dotnet-class-library.md) 程式碼。

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

如需有關 `BlobTrigger` 屬性的詳細資訊，請參閱[觸發程序 - 先行編譯 C# 的屬性](#trigger---attributes-for-precompiled-c)。

### <a name="trigger---c-script-example"></a>觸發程序 - C# 指令碼範例

下列範例所示範的是使用繫結之 *function.json* 檔案和 [C# 指令碼](functions-reference-csharp.md)中的 Blob 觸發程序繫結。 在 `samples-workitems` 容器中新增或更新 Blob 時，函數會寫入記錄。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

[設定](#trigger---configuration)章節會說明這些屬性。

以下是繫結至 `Stream` 的 C# 指令碼：

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

以下是繫結至 `CloudBlockBlob` 的 C# 指令碼：

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>觸發程序 - JavaScript 範例

下列範例所示範的是使用繫結之 *function.json* 檔案和 [JavaScript 程式碼] (functions-reference-node.md) 中的 Blob 觸發程序繫結。 在 `samples-workitems` 容器中新增或更新 Blob 時，函數會寫入記錄。

以下是 *function.json* 檔案：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

[設定](#trigger---configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes-for-precompiled-c"></a>觸發程序 - 先行編譯 C# 的屬性

對於[先行編譯 C#](functions-dotnet-class-library.md) 函數，請使用下列屬性以設定 Blob 觸發程序：

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)，定義於 NuGet 封裝 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中

  該屬性的建構函式採用路徑字串，指示要監看的容器以及可選的 [Blob 名稱模式](#trigger---blob-name-patterns)。 以下是範例：

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

  您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)，定義於 NuGet 封裝 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中

  提供另一種方式來指定要使用的儲存體帳戶。 建構函式採用的是內含儲存體連接字串的應用程式設定名稱。 屬性可以套用在參數、方法或類別層級。 下列範例所示範的是類別層級與方法層級：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

要使用的儲存體帳戶按以下順序決定：

* `BlobTrigger` 屬性的 `Connection` 內容。
* `StorageAccount` 屬性套用至與 `BlobTrigger` 屬性相同的參數。
* `StorageAccount` 屬性套用至該函式。
* `StorageAccount` 屬性套用至該類別。
* 函數應用程式 (「AzureWebJobsStorage」應用程式設定) 的預設儲存體帳戶。

## <a name="trigger---configuration"></a>觸發程式 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `BlobTrigger` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `blobTrigger`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 例外狀況在[使用方式](#trigger---usage)一節中會加以說明。 |
|**name** | n/a | 表示函式程式碼中 Blob 的變數名稱。 | 
|**路徑** | **BlobPath** |要監視的容器。  可能是 [Blob 名稱模式](#trigger-blob-name-patterns)。 | 
|**連接** | **連接** | 應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為「MyStorage」，則函式執行階段會尋找名稱為「AzureWebJobsMyStorage」的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。<br><br>連接字串必須為一般用途的儲存體帳戶，不可為[僅限 Blob 的儲存體帳戶](../storage/common/storage-create-storage-account.md#blob-storage-accounts)。<br>當您要在本機開發時，應用程式設定會進入 [local.settings.json 檔案](functions-run-local.md#local-settings-file)的值。|

## <a name="trigger---usage"></a>觸發程序 - 使用方式

在 C# 和 C# 指令碼中，使用方法參數 (例如 `Stream paramName`) 來存取 Blob 資料。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 您可以繫結至下列任何類型：

* `TextReader`
* `Stream`
* `ICloudBlob` (需要 *function.json* 中的 "inout" 繫結方向)
* `CloudBlockBlob` (需要 *function.json* 中的 "inout" 繫結方向)
* `CloudPageBlob` (需要 *function.json* 中的 "inout" 繫結方向)
* `CloudAppendBlob` (需要 *function.json* 中的 "inout" 繫結方向)

如上所述，其中一些類型需要 *function.json* 中的 `inout` 繫結方向。 Azure 入口網站中的標準編輯器不支援此方向，因此您必須使用進階編輯器。

如果預期為文字 Blob，您可以繫結至 `string` 類型。 由於會將整個 blob 內容載入記憶體中，因此只有在 blob 大小很小時才建議這樣做。 一般而言，最好使用 `Stream` 或 `CloudBlockBlob` 類型。

在 JavaScript 中，使用 `context.bindings.<name>` 存取 Blob 的輸入資料。

## <a name="trigger---blob-name-patterns"></a>觸發程序 - Blob 名稱模式

您可以在 *function.json* 中的 `path` 屬性或 `BlobTrigger` 屬性建構函式中，指定 Blob 名稱模式。 名稱模式可以是[篩選條件或繫結運算式](functions-triggers-bindings.md#binding-expressions-and-patterns)。

### <a name="filter-on-blob-name"></a>Blob 名稱上的篩選條件

下列範例只會觸發 `input` 容器中以字串 "original-" 開頭的 Blob：

```json
"path": "input/original-{name}",
```
 
如果 Blob 名稱為 *original-Blob1.txt*，則函式程式碼中 `name` 變數的值為 `Blob1`。

### <a name="filter-on-file-type"></a>檔案類型上的篩選條件

下列範例只會在 *.png* 檔案上觸發：

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>檔案名稱中大括號上的篩選條件

若要尋找檔案名稱中的大括號，請使用兩個括號逸出括號。 下列範例篩選名稱中有大括號的 Blob：

```json
"path": "images/{{20140101}}-{name}",
```

如果 Blob 名稱為 *{20140101}-soundfile.mp3*，則函式程式碼中的 `name` 變數值為 *soundfile.mp3*。 

### <a name="get-file-name-and-extension"></a>取得檔案名稱和副檔名

下列範例示範如何分別繫結至 Blob 檔案名稱和副檔名：

```json
"path": "input/{blobname}.{blobextension}",
```
如果 Blob 名稱為 *original-Blob1.txt*，則函式程式碼中的 `blobname` 和 `blobextension` 變數值為 *original-Blob1* 和 *txt*。

## <a name="trigger---metadata"></a>觸發程序 - 中繼資料

Blob 觸發程序提供數個中繼資料屬性。 這些屬性可作為其他繫結中繫結運算式的一部分或程式碼中的參數使用。 這些值的語意與 [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) 類型相同。


|屬性  |類型  |說明  |
|---------|---------|---------|
|`BlobTrigger`|`string`|觸發 Blob 的路徑。|
|`Uri`|`System.Uri`|Blob 的主要位置 URI。|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Blob 的系統屬性。 |
|`Metadata` |`IDictionary<string,string>`|Blob 的使用者定義中繼資料。|

## <a name="trigger---blob-receipts"></a>觸發程序 - Blob 回條

Azure Functions 執行階段可確保不會針對一樣新或更新的 blob 多次呼叫 blob 觸發程序函式。 為了判斷指定的 blob 版本是否已處理過，它會維護 *blob 回條*。

Azure Functions 會將 blob 回條儲存在您函數應用程式 (`AzureWebJobsStorage` 應用程式設定所定義) 的 Azure 儲存體帳戶中名為 *azure-webjobs-hosts*的容器中。 Blob 回條具有下列資訊：

* 已觸發的函數 ("&lt;函數應用程式名稱>.Functions.&lt;函數名稱>"，例如："MyFunctionApp.Functions.CopyBlob")
* 容器名稱
* Blob 類型 ("BlockBlob" 或 "PageBlob")
* Blob 名稱
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

要強制重新處理某個 Blob，可以從 *azure-webjobs-hosts* 容器中手動刪除該 Blob 的 Blob 回條。

## <a name="trigger---poison-blobs"></a>觸發程序 - 有害的 Blob

當指定 blob 的 blob 觸發程序函式失敗時，Azure Functions 預設一共會重試該函式 5 次。 

如果 5 次嘗試全都失敗，Azure Functions 會將訊息新增至名為 *webjobs-blobtrigger-poison* 的儲存體佇列。 適用於有害 Blob 的佇列訊息是一個 JSON 物件，其中包含下列屬性：

* FunctionId (格式為 *&lt;function app name>*.Functions.*&lt;function name>*)
* BlobType ("BlockBlob" 或 "PageBlob")
* ContainerName
* BlobName
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

## <a name="trigger---polling-for-large-containers"></a>觸發程序 - 大型容器的輪詢

如果所監看的 blob 容器包含超過 10,000 個 blob，Functions 執行階段會掃描記錄檔以監看新增或變更的 blob。 此程序可能會導致延遲。 可能直到建立 Blob 之後數分鐘或更久，才會觸發函數。 此外，[會以「最大努力」建立儲存體記錄](/rest/api/storageservices/About-Storage-Analytics-Logging)。 並不保證會擷取所有事件。 在某些情況下可能會遺失記錄檔。 如果您需要更快或更可靠的 Blob 處理，請考慮在建立 Blob 時建立[佇列訊息](../storage/queues/storage-dotnet-how-to-use-queues.md)。 然後，使用[佇列觸發程序](functions-bindings-storage-queue.md) (而不是 Blob 觸發程序) 處理該 Blob。 另一個選項是使用 Event Grid；請參閱教學課程[使用 Event Grid 自動調整已上傳映像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md)。

## <a name="blob-storage-input--output-bindings"></a>Blob 儲存體輸入和輸出繫結

使用 Blob 儲存體輸入和輸出繫結來讀取和寫入 Blob。

## <a name="input--output---example"></a>輸入和輸出 - 範例

請參閱特定語言的範例：

* [先行編譯 C#](#input--output---c-example)
* [C# 指令碼](#input--output---c-script-example)
* [JavaScript](#input--output---javascript-example)

### <a name="input--output---c-example"></a>輸入和輸出 - C# 範例

下列範例是使用一個輸入和兩個輸出 Blob 繫結的[先行編譯 C#](functions-dotnet-class-library.md) 函式。 此函式是藉由在 *sample-images* 容器中建立映像 Blob 而觸發。 它會建立映像 Blob 的小型及中型複本。 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

### <a name="input--output---c-script-example"></a>輸入和輸出 - C# 指令碼範例

下列範例所示範的是使用繫結之 *function.json* 檔案和 [C# 指令碼](functions-reference-csharp.md)中的 Blob 觸發程序繫結。 此函式會建立 Blob 的複本。 此函式是由佇列訊息 (包含要複製的 Blob 名稱) 觸發。 新的 Blob 名稱為 *{originalblobname}-Copy*。

在 *function.json* 檔案中，`queueTrigger` 中繼資料屬性用於指定 `path` 屬性中的 Blob 名稱：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[設定](#input--output---configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

```cs
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input--output---javascript-example"></a>輸入和輸出 - JavaScript 範例

下列範例所示範的是使用繫結之 *function.json* 檔案和 [JavaScript 程式碼] (functions-reference-node.md) 中的 Blob 觸發程序繫結。 此函式會建立 Blob 的複本。 此函式是由佇列訊息 (包含要複製的 Blob 名稱) 觸發。 新的 Blob 名稱為 *{originalblobname}-Copy*。

在 *function.json* 檔案中，`queueTrigger` 中繼資料屬性用於指定 `path` 屬性中的 Blob 名稱：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[設定](#input--output---configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input--output---attributes-for-precompiled-c"></a>輸入和輸出 - 先行編譯 C# 的屬性

對於[先行編譯 C#](functions-dotnet-class-library.md) 函式，會使用 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs)，其定義於 NuGet 封裝 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)。

屬性的建構函式會採用 Blob 路徑和指示讀取或寫入的 `FileAccess` 參數，如下列範例所示：

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
```

您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
```

您可以使用 `StorageAccount` 屬性來指定類別、方法或參數層級的儲存體帳戶。 如需詳細資訊，請參閱[觸發程序 - 先行編譯 C# 的屬性](#trigger---attributes-for-precompiled-c)。

## <a name="input--output---configuration"></a>輸入和輸出 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `Blob` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `blob`。 |
|**direction** | n/a | 必須針對輸入繫結設為 `in`，或針對輸出繫結設為 out。 例外狀況在[使用方式](#input--output---usage)一節中會加以說明。 |
|**name** | n/a | 表示函式程式碼中 Blob 的變數名稱。  設為 `$return` 以參考函式傳回值。|
|**路徑** |**BlobPath** | Blob 的路徑。 | 
|**連接** |**連接**| 應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為「MyStorage」，則函式執行階段會尋找名稱為「AzureWebJobsMyStorage」的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。<br><br>連接字串必須為一般用途的儲存體帳戶，不可為[僅限 Blob 的儲存體帳戶](../storage/common/storage-create-storage-account.md#blob-storage-accounts)。<br>當您要在本機開發時，應用程式設定會進入 [local.settings.json 檔案](functions-run-local.md#local-settings-file)的值。|
|n/a | **Access** | 指出您是否將讀取或寫入。 |

## <a name="input--output---usage"></a>輸入和輸出 - 使用方式

在先行編譯 C# 和 C# 指令碼中，使用方法參數 (例如 `Stream paramName`) 存取 Blob。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 您可以繫結至下列任何類型：

* `out string`
* `TextWriter` 
* `TextReader`
* `Stream`
* `ICloudBlob` (需要 *function.json* 中的 "inout" 繫結方向)
* `CloudBlockBlob` (需要 *function.json* 中的 "inout" 繫結方向)
* `CloudPageBlob` (需要 *function.json* 中的 "inout" 繫結方向)
* `CloudAppendBlob` (需要 *function.json* 中的 "inout" 繫結方向)

如上所述，其中一些類型需要 *function.json* 中的 `inout` 繫結方向。 Azure 入口網站中的標準編輯器不支援此方向，因此您必須使用進階編輯器。

如果您正在讀取文字 Blob，可以繫結至 `string` 類型。 由於會將整個 blob 內容載入記憶體中，因此只有在 blob 大小很小時才建議使用此類型。 一般而言，最好使用 `Stream` 或 `CloudBlockBlob` 類型。

在 JavaScript 中，使用 `context.bindings.<name>` 存取 Blob 資料。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至使用 Blob 儲存體觸發程序的快速入門](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
