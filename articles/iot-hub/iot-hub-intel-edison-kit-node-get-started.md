---
title: "Intel Edison 到雲端 (Node.js) - 將 Intel Edison 連線到 Azure IoT 中樞 | Microsoft Docs"
description: "了解如何設定及連線 Intel Edison 和 Azure IoT 中樞，在本教學課程中讓 Intel Edison 將資料傳送到 Azure 雲端平台。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot intel edison, intel edison iot 中樞, intel edison 傳送資料到雲端, intel edison 到雲端"
ms.assetid: a7c9cf2d-c102-41b0-aa45-41285c6877eb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/15/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5a31efba704045196b5563f7bc467c773bea7805
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-intel-edison-to-azure-iot-hub-nodejs"></a>將 Intel Edison 連線到 Azure IoT 中樞 (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

在本教學課程中，您一開始會先了解使用 Intel Edison 的基本知識。 接著會了解如何使用 [Azure IoT 中樞](iot-hub-what-is-iot-hub.md)讓您的裝置順暢地與雲端連線。

還沒有套件嗎？ 從[這裡](https://azure.microsoft.com/develop/iot/starter-kits)開始

## <a name="what-you-do"></a>您要做什麼

* 設置 Intel Edison 和 Grove 模組。
* 建立 IoT 中樞。
* 在 IoT 中樞註冊 Edison 的裝置。
* 在 Edison 上執行範例應用程式，將感應器資料傳送至 IoT 中樞。

將 Intel Edison 連線至您建立的 IoT 中樞。 然後，在 Edison 上執行範例應用程式，以收集 Grove 溫度感應器中的溫度和溼度資料。 最後，將感應器資料傳送至 IoT 中樞。

## <a name="what-you-learn"></a>您學到什麼

* 如何建立 Azure IoT 中樞，並取得新的裝置連接字串。
* 如何將 Edison 與 Grove 溫度感應器連接。
* 如何在 Edison 上執行範例應用程式來收集感應器資料。
* 如何將感應器資料傳送至 IoT 中樞。

## <a name="what-you-need"></a>您需要什麼

![您需要什麼](media/iot-hub-intel-edison-kit-node-get-started/0_kit.png)

* Intel Edison 開發板
* Arduino 擴充板
* 有效的 Azure 訂用帳戶。 如果您沒有 Azure 帳戶，請花幾分鐘的時間[建立免費的 Azure 試用帳戶](https://azure.microsoft.com/free/)。
* 執行 Windows 或 Linux 的 Mac 或 PC。
* 網際網路連線。
* Micro B 轉 Type A 的 USB 纜線
* 直流電 (DC) 電源供應器。 電源供應器的額定值應如下所示︰
  - 7-15V DC
  - 至少 1500mA
  - 中心/內部插銷應該是電源供應器的正極

下列項目是選用項目︰

* Grove Base Shield V2
* Grove - 溫度感應器
* Grove 纜線
* 包裝內所含的任何間隔柱或螺絲，包括兩個用來將模組鎖到擴充板的螺絲，和四組螺絲和塑膠間隔柱。

> [!NOTE] 
這些項目都是選用項目，因為程式碼範例支援模擬感應器資料。

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>安裝 Intel Edison

### <a name="assemble-your-board"></a>組裝面板

本節所含步驟會將 Intel® Edison 模組連接至擴充板。

1. 將 Intel® Edison 模組放在擴充板的白框內，讓模組上的螺絲孔對齊擴充板上的螺絲。

2. 在模組上的 `What will you make?` 字樣正下方往下按，直到您感覺到兩者咬合。

   ![組裝面板 2](media/iot-hub-intel-edison-kit-node-get-started/1_assemble_board2.jpg)

3. 使用兩個六角螺帽 (包裝內含) 將模組鎖緊到擴充板上。

   ![組裝面板 3](media/iot-hub-intel-edison-kit-node-get-started/2_assemble_board3.jpg)

4. 在擴充板四個角落的其中一個螺絲孔中插入螺絲。 將其中一個白色塑膠間隔柱旋緊到螺絲上。

   ![組裝面板 4](media/iot-hub-intel-edison-kit-node-get-started/3_assemble_board4.jpg)

5. 重複裝上其他三個角落的間隔柱。

   ![組裝面板 5](media/iot-hub-intel-edison-kit-node-get-started/4_assemble_board5.jpg)

面板現已組裝好。

   ![組裝好的面板](media/iot-hub-intel-edison-kit-node-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>連接 Grove Base Shield 和溫度感應器

1. 將 Grove Base Shield 放上主機板。 確定所有針腳都緊密地插入主機板。
   
   ![Grove Base Shield](media/iot-hub-intel-edison-kit-node-get-started/6_grove_base_sheild.jpg)

2. 使用 Grove 纜線將 Grove 溫度感應器連接到 Grove Base Shield **A0** 連接埠。

   ![連接到溫度感應器](media/iot-hub-intel-edison-kit-node-get-started/7_temperature_sensor.jpg)

   ![Edison 和感應器連接](media/iot-hub-intel-edison-kit-node-get-started/16_edion_sensor.png)

現在您的感應器已就緒。

### <a name="power-up-edison"></a>將 Edison 接上電源

1. 插上電源供應器。

   ![插上電源供應器](media/iot-hub-intel-edison-kit-node-get-started/8_plug_power.jpg)

2. 綠色 LED (在 Arduino* 擴充板上標示為 DS1) 應該會亮起，並持續亮著。

3. 等候一分鐘，讓面板完成開機。

   > [!NOTE]
   > 如果您沒有 DC 電源供應器，也可以透過 USB 連接埠為面板供電。 如需詳細資訊，請參閱`Connect Edison to your computer`一節。 以此方式為面板供電，可能會讓面板發生無法預期的行為，尤其是在使用 Wi-Fi 或驅動馬達時。

### <a name="connect-edison-to-your-computer"></a>將 Edison 連接到電腦

1. 將微型開關往下扳動到靠近兩個 micro USB 連接埠的方向，使 Edison 進入裝置模式。 若要了解裝置模式與主機模式的差異，請參閱[這裡](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode)。

   ![將微型開關往下扳動](media/iot-hub-intel-edison-kit-node-get-started/9_toggle_down_microswitch.jpg)

2. 將 micro USB 纜線插入最上方的 micro USB 連接埠。

   ![最上方的 micro USB 連接埠](media/iot-hub-intel-edison-kit-node-get-started/10_top_usbport.jpg)

3. 將 USB 纜線的另一端插入電腦。

   ![電腦 USB](media/iot-hub-intel-edison-kit-node-get-started/11_computer_usb.jpg)

4. 當電腦掛接新的磁碟機 (很像將 SD 卡插入電腦) 時，您就會知道面板已完全初始化。

## <a name="download-and-run-the-configuration-tool"></a>下載並執行組態工具
從[此連結](https://software.intel.com/en-us/iot/hardware/edison/downloads)的 `Installers` 標題底下所列項目取得最新的組態工具。 執行工具，並依照其螢幕上的指示進行，視需要按 [Next]

### <a name="flash-firmware"></a>更新韌體
1. 在 `Set up options` 頁面上，按一下 `Flash Firmware`。
2. 執行下列其中一項，選取要更新至面板的映像︰
   - 若要下載 Intel 所提供之最新韌體映像並以此映像更新面板，請選取 `Download the latest image version xxxx`。
   - 若要以電腦上已儲存的映像更新面板，請選取 `Select the local image`。 瀏覽並選取您要更新至面板的映像。
3. 安裝工具會嘗試更新面板。 整個更新程序最多可能需要 10 分鐘。

### <a name="set-password"></a>設定密碼
1. 在 `Set up options` 頁面上，按一下 `Enable Security`。
2. 您可以為 Intel® Edison 面板設定自訂名稱。 這是選擇性。
3. 輸入面板的密碼，然後按一下 `Set password`。
4. 記下密碼，以供稍後使用。

### <a name="connect-wi-fi"></a>連接 Wi-Fi
1. 在 `Set up options` 頁面上，按一下 `Connect Wi-Fi`。 最多請等候一分鐘，讓電腦掃描可用的 Wi-Fi 網路。
2. 從 `Detected Networks` 下拉式清單中選取網路。
3. 從 `Security` 下拉式清單中選取網路的安全性類型。
4. 提供登入和密碼資訊，然後按一下 `Configure Wi-Fi`。
5. 記下 IP 位址，以供稍後使用。

> [!NOTE]
> 請確定 Edison 是連接到與您電腦相同的網路。 電腦會使用該 IP 位址連接到 Edison。

   ![連接到溫度感應器](media/iot-hub-intel-edison-kit-node-get-started/12_configuration_tool.png)

恭喜！ 您已經成功設定 Edison。

## <a name="run-a-sample-application-on-intel-edison"></a>在 Intel Edison 上執行範例應用程式

### <a name="prepare-the-azure-iot-device-sdk"></a>準備 Azure IoT 裝置 SDK

1. 使用下列其中一個 SSH 用戶端，從主機電腦連線到 Intel Edison。 IP 位址來自組態工具，密碼則是您在該工具中設定的密碼。
    - [PuTTY](http://www.putty.org/) 適用於 Windows。
    - Ubuntu 或 macOS 上內建的 SSH 用戶端。

2. 將範例用戶端應用程式複製到裝置。 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-intel-edison-client-app
   ```

3. 然後瀏覽到存放庫資料夾，執行下列命令來安裝所有套件，這可能需要幾分鐘才能完成。
   
   ```bash
   cd iot-hub-node-intel-edison-client-app
   npm install
   ```


### <a name="configure-and-run-the-sample-application"></a>設定和執行範例應用程式

1. 執行下列命令以開啟組態檔：

   ```bash
   nano config.json
   ```

   ![組態檔](media/iot-hub-intel-edison-kit-node-get-started/13_configure_file.png)

   此檔案中有兩個巨集可供您設定。 第一個是 `INTERVAL`，這可定義傳送至雲端的兩個訊息之間相隔的時間間隔。 第二個是 `simulatedData`，這是是否使用模擬感應器資料的布林值。

   如果**沒有感應器**，請將 `simulatedData` 值設定為 `true`，使範例應用程式建立和使用模擬感應器資料。

1. 按下 [Control-O] > 輸入 > [Control-X] 儲存並結束。


1. 執行下列命令，執行範例應用程式：

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   確定複製裝置連接字串，並貼到單引號中。

您應該會看見下列輸出，顯示傳送至 IoT 中樞的感應器資料和訊息。

![輸出 - 從 Intel Edison 傳送至 IoT 中樞的感應器資料](media/iot-hub-intel-edison-kit-node-get-started/15_message_sent.png)

## <a name="next-steps"></a>後續步驟

您已執行範例應用程式收集感應器資料並傳送至 IoT 中樞。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
