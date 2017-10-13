---
title: "Azure Functions Mobile Apps 繫結 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure Mobile Apps 繫結。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
ms.openlocfilehash: d2c0e4e233761584bad2df05a8e702e4fc77e84f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-mobile-apps-bindings"></a>Azure Functions Mobile Apps 繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

這篇文章說明如何在 Azure Functions 中為 [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) 繫結進行設定及撰寫程式碼。 Azure Functions 支援 Mobile Apps 的輸入和輸出繫結。

Mobile Apps 輸入和輸出繫結可讓您在行動裝置應用程式中[對資料表往返讀取和寫入資料](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>Mobile Apps 輸入繫結
Mobile Apps 輸入繫結會從行動資料表端點載入記錄，並將它傳遞到您的函式。 在 C# 和 F# 函式中，當函式成功結束時，會將記錄所做的任何變更自動傳回資料表。

函式的 Mobile Apps 輸入會使用 function.json `bindings` 陣列中的下列 JSON 物件︰

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

請注意：

* `id` 可以是靜態，或基於叫用函式的觸發程序。 例如，如果您對函式使用[佇列觸發程序]()，則 `"id": "{queueTrigger}"` 會使用佇列訊息的字串值做為要擷取的記錄識別碼。
* `connection` 應該包含函式應用程式中應用程式設定的名稱，因而包含行動裝置應用程式的 URL。 函式會使用此 URL 針對您的行動裝置應用程式建構所需的 REST 作業。 您會[在包含您的行動裝置應用程式 URL 的函式應用程式中建立應用程式設定]() (看起來類似 `http://<appname>.azurewebsites.net`)，然後在輸入繫結的 `connection` 屬性中指定應用程式設定的名稱。 
* 如果您[在您的 Node.js 行動裝置應用程式後端中實作 API 金鑰](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)，或[在您的 .NET 行動裝置應用程式後端中實作 API 金鑰](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)，則必須指定 `apiKey`。 若要這樣做，您會[在包含 API 金鑰的函式應用程式中建立應用程式設定]()，然後在具有應用程式設定名稱的輸入繫結中新增 `apiKey` 屬性。 
  
  > [!IMPORTANT]
  > 不能與您的行動裝置應用程式用戶端共用此 API 金鑰。 只應該安全地散佈給服務端用戶端，如 Azure Functions。 
  > 
  > [!NOTE]
  > Azure Functions 將會您的連接資訊和 API 金鑰儲存為應用程式設定，使得不會將讓它們簽入至您的原始檔控制儲存機制。 這可保護您的敏感資訊。
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>輸入使用方式
本節說明如何在您的函式程式碼中使用您的 Mobile Apps 輸入繫結。 

找到具有指定的資料表和記錄識別碼的記錄時，它會傳遞到具名 [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) 參數 (或在 Node.js 中，則會傳遞到 `context.bindings.<name>` 物件)。 找不到記錄時，參數為 `null`。 

在 C# 和 F# 函式中，當函式成功結束時，會將對輸入記錄 (輸入參數) 所做的任何變更自動傳送回 Mobile Apps。 在 Node.js 函式中，使用 `context.bindings.<name>` 來存取輸入記錄。 您無法在 Node.js 中修改記錄。

<a name="inputsample"></a>

## <a name="input-sample"></a>輸入範例
假設您有下列 function.json，其擷取具有下列訊息佇列觸發程序識別碼的 Mobile App 資料表記錄︰

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
],
"disabled": false
}
```

請參閱使用來自繫結之輸入記錄的特定語言範例。 C# 和 F# 範例也會修改記錄的 `text` 屬性。

* [C#](#inputcsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>C# 中的輸入範例 #

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js 中的輸入範例

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>Mobile Apps 輸出繫結
使用 Mobile Apps 輸出繫結將新記錄寫入至 Mobile Apps 資料表端點。  

函式的 Mobile Apps 輸出會使用 function.json `bindings` 陣列中的下列 JSON 物件︰

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

請注意：

* `connection` 應該包含函式應用程式中應用程式設定的名稱，因而包含行動裝置應用程式的 URL。 函式會使用此 URL 針對您的行動裝置應用程式建構所需的 REST 作業。 您會[在包含您的行動裝置應用程式 URL 的函式應用程式中建立應用程式設定]() (看起來類似 `http://<appname>.azurewebsites.net`)，然後在輸入繫結的 `connection` 屬性中指定應用程式設定的名稱。 
* 如果您[在您的 Node.js 行動裝置應用程式後端中實作 API 金鑰](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)，或[在您的 .NET 行動裝置應用程式後端中實作 API 金鑰](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)，則必須指定 `apiKey`。 若要這樣做，您會[在包含 API 金鑰的函式應用程式中建立應用程式設定]()，然後在具有應用程式設定名稱的輸入繫結中新增 `apiKey` 屬性。 
  
  > [!IMPORTANT]
  > 不能與您的行動裝置應用程式用戶端共用此 API 金鑰。 只應該安全地散佈給服務端用戶端，如 Azure Functions。 
  > 
  > [!NOTE]
  > Azure Functions 將會您的連接資訊和 API 金鑰儲存為應用程式設定，使得不會將讓它們簽入至您的原始檔控制儲存機制。 這可保護您的敏感資訊。
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>輸出使用方式
本節說明如何在您的函式程式碼中使用您的 Mobile Apps 輸出繫結。 

在 C# 函式中，使用類型 `out object` 的具名輸出參數來存取輸出記錄。 在 Node.js 函式中，使用 `context.bindings.<name>` 來存取輸出記錄。

<a name="outputsample"></a>

## <a name="output-sample"></a>輸出範例
假設您有下列 function.json，其定義一個佇列觸發程序和一個 Mobile Apps 輸出︰

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

請參閱會在 Mobile Apps 資料表端點建立一筆記錄與佇列訊息內容的特定語言範例。

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C# 中的輸出範例 #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js 中的輸出範例

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

