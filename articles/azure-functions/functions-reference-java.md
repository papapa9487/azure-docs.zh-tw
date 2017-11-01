---
title: "適用於 Azure Functions 的 Java 開發人員參考 | Microsoft Docs"
description: "了解如何使用 Java 開發函式。"
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: "azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構, Java"
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/20/2017
ms.author: routlaw
ms.openlocfilehash: dc9a1b6061c41cd623e1ddb3bb9dbb87530a13d5
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2017
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 開發人員指南
> [!div class="op_single_selector"]
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

## <a name="programming-model"></a>程式設計模型 

您的 Azure 函式應該是無狀態類別方法，處理輸入及產生輸出。 雖然您可以寫入執行個體方法，但是您的函式不得相依於類別的任何執行個體欄位。 所有函式方法必須有 `public` 存取修飾詞。

## <a name="triggers-and-annotations"></a>觸發程序和註解

通常 Azure 函式會因為外部觸發程序而被叫用。 您的函式必須處理該觸發程序和其相關聯的輸入，並產生一或多個輸出。

Java 註釋會包含在 `azure-functions-java-core` 套件中，以將輸入和輸出繫結至您的方法。 下表包含支援的輸入觸發程序和輸出繫結註釋：

繫結 | 註解
---|---
CosmosDB | N/A
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | N/A
通知中樞 | N/A
儲存體 Blob | <ul><li>`BlobTrigger`</li><li>`BlobOutput`</li><li>`BlobOutput`</li></ul>
儲存體佇列 | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
儲存體資料表 | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
計時器 | <ul><li>`TimerTrigger`</li></ul>
Twilio | N/A

觸發程序輸入和輸出也可以在應用程式的 [function.json](/azure/azure-functions/functions-reference#function-code) 中定義。

> [!IMPORTANT] 
> 您必須在 [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) 中設定 Azure 儲存體帳戶，以在本機執行 Azure 儲存體 Blob、佇列或資料表觸發程序。

使用註釋的範例：

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

撰寫的相同函式且沒有註釋：

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

具有對應 `function.json`：

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="data-types"></a>資料類型

您可以安心地針對輸入和輸出資料在 Java 中使用所有資料類型，包括原生類型。自訂 Java 類型及特殊 Azure 類型會在 `azure-functions-java-core` 套件中定義。 Azure Functions 執行階段會嘗試將收到的輸入轉換為您的程式碼要求的類型。

### <a name="strings"></a>字串

如果函式的對應輸入參數類型是 `String`，傳遞至函式方法的值會轉換成字串。 

### <a name="plain-old-java-objects-pojos"></a>純舊 Java 物件 (POJO)

如果函式方法的輸入預期為 Java 類型，使用 JSON 格式化的字串會轉換為 Java 類型。 這項轉換可讓您將 JSON 輸入傳遞至函式，並且與程式碼中的 Java 類型搭配使用，而不必在您自己的程式碼中實作轉換。

作為函式輸入的 POJO 類型必須具有與在其中使用之函式方法相同的 `public` 存取修飾詞。 您不需要宣告 POJO 類別欄位 `public`。 例如，JSON 字串 `{ "x": 3 }` 可以轉換成下列 POJO 類型：

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>二進位資料

二進位資料在 Azure 函式程式碼中會表示為 `byte[]`。 藉由將 function.json 中的 `dataType` 欄位設定為 `binary`，將二進位輸入或輸出繫結至您的函式：

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

然後在您的函式程式碼中使用它：

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

使用 `OutputBinding<byte[]>` 類型來進行二進位輸出繫結。


## <a name="function-method-overloading"></a>函式方法多載

您可以使用相同名稱但是不同類型，多載函式方法。 例如，您可以在一個類別中同時具有 `String echo(String s)` 和 `String echo(MyType s)`，Azure Functions 執行階段會決定要叫用哪一個，方法是檢查實際輸入類型 (針對 HTTP 輸入，MIME 類型 `text/plain` 會導致 `String`，`application/json` 代表 `MyType`)。

## <a name="inputs"></a>輸入

在 Azure Functions 中輸入會分成兩個類別：一個是觸發程序輸入，另一個是額外的輸入。 雖然它們在 `function.json` 中是不同的，在 Java 程式碼中的使用方式則相同。 讓我們以下列程式碼片段為例：

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String in, @BindingName("item") MyObject obj) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

`@BindingName` 註釋接受 `String` 屬性，它表示 `function.json` 中定義的繫結/觸發程序的名稱：

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

因此叫用此函式時，HTTP 要求裝載會為引數 `in` 傳遞 `String`，Azure 資料表儲存體 `MyObject` 會傳遞至引數 `obj`。

## <a name="outputs"></a>輸出

輸出可以使用傳回值或輸出參數來表示。 如果只有一個輸出，建議您使用傳回值。 若為多個輸出，您必須使用輸出參數。

傳回值是最簡單形式的輸出，您只要傳回任何類型的值，Azure Functions 執行階段就會嘗試將它封送處理回實際類型 (例如 HTTP 回應)。 在 `functions.json` 中，您使用 `$return` 作為輸出繫結的名稱。

若要產生多個輸出值，請使用 `azure-functions-java-core` 套件中定義的 `OutputBinding<T>` 類型。 如果您需要進行 HTTP 回應，同時將訊息推送至佇列，您可以撰寫如下的程式碼：

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputBinding;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, 
    @QueueOutput(queueName = "messages", connection = "AzureWebJobsStorage", name = "queue") OutputBinding<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

此程式碼應該會在 `function.json` 中定義輸出繫結：

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "queue",
      "direction": "out",
      "queueName": "messages",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>特殊類型

有時候函式必須對輸入和輸出有細微控制權。 在 `azure-functions-java-core` 套件中為您提供特殊類型，來管理要求資訊以及訂製 HTTP 觸發程序的傳回狀態：

| 特殊類型      |       目標        | 一般使用方式                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage`  |    HTTP 觸發程序     | 取得方法、標頭或查詢 |
| `HttpResponseMessage` | HTTP 輸出繫結 | 傳回 200 以外的狀態   |

> [!NOTE] 
> 您也可以使用 `@BindingName` 註釋來取得 HTTP 標頭和查詢。 例如，`@Bind("name") String query` 會逐一查看 HTTP 要求標頭和查詢，並將該值傳遞至方法。 例如，如果要求 URL 是 `http://example.org/api/echo?name=test`，則 `query` 是 `"test"`。

## <a name="functions-execution-context"></a>函式執行內容

您透過 `azure-functions-java-core` 套件中定義的 `ExecutionContext` 物件，與 Azure Functions 執行環境互動。 使用 `ExecutionContext` 物件以在您的程式碼中使用引動資訊和函式執行階段資訊。

### <a name="logging"></a>記錄

可以透過 `ExecutionContext` 物件存取函式執行階段記錄器。 這個記錄器會繫結至 Azure 監視，並可讓您將函式執行期間遇到的警告和錯誤加上旗標。

下列範例程式碼會在收到的要求主體是空白時，記錄一則警告訊息。

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received in " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱下列資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
