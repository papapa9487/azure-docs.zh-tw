---
title: "比較 Azure IoT 中樞與 Azure 事件中樞 | Microsoft Docs"
description: "IoT 中樞和事件中樞 Azure 服務的比較，重點在於功能差異和使用案例。 比較的項目包括支援的通訊協定、裝置管理、監視及檔案上傳。"
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 12389b4be03b714d99edcf3fd621bd2d9c586540
ms.contentlocale: zh-tw
ms.lasthandoff: 08/10/2017

---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Azure IoT 中樞和 Azure 事件中樞的比較
IoT 中樞的主要使用案例之一是從裝置收集遙測。 因此，經常會比較 IoT 中樞與 [Azure 事件中樞][Azure Event Hubs]。 類似於 IoT 中樞，事件中樞是事件處理服務，它能提供大規模進入雲端的事件和遙測入口，並具備低延遲和高可靠性等特性。

不過，這些服務有許多差異，將在下表中詳述：

| 領域 | IoT 中樞 | 事件中樞 |
| --- | --- | --- |
| 通訊模式 | 能夠進行[裝置到雲端通訊][lnk-d2c-guidance] (傳訊、檔案上傳和報告屬性) 和[雲端到裝置通訊][lnk-c2d-guidance] (直接方法、所需屬性、傳訊)。 |僅提供事件輸入 (通常視為裝置到雲端案例)。 |
| 裝置狀態資訊 | [裝置對應項][lnk-twins]可以儲存和查詢裝置狀態資訊。 | 無法儲存裝置狀態資訊。 |
| 裝置通訊協定支援 |支援 MQTT、MQTT over WebSockets、AMQP、AMQP over WebSocket 及 HTTP。 此外，IoT 中樞還可搭配 [Azure IoT 通訊協定閘道][lnk-azure-protocol-gateway]來使用，後者是支援自訂通訊協定的可自訂通訊協定閘道實作。 |支援 AMQP、透過 WebSockets 的 AMQP 和 HTTP。 |
| 安全性 |提供每個裝置的身分識別與可撤銷的存取控制。 請參閱 [IoT 中樞開發人員指南的安全性章節]。 |提供全事件中樞的[共用存取原則][Event Hubs - security]，以及透過[發行者原則][Event Hubs publisher policies]的有限撤銷支援。 IoT 解決方案通常需要實作自訂解決方案來支援每個裝置的認證以及防詐騙措施。 |
| 作業監視 |可讓 IoT 解決方案訂閱一組豐富的裝置身分識別管理和連線事件，例如個別裝置驗證錯誤、節流和格式錯誤的例外狀況。 這些事件可讓您快速識別個別裝置層級的連線問題。 |僅公開彙總的度量。 |
| 調整 |已最佳化以支援數百萬同時連接的裝置。 |依據每個 [Azure 事件中樞配額][Azure Event Hubs quotas]計量連線數目。 另一方面，事件中樞可讓您指定每個傳送訊息的分割。 |
| 裝置 SDK |除了直接 MQTT、AMQP 和 HTTP API 以外，提供[裝置 SDK][Azure IoT SDKs] 給各種平台和語言。 |除了 AMQP 和 HTTP 傳送介面以外，在 .NET、Java 和 C 上都提供支援。 |
| 檔案上傳 |可讓 IoT 解決方案將檔案從裝置上傳到雲端。 包含一個用於整合工作流程的檔案通知端點，以及一個用於支援偵錯的作業監視類別。 | 不支援。 |
| 將訊息路由至多個端點 | 最多支援 10 個自訂端點。 規則會決定訊息路由至自訂端點的方式。 如需詳細資訊，請參閱[使用 IoT 中樞傳送及接收訊息][lnk-devguide-messaging]。 | 需要撰寫及裝載額外的程式碼才能發送訊息。 |

總而言之，即使唯一的使用案例是裝置到雲端遙測輸入，IoT 中樞仍會提供為 IoT 裝置連線所設計的服務。 它會持續擴充具備 IoT 功能之案例的價值主張。 事件中樞的設計對象是資料中心之間及資料中心內部案例的大規模事件輸入。

在相同解決方案中同時使用 IoT 中樞和事件中樞的情況並不常見。 IoT 中樞是處理裝置到雲端通訊，而事件中樞處理後期事件輸入至即時處理引擎。

### <a name="next-steps"></a>後續步驟
若要深入了解如何規劃 IoT 中樞部署，請參閱[調整、HA 及 DR][lnk-scaling]。

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南][lnk-devguide]
* [使用 IoT Edge 來模擬裝置][lnk-iotedge]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[IoT 中樞開發人員指南的安全性章節]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-features.md#event-publishers
[Azure Event Hubs quotas]: ../event-hubs/event-hubs-quotas.md
[Azure IoT SDKs]: https://github.com/Azure/azure-iot-sdks
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md

