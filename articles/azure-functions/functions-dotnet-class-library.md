---
title: "使用 .NET 類別庫搭配 Azure Functions | Microsoft Docs"
description: "了解如何撰寫 .NET 類別庫來與 Azure Functions 搭配使用"
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: 9f5db0c2-a88e-4fa8-9b59-37a7096fc828
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/09/2017
ms.author: donnam
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: f45b3f705ba3d11dd20221e3a7a465796d7a86a1
ms.contentlocale: zh-tw
ms.lasthandoff: 09/29/2017

---
# <a name="using-net-class-libraries-with-azure-functions"></a>使用 .NET 類別庫搭配 Azure Functions

除了指令碼檔之外，Azure Functions 還支援發佈類別庫作為一個或多個函式的實作。 建議您使用 [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/)。

## <a name="prerequisites"></a>必要條件 

本文有下列先決條件：

- [Visual Studio 2017 15.3 預覽](https://www.visualstudio.com/vs/preview/)。 安裝 **ASP.NET 和 Web 開發**及 **Azure 開發**工作負載。
- [Azure Function Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=AndrewBHall-MSFT.AzureFunctionToolsforVisualStudio2017)

## <a name="functions-class-library-project"></a>Functions 類別庫專案

從 Visual Studio 中建立 Azure Functions 專案。 新的專案範本會建立 host.json 和 local.settings.json 檔案。 您可以[在 host.json 中自訂 Azure Functions 執行階段設定](functions-host-json.md)。 

local.settings.json 檔案會儲存應用程式設定、連接字串和 Azure Functions Core Tools 的設定。 若要深入了解其結構，請參閱[在本機撰寫程式碼和測試 Azure Functions](functions-run-local.md#local-settings)。

### <a name="functionname-attribute"></a>FunctionName 屬性

屬性 [`FunctionNameAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/FunctionNameAttribute.cs) 會將方法標記為函式進入點。 它必須剛好僅與一個觸發程序以及 0 個以上的輸入和輸出繫結搭配使用。

### <a name="conversion-to-functionjson"></a>轉換成 function.json

當您建置 Azure Functions 專案時，會在函式的目錄中建立 *function.json* 檔案。 目錄名稱會與 `[FunctionName]` 屬性所指定的函式名稱相同。 *function.json* 檔案包含觸發程序和繫結，並指向專案組件檔。

此轉換是由 NuGet 套件 [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions) 執行。 來源可在 GitHub 存放庫 [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk) 提供使用。

## <a name="triggers-and-bindings"></a>觸發和繫結

下表列出的觸發程序和繫結可在 Azure Functions 類別庫專案中提供使用。 所有屬性都在 `Microsoft.Azure.WebJobs` 命名空間中。

| 繫結 | 屬性 | Nuget 套件 |
|------   | ------    | ------        |
| [Blob 儲存體觸發程序, 輸入, 輸出](#blob-storage) | [BlobAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | [Blob 儲存體] |
| [Cosmos DB 輸入和輸出繫結](#cosmos-db) | [DocumentDBAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] | 
| [事件中心觸發程序和輸出](#event-hub) | [EventHubTriggerAttribute], [EventHubAttribute] | [Microsoft.Azure.WebJobs.ServiceBus] |
| [外部檔案輸入和輸出](#api-hub) | [ApiHubFileAttribute] | [Microsoft.Azure.WebJobs.Extensions.ApiHub] |
| [HTTP 和 Webhook 觸發程序](#http) | [HttpTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.Http] |
| [Mobile Apps 輸入和輸出](#mobile-apps) | [MobileTableAttribute] | [Microsoft.Azure.WebJobs.Extensions.MobileApps] | 
| [通知中樞輸出](#nh) | [NotificationHubAttribute] | [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] | 
| [佇列儲存體觸發程序和輸出](#queue) | [QueueAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [SendGrid 輸出](#sendgrid) | [SendGridAttribute] | [Microsoft.Azure.WebJobs.Extensions.SendGrid] | 
| [服務匯流排觸發程序和輸出](#service-bus) | [ServiceBusAttribute], [ServiceBusAccountAttribute] | [Microsoft.Azure.WebJobs.ServiceBus]
| [資料表儲存體輸入和輸出](#table) | [TableAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [計時器觸發程序](#timer) | [TimerTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions] | 
| [Twilio 輸出](#twilio) | [TwilioSmsAttribute] | [Microsoft.Azure.WebJobs.Extensions.Twilio] | 

<a name="blob-storage"></a>

### <a name="blob-storage-trigger-input-and-output-bindings"></a>Blob 儲存體觸發程序、輸入和輸出繫結

Azure Functions 支援適用於 Azure Blob 儲存體的觸發程序、輸入和輸出繫結。 如需有關繫結運算式和中繼資料的詳細資訊，請參閱 [Azure Functions Blob 儲存體繫結](functions-bindings-storage-blob.md)。

Blob 觸發程序是使用 `[BlobTrigger]` 屬性定義。 您可以使用 `[StorageAccount]` 屬性來定義整個函式或類別所使用的儲存體帳戶。

```csharp
[StorageAccount("AzureWebJobsStorage")]
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Blob 輸入和輸出是使用 `[Blob]` 屬性定義，以及表示讀取或寫入的 `FileAccess` 參數。 下列範例是使用 blob 觸發程序和 blob 輸出繫結。

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
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

<a name="cosmos-db"></a>

### <a name="cosmos-db-input-and-output-bindings"></a>Cosmos DB 輸入和輸出繫結

Azure Functions 支援 Cosmos DB 的輸入和輸出繫結。 若要深入了解 Cosmos DB 繫結的功能，請參閱 [Azure Functions Cosmos DB 繫結](functions-bindings-documentdb.md)。

若要繫結至 Cosmos DB 文件，請使用 NuGet 套件 [Microsoft.Azure.WebJobs.Extensions.DocumentDB] 中的 `[DocumentDB]` 屬性。 下列範例具有佇列觸發程序和 DocumentDB API 輸出繫結：

```csharp
[FunctionName("QueueToDocDB")]        
public static void Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, 
    [DocumentDB("ToDoList", "Items", ConnectionStringSetting = "DocDBConnection")] out dynamic document)
{
    document = new { Text = myQueueItem, id = Guid.NewGuid() };
}

```

<a name="event-hub"></a>

### <a name="event-hubs-trigger-and-output"></a>事件中心觸發程序和輸出

Azure Functions 支援事件中樞的觸發程序和輸出繫結。 如需詳細資訊，請參閱 [Azure Functions 事件中樞繫結](functions-bindings-event-hubs.md)。

`[Microsoft.Azure.WebJobs.ServiceBus.EventHubTriggerAttribute]` 和 `[Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute]` 類型定義於 NuGet 套件 [Microsoft.Azure.WebJobs.ServiceBus] 中。 

下列範例會使用事件中心觸發程序：

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

下列範例具有輸出事件中心，是使用方法傳回值作為輸出：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

<a name="api-hub"></a>

### <a name="external-file-input-and-output"></a>外部檔案輸入和輸出

Azure Functions 支援適用於外部檔案 (例如 Google Drive、Dropbox 和 OneDrive) 的觸發程序、輸入和輸出繫結。 若要深入了解，請參閱 [Azure Functions 外部檔案繫結](functions-bindings-external-file.md)。 `[ExternalFileTrigger]` 和 `[ExternalFile]` 屬性定義於 NuGet 套件 [Microsoft.Azure.WebJobs.Extensions.ApiHub] 中。

下列 C# 範例示範外部檔案輸入和輸出繫結。 程式碼會將輸入檔案複製到輸出檔案。

```csharp
[StorageAccount("MyStorageConnection")]
[FunctionName("ExternalFile")]
[return: ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}-Copy", FileAccess.Write)]
public static string Run([QueueTrigger("myqueue-items")] string myQueueItem, 
    [ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}", FileAccess.Read)] string myInputFile, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    return myInputFile;
}
```

<a name="http"></a>

### <a name="http-and-webhooks"></a>HTTP 和 Webhook

使用 `HttpTrigger` 屬性來定義 HTTP 觸發程序或 webhook。 此屬性定義於 NuGet 套件 [Microsoft.Azure.WebJobs.Extensions.Http] 中。 您可以自訂授權層級、webhook 類型、路由和方法。 下列範例會使用匿名驗證和 _genericJson_ webhook 類型來定義 HTTP 觸發程序。

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a name="mobile-apps"></a>

### <a name="mobile-apps-input-and-output"></a>Mobile Apps 輸入和輸出

Azure Functions 支援 Mobile Apps 的輸入和輸出繫結。 若要深入了解，請參閱 [Azure Functions Mobile Apps 繫結](functions-bindings-mobile-apps.md)。 `[MobileTable]` 屬性定義於 NuGet 套件 [Microsoft.Azure.WebJobs.Extensions.MobileApps] 中。

下列範例會示範 Mobile Apps 輸出繫結：

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

<a name="nh"></a>

### <a name="notification-hubs-output"></a>通知中樞輸出

Azure Functions 會支援通知中樞的輸出繫結。 若要深入了解，請參閱 [Azure Functions 通知中樞輸出繫結](functions-bindings-notification-hubs.md)。 `[NotificationHub]` 屬性定義於 NuGet 套件 [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] 中。

<a name="queue"></a>

### <a name="queue-storage-trigger-and-output"></a>佇列儲存體觸發程序和輸出

Azure Functions 支援適用於 Azure 佇列的觸發程序和輸出繫結。 如需詳細資訊，請參閱 [Azure Functions 佇列儲存體繫結](functions-bindings-storage-queue.md)。

下列範例示範如何使用 `[Queue]` 屬性，搭配佇列輸出繫結使用函式傳回型別。 若要定義佇列觸發程序，請使用 `[QueueTrigger]` 屬性。

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // HTTP trigger with queue output binding
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }

    // Queue trigger
    [FunctionName("QueueTrigger")]
    [StorageAccount("AzureWebJobsStorage")]
    public static void QueueTrigger([QueueTrigger("myqueue-items")] string myQueueItem, TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}

```

<a name="sendgrid"></a>

### <a name="sendgrid-output"></a>SendGrid 輸出

Azure Functions 支援用於以程式設計方式傳送電子郵件的 SendGrid 輸出繫結。 若要深入了解，請參閱 [Azure Functions SendGrid 繫結](functions-bindings-sendgrid.md)。

`[SendGrid]` 屬性定義於 NuGet 套件 [Microsoft.Azure.WebJobs.Extensions.SendGrid] 中。

以下範例使用服務匯流排佇列觸發程序，以及使用 `SendGridMessage` 的 SendGrid 輸出繫結：

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

<a name="service-bus"></a>

### <a name="service-bus-trigger-and-output"></a>服務匯流排觸發程序和輸出

Azure Functions 支援服務匯流排佇列和主題的觸發程序和輸出繫結。 如需有關如何設定繫結的詳細資訊，請參閱 [Azure Functions 服務匯流排繫結](functions-bindings-service-bus.md)。

`[ServiceBusTrigger]` 和 `[ServiceBus]` 屬性定義於 NuGet 套件 [Microsoft.Azure.WebJobs.ServiceBus] 中。 

以下是服務匯流排佇列觸發程序的範例：

```csharp
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run([ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

以下是服務匯流排輸出繫結的範例，使用方法傳回型別作為輸出：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```        

<a name="table"></a>

### <a name="table-storage-input-and-output"></a>資料表儲存體輸入和輸出

Azure Functions 支援 Azure 資料表儲存體的輸入和輸出繫結。 若要深入了解，請參閱 [Azure Functions 資料表儲存體繫結](functions-bindings-storage-table.md)。

下列範例是具有兩個函式的類別，示範資料表儲存體輸出和輸入繫結。 

```csharp
[StorageAccount("AzureWebJobsStorage")]
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

    // use the metadata parameter "queueTrigger" to bind the queue payload
    [FunctionName("TableInput")]
    public static void TableInput([QueueTrigger("table-items")] string input, [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, TraceWriter log)
    {
        log.Info($"C# function processed: {poco.Text}");
    }
}

```

<a name="timer"></a>

### <a name="timer-trigger"></a>計時器觸發程序

Azure Functions 具有計時器觸發程序繫結，可讓您根據所定義的排程執行函式程式碼。 若要深入了解繫結的功能，請參閱[使用 Azure Functions 排程程式碼執行](functions-bindings-timer.md)。

在耗用量方案中，您可以使用 [CRON 運算式](http://en.wikipedia.org/wiki/Cron#CRON_expression)來定義排程。 如果您是使用 App Service 方案，也可以使用 TimeSpan 字串。 

下列範例會定義每隔 5 分鐘執行的計時器觸發程序：

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

<a name="twilio"></a>

### <a name="twilio-output"></a>Twilio 輸出

Azure Functions 支援 Twilio 輸出繫結，讓函式能傳送 SMS 文字訊息。 若要深入了解，請參閱 [使用 Twilio 輸出繫結從 Azure Functions 傳送 SMS 文字訊息](functions-bindings-twilio.md)。 

`[TwilioSms]` 屬性定義於 NuGet 套件 [Microsoft.Azure.WebJobs.Extensions.Twilio] 中。

下列 C# 範例是使用佇列觸發程序和 Twilio 輸出繫結：

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

## <a name="next-steps"></a>後續步驟

如需有關如何在 C# 指令碼中使用 Azure Functions 的詳細資訊，請參閱 [Azure Functions C\# 指令碼開發人員參考](functions-reference-csharp.md)。

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


<!-- NuGet packages --> 
[Microsoft.Azure.WebJobs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.DocumentDB]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.MobileApps]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.SendGrid]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.Http]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http/1.0.0-beta1
[Microsoft.Azure.WebJobs.Extensions.BotFramework]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.BotFramework/1.0.15-beta
[Microsoft.Azure.WebJobs.Extensions.ApiHub]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApiHub/1.0.0-beta4
[Microsoft.Azure.WebJobs.Extensions.Twilio]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/2.1.0-beta1


<!-- Links to source --> 
[DocumentDBAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs
[EventHubAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs
[EventHubTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs
[MobileTableAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs
[NotificationHubAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs 
[ServiceBusAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs
[ServiceBusAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs
[QueueAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs
[StorageAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs
[BlobAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs
[TableAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs
[TwilioSmsAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs
[SendGridAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs
[HttpTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs
[ApiHubFileAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.ApiHub/ApiHubFileAttribute.cs
[TimerTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs

