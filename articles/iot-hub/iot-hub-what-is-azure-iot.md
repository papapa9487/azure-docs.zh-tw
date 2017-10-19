---
title: "Azure 的物聯網解決方案 (IoT 套件) | Microsoft Docs"
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
ms.openlocfilehash: 417ca4b6ecc39cbdafd8e12b5360b370d0ce79fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>後續步驟

Azure IoT 中樞是一項 Azure 服務，可讓解決方案後端與數百萬個裝置進行安全可靠的雙向通訊。 它可讓解決方案後端︰

* 從您的裝置接收大規模遙測。
* 將資料從您的裝置路由傳送到串流事件處理器。
* 從裝置接收檔案上傳。
* 將雲端到裝置訊息傳送到特定裝置。

您可以使用 IoT 中樞來實作自己的方案後端。 此外，IoT 中樞還包含身分識別登錄，可供用來佈建裝置、其安全性認證和其連線到 IoT 中樞的權限。 若要深入了解 IoT 中樞，請參閱[何謂 IoT 中樞？][lnk-iot-hub]。

若要了解 Azure IoT 中樞如何啟用標準型裝置管理，以便遠端管理您的裝置，請參閱 [IoT 中樞的裝置管理概觀][lnk-device-management]。

您可以使用 Azure IoT 裝置 SDK 來實作用戶端應用程式，以便在各式各樣的裝置硬體平台與作業系統上執行。 裝置 SDK 包含程式庫，可協助將遙測傳送至 IoT 中樞，並接收雲端到裝置訊息。 當您使用裝置 SDK 時，您可以從數種網路通訊協定中選擇，以便與 IoT 中樞通訊。 若要深入了解，請參閱[裝置 SDK 的相關資訊][lnk-device-sdks]。

若要開始撰寫一些程式碼和執行一些範例，請參閱[開始使用 IoT 中樞][lnk-getstarted]教學課程。

您也可能會對 [Azure IoT 套件][lnk-iot-suite]有興趣，這是預先設定的解決方案集合。 IoT 套件可讓您快速入門和調整 IoT 專案，以因應常見的 IoT 案例，例如遠端監視、資產管理和預測性維護。

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md
