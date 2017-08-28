---
title: "ESP8266 到雲端 - 將 Sparkfun ESP8266 Thing Dev 連接到 Azure IoT 中樞 | Microsoft Docs"
description: "了解如何設定及連線 Sparkfun ESP8266 Thing Dev 和 Azure IoT 中樞，在本教學課程中將資料傳送到 Azure 雲端平台。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 587fe292-9602-45b4-95ee-f39bba10e716
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 557f0cdf375b345e0dbe0526f5a5bd3c050dec38
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>將 Sparkfun ESP8266 Thing Dev 連接到雲端的 Azure IoT 中樞

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![DHT22、Thing Dev 及 IoT 中樞之間的連接](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>將執行的作業

將 Sparkfun ESP8266 Thing Dev 連接到您將建立的 IoT 中樞。 然後，在 ESP8266 上執行範例應用程式，以收集 DHT22 感應器中的溫度和溼度資料。 最後，將感應器資料傳送至 IoT 中樞。

> [!NOTE]
> 如果您使用其他 ESP8266 電路版，仍舊可以遵循下列步驟來將它連線到 IoT 中樞。 根據您使用的 ESP8266 電路板，您可能需要重新設定 `LED_PIN`。 例如，如果您使用 AI-Thinker 的 ESP8266，您可以將它從 `0` 變更為 `2`。 還沒有套件嗎？請按一下[這裡](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>學習目標

* 如何建立 IoT 中樞並註冊 Thing Dev 適用的裝置。
* 如何連接 Thing Dev 與感應器和電腦。
* 如何在 Thing Dev 上執行範例應用程式來收集感應器資料。
* 如何將感應器資料傳送至 IoT 中樞。

## <a name="what-you-will-need"></a>必要元件

![本教學課程所需的零件](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

若要完成此作業，您需要 Thing Dev 入門套件中的下列零件：

* Sparkfun ESP8266 Thing Dev 電路板。
* Micro USB 轉 Type A 的 USB 纜線。

您的開發環境還需要下列項目︰

* 有效的 Azure 訂用帳戶。 如果您沒有 Azure 帳戶，請花幾分鐘的時間建立[免費的 Azure 試用帳戶](https://azure.microsoft.com/free/)。
* 執行 Windows 或 Ubuntu 的 Mac 或 PC。
* Sparkfun ESP8266 Thing Dev 要連接的無線網路。
* 用來下載組態工具的網際網路連線。
* [Arduino IDE](https://www.arduino.cc/en/main/software) 1.6.8 版 (或更新版本)，舊版無法與 AzureIoT 程式庫搭配運作。

如果您沒有感應器，下列項目可供選用。 您也可以選擇使用模擬的感應器資料。

* Adafruit DHT22 溫度和溼度感應器。
* 麵包板。
* M/M 跳線。

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>連接 ESP8266 Thing Dev 與感應器和電腦

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>將 DHT22 溫度和溼度感應器連接至 ESP8266 Thing Dev

使用麵包板和跳線來進行連接，如下所示。 如果您沒有感應器，請略過本節，因為您可以改為使用模擬的感應器資料。

![連接參考](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

感應器的針腳會使用下列接線方式︰

| 開始 (感應器)           | 結束 (電路版)           | 纜線顏色   |
| -----------------------  | ---------------------- | ------------: |
| VDD (針腳 27F)            | 3V (針腳 8A)           | 紅色纜線     |
| DATA (針腳 28F)           | GPIO 2 (針腳 9A)       | 白色纜線    |
| GND (針腳 30F)            | GND (針腳 7J)          | 黑色纜線   |


- 如需詳細資訊，請參閱︰[DHT22 感應器安裝 (英文)](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) 和 [Sparkfun ESP8266 Thing Dev 規格 (英文)](https://www.sparkfun.com/products/13711)

現在，您的 Sparkfun ESP8266 Thing Dev 應該已經和作用中的感應器連接。

![連接 dht22 和 ESP8266 Thing Dev](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>將 Sparkfun ESP8266 Thing Dev 連接到電腦

使用 Micro USB 轉 Type A 的 USB 纜線，將 Sparkfun ESP8266 Thing Dev 連接到電腦，如下所示。

![將 feather huzzah 連接到電腦](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>新增序列埠權限 - 僅限 Ubuntu

如果您使用 Ubuntu，請確定一般使用者具有操作 Sparkfun ESP8266 Thing Dev 之 USB 連接埠的權限。 若要為一般使用者新增序列埠權限，請遵循下列步驟︰

1. 在終端機執行下列命令：

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   您會得到下列其中一個輸出︰

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   在輸出中，請注意 `uucp` 或 `dialout` 是 USB 連接埠的群組擁有者名稱。

1. 執行下列命令，將使用者新增至群組︰

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` 是您在上一個步驟中取得的群組擁有者名稱。 `<username>` 是 Ubuntu 使用者名稱。

1. 先登出 Ubuntu 然後重新登入，以讓變更生效。

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>收集感應器資料，並將它傳送至 IoT 中樞

在本節中，您可以在 Sparkfun ESP8266 Thing Dev 上部署和執行範例應用程式。 範例應用程式會在 Sparkfun ESP8266 Thing Dev 上使 LED 閃爍，並將收集自 DHT22 感應器的溫度和溼度資料傳送至 IoT 中樞。

### <a name="get-the-sample-application-from-github"></a>從 GitHub 取得範例應用程式

範例應用程式會裝載在 GitHub 上。 請從 GitHub 複製包含範例應用程式的範例存放庫。 若要複製範例存放庫，請遵循下列步驟︰

1. 開啟命令提示字元或終端機視窗。
1. 移至想要儲存範例應用程式的資料夾。
1. 執行以下命令：

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

在 Arduino IDE 中安裝 Sparkfun ESP8266 Thing Dev 的封裝：

1. 開啟範例應用程式儲存所在的資料夾。
1. 在 Arduino IDE 中開啟應用程式資料夾中的 app.ino 檔案。

   ![在 Arduino IDE 中開啟範例應用程式](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. 在 Arduino IDE 中，按一下 [檔案] > [喜好設定]。
1. 在 [喜好設定] 對話方塊中，按一下 [其他電路板管理員 URL] 文字方塊旁的圖示。
1. 在快顯視窗中，輸入下列 URL，然後按一下 [確定]。

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![在 arduino ide 中指向套件 url](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. 在 [喜好設定] 對話方塊中，按一下 [確定]。
1. 按一下 [工具] > [電路板] > [電路板管理員]，然後搜尋 esp8266。
   應該已安裝 ESP8266 2.2.0 版或更新版本。

   ![已安裝 esp8266 套件](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. 按一下 [工具] > [面板] > [Sparkfun ESP8266 Thing Dev]。

### <a name="install-necessary-libraries"></a>安裝必要的程式庫

1. 在 Arduino IDE 中，按一下 [草圖] > [包含程式庫] > [管理程式庫]。
1. 逐一搜尋下列程式庫名稱。 對找到的每個程式庫按一下 [安裝]。
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>沒有真正的 DHT22 感應器嗎？

如果您沒有真正的 DHT22 感應器，範例應用程式可以模擬溫度和溼度資料。 若要讓範例應用程式使用模擬資料，請遵循下列步驟︰

1. 開啟 `app` 資料夾中的 `config.h` 檔案。
1. 找出下面這行程式碼，並將值從 `false` 變更為 `true`：
   ```c
   define SIMULATED_DATA true
   ```
   ![設定範例應用程式以使用模擬資料](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. 使用 `Control-s` 進行儲存。

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>將範例應用程式部署到 Sparkfun ESP8266 Thing Dev

1. 在 Arduino IDE 中，按一下 [工具] > [連接埠]，然後按一下 Sparkfun ESP8266 Thing Dev 的序列埠。
1. 按一下 [草圖] > [上傳]，以建置範例應用程式並將其部署至 Sparkfun ESP8266 Thing Dev。

> [!Note]
> 如果您使用的作業系統為 macOS，您可能會在上傳時看到下列訊息。 `warning: espcomm_sync failed`,`error: espcomm_open failed`. 請開啟終端機視窗並完成下列動作來解決此問題。
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>輸入認證

上傳程序成功完成後，請遵循步驟來輸入認證︰

1. 在 Arduino IDE 中，按一下 [工具] > [序列監視器]。
1. 在 [序列監視器] 視窗中，請注意右下角的兩個下拉式清單。
1. 在左邊的下拉式清單中選取 [無行尾結束符號]。
1. 在右邊的下拉式清單中選取 [115200 傳輸速率]。
1. 在位於 [序列監視器] 視窗頂端的輸入方塊中，輸入系統要求您提供的下列資訊，然後按一下 [傳送]。
   * Wi-Fi SSID
   * Wi-Fi 密碼
   * 裝置連接字串

> [!Note]
> 認證資訊會儲存在 Sparkfun ESP8266 Thing Dev 的 EEPROM 中。 如果您按一下 Sparkfun ESP8266 Thing Dev 電路板上的重設按鈕，範例應用程式會詢問您是否要清除資訊。 輸入 `Y` 以清除資訊，此時系統會再次要求您提供資訊。

### <a name="verify-the-sample-application-is-running-successfully"></a>確認範例應用程式已成功執行

如果您在 [序列監視器] 視窗看到下列輸出，並在 Sparkfun ESP8266 Thing Dev 上看到閃爍的 LED，則表示範例應用程式已成功執行。

![arduino ide 中的最終輸出](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>後續步驟

您已成功將 Sparkfun ESP8266 Thing Dev 連接到 IoT 中樞，並將擷取到的感應器資料傳送至 IoT 中樞。 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

