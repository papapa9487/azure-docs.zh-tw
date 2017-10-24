---
title: "使用 Azure IoT Edge 來模擬裝置 (Windows) | Microsoft Docs"
description: "如何在 Windows 上使用 Azure IoT Edge 來建立模擬裝置，以透過 Azure IoT Edge 閘道將遙測資料傳送至 IoT 中樞。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.openlocfilehash: 0aa1836ee1445894022b95fefc2338ef53698240
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>使用 Azure IoT Edge 搭配模擬裝置來傳送裝置到雲端訊息 (Windows)

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>執行範例

**build.cmd** 指令碼會在 **iot-edge** 存放庫本機複本的 **build** 資料夾中產生其輸出。 此輸出包含本範例中使用的四個 IoT Edge 模組。

組建指令碼會建立下列檔案：

* **build\\modules\\logger\\Debug** 資料夾中的 **logger.dll**。
* **build\\modules\\iothub\\Debug** 資料夾中的 **iothub.dll**。
* **build\\modules\\identitymap\\Debug** 資料夾中的 **identity\_map.dll**。
* **build\\modules\\simulated\_device\\Debug** 資料夾中的 **simulated\_device.dll**。

使用這些路徑作為**模組路徑**值，如 simulated\_device\_cloud\_upload\_win JSON 設定檔所示。

simulated\_device\_cloud\_upload sample 程序會採用 JSON 組態檔的路徑作為命令列引數。 下列範例 JSON 檔會提供於 SDK 存放庫中，位置為：**samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_win.json**。 除非您修改 build 指令碼以將 IoT Edge 模組或範例可執行檔放置在非預設位置，否則此組態檔將保有原始功能。

> [!NOTE]
> 模組路徑是相對於 simulated\_device\_cloud\_upload\_sample.exe 所在的目錄。 範例 JSON 組態檔的預設值為在目前的工作目錄中寫入 'deviceCloudUploadGatewaylog.log'。

在文字編輯器中，開啟 **iot-edge** 存放庫之本機複本中的 **samples\\simulated\_device\_cloud\_upload\\src\\simulated\_device\_cloud\_upload\_win.json** 檔案。 這個檔案會設定閘道範例中的 IoT Edge 模組︰

* **IoTHub** 模組會連接至您的 IoT 中樞。 您必須設定它，以將資料傳送至您的 IoT 中樞。 特別是將 **IoTHubName** 值設定為 IoT 中樞的名稱，並將 **IoTHubSuffix** 值設定為 **azure-devices.net**。 將 **Transport** 值設定為 **HTTP**、**AMQP** 或 **MQTT** 其中一個。 目前只有 **HTTP** 會為所有裝置訊息共用一個 TCP 連線。 如果您將值設定為 **AMQP** 或 **MQTT**，閘道會為每個裝置維持一個連至 IoT 中樞的個別 TCP 連線。
* **mapping** 模組會將您模擬裝置的 MAC 位址對應到您 IoT 中樞裝置識別碼。 將 **deviceId** 值設定為您新增至 IoT 中樞的兩個裝置之識別碼。 將 **deviceKey** 的值設定為兩個裝置的金鑰。
* **BLE1** 和 **BLE2** 模組是模擬裝置。 請注意模組 MAC 位址如何符合 **mapping** 模組中的 MAC 位址。
* **Logger** 模組會將閘道活動記錄到檔案。
* 下列範例中顯示的**模組路徑**值是相對於 simulated\_device\_cloud\_upload\_sample.exe 所在的目錄。
* JSON 檔案底部的 **links** 陣列會將 **BLE1** 和 **BLE2** 模組連接至 **mapping** 模組，再將 **mapping** 模組連接至 **IoTHub** 模組。 它也可確保 **Logger** 模組會記錄所有訊息。

```json
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

儲存您對組態檔進行的變更。

執行範例：

1. 在命令提示字元中，瀏覽至 **iot-edge** 存放庫本機複本的 **build** 資料夾。
2. 執行以下命令：
   
    ```cmd
    samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe ..\samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. 您可以使用[裝置總管][lnk-device-explorer]或 [iothub-explorer][lnk-iothub-explorer] 工具，以監視 IoT 中樞從閘道接收的訊息。 例如，您可以使用 iothub-explorer，然後使用以下命令監控裝置到雲端的訊息：

    ```cmd
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>後續步驟

如果要更進一步了解 IoT Edge 並實驗一些程式碼範例，請瀏覽下列開發人員教學課程和資源：

* [使用 IoT Edge 從實體裝置傳送裝置到雲端訊息][lnk-physical-device]
* [Azure IoT Edge][lnk-iot-edge]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南][lnk-devguide]
* [徹底保護您的 IoT 解決方案][lnk-securing]

<!-- Links -->
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-physical-device]: iot-hub-iot-edge-physical-device.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
