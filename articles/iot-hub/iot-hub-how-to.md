---
title: "Azure IoT 中樞做法 | Microsoft Docs"
description: "身為開發人員，如何使用各種 IoT 中樞功能？"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 786121ae249d69376b4be4c74000868cbb208989
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-use-azure-iot-hub"></a>如何使用 Azure IoT 中樞

您有各種選項可了解如何開發 IoT 中樞服務：

* 閱讀詳細描述 IoT 中樞功能的概念性文章。
* 遵循涵蓋 IoT 中樞各種功能的其中一個教學課程。

## <a name="developer-guide"></a>開發人員指南

身為開發人員，您可以閱讀[開發人員指南][lnk-devguide]中有關 IoT 中樞的詳細概念性指導方針。 本指南包含：

* 可協助您了解如何使用之所有 IoT 中樞功能的詳細描述。
* 有多個選項可用時如何選擇的指南。

## <a name="tutorials"></a>教學課程

如果您想要透過實際操作練習來了解特定的 IoT 中樞功能，有數個教學課程可供選擇。 其中許多教學課程都是以多種程式設計語言提供。 這些教學課程包括︰

- [使用路由處理 Azure IoT 中樞的裝置到雲端訊息][lnk-routes-tutorial]。 本教學課程說明如何使用 IoT 中樞路由規則，以簡單的設定方式來分派裝置到雲端訊息。

- [使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d-tutorial]。 本教學課程說明如何透過 IoT 中樞傳送雲端到裝置訊息，以及在裝置上接收雲端到裝置訊息。

- [使用 IoT 中樞從裝置將檔案上傳至雲端][lnk-upload-tutorial]。 本教學課程說明如何使用 IoT 中樞的檔案上傳功能。

- [開始使用裝置對應項][lnk-twin-tutorial]。 本教學課程簡介裝置對應項、報告屬性、所需屬性和標記。 您可使用裝置對應項來同步處理值與裝置。

- [使用直接方法][lnk-methods-tutorial]。 本教學課程說明如何使用直接方法。 您可在模擬裝置中新增直接方法的處理常式，並從 IoT 中樞叫用直接方法。

- [開始使用裝置管理][lnk-dm-tutorial]。 本教學課程說明如何使用主要裝置管理功能，例如裝置對應項和直接方法。 您可以使用這些功能從遠端重新啟動您的模擬裝置。

- [使用所需屬性來設定裝置][lnk-properties-tutorial]。 本教學課程說明如何使用裝置對應項的所需屬性以及報告屬性，從遠端設定您的裝置。

- [使用裝置作業來起始裝置韌體更新][lnk-jobs-tutorial]。 本教學課程說明如何使用主要裝置管理功能，例如裝置對應項和直接方法。 了解如何使用這些功能以從遠端更新裝置的韌體。

- [排程及廣播作業][lnk-schedule-tutorial]。 本教學課程說明如何使用所需屬性和直接方法，在排定的時間與多個裝置互動。

## <a name="next-steps"></a>後續步驟

若要深入了解 IoT 中樞服務，請參閱[開發人員指南][lnk-devguide]。

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-routes-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md
[lnk-c2d-tutorial]: ./iot-hub-csharp-csharp-c2d.md
[lnk-upload-tutorial]: ./iot-hub-csharp-csharp-file-upload.md
[lnk-twin-tutorial]: ./iot-hub-node-node-twin-getstarted.md
[lnk-methods-tutorial]: ./iot-hub-node-node-direct-methods.md
[lnk-dm-tutorial]: ./iot-hub-node-node-device-management-get-started.md
[lnk-properties-tutorial]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-jobs-tutorial]: ./iot-hub-node-node-firmware-update.md
[lnk-schedule-tutorial]: ./iot-hub-node-node-schedule-jobs.md