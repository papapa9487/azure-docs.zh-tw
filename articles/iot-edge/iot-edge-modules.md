---
title: "了解 Azure IoT Edge 模組 | Microsoft Docs"
description: "深入了解 Azure IoT Edge 模組及其設定方式"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 726bbafa9e4ba35cfa4a9cbf4d89056d52fe7963
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="understand-azure-iot-edge-modules---preview"></a>了解 Azure IoT Edge 模組 - 預覽

Azure IoT Edge 可讓您在模組表單的邊緣上部署和管理商務邏輯。 Azure IoT Edge 模組是 IoT Edge 管理的最小計算單位，可以包含 Azure 服務 (例如 Azure 串流分析) 或您自己的解決方案特定程式碼。 若要了解模組如何開發、部署和維護，考慮組成模組的四個概念片段會有幫助：

* **模組映像**是套件，其中包含定義模組的軟體。
* **模組執行個體**是在 IoT Edge 裝置上執行模組映像的計算單位。 模組執行個體是由 IoT Edge 執行階段啟動。
* **模組身分識別**是一段資訊 (包括安全性認證)，儲存在與每個模組執行個體相關聯的 IoT 中樞。
* **模組對應項**是儲存在 IoT 中樞的 JSON 文件，其中包含模組執行個體的狀態資訊，包括中繼資料、設定和條件。 

## <a name="module-images-and-instances"></a>模組映像和執行個體

IoT Edge 模組映像包含應用程式，該應用程式會利用 IoT Edge 執行階段的管理、安全性和通訊功能。 您可以開發自己的模組映像，或從支援的 Azure 服務 (例如 Azure 串流分析) 匯出。
映像存在於雲端中，它們可以在不同的解決方案中更新、變更及部署。 例如，使用機器學習來預測生產線輸出的模組，相較於使用電腦版本以控制無人機的模組，會以個別映像存在。 

每次模組映像部署至裝置並且由 IoT Edge 執行階段啟動時，就會建立該模組的新執行個體。 在世界不同地方的兩個裝置可以使用相同的模組映像。不過在裝置上啟動模組時，每個裝置會有自己的模組執行個體。 

![雲端中的模組映像 - 裝置上的模組執行個體][1]

在實作中，容器映像在存放庫中存在為容器映像，而容器執行個體是裝置上的容器。 隨著 Azure IoT Edge 的使用案例成長，將會建立新類型的模組映像和執行個體。 例如，限制資源裝置無法執行容器，因此可能需要存在為動態連結程式庫和可執行檔執行個體的模組映像。 

## <a name="module-identities"></a>模組身分識別

當 IoT Edge 執行階段建立新模組執行個體時，執行個體與對應的模組身分識別相關聯。 模組身分識別會儲存在 IoT 中樞，作為該特定模組執行個體之所有本機和雲端通訊的定址和安全性範圍。
與模組執行個體相關聯的身分識別取決於在其上執行執行個體的裝置身分識別，以及您提供給解決方案中該模組的名稱。 例如，如果您將使用 Azure 串流分析的模組稱為 `insight`，並且在稱為 `Hannover01` 的裝置上部署，IoT Edge 執行階段會建立稱為 `/devices/Hannover01/modules/insight` 的對應模組身分識別。

很明顯地，在案例中當您需要在相同裝置上部署一個模組映像多次時，您可以使用不同名稱部署相同映像多次。

![模組身分識別是唯一的][2]

## <a name="module-twins"></a>模組對應項

每個模組執行個體也有對應的模組對應項，您可以用來設定模組執行個體。 執行個體和對應項是透過模組身分識別彼此相互關聯。 

模組對應項是 JSON 文件，它會儲存模組資訊和設定屬性。 這個概念與 IoT 中樞的[裝置對應項][lnk-device-twin]概念相同。 模組對應項的結構與裝置對應項完全相同。 用來與這兩種對應項互動的 API 也相同。 兩者之間唯一的差別是用來具現化用戶端 SDK 的身分識別。 

```
// Create a DeviceClient object. This DeviceClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
await client.OpenAsync(); 
 
// Get the model twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="next-steps"></a>後續步驟
 - [了解 Azure IoT Edge 執行階段和架構][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md