---
title: "使用 Azure IoT 中樞裝置對應項屬性 (.NET/Node) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞裝置對應項來設定裝置。 您可以使用適用於 Node.js 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式，也可以使用適用於 .NET 的 Azure IoT 服務 SDK，實作服務應用程式，以修改使用裝置對應項的裝置組態。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: elioda
ms.openlocfilehash: 8f9626fc47e7d32cb104b960bea9b7de9efa6f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>使用所需屬性來設定裝置
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

在本教學課程結尾，您將會有兩個主控台應用程式：

* **SimulateDeviceConfiguration.js**，這是模擬裝置應用程式，可等候所需的組態更新，並報告模擬組態更新程序的狀態。
* **SetDesiredConfigurationAndQuery.js**，這是 .NET 後端應用程式，可在裝置上設定所需的設定，並查詢組態更新程序。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供可用來建置裝置和後端應用程式之 Azure IoT SDK 的相關資訊。
> 
> 

若要完成此教學課程，您需要下列項目：

* Visual Studio 2015 或 Visual Studio 2017。
* Node.js 4.0.x 版或更新版本。
* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。

如果您遵循[開始使用裝置對應項][lnk-twin-tutorial]教學課程進行，您便已經有一個 IoT 中樞和一個稱為 **myDeviceId** 的裝置身分識別。 在該情況下，您可以直接跳到[建立模擬裝置應用程式][lnk-how-to-configure-createapp]一節。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您將建立 Node.js 主控台應用程式，此應用程式會以 **myDeviceId** 連接到您的中樞、等候所需的組態更新，然後報告模擬組態更新程序上的更新。

1. 建立稱為 **simulatedeviceconfiguration** 的新空白資料夾。 在 **simulatedeviceconfiguration** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。 接受所有預設值。
   
    ```
    npm init
    ```
1. 在 **simulatedeviceconfiguration** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iot-device** 和 **azure-iot-device-mqtt** 套件：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. 使用文字編輯器，在 **simulatedeviceconfiguration** 資料夾中建立新的 **SimulateDeviceConfiguration.js** 檔案。
1. 將下列程式碼新增至 **SimulateDeviceConfiguration.js** 檔案，並以您建立 **myDeviceId** 裝置身分識別時所複製的裝置連接字串，取代 **{device connection string}** 預留位置︰
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    **Client** 物件會公開從裝置來與裝置對應項進行互動時所需的所有方法。 此程式碼會初始化 **Client** 物件，擷取 **myDeviceId** 的裝置對應項，然後附加處理常式來處理*所需屬性*的更新。 此處理常式會比較 configIds 來驗證有實際的組態變更要求，然後叫用方法來啟動組態變更。
   
    請注意，為了簡單起見，此程式碼使用硬式編碼的預設值作為初始組態。 實際的應用程式可能會從本機儲存體載入該組態。
   
   > [!IMPORTANT]
   > 所需的屬性變更事件永遠會在裝置連線時發出一次。 在執行任何動作之前，務必檢查所需的屬性有實際的變更。
   > 
   > 
1. 在 `client.open()` 叫用之前新增下列方法︰
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    **InitConfigChange** 方法會依組態更新要求，在本機裝置對應項物件上更新所報告的屬性，並將狀態設為 **Pending**，然後更新服務上的裝置對應項。 成功更新裝置對應項之後，它會模擬一個長時間執行而在 **completeConfigChange** 執行過程中終止的處理序。 這個方法會更新本機報告屬性，將狀態設定為 **Success**，並移除 **pendingConfig** 物件。 然後更新服務上的裝置對應項。
   
    請注意，為了節省頻寬，更新所報告的屬性時只會指定要修改的屬性 (在上述程式碼中名為 **patch**)，而不是取代整份文件。
   
   > [!NOTE]
   > 本教學課程不會模擬並行組態更新的任何行為。 某些組態更新程序可能在更新執行時能夠接受目標組態的變更，部分程序可能必須將變更排入佇列，還有部分程序可能會因錯誤條件而拒絕變更。 請務必考慮您的特定程序所需的行為，並在初始化組態變更之前新增適當的邏輯。
   > 
   > 
1. 執行裝置應用程式︰
   
        node SimulateDeviceConfiguration.js
   
    您應該會看見訊息 `retrieved device twin`。 保持應用程式執行。

## <a name="create-the-service-app"></a>建立服務應用程式
在本節中，您將建立 .NET 主控台應用程式，在與 **myDeviceId** 相關聯的裝置對應項上，以新的遙測組態物件來更新「所需屬性」。 然後，它會查詢儲存在 IoT 中樞的裝置對應項，並顯示裝置的所需和所報告組態之間的差異。

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，將 Visual C# Windows 傳統桌面專案新增至目前的方案。 將專案命名為 **SetDesiredConfigurationAndQuery**。
   
    ![新的 Visual C# Windows 傳統桌面專案][img-createapp]
1. 在 方案總管 中，以滑鼠右鍵按一下 **SetDesiredConfigurationAndQuery** 專案，然後按一下管理 NuGet 套件...。
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
   
            try
            {
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
            catch (Exception ex)
            {
                Console.WriteLine($"Exception: {ex.Message}");
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
        SetDesiredConfigurationAndQuery().Wait();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. 在 [方案總管] 中，開啟 [設定起始專案...]，並確定 **SetDesiredConfigurationAndQuery** 專案的 [動作] 是 [啟動]。 建置方案。
1. 在 **SimulateDeviceConfiguration.js** 執行時，使用 **F5** 從 Visual Studio 執行 .NET 應用程式，然後您應該會看到所報告的組態從 **Success** 變成 **Pending**，又再變回 **Success**，而且新的作用中傳送頻率是五分鐘，而不是 24 小時。

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
[img-servicenuget]: media/iot-hub-csharp-node-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-node-twin-how-to-configure/deviceconfigured.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-csharp-node-twin-how-to-configure.md#create-the-simulated-device-app
