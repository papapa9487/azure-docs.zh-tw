---
title: "Raspberry Pi 至 cloud (Python) - 將 Raspberry Pi 連線至 Azure IoT 中樞 | Microsoft Docs"
description: "了解在本教學課程中如何設定及連線 Raspberry Pi 至 Azure IoT 中樞，讓 Raspberry Pi 將資料傳送到 Azure 雲端平台。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot raspberry pi, raspberry pi iot 中樞, raspberry pi 將資料傳送至雲端, raspberry pi 至 cloud"
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/31/2017
ms.author: xshi
ms.openlocfilehash: 1b1a9dc960846cbc15ce09d0fd106e1492937439
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-python"></a>將 Raspberry Pi 連線至 Azure IoT Hub (Python)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

在本教學課程中，您會開始了解執行 Raspbian 的 Raspberry Pi 在使用方面的基本知識。 接著會了解如何使用 [Azure IoT 中樞](iot-hub-what-is-iot-hub.md)讓您的裝置順暢地與雲端連線。 如需 Windows 10 IoT 核心範例，請移至 [Windows 開發人員中心](http://www.windowsondevices.com/)。

還沒有套件嗎？ 試用 [Raspberry Pi 線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)。 或在[這裡](https://azure.microsoft.com/develop/iot/starter-kits)購買新的套件。

## <a name="what-you-do"></a>您要做什麼

* 建立 IoT 中樞。
* 在 IoT 中樞對於 Pi 註冊裝置。
* 設定 Raspberry Pi。
* 在 Pi 上執行範例應用程式，將感應器資料傳送至 IoT 中樞。

將 Raspberry Pi 連接至您建立的 IoT 中樞。 然後，在 Pi 上執行範例應用程式，以收集 BME280 感應器中的溫度和溼度資料。 最後，將感應器資料傳送至 IoT 中樞。

## <a name="what-you-learn"></a>您學到什麼

* 如何建立 Azure IoT 中樞，並取得新的裝置連接字串。
* 如何連接 Pi 與 BME280 感應器。
* 如何在 Pi 上執行範例應用程式來收集感應器資料。
* 如何將感應器資料傳送至 IoT 中樞。

## <a name="what-you-need"></a>您需要什麼

![您需要什麼](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Raspberry Pi 2 或 Raspberry Pi 3 電路板。
* 有效的 Azure 訂用帳戶。 如果您沒有 Azure 帳戶，請花幾分鐘的時間建立[免費的 Azure 試用帳戶](https://azure.microsoft.com/free/)。
* 連接至 Pi 的監視器、 USB 鍵盤和滑鼠。
* 執行 Windows 或 Linux 的 Mac 或 PC。
* 網際網路連線。
* 16 GB 以上的 microSD 記憶卡。
* 一個 USB-SD 配接器或 microSD 記憶卡，以將作業系統映像燒錄到 microSD 記憶卡中。
* 具備 6 英呎 micro USB 纜線的 5V 2A 電源供應器。

下列項目是選用項目︰

* 組裝的 Adafruit BME280 溫度、壓力溼度感應器。
* 麵包板。
* 6 條 F/M 跳線。
* 1 顆漫射型 10 mm LED。


> [!NOTE] 
這些項目都是選用項目，因為程式碼範例支援模擬感應器資料。


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>設定 Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>安裝 Pi 的 Raspbian 作業系統

準備好用來安裝 Raspbian 映像的 microSD 記憶卡。

1. 下載 Raspbian。
   1. [下載具備 Desktop 的 Raspbian Jessie](https://www.raspberrypi.org/downloads/raspbian/) (.zip 檔案)。
   1. 將 Raspbian 映像解壓縮到您電腦上的資料夾。
1. 將 Raspbian 安裝到 microSD 記憶卡。
   1. [下載並安裝 Etcher SD 記憶卡燒錄器公用程式](https://etcher.io/)。
   1. 執行 Etcher 並選取您在步驟 1 中解壓縮的 Raspbian 映像。
   1. 選取 microSD 記憶卡磁碟機。 注意：Etcher 可能已經選取正確的磁碟機。
   1. 按一下 [Flash] 以將 Raspbian 安裝到 microSD 記憶卡。
   1. 安裝完成時，請將 microSD 記憶卡從電腦移除。 您可以放心地直接移除 microSD 記憶卡，因為 Etcher 會在完成時自動退出或卸載 microSD 記憶卡。
   1. 將 microSD 記憶卡插入 Pi。

### <a name="enable-ssh-and-i2c"></a>啟用 SSH 和 I2C

1. 將 Pi 連接至監視器、鍵盤和滑鼠，並啟動 Pi，然後使用使用者名稱 `pi` 和密碼 `raspberry` 登入 Raspbian。
1. 按一下 Raspberry 圖示 > [偏好設定] > [Raspberry Pi 組態]。

   ![[Raspbian 偏好設定] 功能表](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. 在 介面索引標籤上，將 I2C 和 SSH 設定為 啟用，然後按一下確定。 如果您沒有實體感應器，而且想要使用模擬的感應器資料，這便是選擇性步驟。

   ![在 Raspberry Pi 上啟用 I2C 和 SSH](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
若要啟用 SSH 和 I2C，您可以在 [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) 和 [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md) 找到更多參考文件。

### <a name="connect-the-sensor-to-pi"></a>將感應器連接至 Pi

使用麵包板和跳線將 LED 和 BME280 連接至 Pi，如下所示。 如果沒有感應器，請[略過本節](#connect-pi-to-the-network)。

![Raspberry Pi 和感應器連接](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

BME280 感應器可以收集溫度和溼度資料。 而如果裝置與雲端之間有通訊，LED 將會閃爍。 

針對感應器針腳，請使用下列接線方式：

| 啟動 (感應器和 LED)     | 結束 (電路版)            | 纜線顏色   |
| -----------------------  | ---------------------- | ------------: |
| VDD (針腳 5G)             | 3.3V PWR (針腳 1)       | 白色纜線   |
| GND (針腳 7G)             | GND (針腳 6)            | 棕色纜線   |
| SDI (針腳 10G)            | I2C1 SDA (針腳 3)       | 紅色纜線     |
| SCK (針腳 8G)             | I2C1 SCL (針腳 5)       | 橘色纜線  |
| LED VDD (針腳 18F)        | GPIO 24 (針腳 18)       | 白色纜線   |
| LED GND (針腳 17F)        | GND (針腳 20)           | 黑色纜線   |

按一下以檢視 [Raspberry Pi 2 和 3 針腳對應](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi)進行參考。

將 BME280 成功連接至 Raspberry Pi 之後，應該如下圖所示。

![連接的 Pi 和 BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>將 Pi 連線到網路

透過 micro USB 纜線和電源供應器來開啟 Pi。 使用乙太網路纜線將 Pi 連接到有線網路，或遵循來自 Raspberry Pi Foundation 的[指示](https://www.raspberrypi.org/learning/software-guide/wifi/)，將 Pi 連接到無線網路。 在 Pi 成功連線到網路之後，您需要記下 [Pi 的 IP 位址](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)。

![已連接到有線網路](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> 請確定 Pi 是連接到與您電腦相同的網路。 例如，如果您的電腦連線到無線網路，而 Pi 連線到有線網路，您可能不會在 devdisco 輸出中看到 IP 位址。

## <a name="run-a-sample-application-on-pi"></a>在 Pi 上執行範例應用程式

### <a name="install-the-prerequisite-packages"></a>安裝必要條件套件

使用下列其中一個 SSH 用戶端，從主機電腦連接到 Raspberry Pi。
   
   **Windows 使用者**
   1. 下載並安裝適用於 Windows 的 [PuTTY](http://www.putty.org/)。 
   1. 將 Pi 的 IP 位址複製到 [主機名稱] 或 [IP 位址] 區段，並且選取 SSH 作為連線類型。
   
   
   **Mac 和 Ubuntu 使用者**
   
   在 Ubuntu 或 macOS 上使用內建的 SSH 用戶端。 您可能需要執行 `ssh pi@<ip address of pi>`，才能透過 SSH 來連線 Pi。
   > [!NOTE] 
   預設使用者名稱為 `pi`，密碼為 `raspberry`。


### <a name="configure-the-sample-application"></a>設定範例應用程式

1. 執行下列命令，複製範例應用程式：

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-python-raspberrypi-client-app.git
   ```
1. 執行下列命令以開啟組態檔：

   ```bash
   cd iot-hub-python-raspberrypi-client-app
   nano config.py
   ```

   此檔案中有 5 個巨集可供設定。 第一個是 `MESSAGE_TIMESPAN`，這可定義傳送至雲端的兩個訊息之間相隔的時間間隔 (以毫秒為單位)。 第二個是 `SIMULATED_DATA`，這是是否使用模擬感應器資料的布林值。 `I2C_ADDRESS` 是 BME280 感應器連線的 I2C 位址。 `GPIO_PIN_ADDRESS` 是 LED 的 GPIO 位址。 最後一個是 `BLINK_TIMESPAN`，可定義 LED 開啟時以毫秒為單位的時間範圍。

   如果**沒有感應器**，請將 `SIMULATED_DATA` 值設定為 `True`，使範例應用程式建立和使用模擬感應器資料。

1. 按下 [Control-O] > 輸入 > [Control-X] 儲存並結束。

### <a name="build-and-run-the-sample-application"></a>建置並執行範例應用程式

1. 執行下列命令，建置範例應用程式。 由於 Azure IoT SDK for Python 是 Azure IoT 裝置 C SDK 之上的包裝函式，如果您想要或需要從來源程式碼產生 Python 程式庫，則必須編譯 C 程式庫。

   ```bash
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```
   > [!NOTE] 
   您也可以執行 `sudo ./setup.sh [--python-version|-p] [2.7|3.4|3.5]` 指定想要的版本。 如果執行不含參數的指令碼，指令碼會自動偵測已安裝的 python 版本 (搜尋序列 2.7->3.4->3.5)。 請確定 Python 版本在建置和執行期間保持一致。 
   
   > [!NOTE] 
   在 RAM 小於 1GB 的 Linux 裝置上建置 Python 用戶端程式庫 (iothub_client.so) 時，您可能會在建置 iothub_client_python.cpp 時看到組建卡在 98%，如下所示 `[ 98%] Building CXX object python/src/CMakeFiles/iothub_client_python.dir/iothub_client_python.cpp.o`。 如果遇到此問題，請在該段期間內在另一個終端機視窗中使用 `free -m command` 來檢查裝置的記憶體耗用量。 如果在編譯 iothub_client_python.cpp 檔案時記憶體不足，則需要暫時增加交換空間來取得更多可用記憶體，才能成功建置 Python 用戶端裝置 SDK 程式庫。
   
1. 執行下列命令，執行範例應用程式：

   ```bash
   python app.py '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   確定複製裝置連接字串，並貼到單引號中。 如果您使用 python 3，則可以使用命令 `python3 app.py '<your Azure IoT hub device connection string>'`。


   您應該會看見下列輸出，顯示傳送至 IoT 中樞的感應器資料和訊息。

   ![輸出 - 從 Raspberry Pi 傳送至 IoT 中樞的感應器資料](media/iot-hub-raspberry-pi-kit-c-get-started/success.png
)

## <a name="next-steps"></a>後續步驟

您已執行範例應用程式收集感應器資料並傳送至 IoT 中樞。 若要查看 Raspberry Pi 傳送給 IoT 中樞的訊息，或者在命令列介面中將訊息傳送給 Raspberry Pi，請參閱[使用 iothub-explorer 管理雲端裝置訊息教學課程](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
