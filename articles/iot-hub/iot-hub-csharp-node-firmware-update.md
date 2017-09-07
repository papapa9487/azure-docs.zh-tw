---
title: "使用 Azure IoT 中樞進行裝置韌體更新 (.NET/Node) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞上的裝置管理來起始裝置韌體更新。 您可以使用適用於 Node.js 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式，也可以使用適用於 .NET 的 Azure IoT 服務 SDK，實作服務應用程式以觸發韌體更新。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: juanpere
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 16603cc7f2d8743037da22466312f228f4319716
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>使用裝置管理來起始裝置韌體更新 (.NET/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>簡介
在[開始使用裝置管理][lnk-dm-getstarted]教學課程中，您已了解如何使用[裝置對應項][lnk-devtwin]和[直接方案][lnk-c2dmethod]基礎，從遠端重新啟動裝置。 本教學課程使用相同的 IoT 中樞基元，並示範如何進行端對端模擬韌體更新。  此模式用於 [Raspberry Pi 裝置實作範例][lnk-rpi-implementation]的韌體更新實作。

本教學課程說明如何：

* 建立 .NET 主控台應用程式，以透過您的 IoT 中樞在模擬裝置應用程式中呼叫 firmwareUpdate 直接方法。
* 建立會實作 **firmwareUpdate** 直接方法的模擬裝置應用程式。 此方法會起始多階段程序，此程序會等候下載韌映像、下載韌體映像，最後再套用韌體映像。 在更新的每個階段，裝置都會使用回報的屬性來回報進度。

在本教學課程結束時，您會有 Node.js 主控台裝置應用程式和 .NET (C#) 主控台後端應用程式：

**dmpatterns_fwupdate_service.js**：在模擬裝置應用程式中呼叫直接方法、顯示回應，並定期 (每 500 毫秒) 顯示更新的報告屬性。

**TriggerFWUpdate**會將您的 IoT 中樞連接至稍早建立的裝置身分識別，接收 firmwareUpdate 直接方法，透過多重狀態處理執行以模擬韌體更新，包括等待映像下載、下載新的映像，最後套用映像。

若要完成此教學課程，您需要下列項目：

* Visual Studio 2015 或 Visual Studio 2017。
* Node.js 0.12.x 版或更新版本， <br/>  [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Node.js。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

請依照[開始使用裝置管理](iot-hub-csharp-node-device-management-get-started.md)文件，以建立 IoT 中樞及取得 IoT 中樞連接字串。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端韌體更新
在此節中，您會建立 .NET 主控台應用程式 (使用 C#)，此應用程式會在裝置上起始遠端韌體更新。 此應用程式使用直接方法來起始更新，並使用裝置對應項查詢來定期取得作用中韌體更新的狀態。

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 將專案命名為 **TriggerFWUpdate**。

    ![新的 Visual C# Windows 傳統桌面專案][img-createapp]

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [TriggerFWUpdate] 專案，然後按一下 [管理 NuGet 套件]。
1. 在 [Nuget 套件管理員] 視窗中選取 [瀏覽]、搜尋 **microsoft.azure.devices**、選取 [安裝] 以安裝 **Microsoft.Azure.Devices** 套件，並接受使用規定。 此程序會下載及安裝 [Azure IoT 服務 SDK][lnk-nuget-service-sdk] NuGet 套件與其相依項目，並新增對它的參考。

    ![NuGet 套件管理員視窗][img-servicenuget]
1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
1. 將下列欄位新增到 **Program** 類別。 將多個預留位置值取代為您在上一節中為中樞所建立的 IoT 中樞連接字串與您的裝置識別碼。
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
1. 將下列方法加入至 **Program** 類別：
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
1. 將下列方法加入至 **Program** 類別：

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

1. 最後，將下列幾行加入至 **Main** 方法：
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
1. 在 [方案總管] 中，開啟 [設定起始專案]，並確定 **TriggerFWUpdate** 專案的 [動作] 是 [起始]。

1. 建置方案。

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>執行應用程式
您現在可以開始執行應用程式。

1. 在 **manageddevice** 資料夾中，於命令提示字元中執行下列命令以開始接聽重新啟動直接方法。
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. 在 Visual Studio 中，以滑鼠右鍵按一下 [TriggerFWUpdate] 專案、選取 [偵錯] 與 [開始新執行個體]。

3. 您會在主控台中看到直接方法的裝置回應。

    ![已順利更新韌體][img-fwupdate]

## <a name="next-steps"></a>後續步驟
在此教學課程中，您使用直接方法來觸發裝置上的遠端韌體更新，並且使用回報的屬性追蹤韌體更新的進度。

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs]教學課程。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png
[img-fwupdate]: media/iot-hub-csharp-node-firmware-update/fwupdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
