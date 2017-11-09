---
title: "使用 C 將 Raspberry Pi 連線到 Azure IoT 套件來支援韌體更新 | Microsoft Docs"
description: "使用 Raspberry Pi 3 和 Azure IoT 套件的 Microsoft Azure IoT 入門套件。 使用 C 將 Raspberry Pi 連線到遠端監視解決方案、將遙測從感應器傳送到雲端，並執行遠端韌體更新。"
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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 8160752b0116c3ef3e6b6ab7920bb35e471f180b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-c"></a>將 Raspberry Pi 3 連線到遠端監視解決方案，並且使用 C 來啟用遠端韌體更新

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

本教學課程將示範如何使用 Raspberry Pi 3 的 Microsoft Azure IoT 入門套件進行︰

* 開發可與雲端通訊的溫度與溼度讀取器。
* 啟用及執行遠端韌體更新，來更新 Raspberry Pi 上的用戶端應用程式。

教學課程會使用：

* Raspbian OS、C 程式設計語言和 Microsoft Azure IoT SDK for C 來實作範例裝置。
* IoT 套件遠端監視預先設定解決方案作為以雲端為基礎的後端。

## <a name="overview"></a>概觀

在本教學課程中，您會完成下列步驟：

* 將遠端監視預先設定解決方案的執行個體部署至您的 Azure 訂用帳戶。 這個步驟會自動部署並設定多個 Azure 服務。
* 設定您的裝置和感應器，以便與您的電腦和遠端監視解決方案進行通訊。
* 更新範例裝置程式碼以連線到遠端監視解決方案，並傳送您可以在解決方案儀表板上檢視的遙測。
* 使用範例裝置程式碼來更新用戶端應用程式。

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> 遠端監視解決方案會佈建一組 Azure 訂用帳戶中的 Azure 服務。 部署會反映實際的企業架構。 若要避免不必要的 Azure 耗用量費用，當您使用完 azureiotsuite.com 中預先設定解決方案的執行個體時，請將它刪除。 如果您還需要預先設定的解決方案，可以輕鬆地將它重新建立。 如需將遠端監視解決方案執行時的耗用量降低的詳細資訊，請參閱[設定 Azure IoT 套件預先設定的解決方案以供示範][lnk-demo-config]。

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>下載並設定範例

您現在可以在 Raspberry Pi 上將遠端監視用戶端應用程式進行下載及設定。

### <a name="clone-the-repositories"></a>複製存放庫

如果您尚未這麼做，請在 Pi 上執行下列命令來複製所需的存放庫︰

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>更新裝置連接字串

使用下列命令，將 **nano** 編輯器中的範例組態檔開啟︰

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

將預留位置值取代為本教學課程開頭所建立及儲存的裝置識別碼和 IoT 中樞。

當您完成時，deviceinfo 檔案的內容看起來應該如下列範例︰

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

儲存您的變更 (**Ctrl-O**、**Enter**) 並結束編輯器 (**Ctrl-X**)。

## <a name="build-the-sample"></a>建置範例

如果您尚未這麼做，請在 Pi 上的終端機執行下列命令，安裝 Microsoft Azure IoT Device SDK for C 的必要條件套件︰

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

您現在可以在 Raspberry Pi 上建置範例解決方案︰

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
```

您現在可以在 Raspberry Pi 上執行範例程式。 輸入命令：

  ```sh
  sudo ~/cmake/remote_monitoring/remote_monitoring
  ```

下列範例輸出是您在 Raspberry Pi 的命令提示字元所看到的輸出範例︰

![Raspberry Pi 應用程式的輸出][img-raspberry-output]

按下 **CTRL-C** 可隨時結束程式。

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. 在解決方案儀表板中，按一下 [裝置] 以造訪 [裝置] 頁面。 在 [裝置清單] 中選取您的 Raspberry Pi。 然後選擇 [方法]：

    ![在儀表板中列出裝置][img-list-devices]

1. 在 [叫用方法] 頁面上，選擇 [方法] 下拉式清單中的 [InitiateFirmwareUpdate]。

1. 在 [FWPackageURI] 欄位中，輸入 **https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit/raw/master/advanced/2.0/package/remote_monitoring.zip**。 此封存檔包含 2.0 版韌體的實作。

1. 選擇 [InvokeMethod]。 Raspberry Pi 上的應用程式會將通知傳回解決方案儀表板。 接著，它會下載新版的韌體來啟動韌體更新程序︰

    ![顯示方法歷程記錄][img-method-history]

## <a name="observe-the-firmware-update-process"></a>觀察軔體更新程序

您可以觀察在裝置上執行時的軔體更新程序，方法為檢視解決方案儀表板中的報告內容︰

1. 您可以在 Raspberry Pi 上的更新程序檢視進度︰

    ![顯示更新進度][img-update-progress]

    > [!NOTE]
    > 更新完成時，會以無訊息模式將遠端監視應用程式重新啟動。 使用 `ps -ef` 命令來確認它正在執行。 如果您想要終止程序，請使用 `kill` 命令與程序識別碼。

1. 您可以在解決方案入口網站中，檢視如裝置所報告的韌體更新狀態。 下列螢幕擷取畫面會顯示更新程序每個階段的狀態和持續時間，以及新的韌體版本︰

    ![顯示作業狀態][img-job-status]

    如果您瀏覽回到儀表板，可以確認裝置仍在將下列韌體更新傳送給遙測。

> [!WARNING]
> 如果讓遠端監視解決方案繼續在您的 Azure 帳戶中執行，您需支付其執行期間的費用。 如需將遠端監視解決方案執行時的耗用量降低的詳細資訊，請參閱[設定 Azure IoT 套件預先設定的解決方案以供示範][lnk-demo-config]。 使用完畢時，從您的 Azure 帳戶將預先設定的解決方案刪除。

## <a name="next-steps"></a>後續步驟

請瀏覽 [Azure IoT 開發人員中心](https://azure.microsoft.com/develop/iot/)，取得更多 Azure IoT 的相關範例和文件。


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md