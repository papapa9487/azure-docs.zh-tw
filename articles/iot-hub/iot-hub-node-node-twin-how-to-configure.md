---
title: "使用 Azure IoT 中樞裝置對應項屬性 (Node) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞裝置對應項來設定裝置。 您可以使用適用於 Node.js 的 Azure IoT SDK，實作模擬裝置應用程式和服務應用程式，以修改使用裝置對應項的裝置組態。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: elioda
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: 6ff6f1c331d5a77e7ac0a47af6806f5d90fb0fdc
ms.contentlocale: zh-tw
ms.lasthandoff: 09/08/2017

---
# <a name="use-desired-properties-to-configure-devices-node"></a>使用所需屬性來設定裝置 (Node)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

在本教學課程結尾端，您將會有兩個 Node.js 主控台應用程式：

* **SimulateDeviceConfiguration.js**，這是模擬裝置應用程式，可等候所需的組態更新，並報告模擬組態更新程序的狀態。
* **SetDesiredConfigurationAndQuery.js**，這是 Node.js 後端應用程式，可在裝置上設定所需的設定，並查詢組態更新程序。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供可用來建置裝置和後端應用程式之 Azure IoT SDK 的相關資訊。
> 
> 

若要完成此教學課程，您需要下列項目：

* Node.js 4.0.x 版或更新版本。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

如果您依照[開始使用裝置對應項][lnk-twin-tutorial]教學課程進行，您便已經有一個 IoT 中樞和一個稱為 **myDeviceId** 的裝置身分識別，您可以直接跳到[建立模擬裝置應用程式][lnk-how-to-configure-createapp]一節。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您將建立 Node.js 主控台應用程式，此應用程式會以 **myDeviceId** 連接到您的中樞、等候所需的組態更新，然後報告模擬組態更新程序上的更新。

1. 建立稱為 **simulatedeviceconfiguration** 的新空白資料夾。 在 **simulatedeviceconfiguration** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。 接受所有預設值：
   
    ```
    npm init
    ```
2. 在 **simulatedeviceconfiguration** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iot-device** 和 **azure-iot-device-mqtt** 套件：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 使用文字編輯器，在 **simulatedeviceconfiguration** 資料夾中建立新的 **SimulateDeviceConfiguration.js** 檔案。
4. 將下列程式碼新增至 **SimulateDeviceConfiguration.js** 檔案，並以您建立 **myDeviceId** 裝置身分識別時所複製的裝置連接字串，取代 **{device connection string}** 預留位置︰
   
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
   
    **Client** 物件會公開從裝置來與裝置對應項進行互動時所需的所有方法。 先前的程式碼在初始化 **Client** 物件之後，會擷取 **myDeviceId** 的裝置對應項，並附加處理常式來處理所需屬性的更新。 此處理常式會比較 configIds 來驗證有實際的組態變更要求，然後叫用方法來啟動組態變更。
   
    請注意，為了簡單起見，先前的程式碼使用硬式編碼的預設值作為初始組態。 實際的應用程式可能會從本機儲存體載入該組態。
   
   > [!IMPORTANT]
   > 所需的屬性變更事件永遠會在裝置連線時發出一次，在執行任何動作之前，請務必檢查所需的屬性有實際的變更。
   > 
   > 
5. 在 `client.open()` 叫用之前新增下列方法︰
   
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
   
    **InitConfigChange** 方法會依組態更新要求，在本機裝置對應項物件上更新所報告的屬性，並將狀態設為 **Pending**，然後更新服務上的裝置對應項。 成功更新裝置對應項之後，它會模擬一個長時間執行而在 **completeConfigChange** 執行過程中終止的處理序。 這個方法會更新本機裝置對應項所報告的屬性，將狀態設定為 **Success**，並移除 **pendingConfig** 物件。 然後更新服務上的裝置對應項。
   
    請注意，為了節省頻寬，更新所報告的屬性時只會指定要修改的屬性 (在上述程式碼中名為 **patch**)，而不是取代整份文件。
   
   > [!NOTE]
   > 本教學課程不會模擬並行組態更新的任何行為。 某些組態更新程序可能在更新執行時能夠接受目標組態的變更，其他程序可能必須將變更排入佇列，還有其他程序可能會因錯誤條件而拒絕變更。 請務必考慮您的特定程序所需的行為，並在初始化組態變更之前新增適當的邏輯。
   > 
   > 
6. 執行裝置應用程式︰
   
        node SimulateDeviceConfiguration.js
   
    您應該會看見訊息 `retrieved device twin`。 保持應用程式執行。

## <a name="create-the-service-app"></a>建立服務應用程式
在本節中，您將建立 Node.js 主控台應用程式，在與 **myDeviceId** 相關聯的裝置對應項上，以新的遙測組態物件來更新「所需屬性」。 然後，它會查詢儲存在 IoT 中樞的裝置對應項，並顯示裝置的所需和所報告組態之間的差異。

1. 建立稱為 **setdesiredandqueryapp** 的新空白資料夾。 在 **setdesiredandqueryapp** 資料夾中，於命令提示字元使用下列命令建立新的 package.json 檔案。 接受所有預設值：
   
    ```
    npm init
    ```
2. 在命令提示字元下，於 **callmethodondevice** 資料夾中執行下列命令以安裝 **azure-iothub** 套件：
   
    ```
    npm install azure-iothub node-uuid --save
    ```
3. 使用文字編輯器，在 **setdesiredandqueryapp** 資料夾中建立新的 **SetDesiredAndQuery.js** 檔案。
4. 將下列程式碼新增至 **SetDesiredAndQuery.js** 檔案，並以您建立中樞時所複製的 IoT 中樞連接字串，取代 **{iot hub connection string}** 預留位置︰
   
        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });

    **Registry** 物件會公開從服務來與裝置對應項進行互動時所需的所有方法。 先前的程式碼在初始化 **Registry** 物件之後，會擷取 **myDeviceId** 的裝置對應項，並以新的遙測組態物件來更新其所需屬性。 之後，它每 10 秒呼叫一次 **queryTwins** 函式。

    > [!IMPORTANT]
    > 為了便於說明，此應用程式每 10 秒查詢一次 IoT 中樞。 跨許多裝置時，請使用查詢來產生適合使用者的報告，而非偵測變更。 如果您的解決方案需要裝置事件的即時通知，請使用[對應項通知][lnk-twin-notifications]。
    > 
    >。

1. 在 `registry.getDeviceTwin()` 叫用之前，新增下列程式碼來實作 **queryTwins** 函式︰
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };
   
    先前的程式碼會查詢儲存在 IoT 中樞的裝置對應項，並列印所需和所報告的遙測組態。 請參閱 [IoT 中樞查詢語言][lnk-query]，以了解如何產生跨所有裝置的實用報告。
2. 在 **SimulateDeviceConfiguration.js** 執行的情況下，使用下列命令執行應用程式︰
   
        node SetDesiredAndQuery.js 5m
   
    您應該會看到所報告的組態從 **Success** 變成 **Pending**，又再變回 **Success**，而且新的作用中傳送頻率是五分鐘，而不是 24 小時。
   
   > [!IMPORTANT]
   > 裝置報告作業和查詢結果之間的延遲最多一分鐘。 這是為了讓查詢基礎結構能夠以非常高的延展性運作。 若要擷取單一裝置對應項的一致檢視，請使用 **Registry** 類別中的 **getDeviceTwin** 方法。
   > 
   > 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您將會從後端應用程式將所需的組態設為「所需屬性」，還會撰寫模擬裝置應用程式來偵測該變更，並模擬多步驟更新程序，以「所報告屬性」將其狀態回報給裝置對應項。

使用下列資源來了解如何：

* 從裝置傳送遙測，請參閱[開始使用 IoT 中樞][lnk-iothub-getstarted]教學課程，
* 在大量裝置上排定或執行作業，請參閱[排定及廣播作業][lnk-schedule-jobs]教學課程。
* 以互動方式控制裝置 (例如，從使用者控制的應用程式開啟風扇)，請參閱[使用直接方法][lnk-methods-tutorial]教學課程。

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

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
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app

