---
title: "將 Raspberry Pi 連線至 Azure IoT 套件 | Microsoft Docs"
description: "教學課程使用 Node.js 或 C 來幫助您了解如何使用 Raspberry Pi 3 的 Microsoft Azure IoT 入門套件，以及遠端監視解決方案的 IoT 套件。 您可以選擇的教學課程包括模擬遙測、使用真實感應器，或更新遠端韌體。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: d9e737fcaaabd2088d2920b69fca96d6058f4b4a
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-microsoft-azure-iot-raspberry-pi-3-starter-kit-to-the-remote-monitoring-solution"></a>將 Microsoft Azure IoT Raspberry Pi 3 入門套件連線到遠端監視解決方案

本章節的教學課程可協助您了解如何將 Raspberry Pi 3 裝置連線到遠端監視解決方案。 選擇適合您偏好程式設計語言的教學課程，以及您是否有可搭配 Raspberry Pi 使用的感應器硬體。

## <a name="the-tutorials"></a>教學課程

| 教學課程 | 注意事項 | 語言 |
| -------- | ----- | --------- |
| 模擬遙測 (基本)| 模擬感應器資料。 使用獨立 Raspberry Pi。 | [C][lnk-c-simulator]、[Node.js][lnk-node-simulator] |
| 真實感應器 (中級) | 使用連線到 Raspberry Pi 之 BME280 感應器的資料。 | [C][lnk-c-basic]、[Node.js][lnk-node-basic] |
| 實作韌體更新 (進階)| 使用連線到 Raspberry Pi 之 BME280 感應器的資料。 啟用 Raspberry Pi 上的遠端韌體更新。 | [C][lnk-c-advanced]、[Node.js][lnk-node-advanced] |

## <a name="next-steps"></a>後續步驟

請瀏覽 [Azure IoT 開發人員中心](https://azure.microsoft.com/develop/iot/)，取得更多 Azure IoT 的相關範例和文件。

[lnk-node-simulator]: iot-suite-v1-raspberry-pi-kit-node-get-started-simulator.md
[lnk-node-basic]: iot-suite-v1-raspberry-pi-kit-node-get-started-basic.md
[lnk-node-advanced]: iot-suite-v1-raspberry-pi-kit-node-get-started-advanced.md
[lnk-c-simulator]: iot-suite-v1-raspberry-pi-kit-c-get-started-simulator.md
[lnk-c-basic]: iot-suite-v1-raspberry-pi-kit-c-get-started-basic.md
[lnk-c-advanced]: iot-suite-v1-raspberry-pi-kit-c-get-started-advanced.md