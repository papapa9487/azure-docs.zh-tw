---
title: "開始將模擬裝置連線到 Azure IoT 中樞 | Microsoft Docs"
description: "了解如何建立模擬 IoT 裝置，並將它們連線至 Azure IoT 中樞。 您的裝置可以將遙測資料傳送到 IoT 中樞，而 IoT 中樞會監視並管理您的裝置。"
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
ms.date: 09/04/2017
ms.author: dobett
ms.openlocfilehash: 2b015b98b5d5587c9de1f11250386cee86b1f8c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-hub-get-started-with-simulated-devices-tutorials"></a>Azure IoT 中樞開始使用模擬裝置教學課程

這些教學課程將為您介紹 Azure IoT 中樞與裝置的 SDK。 這些教學課程涵蓋的常見 IoT 案例會說明 IoT 中樞的功能。 教學課程也將說明如何將 IoT 中樞與其他 Azure 服務和工具結合，建置更強大的 IoT 解決方案。 下表所列的教學課程會示範如何建立模擬的 IoT 裝置。

| 程式設計語言 |
|----------------------|
| [.NET][Sim_NET]      |
| [Java][Sim_Jav]      |
| [Node.js][Sim_Nd]    |
| [Python][Sim_Pyth]   |

此外，您可以使用 IoT Edge 閘道來讓模擬的裝置連線到您的 IoT 中樞。

| 程式設計語言 | 平台           |
|----------------------|------------------- |
| C                    | [Linux][Sim_Lnx]   |
| C                    | [Windows][Sim_Win] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
