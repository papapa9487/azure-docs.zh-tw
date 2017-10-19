---
title: "Azure IoT 中樞概觀 | Microsoft Docs"
description: "Azure IoT 中樞服務概觀：什麼是 IoT 中樞、裝置連線性、物聯網通訊模式、閘道器和服務輔助通訊模式"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5f44d2ae42ffc6f75887a64c9ef988fe6d8fd69
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-the-azure-iot-hub-service"></a>Azure IoT 中樞服務的概觀

歡迎使用 Azure IoT 中樞。 本文提供 Azure IoT 中樞的概觀，並描述在實作物聯網 (IoT) 解決方案時，您應該使用此服務的原因。 Azure IoT 中樞是一項完全受管理的服務，可在數百萬個 IoT 裝置和一個解決方案後端之間啟用可靠且安全的雙向通訊。 Azure IoT 中樞：

* 提供多個裝置到雲端和雲端到裝置的通訊選項。 這些選項包括單向通訊、檔案傳輸和要求回覆方法。
* 向其他 Azure 服務提供內建宣告式訊息路由。
* 針對裝置中繼資料與同步化狀態資訊提供可查詢存放區。
* 使用每一裝置的安全性金鑰或 X.509 憑證啟用安全通訊與存取控制。
* 可廣泛監視裝置的連線情況和裝置的身分識別管理事件。
* 包括適用於最受歡迎的語言和平台的裝置程式庫。

[IoT 中樞和事件中樞的比較][lnk-compare]一文描述這兩個服務之間的主要差異，並強調說明在 IoT 解決方案中使用 IoT 中樞的優點。

如需有關 Azure 與 IoT 中樞如何協助保護您 IoT 解決方案安全的資訊，請參閱[徹底保護物聯網安全性][lnk-security-ground-up]。

![Azure IoT 中樞做為物聯網解決方案中的雲端閘道][img-architecture]

> [!NOTE]
> 如需 IoT 架構的深入討論，請參閱 [Microsoft Azure IoT 參考架構][lnk-refarch]。

## <a name="iot-device-connectivity-challenges"></a>IoT 裝置連線能力面臨的挑戰

對於以可靠且安全的方法將裝置連線到解決方案後端的挑戰，IoT 中樞和裝置程式庫可以協助解決。 IoT 裝置：

* 通常是無人操作的嵌入式系統。
* 可以位於實體存取很昂貴的遠端位置。
* 可能只能透過解決方案後端來存取。
* 能力和/或處理資源可能都有限。
* 網路連線能力可能不穩定、緩慢或昂貴。
* 可能需要使用專屬、自訂或業界特定的應用程式通訊協定。
* 可以使用一組大型常見的硬體和軟體平台來建立。

除了上述需求之外，任何 IoT 解決方案也必須提供調整、安全性和可靠性。 當您使用傳統技術 (例如 Web 容器和傳訊代理程式) 時，實作連線需求的結果是困難且耗時的。

## <a name="why-use-azure-iot-hub"></a>為何使用 Azure IoT 中心？

Azure IoT 中樞提供一組豐富的[裝置到雲端][lnk-d2c-guidance]和[雲端到裝置][lnk-c2d-guidance]通訊選項。 此外，Azure IoT 中樞可透過下列方式解決裝置的可靠且安全連線所帶來的挑戰：

* **裝置對應項**。 使用[裝置對應項][lnk-twins]，您可以儲存、同步處理，以及查詢裝置中繼資料與狀態資訊。 「裝置對應項」是存放裝置狀態資訊的 JSON 文件，例如中繼資料、組態和條件。 IoT 中樞會為連線到 IoT 中樞的每個裝置維持裝置對應項。

* **每一裝置的驗證和安全連線能力**。 您可以提供每個裝置獨有的[安全性金鑰][lnk-devguide-security]讓它連線到 IoT 中樞。 [IoT 中樞身分識別登錄][lnk-devguide-identityregistry]會在解決方案中儲存裝置身分識別與金鑰。 解決方案後端可將個別裝置加入允許或拒絕清單，以達到完全控制裝置存取權。

* **根據宣告式規則，將裝置對雲端訊息路由傳送至 Azure 服務**。 IoT 中樞可讓您根據路由規則定義訊息路由，以控制您的中樞傳送裝置對雲端訊息的位置。 路由規則不需要撰寫任何程式碼，且可代替自訂後擷取訊息發送器。

* **裝置連線作業的監視**。 您可以收到有關裝置身分識別管理作業與裝置連線事件的詳細作業記錄檔。 這項監視功能可讓您的 IoT 解決方案識別連線能力問題。 使用這些記錄可識別提供錯誤認證、訊息傳送頻率過高，或拒絕所有雲端到裝置訊息的裝置。

* **一組廣泛的裝置程式庫**。 [Azure IoT 裝置 SDK][lnk-device-sdks] 可供各種語言和平台使用並受其支援，例如許多 Linux 發行版本都支援的 C、Windows 和即時作業系統。 Azure IoT 裝置 SDK 也支援 C#、Java 和 JavaScript 等 Managed 語言。

* **IoT 通訊協定和擴充性**。 如果您的解決方案不能使用裝置程式庫，Azure IoT 中樞會公開可讓裝置以原生方式使用 MQTT v3.1.1、HTTPS 1.1 或 AMQP 1.0 通訊協定的公用通訊協定。 您也可以擴充 IoT 中樞以支援自訂通訊協定，方法如下：

  * 使用 [Azure IoT Edge][lnk-iot-edge] 建立領域閘道，以將自訂通訊協定轉換成 IoT 中樞所理解的三種通訊協定之一。
  * 自訂 [Azure IoT 通訊協定閘道][protocol-gateway]，這是在雲端執行的開放原始碼元件。

* **規模**。 Azure IoT 中樞會調整為數百萬個同時連接的裝置，以及每秒數百萬個事件。

## <a name="gateways"></a>閘道

IoT 解決方案中的閘道通常是部署於雲端中的[通訊協定閘道][lnk-iotedge]或隨您的裝置部署在本機的[領域閘道][lnk-field-gateway]。 通訊協定閘道會執行通訊協定轉譯，例如 AMQP 到 MQTT。 領域閘道可以在邊緣上執行分析、進行可降低延遲的時效性決策、提供裝置管理服務、強制執行安全性和隱私權條件約束，也可以執行通訊協定轉譯。 這兩種閘道器可作為您的裝置與 IoT 中樞之間的媒介。

現場閘道器與簡單的流量路由裝置 (例如網路位址轉譯裝置或防火牆) 不同，因為它通常會在解決方案內管理存取和資訊流程中扮演主動的角色。

解決方案可以同時包含通訊協定閘道和領域閘道。

## <a name="how-does-iot-hub-work"></a>IoT 中樞如何運作？

Azure IoT 中樞會實作[服務輔助通訊][lnk-service-assisted-pattern]模式，以調節您的裝置與解決方案後端之間的互動。 服務輔助通訊的目標是要在控制系統 (例如 IoT 中樞) 以及在不受信任實體空間中的特殊用途裝置之間，建立可信任的雙向通訊路徑。 該模式會建立下列原則：

* 安全性的優先順序高於所有其他功能。

* 裝置不會接受未經要求的網路資訊。 裝置會以僅限輸出方式建立所有連接和路由。 若要讓裝置從解決方案後端接收命令，裝置必須定期初始連接以檢查有沒有任何暫止的命令要處理。

* 裝置應該只連接至或是建立路由至與它們對等的已知服務 (例如 IoT 中樞)。

* 裝置和服務之間或裝置和閘道器之間的通訊路徑會在應用程式通訊協定層受到保護。

* 系統層級的授權和驗證是以每個裝置的身分識別為基礎。 可讓存取認證和權限能近乎即時撤銷。

* 對於因電源或連線能力而導致連線不穩定的裝置而言，可透過保留命令和通知直到裝置連線並接收它們，從而促進其雙向通訊。 IoT 中樞會為傳送的命令維護裝置特定的佇列。

* 針對透過閘道對特定服務的受保護傳輸，應用程式承載資料會個別受到保護。

行動產業已使用服務輔助通訊模式來實作推播通知服務，例如 [Windows 推播通知服務][lnk-wns]、[Google 雲端通訊][lnk-google-messaging]和 [Apple Push Notification Service][lnk-apple-push]。

ExpressRoute 的公用對等互連路徑支援 IoT 中樞。

## <a name="next-steps"></a>後續步驟

若要了解如何從裝置傳送及從 IoT 中樞接收訊息，以及如何設定訊息路由，請參閱[使用 IoT 中樞傳送及接收訊息][lnk-send-messages]。

若要了解 IoT 中樞如何啟用標準型裝置管理，以便您遠端管理、設定及更新您的裝置，請參閱 [IoT 中樞的裝置管理概觀][lnk-device-management]。

您可以使用 Azure IoT 裝置 SDK 來實作用戶端應用程式，以便在各式各樣的裝置硬體平台與作業系統上執行。 裝置 SDK 包含程式庫，可協助將遙測傳送至 IoT 中樞，並接收雲端到裝置訊息。 當您使用裝置 SDK 時，您可從各種網路通訊協定中選擇，以便與 IoT 中樞通訊。 若要深入了解，請參閱[裝置 SDK 的相關資訊][lnk-device-sdks]。

若要開始撰寫一些程式碼和執行一些範例，請參閱[開始使用 IoT 中樞][lnk-get-started]教學課程。

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "服務輔助通訊 (由 Clemens Vasters 撰寫的部落格文章)"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-iotedge]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-send-messages]: iot-hub-devguide-messaging.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: iot-hub-security-ground-up.md
