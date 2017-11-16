---
title: "開始使用 Azure IoT 中樞 (Python) | Microsoft Docs"
description: "了解如何使用適用於 Python 的 IoT SDK 將裝置到雲端訊息傳送至 Azure IoT 中樞。 建立模擬裝置和服務應用程式來註冊您的裝置、傳送訊息，並從 IoT 中樞讀取訊息。"
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: dkshir
ms.custom: na
ms.openlocfilehash: eec9eb9d5b5ecc0b33ce03f33b9f1600600f6c4e
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>使用 Python 將您的模擬裝置連線至 IoT 中樞
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

在本教學課程結尾，您將會有兩個 Python 應用程式：

* **CreateDeviceIdentity.py**，這會建立裝置身分識別與相關聯的安全性金鑰，來連線到您的模擬裝置應用程式。
* **SimulatedDevice.py**，這會使用先前建立的裝置識別連線至您的 IoT 中樞，並使用 MQTT 通訊協定定期傳送遙測訊息。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供 Azure IoT SDK (可讓您同時建置在裝置與您的解決方案後端執行的兩個應用程式) 的相關資訊。
> 
> 

若要完成此教學課程，您需要下列項目：

* [Python 2.x 或 3.x][lnk-python-download]。 請務必使用安裝程式所需的 32 位元或 64 位元安裝。 在安裝期間出現系統提示時，務必將 Python 新增至平台特有的環境變數。 如果您使用 Python 2.x，則可能需要[安裝或升級 *pip*(Python 套件管理系統][lnk-install-pip])。
* 如果您使用 Windows 作業系統，則 [Visual c + + 可轉散發套件][lnk-visual-c-redist] 允許使用 Python 的原生 DLL。
* [Node.js 4.0 或更新版本][lnk-node-download]。 請務必使用安裝程式所需的 32 位元或 64 位元安裝。 這是安裝 [IoT 中樞總管工具][lnk-iot-hub-explorer]所需的項目。
* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。

> [!NOTE]
> `azure-iothub-service-client` 和 `azure-iothub-device-client` 的 pip 套件目前僅適用於 Windows OS。 若為 Linux/Mac OS，請參閱[針對 Python 準備您的開發環境][lnk-python-devbox]文章上的 Linux 和 Mac OS 特定章節。
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

您現在已經建立 IoT 中樞。 在本教學課程的其他部分使用 IoT 中樞主機名稱和 IoT 中樞連接字串。

> [!NOTE]
> 您也可以使用以 Python 或 Node.js 為基礎的 Azure CLI，在命令列上輕鬆地建立 IoT 中樞。 [使用 Azure CLI 2.0 建立 IoT 中樞][lnk-azure-cli-hub]一文說明如何執行這項操作的快速步驟。 
> 

## <a name="create-a-device-identity"></a>建立裝置識別
本節會列出建立 Python 主控台應用程式的步驟，它可在 IoT 中樞的身分識別登錄中建立裝置識別。 如果裝置在身分識別登錄中具有項目，則只能連線到 IoT 中樞。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]的＜身分識別登錄＞一節。 執行這個主控台應用程式時，它會產生唯一的裝置識別碼及金鑰，當裝置向 IoT 中樞傳送裝置對雲端訊息時，可以用來識別裝置本身。

1. 開啟命令提示字元並安裝 **Azure IoT Hub Service SDK for Python**。 安裝 SDK 之後，請關閉命令提示字元。

    ```
    pip install azure-iothub-service-client
    ```

2. 建立名為 **CreateDeviceIdentity.py** 的 Python 檔案。 在[您所選的 Python 編輯器/IDE][lnk-python-ide-list] (例如預設 [IDLE][lnk-idle]) 中開啟該檔案。

3. 新增下列程式碼，從服務 SDK 匯入必要的模組：

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. 新增下列程式碼，並將 `[IoTHub Connection String]` 的預留位置替換為您在上一節中為 IoT 中樞建立的連接字串。 您可以使用任何名稱作為 `DEVICE_ID`。
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

3. 新增下列函式，以列印部分的裝置資訊。

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. 新增下列函式，以使用登錄管理員建立裝置識別碼。 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. 最後，新增如下所示的 main 函式，然後儲存檔案。

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. 在命令提示字元上執行 **CreateDeviceIdentity.py**，如下所示︰

    ```python
    python CreateDeviceIdentity.py
    ```
6. 您應該會看到已建立模擬裝置。 記下此裝置的 **deviceId** 和 **primaryKey**。 稍後在建立連線至作為裝置之 IoT 中樞的應用程式時，您會需要這些值。

    ![成功建立裝置][1]

> [!NOTE]
> IoT 中樞身分識別登錄只會儲存裝置身分識別，以啟用對 IoT 中樞的安全存取。 它會儲存裝置識別碼和金鑰來作為安全性認證，以及啟用或停用旗標，讓您用來停用個別裝置的存取。 如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]。
> 
> 


## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式
本節會列出建立 Python 主控台應用程式的步驟，它可模擬裝置並將裝置對雲端訊息傳送至 IoT 中樞。

1. 開啟新的命令提示字元並安裝 Azure IoT Hub Device SDK for Python，如下所示。 安裝之後，請關閉命令提示字元。

    ```
    pip install azure-iothub-device-client
    ```
2. 建立名為 **SimulatedDevice.py** 的檔案。 在您所選的 Python 編輯器/IDE (例如 IDLE) 中開啟此檔案。

3. 新增下列程式碼，從裝置 SDK 匯入必要的模組。

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. 新增下列程式碼，並將 `[IoTHub Device Connection String]` 的預留位置替換為您裝置的連接字串。 裝置連接字串的格式通常為 `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>`。 使用您在前一節中建立之裝置的 **deviceId** 和 **primaryKey**，分別取代 `<deviceId>` 和 `<primaryKey>`。 使用 IoT 中樞的主機名稱 (通常為 `<IoT hub name>.azure-devices.net`) 取代 `<hostName>`。

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. 新增下列程式碼來定義和傳送確認回呼。 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. 新增下列程式碼來初始化裝置用戶端。

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        client.set_option("product_info", "HappyPath_Simulated-Python")
        return client
    ```
7. 新增下列函式來格式化並將訊息從模擬裝置傳送到 IoT 中樞。

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. 最後，新增 main 函式。 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. 儲存並關閉 **SimulatedDevice.py** 檔案。 您現在可以開始執行此應用程式。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理][lnk-transient-faults]所建議，實作重試原則 (例如指數型輪詢)。
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>從模擬裝置接收訊息
若要從裝置接收遙測訊息，您必須使用 IoT 中樞所公開的[事件中樞][lnk-event-hubs-overview]相容端點，它會讀取裝置到雲端訊息。 如需有關如何針對 IoT 中樞的事件中樞相容端點處理事件中樞訊息的資訊，請閱讀[開始使用事件中樞][lnk-eventhubs-tutorial]教學課程。 事件中樞尚未支援 Python 中的遙測，因此您可以建立 [Node.js](iot-hub-node-node-getstarted.md#D2C_node) 或 [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) 以事件中樞為基礎的主控台應用程式，以讀取來自 IoT 中樞的裝置到雲端訊息。 本教學課程示範如何使用 [IoT 中樞總管工具][lnk-iot-hub-explorer]來讀取這些裝置訊息。

1. 開啟命令提示字元並安裝 IoT 中樞總管。 

    ```
    npm install -g iothub-explorer
    ```

2. 在命令提示字元執行下列命令，開始監視來自裝置的裝置到雲端資訊。 在 `--login` 之後的預留位置中使用 IoT 中樞的連接字串。

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. 開啟新的命令提示字元並瀏覽至含有 **SimulatedDevice.py** 檔案的目錄。

4. 執行 **SimulatedDevice.py** 檔案，定期將遙測資料傳送至您的 IoT 中樞。 
   
    ```
    python SimulatedDevice.py
    ```
5. 在從上一節執行 IoT 中樞總管的命令提示字元上觀察裝置訊息。 

    ![Python 裝置到雲端訊息][2]

## <a name="next-steps"></a>後續步驟
在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您會將此裝置身分識別用於啟用模擬裝置應用程式，以將裝置到雲端訊息傳送至 IoT 中樞。 在 IoT 中樞總管工具的協助下，您觀察到 IoT 中樞所接收的訊息。 

若要深入探索 Python SDK for Azure IoT Hub 使用量，請造訪[此 Git 中樞存放庫][lnk-python-github]。 若要檢閱 Azure IoT Hub Service SDK for Python 的傳訊功能，您可以下載並執行 [iothub_messaging_sample.py][lnk-messaging-sample]。 若要使用 Azure IoT Hub Device SDK for Python 進行裝置端模擬，您可以下載並執行 [iothub_client_sample.py][lnk-client-sample]。

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [連接您的裝置][lnk-connect-device]
* [開始使用裝置管理][lnk-device-management]
* [開始使用 Azure IoT Edge][lnk-iot-edge]

若要了解如何擴充您的 IoT 解決方案及大規模處理裝置到雲端訊息，請參閱[處理裝置到雲端訊息][lnk-process-d2c-tutorial]教學課程。
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
