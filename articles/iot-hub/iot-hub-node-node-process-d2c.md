---
title: "使用 Azure IoT 中樞路由傳送訊息 (節點) | Microsoft Docs"
description: "如何使用路由規則和自訂端點來處理 Azure IoT 中樞裝置到雲端的訊息，以將訊息分派至其他後端服務。"
services: iot-hub
documentationcenter: node
author: msebolt
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo
ms.openlocfilehash: 5a80195dd474414626cc54623945393c6f88093d
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="routing-messages-with-iot-hub-node"></a>使用 IoT 中樞路由傳送訊息 (節點)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

本教學課程是以[開始使用 IoT 中樞]教學課程為基礎。  教學課程會：

* 說明如何使用路由規則，以簡單的設定方式來分派裝置到雲端訊息。
* 說明如何從解決方案後端隔離需要立即採取行動的互動式訊息，以便進一步處理。  例如，裝置可能會傳送一則警示訊息，以觸發將票證插入 CRM 系統的作業。  相較之下，資料點訊息 (例如溫度遙測) 只會饋送至分析引擎。

在本教學課程結尾處，您會執行三個 Node.js 主控台應用程式：

* **SimulatedDevice.js** (修改自[開始使用 IoT 中樞]教學課程中所建立的應用程式) 每秒會傳送資料點的裝置到雲端訊息，並且以隨機間隔傳送互動式裝置到雲端的訊息。 此應用程式會使用 MQTT 通訊協定與「IoT 中樞」進行通訊。
* **ReadDeviceToCloudMessages.js**會顯示裝置應用程式所傳送的遙測。
* **ReadCriticalQueue.js** 可從連結到 IoT 中樞的服務匯流排佇列中移除重要訊息。

> [!NOTE]
> IoT 中樞對於許多裝置平台和語言 (包括 C、Java 和 JavaScript) 提供 SDK 支援。 如需了解如何以實體裝置取代本教學課程中的裝置，以及如何將裝置連接到「IoT 中樞」，請參閱 [Azure IoT 開發人員中心]。

若要完成此教學課程，您需要下列項目：

* [開始使用 IoT 中樞] 教學課程的完整運作版本。
* Node.js 4.0.x 版或更新版本。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

我們也建議您閱讀 [Azure 儲存體]和 [Azure 服務匯流排]的相關文件。

## <a name="send-interactive-messages-from-a-device-app"></a>從裝置應用程式傳送互動式訊息
在本節中，您會修改您在[開始使用 IoT 中樞]教學課程中建立的裝置應用程式，偶爾傳送需要立即處理的訊息。

1. 使用文字編輯器開啟 **simulateddevice\SimulatedDevice.js** 檔案。 這個檔案包含您在[開始使用 IoT 中樞]教學課程中建立的 **SimulatedDevice**應用程式。

2. 使用下列程式碼取代 **connectCallback** 函式：

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    這個方法會隨機將 `"level": "critical"` 和 `"level": "storage"` 屬性新增至裝置所傳送的訊息，該裝置會模擬需要應用程式後端立即採取行動的訊息，或需要永久儲存的訊息。 應用程式會在訊息屬性中傳遞此資訊，而不是在訊息主體中傳遞，因此 IoT 中樞可以將訊息路由傳送至適當的訊息目的地。
   
   > [!NOTE]
   > 除了此處顯示的最忙碌路徑範例以外，您可以使用訊息屬性來路由傳送各種案例的訊息，包括冷門路徑處理。

2. 儲存並關閉 **simulateddevice\SimulatedDevice.js** 檔案。

    > [!NOTE]
    > 我們建議您如 MSDN 文章[暫時性錯誤處理]所建議來實作重試原則 (例如指數型輪詢)。

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>將服務匯流排佇列新增至 IoT 中樞並將訊息路由傳送至該佇列

本節中，您會建立服務匯流排佇列、將它連接到您的 IoT 中樞，以及設定您的 IoT 中樞，進而根據訊息的屬性目前狀態，將訊息傳送至佇列。 如需有關如何處理來自服務匯流排佇列之訊息的詳細資訊，請參閱[開始使用佇列][lnk-sb-queues-node]。

1. 如[開始使用佇列][lnk-sb-queues-node]所述，建立服務匯流排佇列。 記下命名空間和佇列名稱。

2. 在 Azure 入口網站中，開啟 IoT 中樞然後按一下 [端點]。

    ![IoT 中樞端點][30]

3. 在 [端點] 刀鋒視窗中，按一下頂端的 [新增] 將您的佇列新增至 IoT 中樞。 將端點命名為 **CriticalQueue**，並使用下拉式清單來選取 [服務匯流排佇列]、您的佇列所在的服務匯流排命名空間，以及您的佇列名稱。 完成時，請按一下底部的 [確定]。  

    ![新增端點][31]

4. 現在按一下 IoT 中樞中的 [路由]。 按一下刀鋒視窗頂端的 [新增] 來建立路由規則，以便將訊息路由傳送至您剛才新增的佇列。 選取**裝置訊息** 作為資料來源。 輸入 `level="critical"` 作為條件，然後選擇 **CriticalQueue**作為路由規則端點。 在底部按一下 [儲存]。  

    ![新增路由][32]

    確定後援路由設定為 [開啟]。 此設定為 IoT 中樞的預設設定。

    ![後援路由][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(選用) 從佇列端點讀取

在本節中，您會建立 Node.js 主控台應用程式，以讀取來自 IoT 服務匯流排的重大訊息。 如需更深入的資訊，請參閱[開始使用佇列][lnk-sb-queues-node]。 

1. 建立稱為 `readcriticalqueue` 的空資料夾。 在 `readcriticalqueue` 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。 接受所有預設值：

    ```cmd/sh
    npm init
    ```

2. 在命令提示字元上，於 `readcriticalqueue` 資料夾中執行下列命令，以安裝 **azure** 封裝：

    ```cmd/sh
    npm install azure --save
    ```

3. 使用文字編輯器，在 `readcriticalqueue` 資料夾中建立 **ReadCriticalQueue.js** 檔案。

4. 在 **ReadCriticalQueue.js** 檔案開頭新增下列 `require` 陳述式：

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. 新增下列變數宣告，並將預留位置值替換為 IoT 服務匯流排連接字串和佇列名稱：

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. 新增下列兩個會將輸出列印到主控台的函數：

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. 儲存並關閉 **ReadCriticalQueue.js** 檔案。

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行這三個應用程式。

1. 若要執行 **ReadDeviceToCloudMessages.js** 應用程式，請在命令提示字元或殼層中，瀏覽至 readdevicetocloudmessages 資料夾，然後執行下列命令：

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![執行 read-d2c-messages][readd2c]

2. 若要執行 **ReadCriticalQueue.js** 應用程式，請在命令提示字元或殼層中，瀏覽至 readcriticalqueue 資料夾，然後執行下列命令：

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![執行 read-critical-messages][readqueue]

3. 若要執行 **SimulatedDevice.js** 應用程式，請在命令提示字元或殼層中，瀏覽至 simulateddevice 資料夾，然後執行下列命令：

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![執行 simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(選擇性) 將儲存體容器新增到您的 IoT 中樞並將訊息路由傳送給它

本節中，您會建立儲存體帳戶、將它連線到您的 IoT 中樞，以及設定您的 IoT 中樞，進而根據訊息所帶有的屬性，將訊息傳送至該帳戶。 如需如何管理儲存體的詳細資訊，請參閱[開始使用 Azure 儲存體][Azure 儲存體]。

 > [!NOTE]
   > 如果您不受限於一個**端點**，則您除了 **CriticalQueue** 之外還可以設定 **StorageContainer**，並且兩個同時執行。

1. 按照 [Azure 儲存體文件][lnk-storage]中所述的方法來建立儲存體帳戶。 記下帳戶名稱。

2. 在 Azure 入口網站中，開啟 IoT 中樞然後按一下 [端點]。

3. 在 [端點] 刀鋒視窗中，選取 [CriticalQueue] 端點，然後按一下 [刪除]。 按一下 [是]，然後按一下 [新增]。 將端點命名為 **StorageContainer**，並使用下拉式清單選取 [Azure 儲存體容器]，然後建立**儲存體帳戶**和**儲存體容器**。  記下名稱。  完成時，請按一下底部的 [確定]。 

 > [!NOTE]
   > 如果您不受限於一個**端點**，您不需要刪除 **CriticalQueue**。

4. 按一下 IoT 中樞中的 [路由]。 按一下刀鋒視窗頂端的 [新增] 來建立路由規則，以便將訊息路由傳送至您剛才新增的佇列。 選取**裝置訊息** 作為資料來源。 輸入 `level="storage"` 作為條件，然後選擇 **StorageContainer**作為路由規則端點。 在底部按一下 [儲存]。  

    確定後援路由設定為 [開啟]。 此設定為 IoT 中樞的預設設定。

1. 請確定先前的應用程式 **SimulatedDevice.js** 仍在執行。 

1. 在 Azure 入口網站中移至儲存體帳戶，並在 **Blob 服務**下方按一下 [瀏覽 Blob...].選取您的容器，瀏覽至 JSON 檔案並按一下，然後按一下 [下載] 來檢視資料。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已學到如何使用 IoT 中樞的訊息路由功能來可靠地分派裝置到雲端訊息。

[如何使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d]示範如何從解決方案後端將訊息傳送至您的裝置。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 套件][lnk-suite]。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南]。

若要深入了解 IoT 中樞的訊息路由，請參閱[使用 IoT 中樞傳送和接收訊息][lnk-devguide-messaging]。

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[Azure 儲存體]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服務匯流排]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT 中樞開發人員指南]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[開始使用 IoT 中樞]: iot-hub-node-node-getstarted.md
[Azure IoT 開發人員中心]: https://azure.microsoft.com/develop/iot
[暫時性錯誤處理]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[暫時性錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/en-us/azure/storage/