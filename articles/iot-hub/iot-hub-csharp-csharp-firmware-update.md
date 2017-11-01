---
title: "使用 Azure IoT 中樞進行裝置韌體更新 (.NET/.NET) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞上的裝置管理來起始裝置韌體更新。 您可以使用適用於 .NET 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式，也可以使用適用於 .NET 的 Azure IoT 服務 SDK，實作服務應用程式以觸發韌體更新。"
services: iot-hub
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr
ms.openlocfilehash: bd0a227861d75dc66af8fb4865a17a3b6d0f70ba
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>使用裝置管理來起始裝置韌體更新 (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>簡介
在[開始使用裝置管理][lnk-dm-getstarted]教學課程中，您已了解如何使用[裝置對應項][lnk-devtwin]和[直接方案][lnk-c2dmethod]基礎，從遠端重新啟動裝置。 本教學課程使用相同的 IoT 中樞基元，並示範如何進行端對端模擬韌體更新。  此模式用於 [Raspberry Pi 裝置實作範例][lnk-rpi-implementation]的韌體更新實作。

本教學課程說明如何：

* 建立 .NET 主控台應用程式，以透過您的 IoT 中樞在模擬裝置應用程式中呼叫 **firmwareUpdate** 直接方法。
* 建立會實作 **firmwareUpdate** 直接方法的模擬裝置應用程式。 此方法會起始多階段程序，此程序會等候下載韌映像、下載韌體映像，最後再套用韌體映像。 在更新的每個階段，裝置都會使用回報的屬性來回報進度。

在本教學課程結束時，您會有 .NET (C#) 主控台裝置應用程式和 .NET (C#) 主控台後端應用程式：

* **SimulatedDeviceFwUpdate** 會將您的 IoT 中樞連接至稍早建立的裝置身分識別，接收 **firmwareUpdate** 直接方法，透過多重狀態處理執行以模擬韌體更新，包括等待映像下載、下載新的映像，最後套用映像。

* **TriggerFWUpdate**，其在模擬裝置上使用服務 SDK 來遠端叫用 **firmwareUpdate** 直接方法，顯示回應，並定期 (每 500 毫秒) 顯示更新的報告屬性。

若要完成此教學課程，您需要下列項目：

* Visual Studio 2015 或 Visual Studio 2017。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

請依照[開始使用裝置管理](iot-hub-csharp-csharp-device-management-get-started.md)文件，以建立 IoT 中樞及取得 IoT 中樞連接字串。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端韌體更新
在此節中，您會建立 .NET 主控台應用程式 (使用 C#)，此應用程式會在裝置上起始遠端韌體更新。 此應用程式使用直接方法來起始更新，並使用裝置對應項查詢來定期取得作用中韌體更新的狀態。

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 將專案命名為 **TriggerFWUpdate**。

    ![新的 Visual C# Windows 傳統桌面專案][img-createserviceapp]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **TriggerFWUpdate** 專案，然後按一下 [管理 NuGet 套件]。
3. 在 [Nuget 套件管理員] 視窗中選取 [瀏覽]、搜尋 **microsoft.azure.devices**、選取 [安裝] 以安裝 **Microsoft.Azure.Devices** 套件，並接受使用規定。 此程序會下載及安裝 [Azure IoT 服務 SDK][lnk-nuget-service-sdk] NuGet 套件與其相依項目，並新增對它的參考。

    ![NuGet 套件管理員視窗][img-servicenuget]
4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. 將下列欄位新增到 **Program** 類別。 將多個預留位置值取代為您在上一節中為中樞所建立的 IoT 中樞連接字串與您的裝置識別碼。
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. 將下列方法新增至 **Program** 類別。 這個方法每隔 500 毫秒會輪詢裝置對應項的更新狀態。 只有在實際上已變更狀態時，它才會寫入主控台。 在此範例中，為防止在您的訂用帳戶中耗用額外的 IoT 中樞訊息，當裝置報告狀態 **applyComplete** 或錯誤時會停止輪詢。  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. 將下列方法加入至 **Program** 類別：

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. 最後，將下列幾行新增到 **Main** 方法中。 這會建立登錄管理員來讀取裝置對應項、在工作者執行緒上啟動輪詢工作，然後觸發軔體更新。
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. 建置方案。

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您可：

* 建立 .NET 主控台應用程式，以回應雲端所呼叫的直接方法
* 透過直接的方法來模擬後端服務所觸發的韌體更新
* 使用報告屬性來啟用裝置對應項查詢，以識別裝置及其上次完成韌體更新的時間

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 將專案命名為 **SimulateDeviceFWUpdate**。
   
    ![新的 Visual C# Windows 傳統裝置應用程式][img-createdeviceapp]
    
2. 在 [方案總管] 中，以滑鼠右鍵按一下 **SimulateDeviceFWUpdate** 專案，然後按一下 [管理 NuGet 套件]。
3. 在 [NuGet 套件管理員] 視窗中，選取 [瀏覽]，並搜尋 **microsoft.azure.devices.client**。 選取 [安裝] 來安裝 **Microsoft.Azure.Devices.Client** 套件，並接受使用規定。 此程序會下載及安裝 [Azure IoT 裝置 SDK][lnk-nuget-client-sdk] NuGet 套件與其相依項目，並新增對它的參考。
   
    ![NuGet 套件管理員視窗用戶端應用程式][img-clientnuget]
4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. 將下列欄位新增到 **Program** 類別。 將預留位置的值取代為您在**建立裝置識別**一節中所記下的裝置連接字串。
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. 新增下列方法，透過裝置對應項向雲端回報狀態： 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. 新增下列方法以模擬下載韌體映像：
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. 新增下列方法以模擬將韌體映像套用至裝置：
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  新增下列方法以模擬等待韌體映像下載。 將狀態更新為 [等候中]，並清除對應項上的其他韌體更新屬性。 這些屬性都會清除，以移除先前韌體更新的任何現有值。 這是必要的，因為報告的屬性會傳送為 PATCH 作業 (差異) 而非 PUT 作業 (一組完整的屬性，會取代所有先前的值)。 一般而言，會通知裝置可用的更新，系統管理員會定義原則讓裝置開始下載並套用更新。 此函式是讓該原則執行的邏輯所在位置。 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. 新增下列方法以執行下載。 它會透過裝置對應項將狀態更新為 [下載中]、呼叫模擬下載方法，並根據下載作業的結，透過對應項報告狀態 **downloadComplete** 或 **downloadFailed**。 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. 新增下列方法以套用映像。 它會透過裝置對應項將狀態更新為 [套用中]、呼叫模擬套用映像方法，並根據套用作業的結，透過對應項將狀態更新為 **applyComplete** 或 **applyFailed**。 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. 新增下列方法來排序韌體更新作業，透過將映像套用至裝置，將等候變為下載：
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. 新增下列方法，從雲端處理 **updateFirmware** 直接方法。 它會從訊息內容中擷取韌體更新的 URL，並將其傳遞給 **doUpdate** 工作，該工作會在另一個執行緒集區執行緒上啟動。 然後立即將方法回應傳回雲端。
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> 這個方法會觸發模擬更新做為**工作**執行，然後立即回應給方法呼叫，通知服務已啟動軔體更新。 會透過裝置對應項的報告屬性，將更新狀態和完成傳送給服務。 我們將在開始更新時回應方法呼叫，而不是在其完成之後，因為：
> * 實際的更新程序花費的時間很可能比方法呼叫逾時長。
> * 實際的更新程序很可能需要重新開機，而且會重新啟動此應用程式，使得 **MetodRequest** 物件無法使用。 (不過，甚至在重新開機後也能更新報告的屬性。) 

14. 最後，在 **Main** 方法中新增下列程式碼以開啟 IoT 中樞的連線，並啟動方法接聽程式︰
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. 建置方案。       

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。


## <a name="run-the-apps"></a>執行應用程式
您現在可以開始執行應用程式。
1. 執行 .NET 裝置應用程式 **SimulatedDeviceFWUpdate**。  在 [SimulatedDeviceFWUpdate] 專案上按一下滑鼠右鍵，選取 [偵錯]，然後選取 [開始新執行個體]。 它應該會開始接聽來自 IoT 中樞的方法呼叫： 

2. 連接裝置並正在等候方法叫用後，請執行 .NET **TriggerReboot** 應用程式，在模擬裝置應用程式中叫用韌體更新方法。 以滑鼠右鍵按一下 **TriggerFWUpdate** 專案，選取 [偵錯]，然後選取 [啟動新的執行個體]。 您應該會看到 **SimulatedDeviceFWUpdate** 主控台中撰寫的更新序列，以及在 **TriggerFWUpdate** 主控台中透過裝置的報告屬性所報告的序列。 請注意，這個程序需要幾秒鐘的時間才能完成。 
   
    ![服務和裝置應用程式執行][img-combinedrun]


## <a name="next-steps"></a>後續步驟
在此教學課程中，您使用直接方法來觸發裝置上的遠端韌體更新，並且使用回報的屬性追蹤韌體更新的進度。

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs]教學課程。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/