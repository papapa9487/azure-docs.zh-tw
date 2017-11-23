---
title: "開始使用 Azure IoT 中樞 (.NET) | Microsoft Docs"
description: "了解如何使用適用於 .NET 的 IoT SDK 將裝置到雲端訊息傳送至 Azure IoT 中樞。 建立模擬裝置和服務應用程式來註冊您的裝置、傳送訊息，並從 IoT 中樞讀取訊息。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 24b75e4021d9d16aed317b48c2f963ff9dea04ac
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2017
---
# <a name="connect-your-device-to-your-iot-hub-using-net"></a>使用 .NET 將您的裝置連線至 IoT 中樞

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

在本教學課程結尾處，您會有三個 .NET 主控台應用程式：

* **CreateDeviceIdentity**，這會建立裝置身分識別與相關聯的安全性金鑰，以連線到您的裝置應用程式。
* **ReadDeviceToCloudMessages**，其中顯示裝置應用程式所傳送的遙測。
* **SimulatedDevice**，這會使用先前建立的裝置識別連接到您的 IoT 中樞，並使用 MQTT 通訊協定每秒傳送遙測訊息。

您可以下載或複製 Visual Studio 方案，其中包含來自 Github 的三個應用程式。

```bash
git clone https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app.git
```

> [!NOTE]
> 如需可用來建置兩個應用程式，以在裝置與您的解決方案後端執行之 Azure IoT SDK 的相關資訊，請參閱 [Azure IoT SDK][lnk-hub-sdks]。

若要完成此教學課程，您需要下列項目：

* Visual Studio 2015 或 Visual Studio 2017。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

您現在已經建立 IoT 中樞，因此您已具有完成本教學課程的其餘部分所需的主機名稱和 IoT 中樞連接字串。

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-csharp](../../includes/iot-hub-get-started-create-device-identity-csharp.md)]

<a id="D2C_csharp"></a>
## <a name="receive-device-to-cloud-messages"></a>接收裝置到雲端的訊息
在本節中，您會建立 .NET 主控台應用程式，以讀取來自 IoT 中樞的裝置到雲端訊息。 IoT 中樞會公開與 [Azure 事件中樞][lnk-event-hubs-overview]相容的端點以讓您讀取裝置到雲端訊息。 為了簡單起見，本教學課程會建立的基本讀取器不適合用於高輸送量部署。 若要了解如何大規模處理裝置到雲端訊息，請參閱[處理裝置到雲端訊息][lnk-process-d2c-tutorial]教學課程。 如需有關如何處理來自「事件中樞」之訊息的詳細資訊，請參閱[開始使用事件中樞][lnk-eventhubs-tutorial]教學課程。 (本教學課程適用於 IoT 中樞的事件中樞相容端點)。

> [!NOTE]
> 用於讀取裝置到雲端訊息的事件中樞相容端點一律會使用 AMQP 通訊協定。

1. 在 Visual Studio 中，使用 [主控台應用程式 (.NET Framework)] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的解決方案。 確定 .NET Framework 為 4.5.1 或更新版本。 將專案命名為 **ReadDeviceToCloudMessages**。

    ![新的 Visual C# Windows 傳統桌面專案][10a]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **ReadDeviceToCloudMessages** 專案，然後按一下 [管理 NuGet 套件]。

3. 在 [NuGet 套件管理員] 視窗中，搜尋 **WindowsAzure.ServiceBus**，選取 [安裝] 並接受使用規定。 此程序會下載及安裝 [Azure 服務匯流排][lnk-servicebus-nuget]，並新增對它的參考與其所有相依項目。 此套件可讓應用程式連接到 IoT 中樞上的事件中樞相容端點。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

    ```csharp
    using Microsoft.ServiceBus.Messaging;
    using System.Threading;
    ```

5. 將下列欄位新增到 **Program** 類別。 將預留位置的值替換為您在＜建立 IoT 中樞＞一節中為中樞所建立的 IoT 中樞連接字串。

    ```csharp
    static string connectionString = "{iothub connection string}";
    static string iotHubD2cEndpoint = "messages/events";
    static EventHubClient eventHubClient;
    ```

6. 將下列方法新增至 **Program** 類別：

    ```csharp
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested) break;
            EventData eventData = await eventHubReceiver.ReceiveAsync();
            if (eventData == null) continue;

            string data = Encoding.UTF8.GetString(eventData.GetBytes());
            Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
        }
    }
    ```

    這個方法會使用 **EventHubReceiver** 執行個體接收來自所有 IoT 中樞裝置對雲端接收資料分割的訊息。 請注意當您建立 **EventHubReceiver** 物件時如何傳遞 `DateTime.Now` 參數，使它只會收到它啟動後傳送的訊息。 此篩選器很適合測試環境，因為如此一來您就可以看到目前的訊息集。 在生產環境中，您的程式碼應該要確定它能處理所有訊息。 如需詳細資訊，請參閱[如何處理 IoT 中樞裝置到雲端訊息][lnk-process-d2c-tutorial]教學課程。

7. 最後，將下列幾行新增至 **Main** 方法：

    ```csharp
    Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
    eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

    var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

    CancellationTokenSource cts = new CancellationTokenSource();

    System.Console.CancelKeyPress += (s, e) =>
    {
        e.Cancel = true;
        cts.Cancel();
        Console.WriteLine("Exiting...");
    };

    var tasks = new List<Task>();
    foreach (string partition in d2cPartitions)
    {
        tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
    }  
    Task.WaitAll(tasks.ToArray());
    ```

## <a name="create-a-device-app"></a>建立裝置應用程式

在本節中，您會撰寫 .NET 主控台應用程式，模擬裝置傳送裝置對雲端訊息至 IoT 中樞。

1. 在 Visual Studio 中，使用 [主控台應用程式 (.NET Framework)] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的解決方案。 確定 .NET Framework 為 4.5.1 或更新版本。 將專案命名為 **SimulatedDevice**。

    ![新的 Visual C# Windows 傳統桌面專案][10b]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **SimulatedDevice** 專案，然後按一下 [管理 NuGet 套件]。

3. 在 [NuGet 套件管理員] 視窗中選取 [瀏覽]、搜尋 **Microsoft.Azure.Devices.Client**、選取 [安裝] 以安裝 **Microsoft.Azure.Devices.Client** 套件，並接受使用規定。 此程序會下載及安裝 [Azure IoT 裝置 SDK NuGet 套件][lnk-device-nuget]與其相依項目，並新增對它的參考。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Newtonsoft.Json;
    ```

5. 將下列欄位新增到 **Program** 類別。 以您在「建立 IoT 中樞」一節中擷取的 IoT 中樞主機名稱替代 `{iot hub hostname}`。 以您在「建立裝置身分識別」一節中擷取的裝置金鑰替代 `{device key}`。

    ```csharp
    static DeviceClient deviceClient;
    static string iotHubUri = "{iot hub hostname}";
    static string deviceKey = "{device key}";
    ```

6. 將下列方法加入至 **Program** 類別：

    ```csharp
    private static async void SendDeviceToCloudMessagesAsync()
    {
        double minTemperature = 20;
        double minHumidity = 60;
        int messageId = 1;
        Random rand = new Random();

        while (true)
        {
            double currentTemperature = minTemperature + rand.NextDouble() * 15;
            double currentHumidity = minHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
                messageId = messageId++,
                deviceId = "myFirstDevice",
                temperature = currentTemperature,
                humidity = currentHumidity
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("temperatureAlert", (currentTemperature > 30) ? "true" : "false");

            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
    ```

    這個方法會每秒傳送新的裝置對雲端訊息。 此訊息包含 JSON 序列化物件及裝置識別碼與隨機產生的數字，以模擬溫度感應器和溼度感應器。

7. 最後，將下列幾行加入至 **Main** 方法：

    ```csharp
    Console.WriteLine("Simulated device\n");
    deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey ("myFirstDevice", deviceKey), TransportType.Mqtt);

    SendDeviceToCloudMessagesAsync();
    Console.ReadLine();
    ```

    根據預設，**Create** 方法會在 .NET Framework 應用程式中建立一個使用 AMQP 通訊協定的 **DeviceClient** 執行個體來與 IoT 中樞通訊。 若要使用 MQTT 或 HTTPS 通訊協定，請使用可讓您指定通訊協定的 **Create** 方法的覆寫。 UWP 和 PCL 用戶端預設使用 HTTPS 通訊協定。 若您使用 HTTPS 通訊協定，也應該將 **Microsoft.AspNet.WebApi.Client** NuGet 套件新增至您的專案，以包含 **System.Net.Http.Formatting** 命名空間。

本教學課程會逐步引導您完成建立 IoT 中樞裝置應用程式的步驟。 您也可以使用 [Azure IoT 中樞的已連結服務][lnk-connected-service] Visual Studio 擴充功能，將必要的程式碼新增至裝置應用程式。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。

## <a name="run-the-apps"></a>執行應用程式

您現在可以開始執行應用程式。

1. 在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下您的方案，然後按一下 [設定啟始專案]。 選取 [多個啟始專案]，然後同時針對 **ReadDeviceToCloudMessages** 和 **SimulatedDevice** 專案選取 **Start** 作為動作。

    ![啟動專案屬性 ][41]

2. 按下 **F5** 來啟動這兩個執行的應用程式。 來自 **SimulatedDevice** 應用程式的主控台輸出會顯示裝置應用程式傳送給您的 IoT 中樞的訊息。 來自 **ReadDeviceToCloudMessages** 應用程式的主控台輸出則會顯示您的 IoT 中樞接收的訊息。

    ![來自應用程式的主控台輸出][42]

3. [Azure 入口網站][lnk-portal]中的 [使用量] 圖格會顯示傳送至 IoT 中樞的訊息數目︰

    ![Azure 入口網站的使用量圖格][43]

## <a name="next-steps"></a>後續步驟

在此教學課程中，您在 Azure 入口網站中設定了 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您會將此裝置身分識別用於啟用裝置應用程式，以將裝置到雲端訊息傳送至 IoT 中樞。 您也會建立一個應用程式來顯示 IoT 中樞所接收的訊息。

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [連接您的裝置][lnk-connect-device]
* [開始使用裝置管理][lnk-device-management]
* [開始使用 IoT Edge][lnk-iot-edge]

若要了解如何擴充您的 IoT 解決方案及大規模處理裝置到雲端訊息，請參閱[處理裝置到雲端訊息][lnk-process-d2c-tutorial]教學課程。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png


<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
