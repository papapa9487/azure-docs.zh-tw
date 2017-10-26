---
title: "Azure Functions 計時器觸發程序 | Microsoft Docs"
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
ms.openlocfilehash: 12beb090a95a31c7e83ae03a920016bdfbf474e3
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2017
---
# <a name="azure-functions-timer-trigger"></a>Azure Functions 計時器觸發程序

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

這篇文章說明如何在 Azure Functions 中設定及撰寫計時器觸發程序。 Azure Functions 具有計時器觸發程序繫結，可讓您根據所定義的排程執行函式程式碼。 

計時器觸發程序支援多個執行個體向外延展。特定計時器函式的單一執行個體會對所有執行個體執行。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>計時器觸發程序
函式的計時器觸發程序會使用 function.json `bindings` 陣列中的下列 JSON 物件︰

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

`schedule` 的值是包含以下 6 個欄位的 [CRON 運算式](http://en.wikipedia.org/wiki/Cron#CRON_expression)︰ 

    {second} {minute} {hour} {day} {month} {day-of-week}
&nbsp;
>[!NOTE]   
>您會在線上找到的許多 cron 運算式會省略 `{second}` 欄位。 如果您從中複製一個，您需要對 `{second}` 欄位進行額外調整。 如需特定範例，請參閱以下的[排程範例](#examples)。

CRON 運算式使用的預設時區是國際標準時間 (UTC)。 若要讓 CRON 運算式以另一個時區為基礎，請為名為 `WEBSITE_TIME_ZONE` 的函式應用程式建立新的應用程式設定。 將值設定為所需的時區名稱，如 [Microsoft 時區索引](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx)中所示。 

例如，*美加東部標準時間*是 UTC-05:00。 若要讓計時器觸發程序在每天上午 10:00 (美加東部標準時間) 觸發，您可以使用說明 UTC 時區的下列 CRON 運算式︰

```json
"schedule": "0 0 15 * * *",
``` 

或者，您可以為名為 `WEBSITE_TIME_ZONE` 的函式應用程式新增新的應用程式設定，並將值設為**美加東部標準時間**。  那麼，下列 CRON 運算式即可用於 EST 上午 10:00： 

```json
"schedule": "0 0 10 * * *",
``` 


<a name="examples"></a>

## <a name="schedule-examples"></a>排程範例
以下是您可以用於 `schedule` 屬性的 CRON 運算式的一些範例。 

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

<a name="usage"></a>

## <a name="trigger-usage"></a>觸發程序使用方式
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

<a name="sample"></a>

## <a name="trigger-sample"></a>觸發程序範例
假設您的 function.json `bindings` 陣列中有下列計時器觸發程序︰

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

請參閱可讀取計時器物件，以查看是否晚執行的特定語言範例。

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C# 中的觸發程序範例 #
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

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F# 中的觸發程序範例 #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js 中的觸發程序範例
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

## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

