---
title: "了解 Azure IoT 中樞端點 | Microsoft Docs"
description: "開發人員指南 - IoT 中樞裝置對向端點和服務對向端點的相關參考資訊。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 93ada731fe70cf7d294537241f8104c0b89940ed
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="reference---iot-hub-endpoints"></a>參考 - IoT 中樞端點

## <a name="iot-hub-names"></a>IoT 中樞名稱

您可以在入口網站的 [概觀] 刀鋒視窗上，找到端點裝載所在的 IoT 中樞名稱。 根據預設，IoT 中樞的 DNS 名稱看起來像：`{your iot hub name}.azure-devices.net`。

您可以使用 Azure DNS 來建立 IoT 中樞的自訂 DNS 名稱。 如需詳細資訊，請參閱[使用 Azure DNS 為 Azure 服務提供自訂網域設定](../dns/dns-custom-domain.md#azure-iot)。

## <a name="list-of-built-in-iot-hub-endpoints"></a>內建 IoT 中樞端點清單

Azure IoT 中樞是一項多租用戶服務，可將其功能公開給各種動作項目。 下圖顯示 IoT 中樞公開的各種端點。

![IoT 中樞端點][img-endpoints]

下列清單說明這些端點：

* **資源提供者**。 IoT 中樞資源提供者會公開 [Azure Resource Manager][lnk-arm] 介面。 此介面可讓 Azure 訂用帳戶擁有者建立和刪除 IoT 中樞，以及更新 IoT 中樞屬性。 IoT 中樞屬性用來管理[中樞層級的安全性原則][lnk-accesscontrol] (相對於裝置層級的存取控制) 和雲端到裝置與裝置到雲端傳訊的功能選項。 IoT 中樞資源提供者也可讓您[匯出裝置身分識別][lnk-importexport]。
* **裝置身分識別管理**。 每個 IoT 中樞都會公開一組用來管理裝置身分識別的 HTTP REST 端點 (建立、擷取、更新和刪除)。 [裝置身分識別][lnk-device-identities]用於裝置驗證和存取控制。
* **裝置對應項管理**。 每個 IoT 中樞會公開一組服務面向的 HTTP REST 端點，以查詢和更新[裝置對應項][lnk-twins] (更新標籤和屬性)。
* **作業管理**。 每個 IoT 中樞會公開一組服務面向的 HTTP REST 端點，以查詢和管理[作業][lnk-jobs]。
* **裝置端點**。 針對身分識別登錄中的每個裝置，IoT 中樞會公開一組端點：

  * 傳送裝置到雲端的訊息。 裝置會使用這個端點來[傳送裝置到雲端的訊息][lnk-d2c]。
  * 接收雲端到裝置的訊息。 使用此端點來接收目標[雲端到裝置訊息][lnk-c2d]的裝置。
  * *起始檔案上傳*。 裝置會使用這個端點從 IoT 中樞接收 Azure 儲存體 SAS URI，以[上傳檔案][lnk-upload]。
  * *擷取和更新裝置對應項的屬性*。 裝置使用此端點來存取其[裝置對應項][lnk-twins]的屬性。
  * *接收直接方法要求*。 裝置使用此端點來接聽[直接方法][lnk-methods]的要求。

    公開這些端點時，是使用 [MQTT v3.1.1][lnk-mqtt]、HTTP 1.1 及 [AMQP 1.0][lnk-amqp] 通訊協定來公開。 您也可以透過連接埠 443 上的 [WebSockets][lnk-websockets] 取得 AMQP。

    裝置對應項端點和方法端點只能在使用 [MQTT v3.1.1][lnk-mqtt] 通訊協定時取得。

* **服務端點**。 每個 IoT 中樞都會公開一組端點，供您的解決方案後端用來與您的裝置通訊。 但有一個例外狀況，這些端點只會在使用 [AMQP][lnk-amqp] 通訊協定時公開。 方法引動過程端點會透過 HTTP 通訊協定來公開。
  
  * *接收裝置到雲端的訊息*。 此端點與 [Azure 事件中樞][lnk-event-hubs]相容。 後端服務可用它來讀取由您的裝置所傳送的[裝置到雲端訊息][lnk-d2c]。 除了這個內建端點外，您可以在 IoT 中樞上建立自訂端點。
  * *傳送雲端到裝置的訊息及接收傳遞通知*。 這些端點可讓您的解決方案後端傳送可靠的[雲端到裝置訊息][lnk-c2d]，以及接收相對應的傳遞或到期通知。
  * *接收檔案通知*。 此訊息的端點可讓您接收您的裝置已成功上傳檔案的通知。 
  * *直接方法叫用*。 此端點可讓後端服務在裝置上叫用[直接方法][lnk-methods]。
  * *接收作業監視事件*。 如果已設定 IoT 中樞要發出接收作業監視事件，此端點可讓您這些事件。 如需詳細資訊，請參閱 [IoT 中樞作業監視][lnk-operations-mon]。

[Azure IoT SDK][lnk-sdks] 一文說明您可用來存取這些端點的各種方式。

所有的 IoT 中樞端點都使用 [TLS][lnk-tls] 通訊協定，且絕不會在未加密/不安全的通道上公開任何端點。

## <a name="custom-endpoints"></a>自訂端點

您可以將訂用帳戶中的現有 Azure 服務連結至 IoT 中樞，以便做為訊息路由傳送的端點。 這些端點可做為服務端點，並當作訊息路由的接收器。 裝置無法直接寫入至其他端點。 若要深入了解訊息路由，請參閱[透過 IoT 中樞傳送和接收訊息][lnk-devguide-messaging]上的開發人員指南項目。

IoT 中樞目前支援下列 Azure 服務做為額外的端點︰

* 事件中樞
* 服務匯流排佇列
* 服務匯流排主題

IoT 中樞需要這些服務端點的寫入權限，才能將訊息路由傳送至工作。 如果您透過 Azure 入口網站設定您的端點，則會為您新增必要的權限。 請務必設定您的服務，以支援預期的輸送量。 當您第一次設定您的 IoT 解決方案時，您可能需要監視其他端點，然後對實際負載進行必要的調整。

如果訊息符合多個指向相同端點的路由，則 IoT 中樞只會將訊息傳遞至該端點一次。 因此，您不需要在您的服務匯流排佇列或主題上設定重複資料刪除。 在分割佇列中，分割區同質性可保證訊息排序。

> [!NOTE]
> 做為 IoT 中樞端點的服務匯流排佇列和主題不能啟用 [工作階段] 或 [重複偵測]。 如果已啟用其中一個選項，端點會在 Azure 入口網站中顯示為 [無法連線]。

如需您可以新增的端點數目限制，請參閱[配額和節流][lnk-devguide-quotas]。

## <a name="field-gateways"></a>現場閘道器

在 IoT 解決方案中， *現場閘道*位於裝置和 IoT 中樞端點之間。 它通常位於接近您的裝置的位置。 您的裝置會使用裝置所支援的通訊協定，直接與現場閘道器通訊。 現場閘道會使用 IoT 中樞所支援的通訊協定來連線到 IoT 中樞端點。 現場閘道可能是專用的硬體裝置或是執行自訂閘道軟體的低功率電腦。

您可以使用 [Azure IoT Edge][lnk-iot-edge] 來實作現場閘道。 IoT Edge 提供某些功能，例如對從多個裝置到相同 IoT 中樞連線的通訊進行多工處理。

## <a name="next-steps"></a>後續步驟

此 IoT 中樞開發人員指南中的其他參考主題包括︰

* [裝置對應項、作業和訊息路由的 IoT 中樞查詢語言][lnk-devguide-query]
* [配額和節流][lnk-devguide-quotas]
* [IoT 中樞的 MQTT 支援][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md

