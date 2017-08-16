---
title: "M0 連線到雲端：將 Feather M0 WiFi 連線到 Azure IoT 中樞 | Microsoft Docs"
description: "了解如何設定及連線 Adafruit Feather M0 WiFi 和 Azure IoT 中樞，在本教學課程中將資料傳送到 Azure 雲端平台。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/16/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: 8eee4b2eea165176bddf0896685636bbac4db321
ms.contentlocale: zh-tw
ms.lasthandoff: 07/13/2017

---

# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>將 Adafruit Feather M0 WiFi 連線到位於雲端的 Azure IoT 中樞
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![BME280、Feather M0 WiFi 與 IoT 中樞之間的連線](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

在本教學課程中，您一開始會先了解使用 Arduino 面板的基本知識。 接著會了解如何使用 [Azure IoT 中樞](iot-hub-what-is-iot-hub.md)讓您的裝置順暢地與雲端連線。

## <a name="what-you-do"></a>您要做什麼

將 Adafruit Feather M0 WiFi 連線到您將建立的 IoT 中樞。 然後，在 M0 WiF 上執行範例應用程式，以收集 BME280 感應器中的溫度和溼度資料。 最後，將感應器資料傳送至 IoT 中樞。


## <a name="what-you-learn"></a>您學到什麼

* 如何建立 IoT 中樞並為 Feather M0 WiFi 註冊裝置。
* 如何連接 Feather M0 WiFi 與感應器和電腦
* 如何在 Feather M0 WiFi 上執行範例應用程式來收集感應器資料
* 如何將感應器資料傳送至 IoT 中樞

## <a name="what-you-need"></a>您需要什麼

![本教學課程所需的零件](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

若要完成此作業，您需要 Feather M0 WiFi 入門套件中的下列零件：

* Feather M0 WiFi 面板
* Micro USB 轉 Type A 的 USB 纜線

您的開發環境還需要下列事項：

* 有效的 Azure 訂用帳戶。 如果您沒有 Azure 帳戶，請花幾分鐘的時間[建立免費的 Azure 試用帳戶](https://azure.microsoft.com/free/)。
* 執行 Windows 或 Ubuntu 的 Mac 或 PC。
* 供 Feather M0 WiFi 連線的無線網路。
* 用來下載設定工具的網際網路連線。
* [Arduino IDE](https://www.arduino.cc/en/main/software) 1.6.8 版或更新版本。 舊版無法與 Azure IoT 中樞程式庫搭配運作。

如果您沒有感應器，下列項目可供選用。 您也可以選擇使用模擬的感應器資料：

* BME280 溫度和溼度感應器
* 麵包板
* M/M 跳線

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>連接 Feather M0 WiFi 與感應器和電腦
在本節中，您可以將感應器連接至您的面板。 然後您可以將您的裝置插入電腦持續使用。

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>將 DHT22 溫度和溼度感應器連接至 Feather M0 WiFi

使用試驗電路板 和跳線來進行連接。 如果您沒有感應器，請略過本節，因為您可以改為使用模擬的感應器資料。

![連接參考](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


針對感應器針腳，請使用下列接線方式：


| 開始 (感應器)           | 結束 (電路版)            | 纜線顏色   |
| -----------------------  | ---------------------- | ------------: |
| VDD (針腳 27A)            | 3V (針腳 3A)            | 紅色纜線     |
| GND (針腳 29A)            | GND (針腳 6A)           | 黑色纜線   |
| SCK (針腳 30A)            | SCK (針腳 12A)          | 黃色纜線  |
| SDO (針腳 31A)            | MI (針腳 14A)           | 白色纜線   |
| SDI (針腳 32A)            | M0 (針腳 13A)           | 藍色纜線    |
| cs (針腳 33A)             | GPIO 5 (針腳 15J)       | 橘色纜線  |

如需詳細資訊，請參閱 [Adafruit BME280 Humidity + Barometric Pressure + Temperature Sensor Breakout](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) (Adafruit BME280 溼度 + 氣壓 + 溫度感應器分組) 和 [Adafruit Feather M0 WiFi pinouts](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts) (Adafruit Feather M0 WiFi 接腳圖)。



現在，Feather M0 WiFi 應該已經和作用中的感應器連接。

![連接 DHT22 與 Feather Huzzah](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>將 Feather M0 WiFi 連接到電腦

請使用 Micro USB 轉 Type A 的 USB 纜線將 Feather M0 WiFi 連接到電腦，如下所示：

![將 Feather Huzzah 連接到電腦](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>新增序列埠權限 (僅限 Ubuntu)

如果您使用 Ubuntu，請確定您具有操作 Feather M0 WiFi 的 USB 連接埠所需的權限。 若要新增序列埠權限，請遵循下列步驟：


1. 在終端機中執行下列命令：

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   您會得到下列其中一個輸出︰

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   在輸出中，請注意 `uucp` 或 `dialout` 是 USB 連接埠的群組擁有者名稱。

2. 請執行下列命令，將使用者新增至群組︰

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` 是您在上一個步驟中取得的群組擁有者名稱。 `<username>` 是 Ubuntu 使用者名稱。

3. 登出 Ubuntu，然後再次登入，以顯示變更。

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>收集感應器資料，並將它傳送至 IoT 中樞

在本節中，您可以在 Feather M0 WiFi 上部署和執行範例應用程式。 範例應用程式會讓 LED 在 Feather M0 WiFi 上閃爍。 然後，將從 BME280 感應器收集的溫度和溼度資料傳送至 IoT 中樞。

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>從 GitHub 取得範例應用程式，並準備 Arduino IDE。

範例應用程式會裝載在 GitHub 上。 請從 GitHub 複製包含範例應用程式的範例存放庫。 若要複製範例存放庫，請遵循下列步驟︰

1. 開啟命令提示字元或終端機視窗。

2. 移至想要儲存範例應用程式的資料夾。
3. 執行以下命令：

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>在 Arduino IDE 中安裝 Feather M0 WiFi 套件

1. 開啟範例應用程式儲存所在的資料夾。

2. 在 Arduino IDE 中開啟應用程式資料夾中的 app.ino 檔案。

   ![在 Arduino IDE 中開啟範例應用程式](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. 按一下 [檔案] > [喜好設定] (Windows/Linux) 或 [Arduino] > [喜好設定] (Mac)，然後將下列連結複製貼入 Arduino IDE 喜好設定的 [Additional Boards Manager URLs] (其他面板管理員 URL) 選項。
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json, https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. 按一下 [工具] > [面板] > [面板管理員]，然後安裝 `Arduino SAMD Boards` 版本 `1.6.2` 或更新版本。 

1. 然後在相同的視窗中，安裝 `Adafruit SAMD Boards` 套件以新增面板檔案定義。

   ![已安裝 esp8266 套件](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. 按一下 [工具] > [電路板] > [Adafruit M0 WiFi]。

5. 安裝驅動程式 (僅限 Windows)。 當您插入 Feather M0 WiFi 時，您可能需要安裝驅動程式。 按一下[網頁上的下載連結](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe)下載驅動程式安裝程式。 請遵循步驟安裝想要的驅動程式。

### <a name="install-necessary-libraries"></a>安裝必要的程式庫

1. 在 Arduino IDE 中，按一下 [草圖] > [包含程式庫] > [管理程式庫]。

2. 逐一搜尋下列程式庫名稱。 為找到的每個程式庫，按一下 [安裝]：

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. 手動安裝 `Adafruit_WINC1500`。 移至[此網站](https://github.com/adafruit/Adafruit_WINC1500)並按一下 [複製或下載] > [下載 ZIP]。 然後在 Arduino IDE 中，移至 [草圖] > [包含程式庫] > [新增 .zip 程式庫]，然後新增 ZIP 檔案。

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>如果沒有實體的 BME280 感應器，請使用範例應用程式。

如果您沒有實體的 BME280 感應器，範例應用程式可以模擬溫度和溼度資料。 若要設讓範例應用程式以使用模擬資料，請遵循下列步驟︰

1. 開啟 `app` 資料夾中的 `config.h` 檔案。

2. 找出下面這行程式碼，並將值從 `false` 變更為 `true`：

   ```c
   define SIMULATED_DATA true
   ```
   ![設定範例應用程式以使用模擬資料](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. 使用 `Control-s` 儲存檔案。

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>將範例應用程式部署至 Feather M0 WiFi

1. 在 Arduino IDE 中，按一下 [工具] > [連接埠]，然後按一下 Feather M0 WiFi 的序列埠。

2. 按一下 [草圖] > [上傳]，以建置範例應用程式並將其部署至 Feather M0 WiFi。

### <a name="enter-your-credentials"></a>輸入認證

上傳程序成功完成後，請遵循這些步驟來輸入認證：

1. 在 Arduino IDE 中，按一下 [工具] > [序列監視器]。

2. 在序列監視器視窗的右下角，在左邊的下拉式清單中選取 [No line ending] (沒有行尾結束符號)。
3. 在右邊的下拉式清單中選取 [115200 傳輸速率]。
4. 如果系統要求您提供，請在上方的輸入方塊中輸入下列資訊，然後按一下 [傳送]：

   * Wi-Fi SSID
   * Wi-Fi 密碼
   * 裝置連接字串

> [!Note]
> 認證資訊會儲存在 Feather M0 WiFi 的 EEPROM 中。 如果您按一下 Feather M0 WiFi 電路板上的重設按鈕，範例應用程式會詢問您是否要清除資訊。 輸入 `Y` 清除資訊。 系統會再次要求您提供資訊。

### <a name="verify-that-the-sample-application-is-running-successfully"></a>確認範例應用程式已成功執行

如果您在序列監視器視窗看到下列輸出，並在 Feather M0 WiFi 上看到閃爍的 LED，則表示範例應用程式已成功執行：

![Arduino IDE 中的最終輸出](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>後續步驟

Feather M0 WiFi 已成功連線到 IoT 中樞，並將擷取到的感應器資料傳送至 IoT 中樞。 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


