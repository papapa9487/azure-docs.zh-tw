---
title: "使用 Node.js 連接裝置 |Microsoft Docs"
description: "描述如何使用 Node.js 中已寫入的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 87a2e97638508eef1d90a219cfb38d1fcac81d55
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>將裝置連接至遠端監視預先設定方案 (Node.js)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-nodejs-sample-solution"></a>建立 node.js 範例解決方案

請確定 Node.js 0.11.5 版或更新版本已經安裝在您的開發電腦上。 您可以在命令列執行 `node --version` 來檢查版本。

1. 在開發電腦上建立名為 **RemoteMonitoring** 的資料夾。 在命令列環境中瀏覽至此資料夾。

1. 執行下列命令，以下載並安裝完成範例應用程式所需的套件︰

    ```
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. 在 **RemoteMonitoring** 資料夾中，建立名為 **remote_monitoring.js** 的檔案。 在文字編輯器中開啟這個檔案。

1. 在 **remote_monitoring.js** 檔案中，新增下列 `require` 陳述式︰

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. 在 `require` 陳述式之後新增下列變數宣告。 使用您在遠端監視解決方案儀表板中為裝置記下的值來取代 [Device Id] 和 [Device Key] 預留位置值。 使用解決方案儀表板中的「IoT 中樞主機名稱」來取代 [IoTHub Name]。 例如，若您的 IoT 中樞主機名稱是 **contoso.azure-devices.net**，請使用 **contoso** 取代 [IoTHub Name]：

    ```nodejs
    var connectionString = 'HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. 新增下列變數來定義一些基本遙測資料︰

    ```nodejs
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    ```

1. 新增下列協助程式來列印作業結果︰

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. 新增下列協助程式函式，用來將排遙測值隨機化︰

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. 針對裝置在啟動時傳送的 **DeviceInfo** 物件，新增下列定義︰

    ```nodejs
    var deviceMetaData = {
        'ObjectType': 'DeviceInfo',
        'IsSimulatedDevice': 0,
        'Version': '1.0',
        'DeviceProperties': {
            'DeviceID': deviceId,
            'HubEnabledState': 1
        }
    };
    ```

1. 針對裝置對應項報告值新增下列定義。 此定義包含裝置支援的直接方法說明︰

    ```nodejs
    var reportedProperties = {
        "Device": {
            "DeviceState": "normal",
            "Location": {
                "Latitude": 47.642877,
                "Longitude": -122.125497
            }
        },
        "Config": {
            "TemperatureMeanValue": 56.7,
            "TelemetryInterval": 45
        },
        "System": {
            "Manufacturer": "Contoso Inc.",
            "FirmwareVersion": "2.22",
            "InstalledRAM": "8 MB",
            "ModelNumber": "DB-14",
            "Platform": "Plat 9.75",
            "Processor": "i3-9",
            "SerialNumber": "SER99"
        },
        "Location": {
            "Latitude": 47.642877,
            "Longitude": -122.125497
        },
        "SupportedMethods": {
            "Reboot": "Reboot the device",
            "InitiateFirmwareUpdate--FwPackageURI-string": "Updates device Firmware. Use parameter FwPackageURI to specifiy the URI of the firmware file"
        },
    }
    ```

1. 新增下列函式以處理 **Reboot** 直接方法呼叫：

    ```nodejs
    function onReboot(request, response) {
        // Implement actual logic here.
        console.log('Simulated reboot...');

        // Complete the response
        response.send(200, "Rebooting device", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

1. 新增下列函式以處理 **InitiateFirmwareUpdate** 直接方法呼叫。 此直接方法使用參數來指定要下載之韌體映像的位置，並以非同步方式在裝置上啟始韌體更新︰

    ```nodejs
    function onInitiateFirmwareUpdate(request, response) {
        console.log('Simulated firmware update initiated, using: ' + request.payload.FwPackageURI);

        // Complete the response
        response.send(200, "Firmware update initiated", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });

        // Add logic here to perform the firmware update asynchronously
    }
    ```

1. 新增下列程式碼，以建立用戶端執行個體︰

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. 新增下列程式碼，以便：

    * 開啟連線。
    * 傳送 **DeviceInfo** 物件。
    * 設定所需屬性的處理常式。
    * 傳送報告屬性。
    * 登錄直接方法的處理常式。
    * 開始傳送遙測。

    ```nodejs
    client.open(function (err) {
        if (err) {
            printErrorFor('open')(err);
        } else {
            console.log('Sending device metadata:\n' + JSON.stringify(deviceMetaData));
            client.sendEvent(new Message(JSON.stringify(deviceMetaData)), printErrorFor('send metadata'));

            // Create device twin
            client.getTwin(function(err, twin) {
                if (err) {
                    console.error('Could not get device twin');
                } else {
                    console.log('Device twin created');

                    twin.on('properties.desired', function(delta) {
                        console.log('Received new desired properties:');
                        console.log(JSON.stringify(delta));
                    });

                    // Send reported properties
                    twin.properties.reported.update(reportedProperties, function(err) {
                        if (err) throw err;
                        console.log('twin state reported');
                    });

                    // Register handlers for direct methods
                    client.onDeviceMethod('Reboot', onReboot);
                    client.onDeviceMethod('InitiateFirmwareUpdate', onInitiateFirmwareUpdate);
                }
            });

            // Start sending telemetry
            var sendInterval = setInterval(function () {
                temperature += generateRandomIncrement();
                externalTemperature += generateRandomIncrement();
                humidity += generateRandomIncrement();

                var data = JSON.stringify({
                    'DeviceID': deviceId,
                    'Temperature': temperature,
                    'Humidity': humidity,
                    'ExternalTemperature': externalTemperature
                });

                console.log('Sending device event data:\n' + data);
                client.sendEvent(new Message(data), printErrorFor('send event'));
            }, 5000);

            client.on('error', function (err) {
                printErrorFor('client')(err);
                if (sendInterval) clearInterval(sendInterval);
                client.close(printErrorFor('client.close'));
            });
        }
    });
    ```

1. 將變更儲存至 **remote_monitoring.js** 檔案。

1. 在命令提示字元中執行下列命令，以啟動範例應用程式：
   
    ```
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
