---
title: "了解 Azure IoT 中樞的裝置到雲端傳訊 | Microsoft Docs"
description: "開發人員指南 - 如何使用 IoT 中樞的裝置到雲端傳訊。 包含傳送遙測和非遙測資料，以及使用路由來傳遞訊息的相關資訊。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: dobett
ms.openlocfilehash: 4e346306ecb8f4897a249454c537ce9a1a4c4011
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>將裝置到雲端訊息傳送至 IoT 中樞

若要從您的裝置傳送時間序列遙測和警示到您的解決方案後端，請從您的裝置傳送裝置到雲端訊息到您的 IoT 中樞。 如需 IoT 中樞所支援之其他裝置到雲端選項的討論，請參閱[裝置對雲端通訊指引][lnk-d2c-guidance]。

您透過裝置面向端點傳送裝置到雲端訊息 (**/devices/{deviceId}/messages/events**)。 路由規則接著會將訊息路由至您 IoT 中樞上的其中一個服務面向端點。 路由規則會使用流經您中樞的裝置到雲端訊息標頭和內文，決定訊息的路由目標。 根據預設，訊息會路由至內建的服務面向端點 (**messages/events**)，此端點與[事件中樞][lnk-event-hubs]相容。 因此，您可以使用標準[事件中樞整合和 SDK][lnk-compatible-endpoint] 在解決方案後端接收裝置到雲端訊息。

IoT 中樞使用串流訊息模式實作裝置到雲端傳訊。 IoT 中樞的裝置到雲端訊息與[事件中樞][lnk-event-hubs]*事件*較為相似 (而不像[服務匯流排][lnk-servicebus]*訊息*) 因為會有大量的事件傳遞給該服務，並可由多個讀取器讀取。

IoT 中樞的裝置對雲端傳訊具有下列特性：

* 裝置到雲端訊息會長期保留在 IoT 中樞的預設 **messages/events** 端點，最多達七天。
* 裝置到雲端訊息最大可能為 256 KB，而且可分成數個批次以最佳化傳送。 批次最多可達 256 KB。
* 如[控制 IoT 中樞的存取權][lnk-devguide-security]一節所述，IoT 中樞會啟用每一裝置的驗證和存取控制。
* IoT 中樞可讓您建立最多 10 個自訂端點。 訊息會根據您 IoT 中樞上所設定的路由傳遞至端點。 如需詳細資訊，請參閱[路由規則](#routing-rules)。
* IoT 中樞可同時連線到數百萬部裝置 (請參閱[配額和節流][lnk-quotas])。
* IoT 中樞不允許任意進行資料分割。 裝置到雲端訊息會根據其原始的 **deviceId**進行分割。

如需 IoT 中樞與事件中樞服務之間差異的詳細資訊，請參閱 [Azure IoT 中樞和 Azure 事件中樞的比較][lnk-comparison]。

## <a name="send-non-telemetry-traffic"></a>傳送非遙測流量

通常除了遙測資料點之外，裝置也會傳送需要在解決方案後端進行個別執行與處理的訊息及要求。 例如，必須於後端觸發特定動作的重大警示。 您可以輕鬆地撰寫[路由規則][lnk-devguide-custom]，以根據訊息的標頭或訊息內文中的值，將這些訊息類型傳送至專屬的處理端點。

如需處理這種訊息最佳方式的詳細資訊，請參閱[教學課程：如何處理 IoT 中樞裝置到雲端訊息][lnk-d2c-tutorial]教學課程。

## <a name="route-device-to-cloud-messages"></a>路由傳送裝置到雲端訊息

您有兩個選項可將裝置對雲端訊息路由傳送到後端應用程式：

* 使用內建[事件中樞相容端點][lnk-compatible-endpoint]，讓後端應用程式讀取中樞所接收的裝置到雲端訊息。 若要深入了解內建事件中樞相容端點，請參閱[從內建端點讀取裝置對雲端訊息][lnk-devguide-builtin]。
* 使用路由規則，將訊息傳送至您 IoT 中樞內的自訂端點。 自訂端點可讓您的後端應用程式使用事件中樞、服務匯流排佇列或服務匯流排主題，來讀取裝置對雲端訊息。 若要深入了解路由和自訂端點，請參閱[使用適用於裝置對雲端訊息的自訂端點和路由規則][lnk-devguide-custom]。

## <a name="anti-spoofing-properties"></a>防詐騙屬性

為了避免裝置到雲端的訊息中出現裝置詐騙，IoT 中樞使用下列屬性在所有訊息上加上戳記：

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

前兩個屬性包含來源裝置的 **deviceId** 和 **generationId** (依據[裝置身分識別屬性][lnk-device-properties])。

**ConnectionAuthMethod** 屬性包含具有下列屬性的 JSON 序列化物件︰

```json
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>後續步驟

如需您可以用來傳送裝置對雲端訊息之 SDK 的資訊，請參閱 [Azure IoT SDK][lnk-sdks]。

[快速入門][lnk-get-started]教學課程示範如何從模擬和實體裝置傳送裝置對雲端訊息。 如需詳細資訊，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息][lnk-d2c-tutorial]教學課程。

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
