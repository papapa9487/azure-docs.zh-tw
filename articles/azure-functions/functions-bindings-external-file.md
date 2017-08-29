---
title: "Azure Functions 外部檔案繫結 (預覽) | Microsoft Docs"
description: "使用 Azure Functions 中的外部檔案繫結"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 2082e4e9b23271be93f3e3ab43997c3243238da8
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---
# <a name="azure-functions-external-file-bindings-preview"></a>Azure Functions 外部檔案繫結 (預覽)
本文說明如何利用內建繫結，在您的函數內操作來自不同 SaaS 提供者 (例如 OneDrive, Dropbox) 的檔案。 Azure Functions 支援適用於外部檔案的觸發程序、輸入和輸出繫結。

此繫結會建立與 SaaS 提供者的 API 連線，或使用函數應用程式之資源群組的現有 API 連線。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>支援的檔案連線

|連接器|觸發程序|輸入|輸出|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[商務用 OneDrive](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google Drive](https://www.google.com/drive/)||x|x|

> [!NOTE]
> 外部檔案連線也可以用在 [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list) 中

## <a name="external-file-trigger-binding"></a>外部檔案觸發程序繫結

Azure 外部檔案觸發程序可讓您監視遠端資料夾，並且在偵測到變更時執行您的函數程式碼。

外部檔案觸發程序使用 function.json 之 `bindings` 陣列中的下列 JSON 物件

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

### <a name="name-patterns"></a>名稱模式
您可以在 `path` 屬性中指定檔案名稱模式。 所參考的資料夾必須存在於 SaaS 提供者。
範例：

```json
"path": "input/original-{name}",
```

此路徑會尋找 *input* 資料夾中名稱為 *original-File1.txt* 的檔案，且函數程式碼中 `name` 變數的值會是 `File1.txt`。

另一個範例：

```json
"path": "input/{filename}.{fileextension}",
```

此路徑也會尋找名為 *original-File1.txt* 的檔案，且函數程式碼中的 `filename` 和 `fileextension` 變數值會是 *original-File1* 和 *txt*。

您可以使用固定的檔案副檔名值來限制檔案的檔案類型。 例如：

```json
"path": "samples/{name}.png",
```

在此案例中，只有 *samples* 資料夾中的 *.png* 檔案會觸發函數。

大括號是名稱模式中的特殊字元。 如果要指定名稱中包含大括號的檔案名稱，請使用兩個大括號。
例如：

```json
"path": "images/{{20140101}}-{name}",
```

此路徑會在 *images* 資料夾中尋找名為 *{20140101}-soundfile.mp3* 的檔案，而函數程式碼中的 `name` 變數值會是 *soundfile.mp3*。

<a name="receipts"></a>

<!--- ### File receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->
<a name="poison"></a>

### <a name="handling-poison-files"></a>處理有害的檔案
當外部檔案觸發程序函數失敗時，Azure Functions 依預設會針對指定的檔案重試該函數最多 5 次 (包括第一次嘗試)。
如果 5 次嘗試全都失敗，函數會將訊息新增至名為 *webjobs-apihubtrigger-poison* 的儲存體佇列。 有害檔案的佇列訊息是包含下列屬性的 JSON 物件：

* FunctionId (格式為 *&lt;function app name>*.Functions.*&lt;function name>*)
* FileType
* FolderName
* FileName
* ETag (檔案版本識別碼，例如："0x8D1DC6E70A277EF")


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>觸發程序使用方式
在 C# 函數中，您使用函數簽章中的具名參數 (例如 `<T> <name>`) 繫結至輸入檔案資料。
其中 `T` 是您要用來還原序列化資料的資料類型，而 `paramName` 是您在 [觸發 JSON](#trigger) 中指定的名稱。 在 Node.js 函數中，您使用 `context.bindings.<name>` 存取輸入檔案資料。

檔案可以還原序列化為下列任何一種類型︰

* 任何[物件 (機器翻譯)](https://msdn.microsoft.com/library/system.object.aspx) - 適用於 JSON 序列化的檔案資料。
  如果您宣告自訂輸入類型 (例如 `FooType`)，Azure Functions 會嘗試將 JSON 資料還原序列化為您指定的類型。
* 字串 - 適用於文字檔案資料。

在 C# 函數中，您也可以繫結至下列任何類型，且 Functions 執行階段會嘗試使用該類型將檔案資料還原序列化︰

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="trigger-sample"></a>觸發程序範例
假設您有下列 function.json，且該檔案定義外部檔案觸發程序︰

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

請參閱會記錄每個新增到受監視資料夾之檔案內容的語言特定範例。

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>C# 中的觸發程序使用方式 #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Node.js 中的觸發程序使用方式

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>外部檔案輸入繫結
Azure 外部檔案輸入繫結可讓您在函數中使用來自外部資料夾的檔案。

函數的外部檔案輸入會使用 function.json 之 `bindings` 陣列中的下列 JSON 物件︰

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

請注意：

* `path` 必須包含資料夾名稱和檔案名稱。 例如，如果您的函數中有[佇列觸發程序](functions-bindings-storage-queue.md)，您可以使用 `"path": "samples-workitems/{queueTrigger}"` 以指向 `samples-workitems` 資料夾中具有與觸發程序訊息中指定之檔案名稱相符之名稱的檔案。   

<a name="inputusage"></a>

## <a name="input-usage"></a>輸入使用方式
在 C# 函數中，您使用函數簽章中的具名參數 (例如 `<T> <name>`) 繫結至輸入檔案資料。
其中 `T` 是您要用來還原序列化資料的資料類型，而 `paramName` 是您在 [輸入繫結](#input) 中指定的名稱。 在 Node.js 函數中，您使用 `context.bindings.<name>` 存取輸入檔案資料。

檔案可以還原序列化為下列任何一種類型︰

* 任何[物件 (機器翻譯)](https://msdn.microsoft.com/library/system.object.aspx) - 適用於 JSON 序列化的檔案資料。
  如果您宣告自訂輸入類型 (例如 `InputType`)，Azure Functions 會嘗試將 JSON 資料還原序列化為您指定的類型。
* 字串 - 適用於文字檔案資料。

在 C# 函數中，您也可以繫結至下列任何類型，且 Functions 執行階段會嘗試使用該類型將檔案資料還原序列化︰

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`


<a name="output"></a>

## <a name="external-file-output-binding"></a>外部檔案輸出繫結
Azure 外部檔案輸出繫結可讓您在函數中將檔案寫入到外部資料夾。

函數的外部檔案輸出會使用 function.json 之 `bindings` 陣列中的下列 JSON 物件︰

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

請注意：

* `path` 必須包含要寫入之資料夾名稱和檔案名稱。 例如，如果您的函數中有[佇列觸發程序](functions-bindings-storage-queue.md)，您可以使用 `"path": "samples-workitems/{queueTrigger}"` 以指向 `samples-workitems` 資料夾中具有與觸發程序訊息中指定之檔案名稱相符之名稱的檔案。   

<a name="outputusage"></a>

## <a name="output-usage"></a>輸出使用方式
在 C# 函數中，您使會用函數簽章中名為 `out` 的參數 (例如 `out <T> <name>`) 繫結至輸出檔案，其中 `T` 是您想要用來序列化資料的資料類型，而 `paramName` 是您在[輸出繫結](#output)中指定的名稱。 在 Node.js 函數中，您會使用 `context.bindings.<name>` 存取輸出檔案。

您可以使用下列任何類型來寫入到輸出檔案︰

* 任何[物件](https://msdn.microsoft.com/library/system.object.aspx) - 適用於 JSON 序列化。
  如果您宣告自訂輸出類型 (例如 `out OutputType paramName`)，Azure Functions 會嘗試將物件序列化為 JSON。 如果函數結束時輸出參數為 Null，則 Functions 執行階段會將檔案建立為 Null 物件。
* 字串 - (`out string paramName`) 適用於文字檔案資料。 當函數結束時，如果字串參數非 Null，Functions 執行階段才會建立檔案。

在 C# 函式中，您也可以輸出至下列任何類型︰

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

<a name="outputsample"></a>

<a name="sample"></a>

## <a name="input--output-sample"></a>輸入 + 輸出範例
假設您有下列 function.json，且該檔案定義[儲存體佇列觸發程序](functions-bindings-storage-queue.md)、外部檔案輸入和外部檔案輸出︰

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
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

請參閱將輸入檔案複製到輸出檔案的語言特定範例。

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="usage-in-c"></a>C# 中的使用方式 #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

```
-->

<a name="innodejs"></a>

### <a name="usage-in-nodejs"></a>Node.js 中的使用方式

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

