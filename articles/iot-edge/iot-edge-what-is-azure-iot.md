---
title: "Azure 的物聯網解決方案 (IoT Edge) | Microsoft Docs"
description: "範例 IoT 解決方案架構以及它與裝置、Azure IoT 中樞服務、Azure IoT 裝置 SDK、Azure IoT 服務 SDK 和其他 Azure 服務之關係的概觀。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 587b733106d511ec63d71f67a06e520324a3e594
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>後續步驟

Azure IoT Edge 是一項能夠在終端進行分析和資料處理的 Azure 服務。 透過 IoT Edge，您可使用以容器為基礎的程式碼強化您的裝置，而該程式碼包含直接從您已使用之 Azure 服務提取的邏輯，或您自己的解決方案特有程式碼。 它可讓您的裝置：

* 當作閘道裝置，用於彙總並處理來自多個分葉裝置的資料。
* 執行異常偵測並回應環境中的變更，而不必等候來自雲端的指示。
* 進行資料前處理並傳送結果，以降低頻寬和儲存體成本。 

IoT Edge 也包含一個能夠進行裝置遠端管理的雲端介面。 不需實際存取您的裝置，您即可部署程式碼、監視狀態並加以更新。 您可以從遠端管理單一裝置，或建立可影響您定義之大量裝置的部署。 如需詳細資訊，請參閱[了解單一裝置或大規模的 IoT Edge 部署][lnk-deployment]。

若要深入了解可啟用 IoT Edge 的元件，請參閱 [Azure IoT Edge 的運作方式][lnk-overview]。

如果您未曾使用 Azure IoT 中樞，您可以從 [Azure IoT 中樞服務概觀][lnk-iot-hub]著手。

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
