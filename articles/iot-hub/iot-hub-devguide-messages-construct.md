---
title: "了解 Azure IoT 中樞訊息格式 | Microsoft Docs"
description: "開發人員指南 - 說明 IoT 中樞訊息的格式和預期內容。"
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
ms.date: 05/25/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: e6eafb1a0030b022da2b5d0b787e092f3067c99f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="create-and-read-iot-hub-messages"></a>建立及讀取 IoT 中樞訊息

為了支援完美的跨通訊協定互通性，IoT 中樞定義了適用於所有裝置面向通訊協定的通用訊息格式。 此訊息格式可用於[裝置對雲端][lnk-d2c]和[雲端對裝置][lnk-c2d]訊息。 [IoT 中樞訊息][lnk-messaging]包含：

* 一組 *系統屬性*。 IoT 中樞解譯或設定的屬性。 這個集合是預先決定的。
* 一組 *應用程式屬性*。 應用程式可以定義的字串屬性字典，而且不需將訊息本文還原序列化即可加以存取。 IoT 中樞不會修改這些屬性。
* 不透明的二進位主體。

在下列情況下，屬性名稱和值只能包含 ASCII 英數字元和 ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``：

* 使用 HTTP 通訊協定傳送裝置到雲端訊息。
* 傳送雲端到裝置的訊息。

如需如何將使用不同通訊協定傳送的訊息編碼和解碼的詳細資訊，請參閱 [Azure IoT SDK][lnk-sdks]。

下表列出 IoT 中樞訊息中的系統屬性集合。

| 屬性 | 說明 |
| --- | --- |
| MessageId |使用者可設定的訊息識別碼，用於「要求-回覆」模式。 格式：區分大小寫的字串，最長為 128 個字元，可使用 ASCII 7 位元英數字元和 `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 |
| 序號 |IoT 中樞指派給每則雲端到裝置訊息的數字 (對每個裝置佇列而言都是唯一的)。 |
| 收件人 |[雲端到裝置][lnk-c2d]訊息中指定的目的地。 |
| ExpiryTimeUtc |訊息到期的日期和時間。 |
| EnqueuedTime |IoT 中樞收到[雲端到裝置][lnk-c2d]訊息的日期和時間。 |
| CorrelationId |回應訊息中的字串屬性，通常包含採用「要求-回覆」模式之要求的 MessageId。 |
| UserId |用來指定訊息來源的識別碼。 當 IoT 中樞產生訊息時，它會設定為 `{iot hub name}`。 |
| Ack |意見反應訊息產生器。 這個屬性是在雲端到裝置訊息中使用，可要求 IoT 中樞因為裝置取用訊息而產生意見反應訊息。 可能的值︰**none** (預設值)︰不會產生任何意見反應訊息；**positive**︰如果訊息已完成，則會收到意見反應訊息；**negative**︰如果訊息未由裝置完成就到期 (或已達到最大傳遞計數) 則會收到意見反應訊息；或者 **full**︰positive 和 negative。 若需詳細資訊，請參閱[訊息意見反應][lnk-feedback]。 |
| ConnectionDeviceId |由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 它包含傳送訊息之裝置的 **deviceId** 。 |
| ConnectionDeviceGenerationId |由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 它包含傳送訊息之裝置的 **generationId** (依據[裝置身分識別屬性][lnk-device-properties])。 |
| ConnectionAuthMethod |由 IoT 中樞在裝置到雲端訊息上設定的驗證方法。 這個屬性包含用來驗證傳送訊息之裝置的驗證方法的相關資訊。 如需詳細資訊，請參閱[裝置到雲端反詐騙][lnk-antispoofing]。 |

## <a name="message-size"></a>訊息大小

IoT 中樞會以不限通訊協定的方式 (只考慮實際的承載) 測量郵件大小。 大小 (以位元組為單位) 的計算方式為下列項目的總和︰

* 內文大小 (以位元組為單位)。
* 訊息系統屬性所有值的大小 (以位元組為單位)。
* 所有使用者屬性名稱和值的大小 (以位元組為單位)。

屬性名稱和值僅限使用 ASCII 字元，因此字串長度會等於大小 (以位元組為單位)。

## <a name="next-steps"></a>後續步驟

如需 IoT 中樞訊息大小限制的資訊，請參閱 [IoT 中樞配額和節流][lnk-quotas]。

若要了解如何以各種程式設計語言建立及讀取 IoT 中樞訊息，請參閱[快速入門][lnk-get-started]教學課程。

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties

