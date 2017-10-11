---
title: "透過 iothub-explorer 進行 Azure IoT 裝置管理 | Microsoft Docs"
description: "使用 iothub-explorer CLI 工具進行 Azure IoT 中樞裝置管理，採用直接方法和對應項所需的屬性管理選項。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot 裝置管理, azure iot 中樞裝置管理, 裝置管理 iot, iot 中樞裝置管理"
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: xshi
ms.openlocfilehash: 5b7a5057bdfb5920fbb5759bed1f5561cfa1d7e0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>使用 iothub-explorer 進行 Azure IoT 中樞裝置管理

![端對端圖表](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) 是您在主機電腦上執行的 CLI 工具，用來管理您的 IoT 中樞登錄中的裝置身分識別。 它隨附的管理選項可供您用來執行各種工作。

| 管理選項          | Task                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| 直接方法             | 使裝置執行動作，例如啟動或停止傳送訊息，或是將裝置重新開機。                                        |
| 對應項的所需屬性    | 讓裝置進入特定狀態，例如將 LED 設定為綠色，或將遙測傳送間隔設定為 30 分鐘。         |
| 對應項的報告屬性   | 取得裝置的報告狀態。 例如，裝置會回報 LED 現在正閃爍不停。                                    |
| 對應項標記                  | 在雲端儲存裝置特定的中繼資料。 例如，販賣機的部署位置。                         |
| 雲端到裝置的訊息   | 將通知傳送至裝置。 例如，「今天很可能會下雨。 別忘了帶傘。」              |
| 裝置對應項查詢        | 查詢所有裝置對應項以擷取具有任意條件的這些裝置，例如識別可供使用的裝置。 |

如需差異的詳細說明和使用這些選項的相關指引，請參閱[裝置對雲端通訊指引](iot-hub-devguide-d2c-guidance.md)和[雲端對裝置通訊指引](iot-hub-devguide-c2d-guidance.md)。

> [!NOTE]
> 「裝置對應項」是存放裝置狀態資訊 (中繼資料、組態和條件) 的 JSON 文件。 IoT 中樞會為其連線的每個裝置保存裝置對應項。 如需裝置對應項的詳細資訊，請參閱[開始使用裝置對應項](iot-hub-node-node-twin-getstarted.md)。

## <a name="what-you-learn"></a>您學到什麼

您會學到在開發電腦上使用 iothub-explorer 搭配各種管理選項。

## <a name="what-you-do"></a>您要做什麼

執行 iothub-explorer 搭配各種管理選項。

## <a name="what-you-need"></a>您需要什麼

- 完成涵蓋下列需求的[設定裝置](iot-hub-raspberry-pi-kit-node-get-started.md)教學課程︰
  - 有效的 Azure 訂用帳戶。
  - 位於您訂用帳戶中的 Azure IoT 中樞。
  - 將訊息傳送到您 Azure IoT 中樞的用戶端應用程式。
- 請確定在本教學課程期間，您的裝置是和用戶端應用程式一起執行。
- iothub-explorer，在開發電腦上[安裝 iothub-explorer](https://github.com/azure/iothub-explorer)。

## <a name="connect-to-your-iot-hub"></a>連線至您的 IoT 中樞

執行下列命令來連線至 IoT 中樞：

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>使用 iothub-explorer 搭配直接方法

透過執行下列命令，在裝置應用程式中叫用 `start` 方法，將訊息傳送至您的 IoT 中樞：

```bash
iothub-explorer device-method <your device Id> start
```

透過執行下列命令，在裝置應用程式中叫用 `stop` 方法，停止將訊息傳送至您的 IoT 中樞：

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>使用 iothub-explorer 搭配對應項所需的屬性

透過執行下列命令，設定需要的屬性間隔 = 3000：

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

此屬性可以由您的裝置讀取。

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>使用 iothub-explorer 搭配對應項的報告屬性

執行下列命令來取得裝置的報告屬性：

```bash
iothub-explorer get-twin <your device id>
```

其中一個屬性是 $metadata.$lastUpdated，它會顯示此裝置上一次傳送或接收訊息的時間。

## <a name="use-iothub-explorer-with-twins-tags"></a>使用 iothub-explorer 搭配對應項標記

執行下列命令來顯示裝置的標記和屬性：

```bash
iothub-explorer get-twin <your device id>
```

執行下列命令來將欄位 role = temperature&humidity 新增至裝置：

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"

```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>對雲端到裝置的訊息使用 iothub-explorer

執行下列命令來將 "Hello World" 訊息傳送到裝置：

```bash
iothub-explorer send <device-id> "Hello World"
```

如需使用此命令的真實案例，請參閱[使用 iothub-explorer 在裝置與 IoT 中樞之間傳送及接收訊息](iot-hub-explorer-cloud-device-messaging.md)。

## <a name="use-iothub-explorer-with-device-twins-queries"></a>使用 iothub-explorer 搭配裝置對應項查詢

藉由執行下列命令，查詢具有標記 role = 'temperature&humidity' 的裝置：

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

藉由執行下列命令，查詢具有標記 role = 'temperature&humidity' 以外的所有裝置：

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>後續步驟

您已學到如何使用 iothub-explorer 搭配各種管理選項。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
