---
title: "Azure IoT 中樞 - 開始將 IoT 裝置連線到雲端 | Microsoft Docs"
description: "了解如何將 IoT 面板和入門套件連線到 Azure IoT 中樞。 您的裝置可以將遙測資料傳送到 IoT 中樞，而 IoT 中樞會監視並管理您的裝置。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "Azure IoT 中樞教學課程"
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 298f30c8241749ca5088f36e3227dcc66d15295d
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="azure-iot-hub-get-started-tutorials"></a>Azure IoT 中樞快速入門教學課程

您可以使用 Azure IoT 中樞和 Azure IoT 裝置 SDK 來建置物聯網 (IoT) 解決方案：

* Azure IoT 中樞是雲端中完全受管理的服務，可安全地連線、監視及管理您的 IoT 裝置。 您可以使用 Azure IoT 裝置 SDK 來實作您的 IoT 裝置。
* 在更複雜的 IoT 案例中使用 IoT 閘道。 例如，您在此案例中必須考慮諸如傳統裝置、頻寬成本、安全性和隱私權原則或邊緣資料處理等因素。 在這些情況下，您可以使用 Azure IoT Edge 來實作閘道以將裝置連線到 IoT 中樞。

## <a name="what-the-tutorials-cover"></a>這些教學課程的涵蓋範圍

這些教學課程將為您介紹 Azure IoT 中樞與裝置的 SDK。 這些教學課程涵蓋的常見 IoT 案例會說明 IoT 中樞的功能。 教學課程也將說明如何將 IoT 中樞與其他 Azure 服務和工具結合，建置更強大的 IoT 解決方案。 在教學課程中，您可以選擇使用模擬或實際的 IoT 裝置。 此外，您可以了解如何使用閘道來將裝置連線到您的 IoT 中樞。

## <a name="set-up-your-device"></a>設定您的裝置

將 IoT 裝置或閘道連線至 Azure IoT 中樞。 您可以選擇要開始使用的實體或模擬裝置：

| IoT 裝置                       | 程式設計語言 |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Node.js][Pi_Nd]、[C][Pi_C]、[Python][Pi_Py]    |
| IoT DevKit                       | [VSCode 中的 Arduino][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd]、[C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing 開發人員       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| 電腦上的模擬的裝置           | [.NET][Sim_NET]、[Java][Sim_Jav]、[Node.js][Sim_Nd]、[Python][Sim_Pyth] |
| 線上裝置模擬器         | [Raspberry Pi (Node.js)][Ol_Sim] |

此外，您可以使用 IoT Edge 閘道來將裝置連線到您的 IoT 中樞：

| 閘道裝置               | 程式設計語言 | 平台         |
|------------------------------|----------------------|------------------|
| Intel NUC (模型 DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| 模擬閘道            | C                    | [Linux][Sim_Lnx]、[Windows][Sim_Win] |

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
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md

