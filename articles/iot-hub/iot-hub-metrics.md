---
title: "使用計量監視 Azure IoT 中樞 | Microsoft Docs"
description: "如何使用 Azure IoT 中樞度量來評估和監視 IoT 中樞的整體健全狀況。"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 82cec2c9545cd5ae33ec6d5d0e4c7ebf1a500e5b
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017

---
# <a name="understand-iot-hub-metrics"></a>了解 IoT 中樞計量
IoT 中樞度量提供更好的資料，讓您了解 Azure 訂用帳戶中各種 Azure 資源的狀態。 IoT 中樞度量可讓您評估 IoT 中樞服務及其連接之裝置的整體健全狀況。 提供給您的統計資料非常重要，因為它們可以協助您了解其 IoT 中樞的情況，並協助您不需要連絡 Azure 支援人員就解決根本問題。

預設會啟用計量。 您可以從 Azure 入口網站啟用 IoT 中樞計量。

## <a name="how-to-view-iot-hub-metrics"></a>如何檢視 IoT 中樞計量
1. 建立 IoT 中樞。 您可以在[開始使用][lnk-get-started]指南中找到如何建立 IoT 中樞的指示。
2. 開啟 IoT 中樞的刀鋒視窗。 按一下此處的 [計量]。
   
    ![][1]
3. 您可以在 [計量] 刀鋒視窗中檢視 IoT 中樞的計量，並建立計量的自訂檢視。 您可以藉由按一下 [診斷設定]，選擇將計量資料傳送到事件中樞端點或 Azure 儲存體帳戶。
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT 中樞度量及其使用方式
IoT 中樞提供數個度量，以讓您概略了解中樞的健全狀況和所連線裝置的總數。 您可以結合多個度量的資訊，以便更清楚地了解 IoT 中樞的狀態。 下表描述每個 IoT 中樞所追蹤的度量，以及每個度量與 IoT 中樞整體狀態的關聯。

|度量|度量顯示名稱|單位|彙總類型|說明|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|遙測訊息傳送嘗試|Count|總計|要嘗試傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|
|d2c.telemetry.ingress.success|已傳送的遙測訊息|Count|總計|成功傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|
|c2d.commands.egress.complete.success|完成的命令|Count|總計|裝置成功完成的雲端到裝置命令數目|
|c2d.commands.egress.abandon.success|放棄的命令|Count|總計|裝置放棄的雲端到裝置命令數目|
|c2d.commands.egress.reject.success|拒絕的命令|Count|總計|裝置拒絕的雲端到裝置命令數目|
|devices.totalDevices|裝置總計|Count|總計|向 IoT 中樞註冊的裝置數目|
|devices.connectedDevices.allProtocol|連接的裝置|Count|總計|連接至 IoT 中樞的裝置數目|
|d2c.telemetry.egress.success|已傳遞的遙測訊息|Count|總計|訊息成功寫入端點的次數 (總計)|
|d2c.telemetry.egress.dropped|捨棄的訊息|Count|總計|因為不符合任何路由且後援路由已停用，而捨棄的訊息數目|
|d2c.telemetry.egress.orphaned|被遺棄的訊息|Count|總計|不符合任何路由 (包括後援路由) 的訊息計數|
|d2c.telemetry.egress.invalid|無效的訊息|Count|總計|因為與端點不相容而未傳遞的訊息計數|
|d2c.telemetry.egress.fallback|符合後援條件的訊息|Count|總計|寫入後援端點的訊息數目|
|d2c.endpoints.egress.eventHubs|傳遞至事件中樞端點的訊息|Count|總計|訊息成功寫入事件中樞端點的次數|
|d2c.endpoints.latency.eventHubs|事件中樞端點的訊息延遲|毫秒|平均值|訊息輸入到 IoT 中樞與訊息輸入到事件中樞端點之間的平均延遲，以毫秒為單位|
|d2c.endpoints.egress.serviceBusQueues|傳遞至服務匯流排佇列端點的訊息|Count|總計|訊息成功寫入服務匯流排佇列端點的次數|
|d2c.endpoints.latency.serviceBusQueues|服務匯流排佇列端點的訊息延遲|毫秒|平均值|訊息輸入到 IoT 中樞與訊息輸入到服務匯流排佇列端點之間的平均延遲，以毫秒為單位|
|d2c.endpoints.egress.serviceBusTopics|傳遞至服務匯流排主題端點的訊息|Count|總計|訊息成功寫入服務匯流排主題端點的次數|
|d2c.endpoints.latency.serviceBusTopics|服務匯流排主題端點的訊息延遲|毫秒|平均值|訊息輸入到 IoT 中樞與訊息輸入到服務匯流排主題端點之間的平均延遲，以毫秒為單位|
|d2c.endpoints.egress.builtIn.events|傳遞至內建端點 (訊息/事件) 的訊息|Count|總計|訊息成功寫入內建端點 (訊息/事件) 的次數|
|d2c.endpoints.latency.builtIn.events|內建端點 (訊息/事件) 的訊息延遲|毫秒|平均值|訊息輸入到 IoT 中樞與訊息輸入到內建端點 (訊息/事件) 之間的平均延遲，以毫秒為單位 |
|d2c.twin.read.success|裝置的成功對應項讀取|Count|總計|裝置起始的所有成功對應項讀取的計數。|
|d2c.twin.read.failure|裝置的失敗對應項讀取|Count|總計|裝置起始的所有失敗對應項讀取的計數。|
|d2c.twin.read.size|裝置的對應項讀取回應大小|位元組|平均值|裝置起始的所有成功對應項讀取的平均值、最小值和最大值。|
|d2c.twin.update.success|裝置的成功對應項更新|Count|總計|裝置起始的所有成功對應項更新的計數。|
|d2c.twin.update.failure|裝置的失敗對應項更新|Count|總計|裝置起始的所有失敗對應項更新的計數。|
|d2c.twin.update.size|裝置的對應項更新大小|位元組|平均值|裝置起始的所有成功對應項更新的大小平均值、最小值和最大值。|
|c2d.methods.success|成功直接方法叫用|Count|總計|所有成功直接方法呼叫的計數。|
|c2d.methods.failure|失敗直接方法叫用|Count|總計|所有失敗直接方法呼叫的計數。|
|c2d.methods.requestSize|直接方法叫用的要求大小|位元組|平均值|所有成功直接方法要求的平均值、最小值和最大值。|
|c2d.methods.responseSize|直接方法叫用的回應大小|位元組|平均值|所有成功直接方法回應的平均值、最小值和最大值。|
|c2d.twin.read.success|後端的成功對應項讀取|Count|總計|後端起始的所有成功對應項讀取的計數。|
|c2d.twin.read.failure|後端的失敗對應項讀取|Count|總計|後端起始的所有失敗對應項讀取的計數。|
|c2d.twin.read.size|後端的對應項讀取回應大小|位元組|平均值|後端起始的所有成功對應項讀取的平均值、最小值和最大值。|
|c2d.twin.update.success|後端的成功對應項更新|Count|總計|後端起始的所有成功對應項更新的計數。|
|c2d.twin.update.failure|後端的失敗對應項更新|Count|總計|後端起始的所有失敗對應項更新的計數。|
|c2d.twin.update.size|後端的對應項更新大小|位元組|平均值|後端起始的所有成功對應項更新的大小平均值、最小值和最大值。|
|twinQueries.success|成功對應項查詢|Count|總計|所有成功對應項查詢的計數。|
|twinQueries.failure|失敗對應項查詢|Count|總計|所有失敗對應項查詢的計數。|
|twinQueries.resultSize|對應項查詢結果大小|位元組|平均值|所有成功對應項查詢的結果大小平均值、最小值和最大值。|
|jobs.createTwinUpdateJob.success|成功建立的對應項更新作業|Count|總計|所有成功建立的對應項更新作業計數。|
|jobs.createTwinUpdateJob.failure|建立失敗的對應項更新作業|Count|總計|所有建立失敗的對應項更新作業計數。|
|jobs.createDirectMethodJob.success|成功建立的方法叫用作業|Count|總計|所有成功建立的直接方法叫用作業計數。|
|jobs.createDirectMethodJob.failure|建立失敗的方法叫用作業|Count|總計|所有建立失敗的直接方法叫用作業計數。|
|jobs.listJobs.success|成功呼叫列出作業|Count|總計|所有成功呼叫列出作業的計數。|
|jobs.listJobs.failure|呼叫列出作業失敗|Count|總計|所有呼叫列出作業失敗的計數。|
|jobs.cancelJob.success|成功取消作業|Count|總計|所有成功呼叫取消作業的計數。|
|jobs.cancelJob.failure|取消作業失敗|Count|總計|所有呼叫取消作業失敗的計數。|
|jobs.queryJobs.success|成功作業查詢|Count|總計|所有成功呼叫查詢作業的計數。|
|jobs.queryJobs.failure|失敗作業查詢|Count|總計|所有呼叫查詢作業失敗的計數。|
|jobs.completed|已完成的工作|Count|總計|所有已完成的作業計數。|
|jobs.failed|失敗作業|Count|總計|所有失敗作業計數。|

## <a name="next-steps"></a>後續步驟
現在您已了解 IoT 中樞度量的概觀，請循著下列連結來深入了解如何管理「Azure IoT 中樞」：

* [作業監視][lnk-monitor]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南][lnk-devguide]
* [使用 Azure IoT Edge 來模擬裝置][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md

