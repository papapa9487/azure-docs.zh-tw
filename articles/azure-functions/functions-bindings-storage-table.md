---
title: "Azure Functions 儲存體資料表繫結 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 儲存體繫結。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
ms.openlocfilehash: 486b7c31c914ba7bb2d75e3f83ccf346a09104e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-storage-table-bindings"></a>Azure Functions 儲存體資料表繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何在 Azure Functions 中為 Azure 儲存體資料表繫結進行設定及撰寫程式碼。 Azure Functions 支援 Azure 儲存體資料表的輸入和輸出繫結。

儲存體資料表繫結支援下列案例：

* **讀取 C# 或 Node 函式中的單一資料列** - 設定 `partitionKey` 和 `rowKey`。 此案例中不使用 `filter` 和 `take` 屬性。
* **讀取 C# 函式中的多個資料列** - Functions 執行階段會提供一個繫結至資料表的 `IQueryable<T>` 物件。 類型 `T` 必須衍生自 `TableEntity` 或實作 `ITableEntity`。 此案例中不使用 `partitionKey`、`rowKey`、`filter` 和 `take`屬性；您可以使用 `IQueryable` 物件來執行任何所需的篩選。 
* **讀取 Node 函式中的多個資料列** - 設定 `filter` 和 `take` 屬性。 請勿設定 `partitionKey` 或 `rowKey`。
* **在 C# 函式中寫入一或多個資料列** - Functions 執行階段會提供一個繫結至資料表的 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 指定您想要新增之實體的結構描述。 一般而言，類型 `T` 會衍生自 `TableEntity` 或實作 `ITableEntity`，但不一定如此。 此案例中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 屬性。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>儲存體資料表輸入繫結
Azure 儲存體資料表輸入繫結可讓您在您的函式中使用儲存資料表。 

函式的儲存體資料表輸入會使用 function.json `bindings` 陣列中的下列 JSON 物件︰

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

請注意： 

* 一起使用 `partitionKey` 和 `rowKey` 來讀取單一實體。 這些屬性是選擇性的。 
* `connection` 必須包含儲存體連接字串的應用程式設定名稱。 在 Azure 入口網站中，當您建立儲存體帳戶或選取一個現有的儲存體帳戶時，[整合] 索引標籤中的標準編輯器可設定此應用程式設定。 您也可以[手動進行此應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)。  

<a name="inputusage"></a>

## <a name="input-usage"></a>輸入使用方式
在 C# 函式中，您使用在您函式簽章中的具名參數 (例如 `<T> <name>`) 繫結至資料表實體 (或多個實體)。
其中 `T` 是您要用來還原序列化資料的資料類型，而 `paramName` 是您在 [輸入繫結](#input) 中指定的名稱。 在 Node.js 函式中，您會使用 `context.bindings.<name>` 來存取輸入資料表實體 (或多個實體)。

可以在 Node.js 或 C# 函式中將輸入資料還原序列化。 還原序列化的物件具有 `RowKey` 和 `PartitionKey` 屬性。

在 C# 函式中，您也可以繫結至下列任何類型，Functions 的執行階段會嘗試使用該類型還原序列化資料表資料︰

* 實作 `ITableEntity` 的任何類型
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>輸入範例
假設您有下列 function.json，其使用佇列觸發程序來讀取單一資料表資料列。 JSON 會指定 `PartitionKey` 
`RowKey`。 `"rowKey": "{queueTrigger}"` 表示資料列索引鍵來自佇列訊息字串。

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

請參閱可讀取單一資料表實體的特定語言範例。

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>C# 中的輸入範例 #
```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>F# 中的輸入範例 #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js 中的輸入範例
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>儲存體資料表輸出繫結
Azure 儲存體資料表輸出繫結可讓您在函式中將實體寫入儲存體資料表。 

函式的儲存體資料表輸出會使用 function.json `bindings` 陣列中的下列 JSON 物件︰

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

請注意： 

* 一起使用 `partitionKey` 和 `rowKey` 來寫入單一實體。 這些屬性是選擇性的。 在您的函式程式碼中建立實體物件時，您也可以指定 `PartitionKey` 和 `RowKey`。
* `connection` 必須包含儲存體連接字串的應用程式設定名稱。 在 Azure 入口網站中，當您建立儲存體帳戶或選取一個現有的儲存體帳戶時，[整合] 索引標籤中的標準編輯器可設定此應用程式設定。 您也可以[手動進行此應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)。 

<a name="outputusage"></a>

## <a name="output-usage"></a>輸出使用方式
在 C# 函式中，您使用函式簽章中名為 `out` 的參數 (例如 `out <T> <name>`) 繫結至資料表輸出，其中 `T` 是您想要用來序列化資料的資料類型，而 `paramName` 是您在 [輸出繫結](#output) 中指定的名稱。 在 Node.js 函式中，您會使用 `context.bindings.<name>` 來存取資料表輸出。

您可以在 Node.js 或 C# 函式中將物件序列化。 在 C# 函式中，您也可以繫結至下列類型︰

* 實作 `ITableEntity` 的任何類型
* `ICollector<T>` (可輸出多個實體。 請參閱[範例](#outcsharp)。)
* `IAsyncCollector<T>` (`ICollector<T>` 的非同步版本)
* `CloudTable` (使用「Azure 儲存體 SDK」。 請參閱[範例](#readmulti)。)

<a name="outputsample"></a>

## <a name="output-sample"></a>輸出範例
下列 *function.json* 和 *run.csx* 範例示範如何在 C# 中撰寫多個資料表實體。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

請參閱可建立多個資料表實體的特定語言範例。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C# 中的輸出範例 #
```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F# 中的輸出範例 #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js 中的輸出範例
```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>範例：讀取 C# 中的多個資料表實體  #
下列「function.json」  和 C# 程式碼範例會讀取佇列訊息中指定的資料分割金鑰的實體。

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# 程式碼會新增對「Azure 儲存體 SDK」的參考，讓實體類型可以衍生自 `TableEntity`。

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

