---
title: "使用 Azure IoT 中樞裝置對應項屬性 (.NET/.NET) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞裝置對應項來設定裝置。 您可以使用適用於 .NET 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式，也可以使用適用於 .NET 的 Azure IoT 服務 SDK，實作服務應用程式，以修改使用裝置對應項的裝置設定。"
services: iot-hub
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dkshir
ms.openlocfilehash: 0bb18f1b289dd01866842a5193437dabdb5fd20b
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>使用所需屬性來設定裝置
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

在本教學課程結尾，您將會有兩個 .NET 主控台應用程式：

* **SimulateDeviceConfiguration**，這是模擬裝置應用程式，可等候所需的設定更新，並報告模擬設定更新程序的狀態。
* **SetDesiredConfigurationAndQuery**，這是後端應用程式，可在裝置上設定所需的設定，並查詢設定更新程序。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供可用來建置裝置和後端應用程式之 Azure IoT SDK 的相關資訊。
> 
> 

若要完成此教學課程，您需要下列項目：

* Visual Studio 2015 或 Visual Studio 2017。
* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。

如果您遵循[開始使用裝置對應項][lnk-twin-tutorial]教學課程進行，您便已經有一個 IoT 中樞和一個稱為 **myDeviceId** 的裝置身分識別。 在該情況下，您可以直接跳到[建立模擬裝置應用程式][lnk-how-to-configure-createapp]一節。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您將建立 .NET 主控台應用程式，此應用程式會以 **myDeviceId** 連接到您的中樞、等候所需的設定更新，然後報告模擬設定更新程序上的更新。

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，建立新的 Visual C# Windows 傳統桌面專案。 將專案命名為 **SimulateDeviceConfiguration**。
   
    ![新的 Visual C# Windows 傳統裝置應用程式][img-createdeviceapp]

1. 在方案總管中，以滑鼠右鍵按一下 **SimulateDeviceConfiguration** 專案，然後按一下 [管理 NuGet 套件...]。
1. 在 [NuGet 套件管理員] 視窗中，選取 [瀏覽]，並搜尋 **microsoft.azure.devices.client**。 選取 [安裝] 來安裝 **Microsoft.Azure.Devices.Client** 套件，並接受使用規定。 此程序會下載及安裝 [Azure IoT 裝置 SDK][lnk-nuget-client-sdk] NuGet 套件與其相依項目，並新增對它的參考。
   
    ![NuGet 套件管理員視窗用戶端應用程式][img-clientnuget]
1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. 將下列欄位新增到 **Program** 類別。 將預留位置的值取代為您在上一節中所記下的裝置連接字串。
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();

1. 將下列方法加入至 **Program** 類別：
 
        public static void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }
    **Client** 物件會公開從裝置來與裝置對應項進行互動時所需的所有方法。 以上所示的程式碼會初始化**用戶端**物件，然後擷取 **myDeviceId** 的裝置對應項。

1. 將下列方法新增至 **Program** 類別。 這個方法會設定本機裝置上的遙測資料起始值，然後更新裝置對應項。

        public static async void InitTelemetry()
        {
            try
            {
                Console.WriteLine("Report initial telemetry config:");
                TwinCollection telemetryConfig = new TwinCollection();
                
                telemetryConfig["configId"] = "0";
                telemetryConfig["sendFrequency"] = "24h";
                reportedProperties["telemetryConfig"] = telemetryConfig;
                Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. 將下列方法新增至 **Program** 類別。 這是會在裝置對應項中偵測到「所需屬性」變更的回呼。

        private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            try
            {
                Console.WriteLine("Desired property change:");
                Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                var currentTelemetryConfig = reportedProperties["telemetryConfig"];
                var desiredTelemetryConfig = desiredProperties["telemetryConfig"];

                if ((desiredTelemetryConfig != null) && (desiredTelemetryConfig["configId"] != currentTelemetryConfig["configId"]))
                {
                    Console.WriteLine("\nInitiating config change");
                    currentTelemetryConfig["status"] = "Pending";
                    currentTelemetryConfig["pendingConfig"] = desiredTelemetryConfig;

                    await Client.UpdateReportedPropertiesAsync(reportedProperties);

                    CompleteConfigChange();
                }
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    此方法會依設定更新要求，在本機裝置對應項物件上更新所報告的屬性，並將狀態設為 **Pending**，然後更新服務上的裝置對應項。 成功更新裝置對應項之後，就會呼叫下一個點中所述的方法 `CompleteConfigChange`，完成設定變更。

1. 將下列方法新增至 **Program** 類別。 這個方法會模擬裝置重設，然後更新本機回報屬性，將狀態設定為 **Success**，並移除 **pendingConfig** 項目。 然後更新服務上的裝置對應項。 

        public static async void CompleteConfigChange()
        {
            try
            {
                var currentTelemetryConfig = reportedProperties["telemetryConfig"];

                Console.WriteLine("\nSimulating device reset");
                await Task.Delay(30000); 

                Console.WriteLine("\nCompleting config change");
                currentTelemetryConfig["configId"] = currentTelemetryConfig["pendingConfig"]["configId"];
                currentTelemetryConfig["sendFrequency"] = currentTelemetryConfig["pendingConfig"]["sendFrequency"];
                currentTelemetryConfig["status"] = "Success";
                currentTelemetryConfig["pendingConfig"] = null;

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
                Console.WriteLine("Config change complete \nPress any key to exit.");
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. 最後，將下列幾行新增至 **Main** 方法：

        try
        {
            InitClient();
            InitTelemetry();

            Console.WriteLine("Wait for desired telemetry...");
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
            Console.ReadKey();
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }

   > [!NOTE]
   > 本教學課程不會模擬並行組態更新的任何行為。 某些組態更新程序可能在更新執行時能夠接受目標組態的變更，部分程序可能必須將變更排入佇列，還有部分程序可能會因錯誤條件而拒絕變更。 請務必考慮您的特定程序所需的行為，並在初始化組態變更之前新增適當的邏輯。
   > 
   > 
1. 建置方案，然後按一下 **F5** 從 Visual Studio 執行裝置應用程式。 在輸出主控台上，應該會有訊息指出：您模擬的裝置正在擷取裝置對應項、設定遙測資料和等候所需的屬性變更。 保持應用程式執行。

## <a name="create-the-service-app"></a>建立服務應用程式
在本節中，您將建立 .NET 主控台應用程式，在與 **myDeviceId** 相關聯的裝置對應項上，以新的遙測組態物件來更新「所需屬性」。 然後，它會查詢儲存在 IoT 中樞的裝置對應項，並顯示裝置的所需和所報告組態之間的差異。

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 將專案命名為 **SetDesiredConfigurationAndQuery**。
   
    ![新的 Visual C# Windows 傳統桌面專案][img-createapp]
1. 在 [方案總管] 中，以滑鼠右鍵按一下 **SetDesiredConfigurationAndQuery** 專案，然後按一下 [管理 NuGet 套件...]。
1. 在 [Nuget 套件管理員] 視窗中選取 [瀏覽]、搜尋 **microsoft.azure.devices**、選取 [安裝] 以安裝 **Microsoft.Azure.Devices** 套件，並接受使用規定。 此程序會下載及安裝 [Azure IoT 服務 SDK][lnk-nuget-service-sdk] NuGet 套件與其相依項目，並新增對它的參考。
   
    ![NuGet 套件管理員視窗][img-servicenuget]
1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. 將下列欄位新增到 **Program** 類別。 將預留位置的值替換為您在上一節中為中樞所建立的 IoT 中樞連接字串。
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. 將下列方法加入至 **Program** 類別：
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }
   
    **Registry** 物件會公開從服務來與裝置對應項進行互動時所需的所有方法。 此程式碼會初始化 **Registry** 物件，擷取 **myDeviceId** 的裝置對應項，然後以新的遙測組態物件來更新其所需屬性。
    之後，它每隔 10 秒就會查詢儲存在 IoT 中樞的裝置對應項，並列印所需和所報告的遙測組態。 請參閱 [IoT 中樞查詢語言][lnk-query]，以了解如何產生跨所有裝置的實用報告。
   
   > [!IMPORTANT]
   > 為了便於說明，此應用程式每 10 秒查詢一次 IoT 中樞。 跨許多裝置時，請使用查詢來產生適合使用者的報告，而非偵測變更。 如果您的解決方案需要裝置事件的即時通知，請使用[對應項通知][lnk-twin-notifications]。
   > 
   > 
1. 最後，將下列幾行新增至 **Main** 方法：
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. 在 [方案總管] 中，開啟 [設定起始專案...]，並確定 **SetDesiredConfigurationAndQuery** 專案的 [動作] 是 [啟動]。 建置方案。
1. 在 **SimulateDeviceConfiguration** 裝置應用程式執行的狀態下，使用 **F5** 從 Visual Studio 執行服務應用程式。 您應該會看到回報的設定從 **Pending** 變更成 **Success**，作用中的新傳送頻率是五分鐘，而不是 24 小時。

 ![已成功設定裝置][img-deviceconfigured]
   
   > [!IMPORTANT]
   > 裝置報告作業和查詢結果之間的延遲最多一分鐘。 這是為了讓查詢基礎結構能夠以非常高的延展性運作。 若要擷取單一裝置對應項的一致檢視，請使用 **Registry** 類別中的 **getDeviceTwin** 方法。
   > 
   > 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您將會從解決方案後端將所需的組態設為「所需屬性」，還會撰寫裝置應用程式來偵測該變更並模擬多步驟更新程序，以透過報告屬性來回報其狀態。

使用下列資源來了解如何：

* 從裝置傳送遙測，請參閱[開始使用 IoT 中樞][lnk-iothub-getstarted]教學課程，
* 在大量裝置上排定或執行作業，請參閱[排定及廣播作業][lnk-schedule-jobs]教學課程。
* 以互動方式控制裝置 (例如，從使用者控制的應用程式開啟風扇)，請參閱[使用直接方法][lnk-methods-tutorial]教學課程。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/devicesdknuget.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
