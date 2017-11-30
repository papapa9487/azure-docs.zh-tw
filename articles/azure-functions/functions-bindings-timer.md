---
title: "Azure Functions 計時器觸發程序"
description: "了解如何在 Azure Functions 中使用計時器觸發程序。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: glenga
ms.custom: 
ms.openlocfilehash: 2a62d70b22081e45bc318dd9fb624b37cf7069e3
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="azure-functions-timer-trigger"></a>Azure Functions 計時器觸發程序

本文說明如何在 Azure Functions 中使用計時器觸發程序。 計時器觸發程序可讓您依照排程執行函式。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>範例

請參閱特定語言的範例：

* [先行編譯 C#](#trigger---c-example)
* [C# 指令碼](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>C# 範例

下列範例示範每五分鐘執行一次的[先行編譯 C# 函式](functions-dotnet-class-library.md)：

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>C# 指令碼範例

下列範例示範 function.json 檔案中的計時器觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式會寫入一項記錄，指出此函式引動過程是否由遺失的排程項目所造成。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

以下是 C# 指令碼程式碼：

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>F# 範例

下列範例示範 function.json 檔案中的計時器觸發程序繫結，以及使用此繫結的 [F# 指令碼函式](functions-reference-fsharp.md)。 此函式會寫入一項記錄，指出此函式引動過程是否由遺失的排程項目所造成。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

以下是 F# 指令碼程式碼：

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>JavaScript 範例

下列範例示範 function.json 檔案中的計時器觸發程序繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式會寫入一項記錄，指出此函式引動過程是否由遺失的排程項目所造成。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

以下是 F# 指令碼程式碼：

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes-for-precompiled-c"></a>先行編譯 C# 的屬性

對於[先行編譯 C#](functions-dotnet-class-library.md) 函式，使用 [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)，其定義於 NuGet 套件 [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions)。

該屬性的建構函式會採用 CRON 運算式，如下列範例所示：

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
 ```

如果您的函式應用程式在 App Service 方案 (非取用量方案) 上執行，您可以指定 `TimeSpan` 而不是 CRON 運算式。

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `TimerTrigger` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設定為 "timerTrigger"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設定為 "in"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中計時器物件的變數名稱。 | 
|**schedule**|**ScheduleExpression**|在取用量方案中，您可以使用 CRON 運算式來定義排程。 如果您使用 App Service 方案，也可以使用 `TimeSpan` 字串。 下列各節說明 CRON 運算式。 您可以將排程運算式放在應用程式設定中，並將此屬性設定為以 **%** 符號包裝的值，如此範例所示："%NameOfAppSettingWithCRONExpression%"。 當您要在本機開發時，應用程式設定會進入 [local.settings.json 檔案](functions-run-local.md#local-settings-file)的值。|

### <a name="cron-format"></a>CRON 格式 

Azure Functions 計時器觸發程序的 [CRON 運算式](http://en.wikipedia.org/wiki/Cron#CRON_expression)包含下列六個欄位： 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>您在線上找到的許多 CRON 運算式會省略 `{second}` 欄位。 如果您複製其中一個運算式，請新增遺漏的 `{second}` 欄位。

### <a name="cron-time-zones"></a>CRON 時區

CRON 運算式使用的預設時區是國際標準時間 (UTC)。 若要讓 CRON 運算式以另一個時區為基礎，請為名為 `WEBSITE_TIME_ZONE` 的函式應用程式建立新的應用程式設定。 將值設定為所需的時區名稱，如 [Microsoft 時區索引](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx)中所示。 

例如，*美加東部標準時間*是 UTC-05:00。 若要讓計時器觸發程序在每天上午 10:00 (美加東部標準時間) 觸發，您可以使用說明 UTC 時區的下列 CRON 運算式︰

```json
"schedule": "0 0 15 * * *",
``` 

或者，您可以為名為 `WEBSITE_TIME_ZONE` 的函式應用程式新增新的應用程式設定，並將值設為**美加東部標準時間**。  那麼，下列 CRON 運算式即可用於 EST 上午 10:00： 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>CRON 範例

以下是您可以在 Azure Functions 中使用於計時器觸發程序的一些 CRON 運算式範例。 

若要每隔 5 分鐘觸發一次︰

```json
"schedule": "0 */5 * * * *"
```

若要在每小時開始時觸發一次︰

```json
"schedule": "0 0 * * * *",
```

若要每隔 2 小時觸發一次：

```json
"schedule": "0 0 */2 * * *",
```

若要在上午 9 點到下午 5 點之間每隔一小時觸發一次：

```json
"schedule": "0 0 9-17 * * *",
```

若要在每天上午 9:30 觸發一次：

```json
"schedule": "0 30 9 * * *",
```

若要在每個工作天上午 9:30 觸發一次：

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="usage"></a>使用量

叫用計時器觸發程序函式時，[計時器物件](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)會傳遞至函式。 下列 JSON 是計時器物件的範例表示法。 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

## <a name="scale-out"></a>向外延展

計時器觸發程序支援多個執行個體向外延展。特定計時器函式的單一執行個體會對所有執行個體執行。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至使用計時器觸發程序的快速入門](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
