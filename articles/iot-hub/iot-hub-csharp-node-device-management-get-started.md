---
title: "開始使用 Azure IoT 中樞裝置管理 (.NET/Node) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞裝置管理來起始遠端裝置重新開機。 您可以使用適用於 Node.js 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式 (包含直接方法)，也可以使用適用於 .NET 的 Azure IoT 服務 SDK，實作服務應用程式 (叫用直接方法)。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: juanpere
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: caab61a18fd5bb27ecc62cf1126823f79c3fdf4d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/28/2017

---
# <a name="get-started-with-device-management-netnode"></a>開始使用裝置管理 (.NET/Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教學課程說明如何：

* 使用 Azure 入口網站來建立 IoT 中樞，並且在 IoT 中樞建立裝置識別。
* 建立模擬裝置應用程式，其包含可將該裝置重新開機的直接方法。 直接方法是從雲端叫用。
* 建立 .NET 主控台應用程式，可透過您的 IoT 中樞在模擬的裝置應用程式中呼叫重新啟動直接方法。

在本教學課程結束時，您會有 Node.js 主控台裝置應用程式和 .NET (C#) 主控台後端應用程式：

**dmpatterns_getstarted_device.js**，它會以先前建立的裝置識別連線到您的 IoT 中樞，接收重新啟動直接方法，模擬實體重新啟動，並報告上一次重新啟動的時間。

**TriggerReboot**，它會在模擬裝置應用程式中呼叫直接方法、顯示回應，以及顯示更新的報告屬性。

若要完成此教學課程，您需要下列項目：

* Visual Studio 2015 或 Visual Studio 2017。
* Node.js 4.0.x 版或更新版本， <br/>  [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Node.js。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端重新啟動
在本節中，您會建立 .NET 主控台應用程式 (使用 C#)，此應用程式會使用直接方法起始遠端重新開機。 應用程式使用裝置對應項查詢來探索該裝置的上次重新開機時間。

1. 在 Visual Studio 中，使用 [主控台應用程式 (.NET Framework)] 專案範本，將 Visual C# Windows 傳統桌面專案新增至新的解決方案。 確定 .NET Framework 為 4.5.1 或更新版本。 將專案命名為 **TriggerReboot**。

    ![新的 Visual C# Windows 傳統桌面專案][img-createapp]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **TriggerReboot** 專案，然後按一下 [管理 NuGet 套件]。
3. 在 [Nuget 套件管理員] 視窗中選取 [瀏覽]、搜尋 **microsoft.azure.devices**、選取 [安裝] 以安裝 **Microsoft.Azure.Devices** 套件，並接受使用規定。 此程序會下載及安裝 [Azure IoT 服務 SDK][lnk-nuget-service-sdk] NuGet 套件與其相依項目，並新增對它的參考。

    ![NuGet 套件管理員視窗][img-servicenuget]
4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. 將下列欄位新增到 **Program** 類別。 將預留位置的值替換為您在上一節中為中樞建立的 IoT 中樞連接字串和目標裝置。
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. 將下列方法新增至 **Program** 類別。  此程式碼會取得用於重新啟動裝置的裝置對應項，並輸出報告屬性。
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. 將下列方法新增至 **Program** 類別。  此程式碼會使用直接方法在裝置上起始重新啟動。

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. 最後，將下列幾行加入至 **Main** 方法：
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. 建置方案。

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您將：

* 建立 Node.js 主控台應用程式，以回應雲端所呼叫的直接方法
* 觸發模擬裝置重新啟動
* 使用報告屬性來啟用裝置對應項查詢，以識別裝置及其上次重新啟動時間

1. 建立稱為 **manageddevice**的新的空資料夾。  在 **manageddevice** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。  接受所有預設值：
   
    ```
    npm init
    ```
2. 在命令提示字元中，於 **manageddevice** 資料夾中執行下列命令來安裝 **azure-iot-device** 裝置 SDK 套件和 **azure-iot-device-mqtt** 套件：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 使用文字編輯器，在 [manageddevice] 資料夾中建立新的 **dmpatterns_getstarted_device.js** 檔案。
4. 在 **dmpatterns_getstarted_device.js** 檔案開頭新增下列 'require' 陳述式：
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. 新增 **connectionString** 變數，並用它來建立**用戶端**執行個體。  以您裝置的連接字串取代連接字串。  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. 新增下列函式以在裝置上實作直接方法
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. 新增下列程式碼以開啟 IoT 中樞的連線，並啟動直接方法接聽程式︰
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. 儲存並關閉 **dmpatterns_getstarted_device.js**檔案。
   
> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。


## <a name="run-the-apps"></a>執行應用程式
您現在可以開始執行應用程式。

1. 在 **manageddevice** 資料夾中，於命令提示字元中執行下列命令以開始接聽重新啟動直接方法。
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. 執行 C# 主控台應用程式 **TriggerReboot**。 以滑鼠右鍵按一下 **TriggerReboot** 專案，請選取 [偵錯]，然後選取 [啟動新的執行個體]。

3. 您會在主控台中看到直接方法的裝置回應。

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
