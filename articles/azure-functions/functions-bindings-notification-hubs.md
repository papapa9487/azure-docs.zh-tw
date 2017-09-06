---
title: "Azure Functions 通知中樞繫結 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用「Azure 通知中樞」繫結。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 02d01d0f6e945ed54dbe766aec2a0fd7c17c510f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-notification-hub-output-binding"></a>Azure Functions 通知中樞輸出繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

這篇文章說明如何在 Azure Functions 中為「Azure 通知中樞」繫結進行設定及撰寫程式碼。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

您的函式可使用已設定的「Azure 通知中樞」搭配幾行程式碼來傳送推播通知。 不過，「Azure 通知中樞」必須針對您要使用的「平台通知服務」(PNS) 做設定。 如需設定 Azure 通知中樞以及開發註冊來接收通知之用戶端應用程式的詳細資訊，請參閱 [開始使用通知中樞](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 並按一下頂端的目標用戶端平台。

您傳送的通知可以是原生通知或範本通知。 原生通知是以輸出繫結的 `platform` 屬性中所設定的特定通知平台為目標。 範本通知可用來以多個平台為目標。   

## <a name="notification-hub-output-binding-properties"></a>通知中樞輸出繫結屬性
function.json 檔案提供下列屬性：


|屬性  |說明  |
|---------|---------|
|**name** | 函式程式碼中用於通知中樞訊息的變數名稱。 |
|**type** | 必須設為 `notificationHub`。 |
|**tagExpression** | 標籤運算式可讓您指定將通知傳遞到一組裝置，這些裝置已註冊要接收與標籤運算式相符的通知。  如需詳細資訊，請參閱 [路由與標籤運算式](../notification-hubs/notification-hubs-tags-segment-push-message.md)。 |
|**hubName** | Azure 入口網站中通知中樞資源的名稱。 |
|**連接** | 此連接字串必須是設定為您通知中樞之 DefaultFullSharedAccessSignature 值的**應用程式設定**連接字串。 |
|**direction** | 必須設為 `out`。 | 
|**platform** | 此平台屬性指出作為您通知目標的通知平台。 依照預設，如果輸出繫結中省略平台屬性，範本通知可用來以「Azure 通知中樞」上所設定的任何平台為目標。 如需有關一般使用範本搭配「Azure 通知中樞」來傳送跨平台通知的詳細資訊，請參閱[範本](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)。 platform 在設定時必須是以下其中一個值： <ul><li><code>apns</code>&mdash;Apple Push Notification Service。 如果有關設定適用於 APNS 的通知中樞及在用戶端 App 中接收通知的詳細資訊，請參閱[使用 Azure 通知中樞將推播通知傳送至 iOS](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)。</li><li><code>adm</code>&mdash;[Amazon 裝置通訊](https://developer.amazon.com/device-messaging)。 如果有關設定適用於 ADM 的通知中樞及在 Kindle App 中接收通知的詳細資訊，請參閱[開始使用適用於 Kindle 應用程式的通知中樞](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)。</li><li><code>gcm</code>&mdash;[Google 雲端通訊](https://developers.google.com/cloud-messaging/)。 也支援 Firebase Cloud Messaging (新版 GCM)。 如需詳細資訊，請參閱[使用 Azure 通知中樞將推播通知傳送至 Android](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)。</li><li><code>wns</code>&mdash;以 Windows 平台為目標的 [Windows 推播通知服務](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)。 WNS 也支援 Windows Phone 8.1 和更新版本。 如需詳細資訊，請參閱[開始使用適用於 Windows 通用平台應用程式的通知中樞](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)。</li><li><code>mpns</code>&mdash;[Microsoft 推播通知服務](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx)。 此平台支援 Windows Phone 8 和舊版 Windows Phone 平台。 如需詳細資訊，請參閱[在 Windows Phone 上使用 Azure 通知中樞傳送推播通知](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)。</li></ul> |

function.json 範例：

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>通知中樞連接字串設定
若要使用通知中樞輸出繫結，必須設定中樞的連接字串。 您可以直接從函式中的 [整合] 索引標籤選取現有通知中樞或建立新的通知中樞。 您也可以手動設定連接字串。 

若要將連接字串設定為現有通知中樞：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)中的通知中樞，選擇 [存取原則]，然後選取 **DefaultFullSharedAccessSignature** 原則旁邊的 [複製] 按鈕。 這會將 DefaultFullSharedAccessSignature 原則的連接字串複製到通知中樞。 此連接字串提供您的函式存取權限來傳送通知訊息。 
    ![複製通知中樞連接字串](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. 瀏覽至 Azure 入口網站中的函式應用程式，選擇 [應用程式設定]，新增 `MyHubConnectionString` 之類的金鑰，貼上針對通知中樞所複製的 DefaultFullSharedAccessSignature 來作為值，然後按一下 [儲存]。

您現在可以使用此具名應用程式設定，在輸出繫結中定義通知中樞連線。

## <a name="apns-native-notifications-with-c-queue-triggers"></a>含 C# 佇列觸發程序的 APNS 原生通知
這個範例示範如何使用 [Microsoft Azure 通知中樞程式庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型來傳送原生 APNS 通知。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>含 C# 佇列觸發程序的 GCM 原生通知
這個範例示範如何使用 [Microsoft Azure 通知中樞程式庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型來傳送原生 GCM 通知。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="wns-native-notifications-with-c-queue-triggers"></a>含 C# 佇列觸發程序的 WNS 原生通知
這個範例示範如何使用 [Microsoft Azure 通知中樞程式庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型來傳送原生 WNS 快顯通知。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="template-example-for-nodejs-timer-triggers"></a>Node.js 計時器觸發程序的範本範例
這個範例會傳送包含 `location` 和 `message` 的[範本註冊](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)通知。

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="template-example-for-f-timer-triggers"></a>F# 計時器觸發程序的範本範例
這個範例會傳送包含 `location` 和 `message` 的[範本註冊](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)通知。

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>使用 out 參數的範本範例
這個範例會傳送在範本中包含 `message` 預留位置的[範本註冊](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)通知。

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

## <a name="template-example-with-asynchronous-function"></a>含非同步函式的範本範例
如果您使用非同步程式碼，則不允許使用 out 參數。 在此情況下，請使用 `IAsyncCollector` 來傳回您的範本通知。 下列程式碼是上述程式碼的非同步範例。 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

## <a name="template-example-using-json"></a>使用 JSON 的範本範例
這個範例會使用有效的 JSON 字串來傳送在範本中包含 `message` 預留位置的[範本註冊](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)通知。

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>使用通知中樞程式庫類型的範本範例
這個範例示範如何使用 [Microsoft Azure 通知中樞程式庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型。 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

## <a name="next-steps"></a>後續步驟
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


