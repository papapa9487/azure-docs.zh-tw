---
title: "使用 C 搭配真實感應器將 Raspberry Pi 連線到 Azure IoT 套件來支援韌體更新 | Microsoft Docs"
description: "使用 Raspberry Pi 3 和 Azure IoT 套件的 Microsoft Azure IoT 入門套件。 使用 C 將 Raspberry Pi 連線到遠端監視解決方案、將遙測從感應器傳送到雲端，並回應解決方案儀表板所叫用的方法。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 418108e8236518d2671abca0f06f1ae8159d6442
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-c"></a>將 Raspberry Pi 3 連線到遠端監視解決方案，並且使用 C 從真實感應器傳送遙測

[!INCLUDE [iot-suite-raspberry-pi-kit-selector](../../includes/iot-suite-raspberry-pi-kit-selector.md)]

本教學課程會示範如何使用 Raspberry Pi 3 的 Microsoft Azure IoT 入門套件來開發可與雲端通訊的溫度與溼度讀取器。 教學課程會使用：

- Raspbian OS、C 程式設計語言和 Microsoft Azure IoT SDK for C 來實作範例裝置。
- IoT 套件遠端監視預先設定解決方案作為以雲端為基礎的後端。

## <a name="overview"></a>概觀

在本教學課程中，您會完成下列步驟：

- 將遠端監視預先設定解決方案的執行個體部署至您的 Azure 訂用帳戶。 這個步驟會自動部署並設定多個 Azure 服務。
- 設定您的裝置和感應器，以便與您的電腦和遠端監視解決方案進行通訊。
- 更新範例裝置程式碼以連線到遠端監視解決方案，並傳送您可以在解決方案儀表板上檢視的遙測。

[!INCLUDE [iot-suite-raspberry-pi-kit-prerequisites](../../includes/iot-suite-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> 遠端監視解決方案會佈建一組 Azure 訂用帳戶中的 Azure 服務。 部署會反映實際的企業架構。 若要避免不必要的 Azure 耗用量費用，當您使用完 azureiotsuite.com 中預先設定解決方案的執行個體時，請將它刪除。 如果您還需要預先設定的解決方案，可以輕鬆地將它重新建立。 如需將遠端監視解決方案執行時的耗用量降低的詳細資訊，請參閱[設定 Azure IoT 套件預先設定的解決方案以供示範][lnk-demo-config]。

[!INCLUDE [iot-suite-raspberry-pi-kit-view-solution](../../includes/iot-suite-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>下載並設定範例

您現在可以在 Raspberry Pi 上將遠端監視用戶端應用程式進行下載及設定。

### <a name="clone-the-repositories"></a>複製存放庫

如果您尚未這麼做，請在 Pi 上的終端機執行下列命令來複製所需的存放庫︰

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
git clone --recursive https://github.com/WiringPi/WiringPi.git
```

### <a name="update-the-device-connection-string"></a>更新裝置連接字串

使用下列命令，將 **nano** 編輯器中的範例來源檔案開啟︰

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/remote_monitoring/remote_monitoring.c
```

找出下列幾行：

```c
static const char* deviceId = "[Device Id]";
static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
```

將預留位置值取代為本教學課程開頭所建立及儲存的裝置和 IoT 中樞。 儲存您的變更 (**Ctrl-O**、**Enter**) 並結束編輯器 (**Ctrl-X**)。

## <a name="build-the-sample"></a>建置範例

請在 Pi 上的終端機執行下列命令，安裝 Microsoft Azure IoT Device SDK for C 的必要條件套件︰

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

您現在可以在 Raspberry Pi 上建置更新的範例解決方案︰

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
```

您現在可以在 Raspberry Pi 上執行範例程式。 輸入命令：

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

下列範例輸出是您在 Raspberry Pi 的命令提示字元所看到的輸出範例︰

![Raspberry Pi 應用程式的輸出][img-raspberry-output]

按下 **CTRL-C** 可隨時結束程式。

[!INCLUDE [iot-suite-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>後續步驟

請瀏覽 [Azure IoT 開發人員中心](https://azure.microsoft.com/develop/iot/)，取得更多 Azure IoT 的相關範例和文件。

[img-raspberry-output]: ./media/iot-suite-raspberry-pi-kit-c-get-started-basic/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
