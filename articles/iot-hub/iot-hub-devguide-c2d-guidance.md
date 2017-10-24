---
title: "Azure IoT 中樞雲端到裝置選項 | Microsoft Docs"
description: "開發人員指南 - 針對雲端到裝置通訊，提供直接方法、裝置對應項的所需屬性或雲端到裝置訊息的使用時機指引。"
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: elioda
ms.openlocfilehash: c0f9d0e13cb159188bdaf2b915c1bf6de73be855
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-to-device-communications-guidance"></a>Cloud-to-device communications guidance
IoT 中樞提供三個選項以便裝置應用程式對後端應用程式公開功能︰

* [直接方法][lnk-methods]，適用於需要立即確認結果的通訊。 直接方法通常用於裝置的互動式控制，例如開啟風扇。
* [對應項的所需屬性][lnk-twins]，適用於可讓裝置進入特定所需狀態的長時間執行命令。 例如，將遙測傳送間隔設定為 30 分鐘。
* [雲端到裝置訊息][lnk-c2d]，適用於對裝置應用程式的單向通知。

以下是各種雲端到裝置通訊選項的詳細比較。

|  | 直接方法 | 對應項的所需屬性 | 雲端到裝置的訊息 |
| ---- | ------- | ---------- | ---- |
| 案例 | 需要立即確認的命令，例如開啟風扇。 | 可讓裝置進入特定所需狀態的長時間執行命令。 例如，將遙測傳送間隔設定為 30 分鐘。 | 對裝置應用程式的單向通知。 |
| 資料流 | 雙向。 裝置應用程式可以立即回應方法。 解決方案後端會接收到根據要求上下文的結果。 | 單向。 裝置應用程式會收到屬性變更的通知。 | 單向。 裝置應用程式接收訊息
| 耐久性 | 無法聯繫已中斷連接的裝置。 解決方案後端會收到裝置未連線的通知。 | 屬性值會保留在裝置對應項中。 裝置會在下一次重新連線時讀取它。 使用 [IoT 中樞查詢語言][lnk-query]可擷取屬性值。 | IoT 中樞可以保留訊息長達 48 小時。 |
| 目標 | 使用 **deviceId** 的單一裝置，或使用[作業][lnk-jobs]的多個裝置。 | 使用 **deviceId** 的單一裝置，或使用[作業][lnk-jobs]的多個裝置。 | 依照 **deviceId** 的單一裝置。 |
| 大小 | 上限為 8 KB 要求和 8 KB 回應。 | 所需屬性大小上限為 8 KB。 | 上限為 64 KB 訊息。 |
| 頻率 | 高。 如需詳細資訊，請參閱 [IoT 中樞限制][lnk-quotas]。 | 中。 如需詳細資訊，請參閱 [IoT 中樞限制][lnk-quotas]。 | 低。 如需詳細資訊，請參閱 [IoT 中樞限制][lnk-quotas]。 |
| 通訊協定 | 可使用 MQTT 或 AMQP。 | 可使用 MQTT 或 AMQP。 | 適用於所有通訊協定。 使用 HTTPS 時，裝置必須輪詢。 |

在下列教學課程中，了解如何使用直接方法、所需屬性和雲端到裝置訊息︰

* [使用直接方法][lnk-methods-tutorial]，適用於直接方法；
* [使用所需屬性來設定裝置][lnk-twin-properties]，適用於裝置對應項的所需屬性； 
* [傳送雲端到裝置訊息][lnk-c2d-tutorial]，適用於雲端到裝置訊息。

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
