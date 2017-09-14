---
title: "Azure IoT 中樞通訊協定和連接埠 | Microsoft Docs"
description: "開發人員指南 - 說明裝置到雲端和雲端到裝置通訊所支援的通訊協定，以及必須開啟的連接埠號碼。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: 2c30d20c4b36148319a068eba2e22382c75cdfa8
ms.contentlocale: zh-tw
ms.lasthandoff: 08/31/2017

---
# 參考 - 選擇通訊協定

IoT 中樞可讓裝置提供下列通訊協定給我們，以供進行裝置端通訊：

* [MQTT][lnk-mqtt]
* 透過 WebSocket 的 MQTT
* [AMQP][lnk-amqp]
* 透過 WebSocket 的 AMQP
* HTTP

如需這些通訊協定如何支援特定 IoT 中樞功能的資訊，請參閱[裝置對雲端通訊指引][lnk-d2c-guidance]和[雲端對裝置通訊指引][lnk-c2d-guidance]。

下表針對您選擇的通訊協定提供概略的建議︰

| 通訊協定 | 應選擇此通訊協定的情況 |
| --- | --- |
| MQTT <br> 透過 WebSocket 的 MQTT |可在不需要透過相同 TLS 連線來連線到多個裝置 (各有自己的每一裝置認證) 的裝置上使用。 |
| AMQP <br> 透過 WebSocket 的 AMQP |在欄位和雲端閘道上使用，以利用跨裝置進行多工的連線。 |
| HTTP |針對無法支援其他通訊協定的裝置使用。 |

當您選擇裝置端通訊的通訊協定時，考慮下列幾點：

* **雲端到裝置的模式**。 HTTP 沒有有效的方式可實作伺服器發送。 因此在使用 HTTP 時，裝置會向 IoT 中樞輪詢雲端到裝置的訊息。 這對於裝置和 IoT 中樞而言都沒有效率。 在目前的 HTTP 指導方針中，每個裝置應每隔 25 分鐘或更久進行一次訊息輪詢。 MQTT 和 AMQP 則支援在收到雲端到裝置訊息時進行伺服器推送。 它們能夠將訊息立即從 IoT 中樞推送到裝置。 如果傳遞延遲是一大考量，最好是使用 MQTT 或 AMQP 通訊協定。 如果是很少連接的裝置，也適用於 HTTP。
* **現場閘道器**。 使用 MQTT 和 HTTP 時，您無法使用相同的 TLS 連線來連線到多個裝置 (各有自己的每一裝置認證)。 對於[現場閘道案例][lnk-azure-gateway-guidance]，這些並不是最理想的通訊協定，因為對於每個連線的裝置，這些案例都需要一個與現場閘道和 IoT 中樞連線的 TLS 連線。
* **低資源裝置**。 MQTT 和 HTTP 程式庫的使用量比 AMQP 程式庫小。 因此，如果裝置的資源有限 (例如 RAM 小於 1 MB)，這些通訊協定可能是唯一可用的通訊協定實作。
* **網路周遊**。 標準的 AMQP 通訊協定會使用連接埠 5671，MQTT 則會接聽連接埠 8883。 使用這些連接埠可能會導致對非 HTTP 通訊協定關閉的網路發生問題。 在此案例中，請使用 MQTT over WebSockets、AMQP over WebSockets 或 HTTP。
* **承載大小**。 MQTT 和 AMQP 是二進位通訊協定，這使得其承載比 HTTP 更精簡。

> [!WARNING]
> 使用 HTTP 時，每個裝置應該每隔 25 分鐘或更久輪詢一次雲端到裝置訊息。 不過，在開發期間，可以接受比每隔 25 分鐘更頻繁的輪詢頻率。

## 連接埠號碼

裝置可以在 Azure 中使用各種通訊協定來與 IoT 中樞通訊。 一般而言，選擇的通訊協定是根據方案的特定需求而定。 下表列出必須要為裝置開啟的輸出連接埠，以使用特定的通訊協定：

| 通訊協定 | 連接埠 |
| --- | --- |
| MQTT |8883 |
| 透過 WebSocket 的 MQTT |443 |
| AMQP |5671 |
| 透過 WebSocket 的 AMQP |443 |
| HTTP |443 |

當您在 Azure 區域中建立 IoT 中樞後，IoT 中樞將在該 IoT 中樞的存留期間保留相同的 IP 位址。 不過，如果 Microsoft 將 IoT 中樞移至不同的縮放單位以維護服務品質，則它會獲派新的 IP 位址。


## 後續步驟

若要深入了解 IoT 中樞如何實作 MQTT 通訊協定，請參閱[使用 MQTT 通訊協定來與 IoT 中樞通訊][lnk-mqtt-support]。

[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways

