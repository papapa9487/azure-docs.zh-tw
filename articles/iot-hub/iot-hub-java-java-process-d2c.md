---
title: "使用 Azure IoT 中樞路由傳送訊息 (Java) | Microsoft Docs"
description: "如何使用路由規則和自訂端點來處理 Azure IoT 中樞裝置到雲端的訊息，以將訊息分派至其他後端服務。"
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.openlocfilehash: 81f846e1fd8cca586613e6fc57737ec27e43a639
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="routing-messages-with-iot-hub-java"></a>使用 IoT 中樞路由傳送訊息 (Java)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Azure IoT 中樞是一項完全受管理的服務，可在數百萬個裝置和一個解決方案後端之間啟用可靠且安全的雙向通訊。 其他教學課程 ([開始使用IoT 中樞入門]和[使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d]) 說明如何使用 IoT 中樞的裝置到雲端和雲端到裝置的基本傳訊功能。

本教學課程是以 [開始使用IoT 中樞入門]中顯示的程式碼為基礎，並示範如何使用訊息路由以可調整的方式來處理裝置到雲端訊息。 本教學課程說明如何從解決方案後端處理需要立即採取行動的訊息。 例如，裝置可能會傳送一則警示訊息，以觸發將票證插入 CRM 系統的作業。 相較之下，資料點訊息只會饋送至分析引擎。 例如，來自裝置且要儲存以供日後分析的溫度遙測就是資料點訊息。

在本教學課程結尾，您會執行三個 Java 主控台應用程式：

* **simulated-device**(在 [開始使用IoT 中樞入門] 教學課程中建立之應用程式的已修改版本) 會每秒傳送資料點裝置到雲端訊息，以及每 10 秒傳送互動式裝置到雲端訊息。 此應用程式會使用 AMQP 通訊協定與「IoT 中樞」進行通訊。
* **read-d2c-messages** 會顯示您的裝置應用程式所傳送的遙測資料。
* **read-critical-queue** 可從連接到 IoT 中樞的服務匯流排佇列中移除重要訊息。

> [!NOTE]
> IoT 中樞對於許多裝置平台和語言 (包括 C、Java 和 JavaScript) 提供 SDK 支援。 如需了解如何以實體裝置取代本教學課程中的裝置，以及如何將裝置連接到「IoT 中樞」，請參閱 [Azure IoT 開發人員中心]。

若要完成此教學課程，您需要下列項目：

* [開始使用IoT 中樞入門] 教學課程的完整運作版本。
* 最新的 [Java SE 開發套件 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

我們也建議您閱讀 [Azure 儲存體]和 [Azure 服務匯流排]的相關文件。

## <a name="send-interactive-messages-from-a-device-app"></a>從裝置應用程式傳送互動式訊息
在本節中，您會修改您在[開始使用IoT 中樞入門]教學課程中建立的裝置應用程式，偶爾傳送需要立即處理的訊息。

1. 使用文字編輯器來開啟 simulated-device\src\main\java\com\mycompany\app\App.java 檔案。 這個檔案包含您在 **開始使用 IoT 中樞** 教學課程中建立的 [開始使用IoT 中樞入門] 應用程式。

2. 以下列程式碼取代 **MessageSender** 類別：

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        if (new Random().nextDouble() > 0.5) {
                            msgStr = "This is a critical message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "critical");
                        } else {
                            msgStr = "This is a storage message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "storage");
                        }
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    這個方法會隨機將 `"level": "critical"` 和 `"level": "storage"` 屬性新增至裝置所傳送的訊息，該裝置會模擬需要應用程式後端立即採取行動的訊息，或需要永久儲存的訊息。 應用程式會在訊息屬性中傳遞此資訊，而不是在訊息主體中傳遞，因此 IoT 中樞可以將訊息路由傳送至適當的訊息目的地。
   
   > [!NOTE]
   > 除了此處顯示的最忙碌路徑範例以外，您可以使用訊息屬性來路由傳送各種案例的訊息，包括冷門路徑處理。

2. 儲存並關閉 simulated-device\src\main\java\com\mycompany\app\App.java 檔案。

    > [!NOTE]
    > 我們建議您如 MSDN 文章[暫時性錯誤處理]所建議來實作重試原則 (例如指數型輪詢)。

3. 若要使用 Maven 建置 **simulated-device** 應用程式，請在命令提示字元中的 simulated-device 資料夾內執行下列命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>將佇列新增至 IoT 中樞並將訊息路由傳送至該佇列

本節中，您會建立服務匯流排佇列、將它連接到您的 IoT 中樞，以及設定您的 IoT 中樞，進而根據訊息的屬性目前狀態，將訊息傳送至佇列。 如需有關如何處理來自服務匯流排佇列之訊息的詳細資訊，請參閱[開始使用佇列][lnk-sb-queues-java]。

1. 如[開始使用佇列][lnk-sb-queues-java]所述，建立服務匯流排佇列。 記下命名空間和佇列名稱。

2. 在 Azure 入口網站中，開啟 IoT 中樞然後按一下 [端點]。

    ![IoT 中樞端點][30]

3. 在 [端點] 刀鋒視窗中，按一下頂端的 [新增] 將您的佇列新增至 IoT 中樞。 將端點命名為 **CriticalQueue**，並使用下拉式清單來選取 [服務匯流排佇列]、您的佇列所在的服務匯流排命名空間，以及您的佇列名稱。 完成時，請按一下底部的 [儲存]。

    ![新增端點][31]

4. 現在按一下 IoT 中樞中的 [路由]。 按一下刀鋒視窗頂端的 [新增] 來建立路由規則，以便將訊息路由傳送至您剛才新增的佇列。 選取 **DeviceTelemetry** 做為資料來源。 輸入 `level="critical"` 做為條件，然後選擇您剛才新增為自訂端點的佇列做為路由規則端點。 完成時，請按一下底部的 [儲存]。

    ![新增路由][32]

    確定後援路由設定為 [開啟]。 此設定為 IoT 中樞的預設設定。

    ![後援路由][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(選用) 從佇列端點讀取

您可以依照[開始使用佇列][lnk-sb-queues-java]中的指示，選擇性地從佇列端點讀取訊息。 將應用程式命名為 **read-critical-queue**。

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行這三個應用程式。

1. 若要執行 **read-d2c-messages** 應用程式，請在命令提示字元或殼層中，瀏覽至 read-d2c 資料夾，然後執行下列命令：

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![執行 read-d2c-messages][readd2c]

2. 若要執行 **read-critical-queue** 應用程式，請在命令提示字元或殼層中，瀏覽至 read-critical-queue 資料夾，然後執行下列命令：

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![執行 read-critical-messages][readqueue]

3. 若要執行 **simulated-device** 應用程式，請在命令提示字元或殼層中，瀏覽至 [simulated-device] 資料夾，然後執行下列命令：

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![執行 simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(選擇性) 將儲存體容器新增到您的 IoT 中樞並將訊息路由傳送給它

本節中，您會建立儲存體帳戶、將它連線到您的 IoT 中樞，以及設定您的 IoT 中樞，進而根據訊息所帶有的屬性，將訊息傳送至該帳戶。 如需如何管理儲存體的詳細資訊，請參閱[開始使用 Azure 儲存體][Azure 儲存體]。

 > [!NOTE]
   > 如果您不受限於一個**端點**，則您除了 **CriticalQueue** 之外還可以設定 **StorageContainer**，並且兩個同時執行。

1. 按照 [Azure 儲存體文件][lnk-storage] 中所述的方法來建立儲存體帳戶。 記下帳戶名稱。

2. 在 Azure 入口網站中，開啟 IoT 中樞然後按一下 [端點]。

3. 在 [端點] 刀鋒視窗中，選取 [CriticalQueue] 端點，然後按一下 [刪除]。 按一下 [是]，然後按一下 [新增]。 將端點命名為 **StorageContainer**，並使用下拉式清單選取 **Azure 儲存體容器**，然後建立**儲存體帳戶**和**儲存體容器**。  記下名稱。  完成時，請按一下底部的 [確定]。 

 > [!NOTE]
   > 如果您不受限於一個**端點**，您不需要刪除 **CriticalQueue**。

4. 按一下 IoT 中樞中的 [路由]。 按一下刀鋒視窗頂端的 [新增] 來建立路由規則，以便將訊息路由傳送至您剛才新增的佇列。 選取 [裝置訊息] 作為資料來源。 輸入 `level="storage"` 作為條件，然後選擇 **StorageContainer**作為路由規則端點。 在底部按一下 [儲存]。  

    確定後援路由設定為 [開啟]。 此設定為 IoT 中樞的預設設定。

1. 確定您先前的應用程式仍在執行。 

1. 在 Azure 入口網站中移至儲存體帳戶，並在 **Blob 服務**下方按一下 [瀏覽 Blob...]。選取您的容器，瀏覽至 JSON 檔案並按一下，然後按一下 [下載] 來檢視資料。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已學到如何使用 IoT 中樞的訊息路由功能來可靠地分派裝置到雲端訊息。

[如何使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d]示範如何從解決方案後端將訊息傳送至您的裝置。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 套件][lnk-suite]。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南]。

若要深入了解 IoT 中樞的訊息路由，請參閱[使用 IoT 中樞傳送和接收訊息][lnk-devguide-messaging]。

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Azure 儲存體]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服務匯流排]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT 中樞開發人員指南]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[開始使用IoT 中樞入門]: iot-hub-java-java-getstarted.md
[Azure IoT 開發人員中心]: https://azure.microsoft.com/develop/iot
[暫時性錯誤處理]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[暫時性錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
