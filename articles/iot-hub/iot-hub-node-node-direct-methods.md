---
title: "Azure IoT 中樞直接方法 (Node) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞直接方法。 您可以使用適用於 Node.js 的 Azure IoT SDK，實作模擬裝置應用程式 (包含直接方法) 和服務應用程式 (叫用直接方法)。"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: c76870c49f5c8e51f521fd4be920d976e4cdbcef
ms.openlocfilehash: 7100856b02935fba7bbf1427d86859ddb7a2b5f3
ms.contentlocale: zh-tw
ms.lasthandoff: 03/02/2017

---
# <a name="use-direct-methods-on-your-iot-device-with-nodejs"></a>搭配 Node.js 在 IoT 裝置上使用直接方法
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

在本教學課程結尾處，您會有兩個 Node.js 主控台應用程式：

* **CallMethodOnDevice.js**，會在模擬裝置應用程式中呼叫方法，並顯示回應。
* **SimulatedDevice.js**，會使用先前建立的裝置身分識別連接到您的 IoT 中樞，並且回應雲端呼叫的方法。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供 Azure IoT SDK (可讓您同時建置在裝置與您的解決方案後端執行的兩個應用程式) 的相關資訊。
> 
> 

若要完成此教學課程，您需要下列項目：

* Node.js 0.10.x 版或更新版本。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您建立 Node.js 主控台應用程式，回應雲端所呼叫的方法。

1. 建立稱為 **simulateddevice**的新的空資料夾。 在 **simulateddevice** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。 接受所有預設值：
   
    ```
    npm init
    ```
2. 在 **simulateddevice** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iot-device** 裝置 SDK 套件以及 **azure-iot-device-mqtt** 套件：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 使用文字編輯器，在 **simulateddevice** 資料夾中建立新的 **SimulatedDevice.js** 檔案。
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
6. 新增下列函式，在裝置上實作方法︰
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. 開啟您 IoT 中樞的連線，並開始初始化方法接聽程式︰
   
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

## <a name="call-a-method-on-a-device"></a>在裝置上呼叫方法
在本節中，您會建立 Node.js 主控台應用程式，在模擬裝置應用程式中呼叫方法，然後顯示回應。

1. 建立稱為 **callmethodondevice**的新的空資料夾。 在 **callmethodondevice** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。 接受所有預設值：
   
    ```
    npm init
    ```
2. 在 **callmethodondevice** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iothub** 套件：
   
    ```
    npm install azure-iothub --save
    ```
3. 使用文字編輯器，在 **callmethodondevice** 資料夾中建立 **CallMethodOnDevice.js** 檔案。
4. 在 **CallMethodOnDevice.js** 檔案開頭新增下列 `require` 陳述式：
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    ```
5. 新增下列變數宣告，並將預留位置值替換為中樞的 IoT 中樞連接字串：
   
    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```
6. 建立用戶端以開啟到您的 IoT 中樞的連接。
   
    ```
    var client = Client.fromConnectionString(connectionString);
    ```
7. 新增下列函式來叫用裝置方法，並列印對主控台的裝置回應︰
   
    ```
    var methodParams = {
        methodName: methodName,
        payload: 'hello world',
        timeoutInSeconds: 30
    };
   
    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```
8. 儲存並關閉 **CallMethodOnDevice.js** 檔案。

## <a name="run-the-apps"></a>執行應用程式
您現在可以開始執行應用程式。

1. 在 **simulateddevice** 資料夾的命令提示字元中，執行下列命令以開始接聽來自您的 IoT 中樞的方法呼叫：
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. 在 **callmethodondevice** 資料夾的命令提示字元中，執行下列命令以開始監視 IoT 中樞：
   
    ```
    node CallMethodOnDevice.js 
    ```
   
    ![][8]
3. 您會看到裝置對方法的反應，方法是列印訊息和應用程式，它們會呼叫方法並且顯示來自裝置的回應：
   
    ![][9]

## <a name="next-steps"></a>後續步驟
在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您會將此裝置識別用於啟用模擬的裝置應用程式，以將雲端所叫用的方法進行反應。 您也會建立應用程式，在裝置上叫用方法，並且顯示來自裝置的回應。 

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [IoT 中樞入門]
* [排程多個裝置上的作業][lnk-devguide-jobs]

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs]教學課程。

<!-- Images. -->
[7]: ./media/iot-hub-node-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-node-node-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-node-node-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[IoT 中樞入門]: iot-hub-node-node-getstarted.md

