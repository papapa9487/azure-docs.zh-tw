---
title: "使用 Azure IoT 中樞直接方法 (.NET/Node) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞直接方法。 您可以使用適用於 Node.js 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式 (包含直接方法)，也可以使用適用於 .NET 的 Azure IoT 服務 SDK，實作服務應用程式 (叫用直接方法)。"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: nberdy
ms.openlocfilehash: 76f1d32b4afeacae1488b4cf28be6c8cf7f4ea37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-netnode"></a>使用直接方法 (.NET/Node)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

在本教學課程中，我們即將開發 .NET 和 Node.js 主控台應用程式：

* **CallMethodOnDevice.js**，這是 NET 後端應用程式，可在模擬裝置應用程式中呼叫方法，並顯示回應。
* **TwinSimulatedDevice.js** 是可模擬裝置的 Node.js 應用程式，而此裝置會以稍早建立的裝置身分識別連線至您的 IoT 中樞，並回應雲端所呼叫的方法。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供 Azure IoT SDK (可讓您同時建置在裝置與您的解決方案後端執行的兩個應用程式) 的相關資訊。
> 
> 

若要完成本教學課程，您需要：

* Visual Studio 2015 或 Visual Studio 2017。
* Node.js 4.0.x 版或更新版本。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您建立 Node.js 主控台應用程式，回應解決方案後端所呼叫的方法。

1. 建立稱為 **simulateddevice**的新的空資料夾。 在 **simulateddevice** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。 接受所有預設值：
   
    ```
    npm init
    ```
2. 在 **simulateddevice** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iot-device** 和 **azure-iot-device-mqtt** 套件：
   
    ```
        npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 使用文字編輯器，在 **simulateddevice** 資料夾中建立檔案並將它命名為 **SimulatedDevice.js**。
4. 在 **SimulatedDevice.js** 檔案開頭新增下列 `require` 陳述式：
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. 新增 **connectionString** 變數，並用它來建立 **DeviceClient** 執行個體。 將 **{device connection string}** 取代為您在*建立裝置身分識別*一節中產生的連接字串：
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. 新增下列函式以在裝置上實作直接方法：
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. 開啟 IoT 中樞的連線並初始化方法接聽程式︰
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. 儲存並關閉 **SimulatedDevice.js** 檔案。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在生產環境程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如連接重試)。
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>在裝置上呼叫直接方法
在本節中，您會建立 .NET 主控台應用程式，以在模擬裝置應用程式中呼叫方法，然後顯示回應。

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 確定 .NET Framework 為 4.5.1 或更新版本。 將專案命名為 **CallMethodOnDevice**。
   
    ![新的 Visual C# Windows 傳統桌面專案][10]
2. 在 方案總管 中，以滑鼠右鍵按一下 **CallMethodOnDevice** 專案，然後按一下管理 NuGet 套件...。
3. 在 [Nuget 套件管理員] 視窗中選取 [瀏覽]、搜尋 **microsoft.azure.devices**、選取 [安裝] 以安裝 **Microsoft.Azure.Devices** 套件，並接受使用規定。 此程序會下載及安裝 [Azure IoT 服務 SDK][lnk-nuget-service-sdk] NuGet 套件與其相依項目，並新增對它的參考。
   
    ![NuGet 套件管理員視窗][11]

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
7. 最後，將下列幾行加入至 **Main** 方法：
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

## <a name="run-the-applications"></a>執行應用程式
現在您已經準備好執行應用程式。

1. 在 Visual Studio 的 方案總管 中，以滑鼠右鍵按一下您的方案，然後按一下設定啟始專案...。選取 單一啟始專案，然後選取下拉式功能表中的 **CallMethodOnDevice** 專案。

2. 在 **simulateddevice** 資料夾的命令提示字元中，執行下列命令以開始接聽來自您的 IoT 中樞的方法呼叫：
   
    ```
    node SimulatedDevice.js
    ```
   等候模擬裝置開啟︰![][7]
3. 裝置現在已連接並等候方法引動過程，請執行 .NET **CallMethodOnDevice** 應用程式，以在模擬裝置應用程式中叫用方法。 您應會看到在主控台中寫入的裝置回應。
   
    ![][8]
4. 此裝置接著藉由列印此訊息來回應此方法︰
   
    ![][9]

## <a name="next-steps"></a>後續步驟
在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您會將此裝置識別用於啟用模擬的裝置應用程式，以將雲端所叫用的方法進行反應。 您也會建立應用程式，在裝置上叫用方法，並且顯示來自裝置的回應。 

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [IoT 中樞入門]
* [排程多個裝置上的作業][lnk-devguide-jobs]

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs]教學課程。

<!-- Images. -->
[7]: ./media/iot-hub-csharp-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-csharp-node-direct-methods/netserviceapp.png
[9]: ./media/iot-hub-csharp-node-direct-methods/methods-output.png

[10]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp1.png
[11]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp2.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[IoT 中樞入門]: iot-hub-node-node-getstarted.md
