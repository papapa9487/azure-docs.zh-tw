---
title: "使用 Azure IoT 中樞直接方法 (.NET/.NET) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞直接方法。 您可以使用適用於 .NET 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式 (包含直接方法)，也可以使用適用於 .NET 的 Azure IoT 服務 SDK，實作服務應用程式 (叫用直接方法)。"
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: dkshir
ms.openlocfilehash: 9ce1fbebb6417c10618aa182e3c1d9ddf8132fb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-netnet"></a>使用直接方法 (.NET/.NET)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

在本教學課程中，我們即將開發兩種 .NET 主控台應用程式：

* **CallMethodOnDevice** 是後端應用程式，可在模擬裝置應用程式中呼叫方法，並顯示回應。
* **SimulateDeviceMethods** 是主控台應用程式，可模擬裝置以稍早建立的裝置身分識別連線至您的 IoT 中樞，並回應雲端所呼叫的方法。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供 Azure IoT SDK (可讓您同時建置在裝置與您的解決方案後端執行的兩個應用程式) 的相關資訊。
> 
> 

若要完成本教學課程，您需要：

* Visual Studio 2015 或 Visual Studio 2017。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

如果您想要改為以程式設計方式建立裝置識別，請閱讀[使用 .NET 將您的模擬裝置連線至 IoT 中樞][lnk-device-identity-csharp]一文中的對應章節。


## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您會建立 .NET 主控台應用程式，回應解決方案後端所呼叫的方法。

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 將專案命名為 **SimulateDeviceMethods**。
   
    ![新的 Visual C# Windows 傳統裝置應用程式][img-createdeviceapp]
    
1. 在方案總管中，以滑鼠右鍵按一下 **SimulateDeviceMethods** 專案，然後按一下管理 NuGet 套件...。
1. 在 [NuGet 套件管理員] 視窗中，選取 [瀏覽]，並搜尋 **microsoft.azure.devices.client**。 選取 [安裝] 來安裝 **Microsoft.Azure.Devices.Client** 套件，並接受使用規定。 此程序會下載及安裝 [Azure IoT 裝置 SDK][lnk-nuget-client-sdk] NuGet 套件與其相依項目，並新增對它的參考。
   
    ![NuGet 套件管理員視窗用戶端應用程式][img-clientnuget]
1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

1. 將下列欄位新增到 **Program** 類別。 將預留位置的值取代為您在上一節中所記下的裝置連接字串。
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. 新增下列項目以在裝置上實作直接方法：

        static Task<MethodResponse> WriteLineToConsole(MethodRequest methodRequest, object userContext)
        {
            Console.WriteLine();
            Console.WriteLine("\t{0}", methodRequest.DataAsJson);
            Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

            string result = "'Input was written to log.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

1. 最後，在 **Main** 方法中新增下列程式碼以開啟 IoT 中樞的連線，並啟動方法接聽程式︰
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "writeLine" method
            Client.SetMethodHandlerAsync("writeLine", WriteLineToConsole, null).Wait();
            Console.WriteLine("Waiting for direct method call\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "writeLine" handler
            Client.SetMethodHandlerAsync("writeLine", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
1. 在 Visual Studio 的 方案總管 中，以滑鼠右鍵按一下您的方案，然後按一下設定啟始專案...。選取 單一啟始專案，然後選取下拉式功能表中的 **SimulateDeviceMethods** 專案。        

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在生產環境程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如連接重試)。
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>在裝置上呼叫直接方法
在本節中，您會建立 .NET 主控台應用程式，以在模擬裝置應用程式中呼叫方法，然後顯示回應。

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 確定 .NET Framework 為 4.5.1 或更新版本。 將專案命名為 **CallMethodOnDevice**。
   
    ![新的 Visual C# Windows 傳統桌面專案][img-createserviceapp]
2. 在 方案總管 中，以滑鼠右鍵按一下 **CallMethodOnDevice** 專案，然後按一下管理 NuGet 套件...。
3. 在 [Nuget 套件管理員] 視窗中選取 [瀏覽]、搜尋 **microsoft.azure.devices**、選取 [安裝] 以安裝 **Microsoft.Azure.Devices** 套件，並接受使用規定。 此程序會下載及安裝 [Azure IoT 服務 SDK][lnk-nuget-service-sdk] NuGet 套件與其相依項目，並新增對它的參考。
   
    ![NuGet 套件管理員視窗][img-servicenuget]

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. 將下列欄位新增到 **Program** 類別。 將預留位置的值替換為您在上一節中為中樞所建立的 IoT 中樞連接字串。
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. 將下列方法加入至 **Program** 類別：
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    此方法會在 `myDeviceId` 裝置上叫用名稱為 `writeLine` 的直接方法。 然後，它會在主控台上寫入裝置所提供的回應。 請注意，可以針對要回應的裝置指定逾時值。
7. 最後，將下列幾行新增至 **Main** 方法：
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. 在 Visual Studio 的 方案總管 中，以滑鼠右鍵按一下您的方案，然後按一下設定啟始專案...。選取 單一啟始專案，然後選取下拉式功能表中的 **CallMethodOnDevice** 專案。

## <a name="run-the-applications"></a>執行應用程式
現在您已經準備好執行應用程式。

1. 執行 .NET 裝置應用程式 **SimulateDeviceMethods**。 它應該會開始接聽來自 IoT 中樞的方法呼叫： 

    ![裝置應用程式已執行][img-deviceapprun]
1. 裝置現在已連接並等候方法引動過程，請執行 .NET **CallMethodOnDevice** 應用程式，以在模擬裝置應用程式中叫用方法。 您應會看到在主控台中寫入的裝置回應。
   
    ![裝置應用程式已執行][img-serviceapprun]
1. 此裝置接著藉由列印此訊息來回應此方法︰
   
    ![在裝置上叫用的直接方法][img-directmethodinvoked]

## <a name="next-steps"></a>後續步驟
在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您會將此裝置識別用於啟用模擬的裝置應用程式，以將雲端所叫用的方法進行反應。 您也會建立應用程式，在裝置上叫用方法，並且顯示來自裝置的回應。 

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [IoT 中樞入門]
* [排程多個裝置上的作業][lnk-devguide-jobs]

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs]教學課程。

<!-- Images. -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-direct-methods/device-app-nuget.png
[img-createserviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-service-app.png
[img-servicenuget]: ./media/iot-hub-csharp-csharp-direct-methods/service-app-nuget.png
[img-deviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-device-app.png
[img-serviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-service-app.png
[img-directmethodinvoked]: ./media/iot-hub-csharp-csharp-direct-methods/direct-method-invoked.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[IoT 中樞入門]: iot-hub-node-node-getstarted.md
