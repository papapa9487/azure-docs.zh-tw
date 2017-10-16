---
title: "使用 IoT 閘道將裝置連線到 Azure IoT 中樞 | Microsoft Docs"
description: "了解如何使用 Intel NUC IoT 閘道連接 TI SensorTag 感應器，並將資料傳送至雲端的 Azure IoT 中樞。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 閘道器將裝置連接至雲端"
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: 4fb77ed0241d15338c2574fd22828507c3e40cb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>使用 IoT 閘道將裝置連接到雲端 - 將 SensorTag 連接到 Azure IoT 中樞

> [!NOTE]
> 開始本教學課程之前，請確定您已完成[將 Intel NUC 設定為 IoT 閘道器](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)。 在[將 Intel NUC 設定為 IoT 閘道器](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)中，您會將 Intel NUC 裝置設定為 IoT 閘道器。

## <a name="what-you-will-learn"></a>學習目標

您將學習如何使用 IoT 閘道器，將 Texas Instruments SensorTag (CC2650STK) 連接到 Azure IoT 中樞。 IoT 閘道會將從 SensorTag 收集的溫度和溼度資料傳送到 Azure IoT 中樞。

## <a name="what-you-will-do"></a>將執行的作業

- 建立 IoT 中樞。
- 在 SensorTag 的 IoT 中樞註冊裝置。
- 啟用 IoT 閘道與 SensorTag 之間的連線。
- 執行 BLE 應用程式，將 SensorTag 資料傳送至 IoT 中樞。

## <a name="what-you-need"></a>您需要什麼

- 在[將 Intel NUC 設定為 IoT 閘道器](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)教學課程中，您已將 Intel NUC 設定為 IoT 閘道器。
- * 有效的 Azure 訂用帳戶。 如果您沒有 Azure 帳戶，請花幾分鐘的時間建立[免費的 Azure 試用帳戶](https://azure.microsoft.com/free/)。
- 在主機電腦上執行的 SSH 用戶端。 Windows 上建議使用 PuTTY。 Linux 和 macOS 已隨附 SSH 用戶端。
- 從 SSH 用戶端存取閘道所用的 IP 位址和使用者名稱和密碼。
- 網際網路連線。

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> 您將在這裡為 SensorTag 註冊這個新裝置

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>啟用 IoT 閘道與 SensorTag 之間的連線

本節中，您將執行下列工作：

- 對於藍牙連線取得 SensorTag 的 MAC 位址。
- 初始化從 IoT 閘道器到 SensorTag 的藍牙連線。

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>對於藍牙連線取得 SensorTag 的 MAC 位址

1. 在主機電腦上執行 SSH 用戶端，並連接到 IoT 閘道器。
1. 執行下列命令解除封鎖藍牙︰

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. 在 IoT 閘道上啟動藍牙服務，並執行下列命令進入藍牙介面設定藍牙︰

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. 在藍牙介面執行下列命令，開啟藍牙控制器電源︰

   ```bash
   power on
   ```

   ![使用 bluetoothctl 在 IoT 閘道器上開啟藍牙控制器電源](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. 執行下列命令，開始掃描附近的藍牙裝置︰

   ```bash
   scan on
   ```

   ![使用 bluetoothctl 掃描附近的藍牙裝置](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. 按下 SensorTag 上的配對按鈕。 SensorTag 上的綠色 LED 閃爍。
1. 在藍牙介面中，您應該會看見找到 SensorTag。 記下 SensorTag 的 MAC 位址。 在此範例中，SensorTag 的 MAC 位址是 `24:71:89:C0:7F:82`。
1. 執行下列命令以關閉掃描：

   ```bash
   scan off
   ```

   ![使用 bluetoothctl 停止掃描附近的藍牙裝置](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>初始化從 IoT 閘道器到 SensorTag 的藍牙連線

1. 執行下列命令來連線至 SensorTag：

   ```bash
   connect <MAC address>
   ```

   ![使用 bluetoothctl 連接到 SensorTag](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. 執行下列命令，與 SensorTag 中斷連接並結束藍牙介面︰

   ```bash
   disconnect
   exit
   ```

   ![使用 bluetoothctl 與 SensorTag 中斷連接](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

此時即已成功啟用 SensorTag 與 IoT 閘道之間的連線。

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>執行 BLE 應用程式，將 SensorTag 資料傳送至 IoT 中樞

Bluetooth Low Energy (BLE) 範例應用程式是由 Azure IoT Edge 提供。 範例應用程式會收集 BLE 連線的資料，並將資料傳送至 IoT 中樞。 若要執行範例應用程式，需要：

1. 設定範例應用程式。
1. 在 IoT 閘道器上執行範例應用程式。

### <a name="configure-the-sample-application"></a>設定範例應用程式

1. 執行下列命令，移至範例應用程式的資料夾：

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples/ble_gateway
   ```

1. 執行下列命令來開啟組態檔：

   ```bash
   vi ble_gateway.json
   ```

1. 在組態檔中，輸入下列值︰

   **IoTHubName**：IoT 中樞的名稱。

   **IoTHubSuffix**︰從您記下的裝置連接字串主索引鍵，取得 IoTHubSuffix。 請確定您取得裝置連接字串的主索引鍵，而非 IoT 中樞連接字串的主索引鍵。 裝置連接字串的主索引鍵格式為 `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY`。

   **Transport**：預設值是 `amqp`。 這個值會顯示傳輸期間的通訊協定。 這可以是 `http`、`amqp` 或 `mqtt`。

   **macAddress**：您對於 SensorTag 記下的 MAC 位址。

   **deviceID**︰ 您建立 IoT 中樞建立的裝置本身的識別碼。

   **deviceKey**：裝置連接字串的主索引鍵。

   ![完成 BLE 範例應用程式的組態檔](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. 按下 `ESC` 並輸入 `:wq` 儲存檔案。

### <a name="run-the-sample-application"></a>執行範例應用程式

1. 確定 SensorTag 已開啟。
1. 執行以下命令：

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>後續步驟

[搭配 Azure IoT Edge 使用 IoT 閘道進行感應器資料轉換](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)
