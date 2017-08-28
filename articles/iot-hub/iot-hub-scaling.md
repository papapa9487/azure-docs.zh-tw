---
title: "Azure IoT 中樞調整 | Microsoft Docs"
description: "如何調整 IoT 中樞以支援您預期的訊息輸送量。 包含每個層級支援的輸送量和分區化選項的摘要。"
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 03eeecc575be3073066a203cdd4ec6944bb23afc
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017

---
# <a name="scale-your-iot-hub-solution"></a>調整您的 IoT 中樞解決方案
Azure IoT 中樞最多可支援百萬個同時連線的裝置。 如需詳細資訊，請參閱 [IoT 中樞價格][lnk-pricing]。 每個 IoT 中樞單位允許一定數量的每日訊息。

為了適當調整您的解決方案，您必須考慮 IoT 中樞的特定使用方式。 尤其要考慮以下類別的作業所需的尖峰輸送量：

* 裝置到雲端的訊息
* 雲端到裝置的訊息
* 身分識別登錄作業

除了此輸送量資訊，請參閱 [IoT 中樞配額和節流][IoT Hub quotas and throttles]，並據以設計您的解決方案。

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>裝置到雲端及雲端到裝置訊息輸送量
若要評估每個單位的流量，最佳方式為調整 IoT 中樞解決方案的大小。

裝置到雲端訊息會遵循這些持續的輸送量指導方針。

| 層 | 持續的輸送量 | 持續的傳送速率 |
| --- | --- | --- |
| S1 |每個單位最多 1111 KB/分鐘<br/>(1.5 GB/天/單位) |每個單位平均 278 個訊息/分鐘<br/>(400,000 個訊息/天/單位) |
| S2 |每個單位最多 16 MB/分鐘<br/>(22.8 GB/天/單位) |每個單位平均 4,167 個訊息/分鐘<br/>(600 萬個訊息/天/單位) |
| S3 |每個單位最多 814 MB/分鐘<br/>(1144.4 GB/天/單位) |每個單位平均 208,333 個訊息/分鐘<br/>(3 億個訊息/天/單位) |

## <a name="identity-registry-operation-throughput"></a>身分識別登錄作業輸送量
大部分的 IoT 中樞識別登錄作業都與裝置佈建有關，應該都不是執行階段作業。

對於明顯暴增的效能數據，請參閱 [IoT 中樞配額和節流][IoT Hub quotas and throttles]。

## <a name="sharding"></a>分區化
雖然單一 IoT 中樞可以擴充到數百萬個裝置，但有時候您的解決方案需要單一 IoT 中樞無法保證的特定效能特性。 在此情況下，建議您將裝置分割成多個 IoT 中樞。 多個 IoT 中心可減緩資料傳輸量暴增，並取得所需的輸送量或作業速率。

## <a name="next-steps"></a>後續步驟
若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南][lnk-devguide]
* [使用 Azure IoT Edge 來模擬裝置][lnk-iotedge]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md

