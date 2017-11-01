---
title: "了解 Azure IoT 中樞 MQTT 支援 | Microsoft Docs"
description: "開發人員指南 - 支援裝置使用 MQTT 通訊協定連接至 IoT 中樞對向端點。 包含 Azure IoT 裝置 SDK 內建的 MQTT 支援的相關資訊。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 1d71c27c-b466-4a40-b95b-d6550cf85144
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1a3ce746601dc42f04f021f3ba142688abdb7e7
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2017
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>使用 MQTT 通訊協定來與 IoT 中樞通訊

「IoT 中樞」可讓裝置在連接埠 8883 使用 [MQTT v3.1.1][lnk-mqtt-org] 通訊協定，或在連接埠 443 使用「透過 WebSocket 的 MQTT v3.1.1」通訊協定，來與「IoT 中樞」裝置端點進行通訊。 「IoT 中樞」要求使用 TLS/SSL 保護所有裝置通訊的安全 (因此，「IoT 中樞」並不支援透過連接埠 1883 的非安全連線)。

## <a name="connecting-to-iot-hub"></a>連接到 IoT 中樞

裝置可以使用 MQTT 通訊協定連接到 IoT 中樞，方法是使用 [Azure IoT SDK][lnk-device-sdks] 中的程式庫或直接使用 MQTT 通訊協定。

## <a name="using-the-device-sdks"></a>使用裝置 SDK

支援 MQTT 通訊協定的[裝置 SDK][lnk-device-sdks] 有提供 Java、Node.js、C、C# 和 Python 等版本。 裝置 SDK 會使用標準的 IoT 中樞連接字串來連接到 IoT 中樞。 若要使用 MQTT 通訊協定，用戶端通訊協定參數必須設定為 **MQTT**。 根據預設，裝置 SDK 會連接到 **CleanSession** 旗標設為 **0** 的 IoT 中樞，並使用 **QoS 1** 來與 IoT 中樞交換訊息。

當裝置連接到 IoT 中樞時，裝置 SDK 會提供方法讓裝置在 IoT 中樞傳送和接收訊息。

下表包含每一種支援語言的程式碼範例連結，並指出要用來以 MQTT 通訊協定連接到 IoT 中樞的參數。

| 語言 | 通訊協定參數 |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>將裝置應用程式從 AMQP 移轉至 MQTT

如果您使用[裝置 SDK][lnk-device-sdks]，則需要在用戶端初始化中變更通訊協定參數 (如上所述)，才能從 AMQP 切換為使用 MQTT。

這麼做時，請務必檢查下列項目︰

* AMQP 在許多情況下會傳回錯誤，而 MQTT 會終止連線。 因此，可能需要稍微變更您的例外狀況處理邏輯。
* MQTT 在接收[雲端到裝置訊息][lnk-messaging]時不支援「拒絕」作業。 如果您的後端應用程式需要接收來自裝置應用程式的回應，請考慮使用[直接方法][lnk-methods]。

## <a name="using-the-mqtt-protocol-directly"></a>直接使用 MQTT 通訊協定
如果裝置無法使用裝置 SDK，它仍可使用連接埠 8883 上的 MQTT 通訊協定連線到公用裝置端點。 在 **CONNECT** 封包中，裝置應使用下列值：

* 在 [ClientId] 欄位中，使用 **deviceId**。
* 在 [Username] 欄位中，使用 `{iothubhostname}/{device_id}/api-version=2016-11-14`，其中 {iothubhostname} 是 IoT 中樞的完整 CName。

    例如，如果您的 IoT 中樞名稱是 **contoso.azure-devices.net**，而且如果您的裝置名稱是 **MyDevice01**，則完整的 **Username** 欄位應包含 `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`。
* 在 [Password] 欄位中，使用 SAS 權杖。 SAS 權杖的格式與 HTTPS 和 AMQP 通訊協定的格式相同：<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`。

    如需如何產生 SAS 權杖的詳細資訊，請參閱[使用 IoT 中樞安全性權杖][lnk-sas-tokens]的裝置一節。

    測試時，您也可以使用[裝置總管][lnk-device-explorer]工具來快速產生 SAS 權杖，方便您複製並貼到您的程式碼中︰

  1. 移至 [裝置總管] 中的 [管理] 索引標籤。
  2. 按一下 [SAS 權杖]  \(右上角)。
  3. 在 [SASTokenForm] 的 [DeviceID] 下拉式清單中，選取您的裝置。 設定您的 **TTL**。
  4. 按一下 [產生]  來建立您的權杖。

     產生的 SAS 權杖具有此結構：`HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`。

     使用 MQTT 連線時，此權杖中作為 [Password] 欄位的部分是︰`SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`。

針對 MQTT 的連接和中斷連接封包，「IoT 中樞」會在「作業監視」通道發出事件，其中包含可協助您對連線問題進行疑難排解的額外資訊。

### <a name="tlsssl-configuration"></a>TLS/SSL 組態

若要直接使用 MQTT 通訊協定，您的用戶端「必須」透過 TLS/SSL 進行連線。 嘗試略過此步驟將會因連線錯誤而發生失敗。

為了建立 TLS 連線，您可能必須下載並參考「DigiCert Baltimore 根憑證」。 這是 Azure 用來保護連線安全的憑證，您可以在 [Azure-iot-sdk-c 儲存機制][lnk-sdk-c-certs]中找到此憑證。 如需有關這些憑證的詳細資訊，請瀏覽 [DigiCert 網站][lnk-digicert-root-certs]。

以下提供一個如何使用 Eclipse Foundation 所提供的 Python 版 [Paho MQTT 程式庫][lnk-paho] 來進行實作的範例。

首先，從您的命令列環境安裝 Paho 程式庫：

```
>pip install paho-mqtt
```

接著，以 Python 指令碼實作用戶端：

```
from paho.mqtt import client as mqtt
import ssl
  
path_to_root_cer = "...local\\path\\to\\digicert.cer"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
subdomain = "<iothub subdomain>"

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.username_pw_set(username=subdomain+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(subdomain+".azure-devices.net", port=8883)
```


### <a name="sending-device-to-cloud-messages"></a>傳送裝置到雲端訊息

成功連線之後，裝置可以使用 `devices/{device_id}/messages/events/` 或 `devices/{device_id}/messages/events/{property_bag}` 作為**主題名稱**，將訊息傳送至 IoT 中樞。 `{property_bag}` 項目可讓裝置以 URL 編碼格式傳送具有其他屬性的訊息。 例如：

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> 這個 `{property_bag}` 元素與 HTTPS 通訊協定中的查詢字串使用相同的編碼。
>
>

裝置應用程式也可以使用 `devices/{device_id}/messages/events/{property_bag}` 作為 **Will 主題名稱**，以定義要當作遙測訊息轉送的「Will 訊息」。

- 「IoT 中樞」不支援 QoS 2 訊息。 如果裝置應用程式發佈 **QoS 2** 的訊息，IoT 中樞會關閉網路連接。
- 「IoT 中樞」不會保存「保留」訊息。 如果裝置傳送 **RETAIN** 旗標設定為 1 的訊息，「IoT 中樞」會在訊息中加入 **x-opt-retain** 應用程式屬性。 在此情況下，「IoT 中樞」不會保存保留訊息，而是會傳遞給後端應用程式。
- IoT 中樞僅支援每個裝置有一個作用中 MQTT 連接。 代表相同裝置識別碼的任何新的 MQTT 連接都會導致 IoT 中樞卸除現有的連接。

如需詳細資訊，請參閱[傳訊開發人員指南][lnk-messaging]。

### <a name="receiving-cloud-to-device-messages"></a>接收雲端到裝置訊息

若要從 IoT 中樞接收訊息，裝置應該使用 `devices/{device_id}/messages/devicebound/#` 做為**主題篩選**來進行訂閱。 「主題篩選」中的多層級萬用字元 **#** 僅供用來允許裝置接收主題名稱中的額外屬性。 「IoT 中樞」並不允許使用 **#** 或 **?** 萬用字元來篩選副主題。 由於「IoT 中樞」不是一般用途的發行/訂閱傳訊訊息代理程式，因此它只支援已記載的主題名稱和主題篩選。

裝置在成功訂閱 IoT 中樞的裝置特定端點 (由 `devices/{device_id}/messages/devicebound/#` 主題篩選代表) 之後，才會收到來自 IoT 中樞的訊息。 建立成功的訂閱之後，裝置將會開始接收訊息，但僅限在訂閱之後傳送給它的雲端到裝置訊息。 如果裝置是在 **CleanSession** 旗標設定為 **0** 的情況下連線，訂閱將會跨不同的工作階段持續保留。 在此情況下，下次裝置以 **CleanSession 0** 進行連線時，就會收到在其中斷連線時傳送給它的未送訊息。 如果裝置使用設定為 **1** 的 **CleanSession** 旗標，則必須等到訂閱 IoT 中樞的裝置端點之後，才會收到來自 IoT 中樞的訊息。

IoT 中樞會附上**主題名稱** `devices/{device_id}/messages/devicebound/` 或 `devices/{device_id}/messages/devicebound/{property_bag}` (如果有任何訊息屬性) 來傳遞訊息。 `{property_bag}` 包含訊息屬性的 url 編碼索引鍵/值組。 屬性包中只會包含應用程式屬性和使用者可設定的系統屬性 (例如 **messageId** 或 **correlationId**)。 系統屬性名稱具有前置詞 **$**，但應用程式屬性則會使用沒有前置詞的原始屬性名稱。

當裝置應用程式訂閱具有 **QoS 2** 的主題時，IoT 中樞會在 **SUBACK** 封包中授與最大 QoS 層級 1。 之後，IoT 中樞會使用 QoS 1 將訊息傳遞給裝置。

### <a name="retrieving-a-device-twins-properties"></a>擷取裝置對應項屬性

首先，裝置會訂閱 `$iothub/twin/res/#`，以接收作業的回應。 然後，它會傳送空白訊息給主題 `$iothub/twin/GET/?$rid={request id}`，其中已填入**要求識別碼**的值。服務接著會使用和要求相同的**要求識別碼**，傳送內含關於 `$iothub/twin/res/{status}/?$rid={request id}` 主題之裝置對應項資料的回應訊息。

根據 [IoT 中樞傳訊開發人員指南][lnk-messaging]，要求識別碼可以是任何有效的訊息屬性值，而狀態會驗證為整數。
回應本文包含裝置對應項的屬性區段：

身分識別登錄項目的本文僅限於 “properties” 成員，例如：

        {
            "properties": {
                "desired": {
                    "telemetrySendFrequency": "5m",
                    "$version": 12
                },
                "reported": {
                    "telemetrySendFrequency": "5m",
                    "batteryLevel": 55,
                    "$version": 123
                }
            }
        }

可能的狀態碼如下︰

|狀態 | 說明 |
| ----- | ----------- |
| 200 | 成功 |
| 429 | 要求過多 (已節流)，根據 [IoT 中樞節流][lnk-quotas] |
| 5** | 伺服器錯誤 |

如需詳細資訊，請參閱[裝置對應項開發人員指南][lnk-devguide-twin]。

### <a name="update-device-twins-reported-properties"></a>更新裝置對應項的報告屬性

下列順序說明在 IoT 中樞的裝置對應項中，裝置如何更新報告的屬性︰

1. 裝置必須訂閱 `$iothub/twin/res/#` 主題，才能從 IoT 中樞接收作業的回應。

1. 裝置會將包含裝置對應項新的訊息傳送至 `$iothub/twin/PATCH/properties/reported/?$rid={request id}` 主題。 此訊息包含 **request id** 值。

1. 服務接著會傳送回應訊息，其中包含`$iothub/twin/res/{status}/?$rid={request id}` 主題上報告之屬性集合的新 ETag 值。 這個回應訊息使用和要求相同的 **request id**。

要求訊息本文包含一份 JSON 文件，可提供所報告屬性的新值 (其他屬性或中繼資料則不可修改)。
JSON 文件中的每個成員會在裝置對應項的文件中更新或新增對應的成員。 設定為 `null` 的成員會從包含的物件中刪除成員。 例如：

        {
            "telemetrySendFrequency": "35m",
            "batteryLevel": 60
        }

可能的狀態碼如下︰

|狀態 | 說明 |
| ----- | ----------- |
| 200 | 成功 |
| 400 | 不正確的要求。 JSON 格式錯誤 |
| 429 | 要求過多 (已節流)，根據 [IoT 中樞節流][lnk-quotas] |
| 5** | 伺服器錯誤 |

如需詳細資訊，請參閱[裝置對應項開發人員指南][lnk-devguide-twin]。

### <a name="receiving-desired-properties-update-notifications"></a>接收所需屬性更新通知

當連接裝置時，IoT 中樞傳送通知給主題 `$iothub/twin/PATCH/properties/desired/?$version={new version}`，其中包含解決方案後端所執行的更新內容。 例如：

        {
            "telemetrySendFrequency": "5m",
            "route": null
        }

和屬性更新一樣，`null` 值表示將要刪除的 JSON 物件成員。


> [!IMPORTANT] 
> IoT 中樞只會在連接裝置時產生變更通知。 請務必實作[裝置重新連線流程][lnk-devguide-twin-reconnection]，以便讓 IoT 中樞與裝置應用程式兩者所需的屬性保持同步。

如需詳細資訊，請參閱[裝置對應項開發人員指南][lnk-devguide-twin]。

### <a name="respond-to-a-direct-method"></a>回應直接方法

首先，裝置必須訂閱 `$iothub/methods/POST/#`。 IoT 中樞會將方法要求傳送至主題 `$iothub/methods/POST/{method name}/?$rid={request id}`，其中含有有效的 JSON 或空白本文。

為了回應，裝置會將具有有效 JSON 或空白本文的訊息傳送至主題 `$iothub/methods/res/{status}/?$rid={request id}`，其中**要求識別碼**必須和要求訊息中的相符，而**狀態**則必須是整數。

如需詳細資訊，請參閱[直接方法開發人員指南][lnk-methods]。

### <a name="additional-considerations"></a>其他考量

最後，如果您需要自訂雲端的 MQTT 通訊協定行為，則應該檢閱 [Azure IoT 通訊協定閘道][lnk-azure-protocol-gateway]，這可讓您部署能與「IoT 中樞」直接互動的高效能自訂通訊協定閘道。 Azure IoT 通訊協定閘道器可讓您自訂裝置通訊協定，以順應要重建的 MQTT 部署或其他自訂通訊協定。 不過，這種方法會要求您執行及操作自訂通訊協定閘道。

## <a name="next-steps"></a>後續步驟

若要深入了解 MQTT 通訊協定，請參閱 [MQTT 文件][lnk-mqtt-docs]。

若要深入了解如何規劃 IoT 中樞部署，請參閱：

* [Azure IoT 認證裝置目錄][lnk-devices]
* [支援其他通訊協定][lnk-protocols]
* [與事件中樞比較][lnk-compare]
* [縮放、HA 及 DR][lnk-scaling]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南][lnk-devguide]
* [使用 Azure IoT Edge 來模擬裝置][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt
