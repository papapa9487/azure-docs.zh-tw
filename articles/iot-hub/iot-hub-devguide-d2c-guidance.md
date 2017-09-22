---
title: "Azure IoT 中樞裝置到雲端選項 | Microsoft Docs"
description: "開發人員指南 - 針對雲端到裝置通訊，提供裝置到雲端訊息、報告屬性或檔案上傳的使用時機指引。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: elioda
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: 146a2e16399a3eeed6b178d72234584c76cc8f51
ms.contentlocale: zh-tw
ms.lasthandoff: 09/08/2017

---
# <a name="device-to-cloud-communications-guidance"></a>Device-to-cloud communications guidance
將資訊從裝置應用程式傳送到解決方案後端時，IoT 中樞會公開三個選項︰

* [裝置到雲端訊息][lnk-d2c]，適用於時間序列遙測和警示。
* [報告屬性][lnk-twins]，適用於報告裝置狀態資訊，如長時間執行之工作流程可用的功能、條件和狀態。 例如，組態和軟體更新。
* [檔案上傳][lnk-fileupload]，適用於間歇性連線裝置所上傳或為了節省頻寬而壓縮的媒體檔案和大型遙測批次。

以下是各種裝置到雲端通訊選項的詳細比較。

|  | 裝置到雲端的訊息 | 報告屬性 | 檔案上傳 |
| ---- | ------- | ---------- | ---- |
| 案例 | 遙測時間序列和警示。 例如，每隔 5 分鐘傳送一次的 256 KB 感應器資料批次。 | 可用的功能與條件。 例如，目前裝置連線能力模式，例如行動電話或 WiFi。 同步處理長時間執行的工作流程，例如組態與軟體更新。 | 媒體檔案。 大型 (通常已壓縮的) 遙測批次。 |
| 儲存和擷取 | 由 IoT 中樞暫時儲存 (最多 7 天)。 僅限循序讀取。 | 由裝置對應項中的 IoT 中樞儲存。 使用 [IoT 中樞查詢語言][lnk-query]擷取。 | 儲存在使用者提供的 Azure 儲存體帳戶中。 |
| 大小 | 最多 256 KB 的訊息。 | 最大回報屬性大小為 8 KB。 | Azure Blob 儲存體所支援的檔案大小上限。 |
| 頻率 | 高。 如需詳細資訊，請參閱 [IoT 中樞限制][lnk-quotas]。 | 中。 如需詳細資訊，請參閱 [IoT 中樞限制][lnk-quotas]。 | 低。 如需詳細資訊，請參閱 [IoT 中樞限制][lnk-quotas]。 |
| 通訊協定 | 適用於所有通訊協定。 | 可使用 MQTT 或 AMQP。 | 可用於使用任何通訊協定時，但需要是裝置上的 HTTP。 |

應用程式可能需要以遙測時間序列或警示形式傳送資訊，而且使其可用於裝置對應項。 在此案例中，您可以選擇下列其中一個選項：

* 裝置應用程式可傳送裝置到雲端訊息及回報屬性變更。
* 解決方案後端可以在收到訊息時將資訊儲存在裝置對應項的標籤中。

由於裝置到雲端訊息允許高於裝置對應項更新的輸送量，所以有時希望避免針對每則裝置到雲端訊息更新裝置對應項。


[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md

