---
title: "使用 Intel NUC 將閘道連線到 Azure IoT 套件 | Microsoft Docs"
description: "使用 Microsoft IoT Commercial Gateway Kit 和預先設定的遠端監視解決方案。 使用 Azure IoT Edge 閘道來連線到遠端監視解決方案、將模擬的遙測資料傳送到雲端，並回應解決方案儀表板所叫用的方法。"
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
ms.openlocfilehash: 9ed57d3c23e2adbd42c054f33c8ed46e3d6c9792
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-simulated-telemetry"></a>將 Azure IoT Edge 閘道連線到預先設定的遠端監視解決方案，並傳送模擬的遙測資料

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

本教學課程說明如何使用 Azure IoT Edge 來模擬溫度和溼度資料，以傳送至預先設定的遠端監視解決方案。 教學課程會使用：

- Azure IoT Edge，以實作範例閘道。
- IoT 套件遠端監視預先設定解決方案作為以雲端為基礎的後端。

## <a name="overview"></a>概觀

在本教學課程中，您會完成下列步驟：

- 將遠端監視預先設定解決方案的執行個體部署至您的 Azure 訂用帳戶。 這個步驟會自動部署並設定多個 Azure 服務。
- 設定 Intel NUC 閘道裝置，以便與您的電腦和遠端監視解決方案進行通訊。
- 將 IoT Edge 閘道設定為傳送模擬的遙測資料，供您在解決方案儀表板上檢視。

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> 遠端監視解決方案會佈建一組 Azure 訂用帳戶中的 Azure 服務。 部署會反映實際的企業架構。 若要避免不必要的 Azure 耗用量費用，當您使用完 azureiotsuite.com 中預先設定解決方案的執行個體時，請將它刪除。 如果您還需要預先設定的解決方案，可以輕鬆地將它重新建立。 如需將遠端監視解決方案執行時的耗用量降低的詳細資訊，請參閱[設定 Azure IoT 套件預先設定的解決方案以供示範][lnk-demo-config]。

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

重複上述步驟來新增第二個裝置，並讓此裝置使用 **device02** 之類的裝置識別碼。 此範例會將閘道中兩個模擬裝置的資料傳送到遠端監控解決方案。

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>建置自訂的 IoT Edge 模組

您現在可以建置自訂的 IoT Edge 模組，以讓閘道傳送訊息給遠端監視解決方案。 如需如何設定閘道和 IoT Edge 模組的詳細資訊，請參閱 [Azure IoT Edge 概念][lnk-gateway-concepts]。

使用下列命令，從 GitHub 下載自訂 IoT Edge 模組的原始程式碼︰

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

使用下列命令來建置自訂的 IoT Edge 模組︰

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

建置指令碼會將 libsimulator.so 自訂 IoT Edge 模組放置在建置資料夾中。

## <a name="configure-and-run-the-iot-edge-gateway"></a>設定和執行 IoT Edge 閘道

您現在可以將 IoT Edge 閘道設定為將模擬的遙測資料傳送到遠端監視儀表板。 如需如何設定閘道和 IoT Edge 模組的詳細資訊，請參閱 [Azure IoT Edge 概念][lnk-gateway-concepts]。

> [!TIP]
> 在本教學課程中，您可以使用 Intel NUC 上的標準 `vi` 文字編輯器。 如果您之前未使用過 `vi`，請先完成入門教學課程 (例如 [Unix - The vi Editor Tutorial][lnk-vi-tutorial] ) 以熟悉這個編輯器。 或者，您可以使用 `smart install nano -y` 命令，來安裝更方便使用的 [nano](https://www.nano-editor.org/) 編輯器。

使用下列命令，將 **vi** 編輯器中的範例組態檔開啟︰

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator/remote_monitoring.json
```

在 IoTHub 模組的組態中找出下列幾行︰

```json
"args": {
  "IoTHubName": "<<Azure IoT Hub Name>>",
  "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
  "Transport": "http"
}
```

將預留位置值取代為本教學課程開頭所建立及儲存的 IoT 中樞資訊。 IoTHubName 的值會像 **yourrmsolution37e08**，IoTSuffix 的值則通常是 **azure-devices.net**。

在對應模組的組態中找出下列幾行︰

```json
args": [
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  },
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

將 **deviceID** 和 **deviceKey** 預留位置取代為您先前在遠端監視解決方案中所建立之兩個裝置的識別碼和金鑰。

儲存您的變更。

您現在可以使用下列命令來執行 IoT Edge 閘道︰

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
/usr/share/azureiotgatewaysdk/samples/simulated_device_cloud_upload/simulated_device_cloud_upload remote_monitoring.json
```

此閘道會在 Intel NUC 上啟動，並將模擬的遙測資料傳送到遠端監視解決方案︰

![IoT Edge 閘道會產生模擬的遙測資料][img-simulated telemetry]

按下 **CTRL-C** 可隨時結束程式。

## <a name="view-the-telemetry"></a>檢視遙測資料

IoT Edge 閘道現在會將磨你的遙測資料傳送至遠端監視解決方案。 您可以在解決方案儀表板上檢視遙測資料。

- 瀏覽至解決方案儀表板。
- 在 [要檢視的裝置] 下拉式清單中，選取您在閘道中所設定之兩個裝置的其中一個。
- 來自閘道裝置的遙測資料會顯示在儀表板上。

![顯示模擬閘道裝置中的遙測資料][img-telemetry-display]

> [!WARNING]
> 如果讓遠端監視解決方案繼續在您的 Azure 帳戶中執行，您需支付其執行期間的費用。 如需將遠端監視解決方案執行時的耗用量降低的詳細資訊，請參閱[設定 Azure IoT 套件預先設定的解決方案以供示範][lnk-demo-config]。 使用完畢時，從您的 Azure 帳戶將預先設定的解決方案刪除。

## <a name="next-steps"></a>後續步驟

請瀏覽 [Azure IoT 開發人員中心](https://azure.microsoft.com/develop/iot/)，取得更多 Azure IoT 的相關範例和文件。

[img-simulated telemetry]: ./media/iot-suite-gateway-kit-get-started-simulator/appoutput.png

[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-simulator/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started