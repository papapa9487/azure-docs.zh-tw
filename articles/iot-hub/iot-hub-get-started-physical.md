---
title: "開始將實體裝置連線到 Azure IoT 中樞 | Microsoft Docs"
description: "了解實體裝置和面板如何連線到 Azure IoT 中樞。 您的裝置可以將遙測資料傳送到 IoT 中樞，而 IoT 中樞會監視並管理您的裝置。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "Azure IoT 中樞教學課程"
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: dobett
ms.openlocfilehash: 26a2d63aef21bd470d65d79e1abf8cefd2828419
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2017
---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>Azure IoT 中樞開始使用實體裝置教學課程

這些教學課程將為您介紹 Azure IoT 中樞與裝置的 SDK。 這些教學課程涵蓋的常見 IoT 案例會說明 IoT 中樞的功能。 教學課程也將說明如何將 IoT 中樞與其他 Azure 服務和工具結合，建置更強大的 IoT 解決方案。 下表所列的教學課程會示範如何建立實體 IoT 裝置。

| IoT 裝置                       | 程式設計語言 |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Python][Pi_Py]、[Node.js][Pi_Nd]、[C][Pi_C]  |
| IoT DevKit                      | [VSCode 中的 Arduino][DevKit]     |
| Intel Edison                    | [Node.js][Ed_Nd]、[C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing 開發人員      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
