---
title: "使用 iothub-explorer 管理 Azure IoT 中樞雲端裝置傳訊 |Microsoft 文件"
description: "了解如何在 Azure IoT 中樞，使用 iothub-explorer CLI 工具來監視裝置到雲端 (D2C) 的訊息，以及傳送雲端到裝置 (C2D) 的訊息。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iothub explorer, 雲端裝置傳訊, iot 中樞雲端到裝置, 雲端到裝置傳訊"
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.openlocfilehash: 30151b7bdc544bc36e959cc3528d37897198fc7e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>使用 iothub-explorer 在裝置與 IoT 中樞之間傳送及接收訊息

![端對端圖表](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) 有數個命令可讓您更輕鬆管理 IoT 中樞。 本學課程著重於如何使用 iothub-explorer，在裝置與 IoT 中樞之間傳送及接收訊息。

## <a name="what-you-will-learn"></a>學習目標

您將了解如何使用 iothub-explorer 來監視裝置到雲端的訊息，以及傳送雲端到裝置的訊息。 裝置到雲端的訊息可能是您的裝置所收集，然後傳送到 IoT 中樞的感應器資料。 雲端到裝置的訊息可能是 IoT 中樞傳送到裝置以使連接到裝置的 LED 閃爍的命令。

## <a name="what-you-will-do"></a>將執行的作業

- 使用 iothub-explorer 來監視裝置到雲端的訊息。
- 使用 iothub-explorer 來傳送雲端到裝置的訊息。

## <a name="what-you-need"></a>您需要什麼

- 完成涵蓋下列需求的[設定裝置](iot-hub-raspberry-pi-kit-node-get-started.md)教學課程︰
  - 有效的 Azure 訂用帳戶。
  - 位於您訂用帳戶中的 Azure IoT 中樞。
  - 將訊息傳送到您 Azure IoT 中樞的用戶端應用程式。
- iothub-explorer。 ([安裝 iothub-explorer](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>監視裝置到雲端的訊息

若要監視從您的裝置傳送到IoT 中樞的訊息，請遵循下列步驟：

1. 開啟主控台視窗。
1. 執行以下命令：

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > 從 IoT 中樞取得 `<device-id>` 和 `<IoTHubConnectionString>`。 確定您已經完成上一個教學課程。 或者，如果您有 `HostName`、`SharedAccessKeyName` 和 `SharedAccessKey`，您可以嘗試使用 `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"`。

## <a name="send-cloud-to-device-messages"></a>傳送雲端到裝置訊息

若要從您的 IoT 中樞將訊息傳送到裝置，請遵循下列步驟：

1. 開啟主控台視窗。
1. 執行下列命令來啟動 IoT 中樞上的工作階段：

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. 執行下列命令來將訊息傳送到您的裝置：

   ```bash
   iothub-explorer send <device-id> <message>
   ```

此命令會使連接到您裝置的 LED 閃爍，並將訊息傳送到您的裝置。

> [!Note]
> 收到訊息之後，裝置不需將個別認可命令傳回您的 IoT 中樞。

## <a name="next-steps"></a>後續步驟

您已了解如何監視裝置到雲端的訊息，以及在 IoT 裝置和 Azure IoT 中樞之間傳送雲端到裝置的訊息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
