---
title: "Azure 的物聯網解決方案 (IoT 套件) | Microsoft Docs"
description: "Azure 上的 IoT 概觀，包括範例解決方案架構，以及它如何與 Azure IoT 套件及預先設定解決方案相關聯。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 437d2655-896f-4a9e-a4a8-b864790d3ef8
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 320190488bb4c7b8192421f9dd50a5264f558584
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="azure-iot-suite"></a>Azure IoT 套件
Microsoft Azure IoT 套件是企業級解決方案，可透過一組可延伸的預先設定解決方案讓您快速入門。 這些解決方案可滿足常見的 IoT 案例，例如[遠端監視][lnk-preconfigured-solutions]、[預測性維護][lnk-predictive-maintenance]和[連線的處理站][lnk-connected-factory]。 這些解決方案是本文章概述之 IoT 解決方案架構的實作。

預先設定之解決方案是完整、有效的端對端解決方案，其中包括︰

- 讓您迅速上手的模擬裝置。
- 預先設定的 Azure 服務，例如 [Azure IoT 中樞][Azure IoT Hub]、[Azure 事件中樞][Azure Event Hubs]、[Azure 串流分析][Azure Stream Analytics]、[Azure Machine Learning][Azure Machine Learning] 和 [Azure 儲存體][Azure storage]。
- 解決方案專用的管理主控台。

預先設定的解決方案包含已實證且已準備好用於生產環境的程式碼，您可以用來自訂及擴充，以實作您自己的特定 IoT 案例。

您可能也會對許多預先設定之解決方案所使用的 [Azure IoT 中樞][Azure IoT Hub]服務感興趣。 [Azure IoT 中樞][Azure IoT Hub]在預先設定的解決方案架構中所使用的裝置和雲端之間，提供安全可靠的雙向通訊。

## <a name="next-steps"></a>後續步驟
請探索下列資源以繼續了解 IoT 套件和預先設定的解決方案：

* [什麼是 Azure IoT 套件？][lnk-whatissuite]
* [什麼是 Azure IoT 套件預先設定的解決方案？][lnk-whatarepreconfigured]

[lnk-whatissuite]: iot-suite-overview.md
[lnk-whatarepreconfigured]: iot-suite-what-are-preconfigured-solutions.md

[lnk-preconfigured-solutions]: iot-suite-getstarted-preconfigured-solutions.md
[Azure IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[Azure Machine Learning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-connected-factory]: iot-suite-connected-factory-overview.md