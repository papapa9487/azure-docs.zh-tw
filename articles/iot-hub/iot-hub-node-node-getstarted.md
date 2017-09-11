---
title: "開始使用 Azure IoT 中樞 (Node) | Microsoft Docs"
description: "了解如何使用適用於 Node.js 的 IoT SDK 將裝置到雲端訊息傳送至 Azure IoT 中樞。 建立模擬裝置和服務應用程式來註冊您的裝置、傳送訊息，並從 IoT 中樞讀取訊息。"
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 56618522-9a31-42c6-94bf-55e2233b39ac
ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: ffc6cfaadae07cb019bc64954343dab21ed23638
ms.contentlocale: zh-tw
ms.lasthandoff: 08/31/2017

---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-node"></a>使用 Node 將您的模擬裝置連線至 IoT 中樞

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

在本教學課程結尾處，您會有三個 Node.js 主控台應用程式：

* **CreateDeviceIdentity.js**，這會建立裝置身分識別與相關聯的安全性金鑰，來連線到您的模擬裝置應用程式。
* **ReadDeviceToCloudMessages.js**，其中顯示模擬裝置應用程式所傳送的遙測。
* **SimulatedDevice.js**，這會使用先前建立的裝置識別連接到您的 IoT 中樞，並使用 MQTT 通訊協定每秒傳送遙測訊息。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供 Azure IoT SDK (可讓您同時建置在裝置與您的解決方案後端執行的兩個應用程式) 的相關資訊。

若要完成此教學課程，您需要下列項目：

* Node.js 0.10.x 版或更新版本。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

您現在已經建立 IoT 中樞。 您具有完成本教學課程的其餘部分所需的 IoT 中樞主機名稱和連接字串。

## <a name="create-a-device-identity"></a>建立裝置識別

在本節中，您會建立 Node.js 主控台應用程式，而它會在 IoT 中樞的識別登錄中建立裝置識別。 如果裝置在身分識別登錄中具有項目，則只能連線到 IoT 中樞。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]的＜身分識別登錄＞一節。 執行這個應用程式來產生唯一的裝置識別碼和金鑰，當裝置傳送裝置對雲端訊息時，其可用來識別裝置本身。

1. 建立名為 `createdeviceidentity` 的新空白資料夾。 在 `createdeviceidentity` 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。 接受所有預設值：

    ```cmd/sh
    npm init
    ```

2. 在 `createdeviceidentity` 資料夾中，於命令提示字元執行下列命令以安裝 `azure-iothub` 服務 SDK 套件：

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. 使用文字編輯器，在 `createdeviceidentity` 資料夾中建立 **CreateDeviceIdentity.js** 檔案。

4. 在 **CreateDeviceIdentity.js** 檔案開頭新增下列 `require` 陳述式：

    ```nodejs
    'use strict';

    var iothub = require('azure-iothub');
    ```

5. 將下列程式碼新增至 **CreateDeviceIdentity.js** 檔案。 將預留位置的值替換為您在上一節中為中樞所建立的 IoT 中樞連接字串：

    ```nodejs
    var connectionString = '{iothub connection string}';

    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. 新增下列程式碼以在 IoT 中樞的身分識別登錄建立裝置定義。 如果身分識別登錄中沒有該裝置識別碼，此程式碼會建立裝置，否則會傳回現有裝置的金鑰：

    ```nodejs
    var device = {
      deviceId: 'myFirstNodeDevice'
    }
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device ID: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.symmetricKey.primaryKey);
      }
    }
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

7. 儲存並關閉 **CreateDeviceIdentity.js** 檔案。

8. 若要執行 `createdeviceidentity` 應用程式，請在 `createdeviceidentity` 資料夾中，於命令提示字元執行下列命令：

    ```cmd/sh
    node CreateDeviceIdentity.js 
    ```

9. 記下**裝置識別碼**和**裝置金鑰**。 稍後在建立連線至做為裝置之 IoT 中樞的應用程式時，您會需要這些值。

> [!NOTE]
> IoT 中樞身分識別登錄只會儲存裝置身分識別，以啟用對 IoT 中樞的安全存取。 它會儲存裝置識別碼和金鑰來作為安全性認證，以及啟用或停用旗標，讓您用來停用個別裝置的存取。 如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]。

<a id="D2C_node"></a>
## <a name="receive-device-to-cloud-messages"></a>接收裝置到雲端的訊息

在本節中，您會建立 Node.js 主控台應用程式，以讀取來自 IoT 中樞的裝置到雲端訊息。 IoT 中樞會公開與[事件中樞][lnk-event-hubs-overview]相容的端點以讓您讀取裝置到雲端訊息。 為了簡單起見，本教學課程會建立的基本讀取器不適合用於高輸送量部署。 [處理裝置到雲端訊息][lnk-process-d2c-tutorial]教學課程會說明如何大規模處理裝置到雲端訊息。 [開始使用事件中樞][lnk-eventhubs-tutorial]教學課程提供進一步的資訊，而此資訊適用於 IoT 中樞事件中樞相容端點。

> [!NOTE]
> 用於讀取裝置到雲端訊息的事件中樞相容端點一律會使用 AMQP 通訊協定。

1. 建立稱為 `readdevicetocloudmessages` 的空資料夾。 在 `readdevicetocloudmessages` 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。 接受所有預設值：

    ```cmd/sh
    npm init
    ```

2. 在 `readdevicetocloudmessages` 資料夾中，於命令提示字元執行下列命令以安裝 **azure-event-hubs** 套件：

    ```cmd/sh
    npm install azure-event-hubs --save
    ```

3. 使用文字編輯器，在 `readdevicetocloudmessages` 資料夾中建立 **ReadDeviceToCloudMessages.js** 檔案。

4. 在 **ReadDeviceToCloudMessages.js** 檔案開頭新增下列 `require` 陳述式：

    ```nodejs
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. 新增下列變數宣告，並將預留位置值替換為中樞的 IoT 中樞連接字串：

    ```nodejs
    var connectionString = '{iothub connection string}';
    ```

6. 新增下列兩個會將輸出列印到主控台的函數：

    ```nodejs
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. 加入下列程式碼以建立 **EventHubClient**、開啟您的 IoT 中樞的連線，並建立每個資料分割的接收者。 在建立開始執行後只會讀取傳送到 IoT 中樞之訊息的收件者時，此應用程式會使用篩選器。 此篩選器很適合測試環境，如此一來您即可看到目前的訊息集。 在生產環境中，您的程式碼應該要確定它能處理所有訊息。 如需詳細資訊，請參閱[如何處理 IoT 中樞裝置到雲端訊息][lnk-process-d2c-tutorial]教學課程：

    ```nodejs
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. 儲存並關閉 **ReadDeviceToCloudMessages.js** 檔案。

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您會撰寫 Node.js 主控台應用程式，模擬裝置傳送裝置對雲端訊息至 IoT 中樞。

1. 建立稱為 `simulateddevice` 的空資料夾。 在 `simulateddevice` 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。 接受所有預設值：

    ```cmd/sh
    npm init
    ```

2. 在 `simulateddevice` 資料夾中，於命令提示字元執行下列命令來安裝 **azure-iot-device** 裝置 SDK 套件和 **azure-iot-device-mqtt** 套件：

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. 使用文字編輯器，在 `simulateddevice` 資料夾中建立 **SimulatedDevice.js** 檔案。

4. 在 **SimulatedDevice.js** 檔案開頭新增下列 `require` 陳述式：

    ```nodejs
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. 新增 `connectionString` 變數，並用它來建立**用戶端**執行個體。 以您在＜建立 IoT 中樞＞ 一節中建立的 IoT 中樞名稱取代 `{youriothostname}`。 以您在＜建立裝置識別＞一節中產生的裝置金鑰值取代 `{yourdevicekey}`：

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';

    var client = clientFromConnectionString(connectionString);
    ```

6. 新增下列函數來顯示應用程式的輸出：

    ```nodejs
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 建立回呼，並使用 **setInterval** 函式每秒將新訊息傳送至 IoT 中樞：

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);            
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

8. 開啟您 IoT 中樞的連線，並開始傳送訊息︰

    ```nodejs
    client.open(connectCallback);
    ```

9. 儲存並關閉 **SimulatedDevice.js** 檔案。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。

## <a name="run-the-apps"></a>執行應用程式

您現在可以開始執行應用程式。

1. 在 `readdevicetocloudmessages` 資料夾的命令提示字元中，執行下列命令以開始監視 IoT 中樞：

    ```cmd/sh
    node ReadDeviceToCloudMessages.js 
    ```

    ![用來監視裝置到雲端訊息的 Node.js IoT 中樞服務應用程式][7]

2. 在 `simulateddevice` 資料夾的命令提示字元中，執行下列命令以開始將遙測資料傳送至 IoT 中樞：

    ```cmd/sh
    node SimulatedDevice.js
    ```

    ![用來傳送裝置到雲端訊息的 Node.js IoT 中樞裝置應用程式][8]

3. [Azure 入口網站][lnk-portal]中的 [使用量] 圖格會顯示傳送至 IoT 中樞的訊息數目︰

    ![顯示傳送到 IoT 中樞之訊息數目的 Azure 入口網站使用量圖格][43]

## <a name="next-steps"></a>後續步驟

在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您會將此裝置身分識別用於啟用模擬裝置應用程式，以將裝置到雲端訊息傳送至 IoT 中樞。 您也會建立一個應用程式來顯示 IoT 中樞所接收的訊息。

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [連接您的裝置][lnk-connect-device]
* [開始使用裝置管理][lnk-device-management]
* [開始使用 Azure IoT Edge][lnk-iot-edge]

若要了解如何擴充您的 IoT 解決方案及大規模處理裝置到雲端訊息，請參閱[處理裝置到雲端訊息][lnk-process-d2c-tutorial]教學課程。
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

