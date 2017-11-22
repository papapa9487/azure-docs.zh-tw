---
title: "Azure Functions 資料表儲存體繫結"
description: "了解如何在 Azure Functions 中使用 Azure 資料表繫結。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: chrande
ms.openlocfilehash: 2f54df931d03318a50e9397211e3c50d0898556d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2017
---
# <a name="azure-functions-table-storage-bindings"></a>Azure Functions 資料表儲存體繫結

本文說明如何在 Azure Functions 中使用 Azure 資料表儲存體繫結。 Azure Functions 支援 Azure 資料表儲存體的輸入和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="table-storage-input-binding"></a>資料表儲存體輸入繫結

使用 Azure 資料表儲存體輸入繫結以讀取 Azure 儲存體帳戶中的資料表。

## <a name="input---example"></a>輸入 - 範例

請參閱特定語言的範例：

* [先行編譯 C# 讀取單一實體](#input---c-example-1)
* [先行編譯 C# 讀取多個實體](#input---c-example-2)
* [C# 指令碼 - 讀取單一實體](#input---c-script-example-1)
* [C# 指令碼 - 讀取多個實體](#input---c-script-example-2)
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>輸入 - C# 範例 1

下列範例所示範的是讀取單一資料表列的[先行編譯 C#](functions-dotnet-class-library.md) 程式碼。 

列索引鍵值 "{queueTrigger}" 表示資料列索引鍵來自佇列訊息字串。

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
    }
}
```

### <a name="input---c-example-2"></a>輸入 - C# 範例 2

下列範例示範讀取多個資料表列的[先行編譯 C#](functions-dotnet-class-library.md) 程式碼。 請注意，`MyPoco` 類別衍生自 `TableEntity`。

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
        }
    }
}
```

### <a name="input---c-script-example-1"></a>輸入 - C# 指令碼範例 1

下列範例所示範的是使用繫結之 *function.json* 檔案，以及 [C# 指令碼](functions-reference-csharp.md)程式碼中的資料表輸入繫結。 此函式會使用佇列觸發程序來讀取單一資料表列。 

*function.json* 檔案會指定 `partitionKey` 和 `rowKey`。 `rowKey` 值 "{queueTrigger}" 表示資料列索引鍵來自佇列訊息字串。

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[設定](#input---configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

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

### <a name="input---c-script-example-2"></a>輸入 - C# 指令碼範例 2

下列範例所示範的是使用繫結之 *function.json* 檔案，以及 [C# 指令碼](functions-reference-csharp.md)程式碼中的資料表輸入繫結。 此函式會讀取佇列訊息中指定之分割區索引鍵的實體。

以下是 *function.json* 檔案：

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[設定](#input---configuration)章節會說明這些屬性。

C# 指令碼程式碼會新增對「Azure 儲存體 SDK」的參考，讓實體類型可以衍生自 `TableEntity`：

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

### <a name="input---f-example"></a>輸入 - F# 範例

下列範例所示範的是使用繫結之 *function.json* 檔案和 [F# 指令碼](functions-reference-fsharp.md)程式碼中的資料表輸入繫結。 此函式會使用佇列觸發程序來讀取單一資料表列。 

*function.json* 檔案會指定 `partitionKey` 和 `rowKey`。 `rowKey` 值 "{queueTrigger}" 表示資料列索引鍵來自佇列訊息字串。

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[設定](#input---configuration)章節會說明這些屬性。

以下是 F# 程式碼：

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

### <a name="input---javascript-example"></a>輸入 - JavaScript 範例

下列範例所示範的是使用繫結之 *function.json* 檔案，以及 [JavaScript 程式碼] (functions-reference-node.md) 中的資料表輸入繫結。 此函式會使用佇列觸發程序來讀取單一資料表列。 

*function.json* 檔案會指定 `partitionKey` 和 `rowKey`。 `rowKey` 值 "{queueTrigger}" 表示資料列索引鍵來自佇列訊息字串。

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[設定](#input---configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes-for-precompiled-c"></a>輸入 - 先行編譯 C# 的屬性
 
對於[先行編譯 C#](functions-dotnet-class-library.md) 函數，請使用下列屬性以設定資料表輸入繫結：

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)，定義於 NuGet 封裝 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中。

  屬性的建構函式採用資料表名稱、分割區索引鍵以及資料列索引鍵。 它可以用於 out 參數或函式的傳回值，如下列範例所示：

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  ```

  您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  ```

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)，定義於 NuGet 封裝 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中

  提供另一種方式來指定要使用的儲存體帳戶。 建構函式採用的是內含儲存體連接字串的應用程式設定名稱。 屬性可以套用在參數、方法或類別層級。 下列範例所示範的是類別層級與方法層級：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

要使用的儲存體帳戶按以下順序決定：

* `Table` 屬性的 `Connection` 內容。
* `StorageAccount` 屬性套用至與 `Table` 屬性相同的參數。
* `StorageAccount` 屬性套用至該函式。
* `StorageAccount` 屬性套用至該類別。
* 函數應用程式 (「AzureWebJobsStorage」應用程式設定) 的預設儲存體帳戶。

## <a name="input---configuration"></a>輸入 - 組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `Table` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `table`。 當您在 Azure 入口網站中建立繫結時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `in`。 當您在 Azure 入口網站中建立繫結時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中的資料表或實體的變數名稱。 | 
|**tableName** | **TableName** | 資料表的名稱。| 
|**partitionKey** | **PartitionKey** |選用。 要讀取之資料表實體的分割區索引鍵。 若要了解如何使用該屬性，請參閱[使用方式](#input---usage)一節。| 
|**rowKey** |**RowKey** | 選用。 要讀取之資料表實體的資料列索引鍵。 若要了解如何使用該屬性，請參閱[使用方式](#input---usage)一節。| 
|**take** |**Take** | 選用。 要在 JavaScript 中讀取的實體數目上限。 若要了解如何使用該屬性，請參閱[使用方式](#input---usage)一節。| 
|**filter** |**Filter** | 選用。 用於在 JavaScript 中輸入資料表的 OData 篩選運算式。 若要了解如何使用該屬性，請參閱[使用方式](#input---usage)一節。| 
|**連接** |**連接** | 應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為「MyStorage」，則函式執行階段會尋找名稱為「AzureWebJobsMyStorage」的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。<br/>當您要在本機開發時，應用程式設定會進入 [local.settings.json 檔案](functions-run-local.md#local-settings-file)的值。|

## <a name="input---usage"></a>輸入 - 使用方式

資料表儲存體輸入繫結支援下列案例：

* **讀取 C# 或 C# 指令碼中的一個資料列**

  請設定 `partitionKey` 和 `rowKey`。 使用方法參數 `T <paramName>` 存取資料表資料。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 `T` 通常是實作 `ITableEntity` 的型別或衍生自 `TableEntity`。 此案例中不使用 `filter` 和 `take` 屬性。 

* **讀取 C# 或 C# 指令碼中的一或多個資料列**

  使用方法參數 `IQueryable<T> <paramName>` 存取資料表資料。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 `T` 通常是實作 `ITableEntity` 的型別或衍生自 `TableEntity`。 您可以使用 `IQueryable` 方法，以執行任何所需的篩選條件。 此案例中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 屬性。  

> [!NOTE]
> `IQueryable` 無法在 .NET Core 中運作，因此它無法在 [Functions v2 執行階段](functions-versions.md)運作。

  替代方式是使用 Azure 儲存體 SDK，藉此利用 `CloudTable paramName` 方法參數來讀取資料表。

* **讀取 JavaScript 中的一或多個資料列**

  請設定 `filter` 和 `take` 屬性。 請勿設定 `partitionKey` 或 `rowKey`。 使用 `context.bindings.<name>` 來存取輸入資料表實體 (或多個實體)。 還原序列化的物件具有 `RowKey` 和 `PartitionKey` 屬性。

## <a name="table-storage-output-binding"></a>資料表儲存體輸出繫結

使用 Azure 資料表儲存體輸出繫結以寫入 Azure 儲存體帳戶中的資料表。

## <a name="output---example"></a>輸出 - 範例

請參閱特定語言的範例：

* [先行編譯 C#](#output---c-example)
* [C# 指令碼](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>輸出 - C# 範例

下列範例所示範的是使用 HTTP 觸發程序寫入單一資料表列的[先行編譯 C#](functions-dotnet-class-library.md) 程式碼。 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>輸出 - C# 指令碼範例

下列範例所示範的是使用繫結之 *function.json* 檔案，以及 [C# 指令碼](functions-reference-csharp.md)程式碼中的資料表輸出繫結。 函式會寫入多個資料表實體。

以下是 *function.json* 檔案：

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[設定](#output---configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

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

### <a name="output---f-example"></a>輸出 - F# 範例

下列範例所示範的是使用繫結之 *function.json* 檔案，以及 [F# 指令碼](functions-reference-fsharp.md)程式碼中的資料表輸出繫結。 函式會寫入多個資料表實體。

以下是 *function.json* 檔案：

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[設定](#output---configuration)章節會說明這些屬性。

以下是 F# 程式碼：

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

### <a name="output---javascript-example"></a>輸出 - JavaScript 範例

下列範例所示範的是使用繫結之 *function.json* 檔案，以及 [JavaScript 函式](functions-reference-node.md)中的資料表輸出繫結。 函式會寫入多個資料表實體。

以下是 *function.json* 檔案：

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[設定](#output---configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

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

## <a name="output---attributes-for-precompiled-c"></a>輸出 - 先行編譯 C# 的屬性

 對於[先行編譯 C#](functions-dotnet-class-library.md) 函式，會使用 [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)，其定義於 NuGet 封裝 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)。

屬性的建構函式採用資料表名稱。 它可以用於 `out` 參數或函式的傳回值，如下列範例所示：

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
```

您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
```

您可以使用 `StorageAccount` 屬性來指定類別、方法或參數層級的儲存體帳戶。 如需詳細資訊，請參閱[輸入 - 先行編譯 C# 的屬性](#input---attributes-for-precompiled-c)。

## <a name="output---configuration"></a>輸出 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `Table` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `table`。 當您在 Azure 入口網站中建立繫結時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `out`。 當您在 Azure 入口網站中建立繫結時，會自動設定此屬性。 |
|**name** | n/a | 函式程式碼中所使用的變數名稱，代表資料表或實體。 設為 `$return` 以參考函式傳回值。| 
|**tableName** |**TableName** | 資料表的名稱。| 
|**partitionKey** |**PartitionKey** | 要寫入之資料表實體的分割區索引鍵。 若要了解如何使用該屬性，請參閱[使用方式](#output---usage)一節。| 
|**rowKey** |**RowKey** | 要寫入之資料表實體的資料列索引鍵。 若要了解如何使用該屬性，請參閱[使用方式](#output---usage)一節。| 
|**連接** |**連接** | 應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為「MyStorage」，則函式執行階段會尋找名稱為「AzureWebJobsMyStorage」的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。<br/>當您要在本機開發時，應用程式設定會進入 [local.settings.json 檔案](functions-run-local.md#local-settings-file)的值。|

## <a name="output---usage"></a>輸出 - 使用方式

資料表儲存體輸出繫結支援下列案例：

* **以任何語言寫入單一資料列**

  在 C# 和 C# 指令碼中，使用方法參數 (例如 `out T paramName`) 或函式傳回值，藉此存取輸出資料表實體。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 如果分割區索引鍵和資料列索引鍵是由 *function.json* 檔案或 `Table` 屬性提供，`T` 可以是任何可序列化的型別。 否則，`T` 必須包含 `PartitionKey` 和 `RowKey` 屬性的型別。 在此案例中，`T` 通常會實作 `ITableEntity` 或衍生自 `TableEntity`，但不一定如此。

* **寫入 C# 或 C# 指令碼中的一或多個資料列**

  在 C# 和 C# 指令碼中，使用方法參數 `ICollector<T> paramName` 或 `ICollectorAsync<T> paramName` 存取輸出資料表實體。 在 C# 指令碼中，`paramName` 是 *function.json* 之 `name` 屬性中指定的值。 `T` 指定了您想要新增之實體的結構描述。 一般而言，`T` 會衍生自 `TableEntity` 或實作 `ITableEntity`，但不一定如此。 *function.json* 中的分割區索引鍵或資料列索引鍵值，或 `Table` 屬性建構函式不會在此案例中使用。

  替代方式是藉由使用 Azure 儲存體 SDK，利用 `CloudTable paramName` 方法參數來寫入資料表。

* **寫入 JavaScript 中的一或多個資料列**

  在 JavaScript 函式中，會使用 `context.bindings.<name>` 來存取資料表輸出。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
