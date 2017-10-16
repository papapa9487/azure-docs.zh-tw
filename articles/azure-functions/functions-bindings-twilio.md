---
title: "Azure Functions Twilio 繫結 | Microsoft Docs"
description: "了解如何搭配使用 Twilio 繫結與 Azure Functions。"
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: a60263aa-3de9-4e1b-a2bb-0b52e70d559b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/20/2016
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e8c5e8f2dfedae26486e1c8afbe0cec3f3228e86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="send-sms-messages-from-azure-functions-using-the-twilio-output-binding"></a>使用 Twilio 輸出繫結從 Azure Functions 傳送手機訊息
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何設定 Twilio 繫結，以及其如何與 Azure Functions 搭配使用。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Functions 支援 Twilio 輸出繫結，讓函式傳送具有數行程式碼和一個 [Twilio](https://www.twilio.com/) 帳戶的 SMS 文字訊息。 

## <a name="functionjson-for-the-twilio-output-binding"></a>適用於 Twilio 輸出繫結的 function.json
function.json 檔案提供下列屬性：

|屬性  |說明  |
|---------|---------|
|**name**| 用於 Twilio 簡訊文字訊息之函式程式碼中的變數名稱。 |
|**type**| 必須設為 `twilioSms`。|
|**accountSid**| 此值必須設定為保留您 Twilio 帳戶 Sid 的應用程式設定名稱。|
|**authToken**| 此值必須設定為保留您 Twilio 驗證權杖的應用程式設定名稱。|
|**to**| 此值設定為簡訊文字傳送至的電話號碼。|
|**from**| 此值設定為從中傳送簡訊文字的電話號碼。|
|**direction**| 必須設為 `out`。|
|**body**| 如果您不需要在函式程式碼中動態設定 SMS 文字訊息，則此值可以用來硬式編碼 SMS 文字訊息。 |

function.json 範例：

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>具有 Twilio 輸出繫結的範例 C# 佇列觸發程序
#### <a name="synchronous"></a>同步
Azure 儲存體佇列觸發程序的這個同步範例程式碼會使用 out 參數，將文字訊息傳送給下單的客戶。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

#### <a name="asynchronous"></a>非同步
Azure 儲存體佇列觸發程序的這個非同步範例程式碼會將文字訊息傳送給下單的客戶。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>具有 Twilio 輸出繫結的範例 Node.js 佇列觸發程序
這個 Node.js 範例會將文字訊息傳送給下單的客戶。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

