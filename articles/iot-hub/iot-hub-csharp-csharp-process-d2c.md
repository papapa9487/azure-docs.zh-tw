---
title: "使用 Azure IoT 中樞路由傳送訊息 (.NET) | Microsoft Docs"
description: "如何使用路由規則和自訂端點來處理 Azure IoT 中樞的裝置到雲端訊息，以將訊息分派至其他後端服務。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: d8fed08aa22577574b30b360ec164daf592ed456
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="routing-messages-with-iot-hub-net"></a>使用 IoT 中樞路由傳送訊息 (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

本教學課程是以[開始使用 IoT 中樞]教學課程為基礎。 教學課程會：

* 說明如何使用路由規則，以簡單的設定方式來分派裝置到雲端訊息。
* 說明如何從解決方案後端隔離需要立即採取行動的互動式訊息，以便進一步處理。 例如，裝置可能會傳送一則警示訊息，以觸發將票證插入 CRM 系統的作業。 相較之下，資料點訊息 (例如溫度遙測) 只會饋送至分析引擎。

在本教學課程結尾處，您會執行三個 .NET 主控台應用程式：

* **SimulatedDevice** (修改自[開始使用 IoT 中樞]教學課程中所建立的應用程式) 每秒會傳送資料點的裝置到雲端訊息，而且每 10 秒會傳送互動式裝置到雲端訊息。
* **ReadDeviceToCloudMessages** 會顯示裝置應用程式所傳送的非關鍵性遙測。
* **ReadCriticalQueue** 可從服務匯流排佇列中移除裝置應用程式所傳送的重要訊息。 此佇列連結至 IoT 中樞。

> [!NOTE]
> IoT 中樞對於許多裝置平台和語言 (包括 C、Java 和 JavaScript) 提供 SDK 支援。 若要了解如何以實體裝置取代本教學課程中的模擬裝置，請參閱 [Azure IoT 開發人員中心]。

若要完成此教學課程，您需要下列項目：

* Visual Studio 2015 或 Visual Studio 2017。
* 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立 [免費帳戶](https://azure.microsoft.com/free/) 。

我們也建議您閱讀 [Azure 儲存體]和 [Azure 服務匯流排]的相關文件。

## <a name="send-interactive-messages"></a>傳送互動式訊息

修改在[開始使用 IoT 中樞]教學課程中建立的裝置應用程式，偶爾傳送互動式訊息。

在 Visual Studio 的 **SimulatedDevice** 專案中，以下列程式碼取代 `SendDeviceToCloudMessagesAsync` 方法：

```csharp
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            if (rand.NextDouble() > 0.5)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else 
            {
                messageString = "This is a storage message";
                levelValue = "storage";
            }
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

這個方法會隨機將 `"level": "critical"` 和 `"level": "storage"` 屬性新增至裝置所傳送的訊息，這會模擬需要應用程式後端立即採取行動的訊息，或是需要永久儲存的訊息。 應用程式會在訊息屬性中傳遞此資訊，而不是在訊息主體中傳遞，因此 IoT 中樞可以將訊息路由傳送至適當的訊息目的地。

> [!NOTE]
> 除了此處顯示的最忙碌路徑範例以外，您可以使用訊息屬性來路由傳送各種案例的訊息，包括冷門路徑處理。

> [!NOTE]
> 我們建議您依 MSDN 文章[暫時性錯誤處理]中的建議，實作如指數型輪詢的重試原則。

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>將訊息路由至 IoT 中樞中的佇列

在本節中，您可：

* 建立服務匯流排佇列。
* 將它連接到您的 IoT 中樞。
* 設定您的 IoT 中樞，根據訊息上存在的屬性將訊息傳送到佇列。

如需有關如何處理來自服務匯流排佇列之訊息的詳細資訊，請參閱[開始使用佇列][Service Bus queue]。

1. 如[開始使用佇列][Service Bus queue]所述，建立服務匯流排佇列。 此佇列必須與您的 IoT 中樞位於相同的訂用帳戶和區域中。 記下命名空間和佇列名稱。

    > [!NOTE]
    > 作為 IoT 中樞端點的服務匯流排佇列和主題不能啟用 [工作階段] 或 [重複偵測]。 如果已啟用其中一個選項，端點會在 Azure 入口網站中顯示為 [無法連線]。

2. 在 Azure 入口網站中，開啟 IoT 中樞然後按一下 [端點]。
    
    ![IoT 中樞端點][30]

3. 在 [端點] 刀鋒視窗中，按一下頂端的 [新增] 將您的佇列新增至 IoT 中樞。 將端點命名為 **CriticalQueue**，並使用下拉式清單來選取 [服務匯流排佇列]、您的佇列所在的服務匯流排命名空間，以及您的佇列名稱。 完成時，請按一下底部的 [儲存]。
    
    ![新增端點][31]
    
4. 現在按一下 IoT 中樞中的 [路由]。 按一下刀鋒視窗頂端的 [新增] 來建立路由規則，以便將訊息路由傳送至您剛才新增的佇列。 選取 **DeviceTelemetry** 做為資料來源。 輸入 `level="critical"` 做為條件，然後選擇您剛才新增為自訂端點的佇列做為路由規則端點。 完成時，請按一下底部的 [儲存]。
    
    ![新增路由][32]
    
    確定後援路由設定為 [開啟]。 這個值是 IoT 中樞的預設設定。
    
    ![後援路由][33]

## <a name="read-from-the-queue-endpoint"></a>從佇列端點讀取

在本節中，您會讀取佇列端點中的訊息。

1. 在 Visual Studio 中，使用 [主控台應用程式 (.NET Framework)] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 將專案命名為 **ReadCriticalQueue**。

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **ReadCriticalQueue** 專案，然後按一下 [管理 NuGet 套件]。 此操作會顯示 [NuGet 套件管理員]  視窗。

3. 搜尋 **WindowsAzure.ServiceBus**，按一下 [安裝]，然後接受使用規定。 此操作會對 Azure 服務匯流排及其所有相依性進行下載、安裝和新增參考。

4. 在 **Program.cs** 檔案的最上方，新增下列 **using** 陳述式：
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. 最後，將下列幾行新增到 **Main** 方法中。 以佇列的 **Listen** 權限取代連接字串：
   
    ```csharp
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>執行應用程式
現在您已經準備好執行應用程式。

1. 在 Visual Studio 的方案總管中，以滑鼠右鍵按一下您的方案，然後選取 [設定啟始專案]。 選取 [多個啟始專案]，然後針對 **ReadDeviceToCloudMessages**、**SimulatedDevice** 和 **ReadCriticalQueue** 專案選取 [啟動] 做為動作。
2. 按 **F5** 啟動三個主控台應用程式。 **ReadDeviceToCloudMessages** 應用程式只有從 **SimulatedDevice** 應用程式傳送的非重要訊息，而 **ReadCriticalQueue** 應用程式只有重要訊息。
   
   ![三個主控台應用程式][50]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(選擇性) 將儲存體容器新增至 IoT 中樞並將訊息路由傳送給它

在本節中，您會建立儲存體帳戶，將它連線到您的 IoT 中樞，然後設定 IoT 中樞以根據訊息上的屬性將訊息傳送至該帳戶。 如需如何管理儲存體的詳細資訊，請參閱[開始使用 Azure 儲存體][Azure 儲存體]。

 > [!NOTE]
   > 如果您不受限於單一**端點**，除了 **CriticalQueue** 之外，您還可以設定 **StorageContainer**，並同時執行它們。

1. 按照 [Azure 儲存體文件][lnk-storage]中所述的方法來建立儲存體帳戶。 記下帳戶名稱。

2. 在 Azure 入口網站中，開啟 IoT 中樞然後按一下 [端點]。

3. 在 [端點] 刀鋒視窗中，選取 [CriticalQueue] 端點，然後按一下 [刪除]。 按一下 [是]，然後按一下 [新增]。 將端點命名為 **StorageContainer**，並使用下拉式清單選取 [Azure 儲存體容器]，然後建立**儲存體帳戶**和**儲存體容器**。  記下它們的名稱。  完成時，請按一下底部的 [確定]。 

 > [!NOTE]
   > 如果您不受限於單一**端點**，則不需要刪除 **CriticalQueue**。

4. 按一下 IoT 中樞中的 [路由]。 按一下刀鋒視窗頂端的 [新增] 來建立路由規則，以便將訊息路由傳送至您剛才新增的佇列。 選取 [裝置訊息] 作為資料來源。 輸入 `level="storage"` 作為條件，然後選擇 [StorageContainer] 作為路由規則端點。 按一下底部的 [儲存]。  

    確定後援路由設定為 [開啟]。 此設定為 IoT 中樞的預設設定。

1. 確定您先前的應用程式仍在執行。 

1. 在 Azure 入口網站中移至儲存體帳戶，並按一下 [Blob 服務] 下方的 [瀏覽 Blob]。選取您的容器，瀏覽至 JSON 檔案並按一下它，然後按一下 [下載] 來檢視資料。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已學到如何使用 IoT 中樞的訊息路由功能來可靠地分派裝置到雲端訊息。

[如何使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d]示範如何從解決方案後端將訊息傳送至您的裝置。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 套件][lnk-suite]。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南]。

若要深入了解 IoT 中樞的訊息路由，請參閱[使用 IoT 中樞傳送和接收訊息][lnk-devguide-messaging]。

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Azure 儲存體]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服務匯流排]: https://azure.microsoft.com/documentation/services/service-bus/
[IoT 中樞開發人員指南]: iot-hub-devguide.md
[開始使用 IoT 中樞]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Azure IoT 開發人員中心]: https://azure.microsoft.com/develop/iot
[暫時性錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
