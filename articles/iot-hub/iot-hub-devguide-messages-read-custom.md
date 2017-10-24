---
title: "了解 Azure IoT 中樞自訂端點 | Microsoft Docs"
description: "開發人員指南 - 使用路由規則將裝置對雲端訊息路由傳送至自訂端點。"
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
ms.openlocfilehash: a499783fc02e1371562edd41b827758e19fbd823
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>針對裝置對雲端訊息使用訊息路由和自訂端點

IoT 中樞可讓您根據訊息屬性，將[裝置對雲端訊息][lnk-device-to-cloud]路由傳送至 IoT 中樞服務面向端點。 路由規則提供您傳送訊息目的地的彈性，而不需要其他服務來處理訊息或撰寫額外程式碼。 您設定的每個路由規則都具有下列屬性：

| 屬性      | 說明 |
| ------------- | ----------- |
| **名稱**      | 可識別規則的唯一名稱。 |
| **來源**    | 要據以處理的資料串流來源。 例如裝置遙測。 |
| **Condition** | 針對訊息標頭和內文執行的路由規則查詢運算式，用來判斷它是否符合端點。 如需建構路由條件的詳細資訊，請參閱[參考 - 裝置對應項和作業的查詢語言][lnk-devguide-query-language]。 |
| **端點**  | IoT 中樞傳送符合條件之訊息的目的地端點名稱。 端點應該與 IoT 中樞位於相同區域，否則您可能需要支付跨區域寫入費用。 |

單一訊息可能符合多個路由規則的條件，在這種情況下 IoT 中樞會將訊息傳遞至與每個符合的規則相關聯的端點。 IoT 中樞也會自動刪除重複的訊息傳遞，如此若有訊息符合多個規則並且都有相同的目的地，則訊息僅會寫入到該目的地一次。

IoT 中樞具有預設[內建端點][lnk-built-in]。 您可以將訂用帳戶中的其他服務連結到中樞，來建立要路由傳送訊息的目標自訂端點。 IoT 中樞目前支援 Azure 儲存體容器、事件中樞、服務匯流排佇列，及服務匯流排主題作為自訂端點。

> [!NOTE]
> IoT 中樞僅支援將資料寫入 Azure 儲存體容器作為 blob。

> [!WARNING]
> 不支援將啟用 [工作階段] 或 [重複偵測] 的服務匯流排佇列和主題作為自訂端點。

如需在 IoT 中樞建立自訂端點的詳細資訊，請參閱 [IoT 中樞端點][lnk-devguide-endpoints]。

如需從自訂端點讀取的詳細資訊，請參閱：

* 從 [Azure 儲存體容器][lnk-getstarted-storage]讀取。
* 從[事件中樞][lnk-getstarted-eh]讀取。
* 從[服務匯流排佇列][lnk-getstarted-queue]讀取。
* 從[服務匯流排主題][lnk-getstarted-topic]讀取。

### <a name="next-steps"></a>後續步驟

如需 IoT 中樞端點的詳細資訊，請參閱 [IoT 中樞端點][lnk-devguide-endpoints]。

如需您用來定義路由規則之查詢語言的詳細資訊，請參閱[裝置對應項、作業和訊息路由的 IoT 中樞查詢語言][lnk-devguide-query-language]。

[使用路由處理 IoT 中樞的裝置對雲端訊息][lnk-d2c-tutorial] 教學課程示範如何使用路由規則和自訂端點。

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
