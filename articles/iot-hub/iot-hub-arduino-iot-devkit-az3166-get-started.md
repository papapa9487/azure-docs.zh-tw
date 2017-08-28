---
title: "IoT DevKit 到雲端 - 連線 IoT DevKit AZ3166 和 Azure IoT 中樞 | Microsoft Docs"
description: "在本教學課程中了解如何設定及連線 IoT DevKit AZ3166 和 Azure IoT 中樞，以將資料傳送到 Azure 雲端平台。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 122fac584ac5b954ef7b33a3121bee2c502ebc63
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>將 IoT DevKit AZ3166 連線到雲端中的 Azure IoT 中樞

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 可用來開發及設定運用 Microsoft Azure 服務的物聯網 (IoT) 解決方案原型。 其包含具有豐富週邊設備與感應器的 Arduino 相容面板、開放原始碼面板封裝，以及不斷擴展的[專案目錄](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。

## <a name="what-you-do"></a>您要做什麼
將 [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 連線到您建立的 Azure IoT 中樞，收集來自感應器的溫度與溼度，並傳送資料至 IoT 中樞。

還沒有 DevKit 嗎？ 在[這裡](https://aka.ms/iot-devkit-purchase)取得新的 DevKit。

## <a name="what-you-learn"></a>您學到什麼

* 如何將 IoT DevKit 連線到無線存取點以及準備您的開發環境。
* 如何建立 IoT 中樞並註冊 MXChip IoT DevKit 適用的裝置。
* 如何在 MXChip IoT DevKit 上執行範例應用程式來收集感應器資料。
* 如何將感應器資料傳送至 IoT 中樞。

## <a name="what-you-need"></a>您需要什麼

* 使用 Micro USB 纜線的 MXChip IoT DevKit 面板。 [立即取得](https://aka.ms/iot-devkit-purchase)
* 執行 Windows 10 或 macOS 10.10+ 的電腦
* 作用中的 Azure 訂用帳戶
  * 啟動 [30 天免費試用 Microsoft Azure 帳戶](https://azureinfo.microsoft.com/us-freetrial.html)

## <a name="prepare-your-hardware"></a>準備硬體

將硬體連接到您的電腦。

### <a name="hardware-you-need"></a>您需要的硬體

* DevKit 面板
* Micro USB 纜線

![getting-started-hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

### <a name="connect-devkit-to-your-computer"></a>將 DevKit 連接到電腦

1. 將 USB 端連接到電腦
2. 將 Micro USB 端連接到 DevKit
3. 電源旁邊的綠色 LED 可確認連接

![getting-started-connect](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wifi"></a>設定 WiFi

IoT 專案依賴網際網路連線。 您可使用下列指示，設定 DevKit 連線至 WiFi。

### <a name="enter-ap-mode"></a>進入 AP 模式

按住按鈕 B，按下並放開 [重設] 按鈕，然後放開按鈕 B。您的 DevKit 將會進入 AP 模式，以設定 WiFi。 此畫面將顯示 DevKit 的服務組識別碼 (SSID) 以及設定入口網站 IP 位址：

![getting-started-wifi-ap](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>連線至 DevKit AP

現在，使用另一個已啟用 WiFi 的裝置 (PC 或行動電話) 以連線至 DevKit SSID (上述螢幕擷取畫面中的醒目顯示)，將密碼保持空白。

![getting-started-ssid](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wifi-for-devkit"></a>為 DevKit 設定 WiFi

在您的電腦上開啟 DevKit 畫面上顯示的 IP 位址，選取您希望 DevKit 連線的 WiFi 網路，然後輸入密碼。 按一下 [連線] 以完成：

![getting-started-wifi-portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

連線成功後，DevKit 會在幾秒內重新啟動。 如果成功，您會在畫面上看到 WiFi 名稱和 IP 位址：

![getting-started-wifi-ip](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
相片中顯示的 IP 位址可能不符合 DevKit 畫面上指派與顯示的實際 IP。 這是正常現象，因為 WiFi 使用 DHCP 以動態方式指派 IP。

設定 WiFi 之後，即使拔除連線，您的認證也會持續存在於該連線的裝置上。 例如，如果您已針對家中 WiFi 設定 DevKit，然後將 DevKit 帶到辦公室，您需要重新設定 AP 模式 (從**進入 AP 模式**步驟開始) 以將 DevKit 連線至您辦公室的 WiFi。 

## <a name="start-using-devkit"></a>開始使用 DevKit

在 DevKit 上執行的預設應用程式將檢查最版本的韌體，並顯示某些感應器診斷資料。

### <a name="upgrade-to-the-latest-firmware"></a>升級至最新的韌體

目前和最新韌體版本畫面上皆會提示您是否需要升級。 請遵循[升級韌體](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/)指南以進行升級。

![getting-started-firmware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
這是單次投入量，一旦您開始在 DevKit 上開發並上傳您的應用程式，您將擁有隨附在應用程式中的最新韌體。

### <a name="test-various-sensors"></a>測試各種感應器

按下按鈕 B 以測試感應器、 繼續按下並放開 B 按鈕，以循環瀏覽每個感應器。

![getting-started-sensors](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-development-environment"></a>準備開發環境

現在可以設定開發環境：工具與封裝可讓您建立令人驚嘆的 IoT 應用程式。 您可以根據您的作業系統選擇 Windows 或 macOS 版本。


### <a name="windows"></a>Windows

建議您使用安裝封裝以準備開發環境。 如果遇到任何問題，可以依照[手動步驟](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/)完成。

#### <a name="download-latest-package"></a>下載最新的封裝

您下載的 `.zip` 檔案包含 DevKit 開發所需的所有必要工具與封裝。

> [!div class="button"]
[下載](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


> [!NOTE] 
> `.zip` 檔案包含下列工具和封裝。 如果您已安裝某些元件，指令碼將會偵測到並跳過它們。
> * Node.js 和 Yarn：安裝指令碼和自動化工作的執行階段
> * [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows) - 管理 Azure 資源的跨平台命令列體驗，MSI 包含相依的 Python 與 pip。
> * [Visual Studio Code](https://code.visualstudio.com/)：DevKit 開發的輕量型程式碼編輯器
> * [適用於 Arduino 的 Visual Studio Code 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino)：使用 VS Code 啟用 Arduino 開發
> * [Arduino IDE](https://www.arduino.cc/en/Main/Software)：適用於 Arduino 的擴充功能依賴此工具
> * DevKit 面板封裝：DevKit 的工具鏈、程式庫與專案
> * ST-Link 公用程式：基本公用程式與驅動程式

#### <a name="run-installation-script"></a>執行安裝指令碼

在 [Windows 檔案總管] 中，找出 `.zip` 並加以解壓縮，尋找 `install.cmd`，按一下滑鼠右鍵，然後選取 [以系統管理員身分執行] 以啟動。

![getting-started-run-admin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

在安裝期間，您會看到每個工具或封裝的進度。

![getting-started-install](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="confirm-to-install-drivers"></a>確認以安裝驅動程式

適用於 Arduino 的 VS Code 擴充功能依賴 Arduino IDE。 如果這是您第一次安裝 Arduino IDE，系統會提示您安裝相關的驅動程式：

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

完成安裝所需時間約為 10 分鐘，視您的網際網路速度而定。 安裝完成之後，您應該會在桌面上看到 Visual Studio Code 和 Arduino IDE 快速鍵。

> [!NOTE] 
有時在您啟動 VS Code 時，系統會出現錯誤提示，指出找不到 Arduino IDE 或相關面板封裝。 若要解決此問題，請關閉 VS Code，再次啟動 Arduino IDE，VS Code 應該會正確找出 Arduino IDE 路徑。


### <a name="macos-preview"></a>macOS (預覽)

請遵循下列步驟準備在 macOS 上開發環境。

#### <a name="install-azure-cli-20"></a>安裝 Azure CLI 2.0

依照[官方指南](https://docs.microsoft.com//cli/azure/install-azure-cli)以安裝 Azure CLI 2.0：

使用 `curl` 命令安裝 Azure CLI 2.0：

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

重新啟動命令殼層，某些變更才會生效：

```bash
exec -l $SHELL
```

#### <a name="install-arduino-ide"></a>安裝 Arduino IDE

Visual Studio Code Arduino 擴充功能依賴 Arduino IDE。 下載並安裝[適用於 macOS 的 Arduino IDE](https://www.arduino.cc/en/Main/Software)。

#### <a name="install-visual-studio-code"></a>安裝 Visual Studio Code

下載並安裝[適用於 macOS 的 Visual Studio Code](https://code.visualstudio.com/)。 這會是建立 DevKit IoT 應用程式的主要開發工具。

####  <a name="download-latest-package"></a>下載最新的封裝

1. 安裝 Node.js。 您可以使用熱門的 macOS 封裝管理員 [Homebrew](https://brew.sh/) 或[預先建立的安裝程式](https://nodejs.org/en/download/)來進行安裝。

2. 下載 `.zip` 檔案，其包含使用 VS Code 開發 DevKit 所需之工作指令碼。

   > [!div class="button"]
   [下載](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

   找出 `.zip` 並將其解壓縮。 然後啟動 [終端機] 應用程式並執行下列命令來設定：

   將解壓縮的資料夾移動至您的 macOS 使用者資料夾：
   ```bash
   mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
   ```
  
   安裝 `npm` 封裝：
   ```
   npm install
   ```

#### <a name="install-vs-code-extension-for-arduino"></a>安裝適用於 Arduino 的 Visual Studio Code 擴充功能

Visual Studio Code 可讓您在工具中直接安裝 Marketplace 擴充功能，僅需按一下左側功能表窗格中的擴充功能圖示，然後搜尋 `Arduino` 以進行安裝：

![installation-extensions](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-devkit-board-package"></a>安裝 DevKit 面板封裝

您必須使用 Visual Studio Code 中的 Board Manager 以新增 DevKit 面板。

1. 使用 `Cmd+Shift+P` 以叫用命令選擇區，並輸入 **Arduino**，然後尋找並選取 [Arduino: Board Manager]。

2. 按一下右下方的 [其他 URL]。
   ![installation-additional-urls](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. 在 `settings.json` 檔案中的 `USER SETTINGS` 窗格底部，新增一行並儲存。
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![installation-settings-json](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. 在 Board Manager 中搜尋 'az3166' 並安裝最新版本。
   ![installation-az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

您現在已具備安裝 macOS 的所有必要工具與封裝。


## <a name="open-project-folder"></a>開啟專案資料夾

### <a name="launch-vs-code"></a>啟動 VS Code

請確定 DevKit 未連線。 先啟動 VS Code，然後將 DevKit 連線至您的電腦。 VS Code 會自動尋找它，並快顯簡介頁面：

![mini-solution-vscode](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
有時候在您啟動 VS Code 時，系統會出現錯誤提示，指明找不到 Arduino IDE 或相關面板封裝。 若要解決此問題，請關閉 VS Code，再次啟動 Arduino IDE，VS Code 應該會正確找出 Arduino IDE 路徑。

### <a name="open-arduino-examples-folder"></a>開啟 Arduino 範例資料夾

切換至 [Arduino 範例] 索引標籤，瀏覽至 `Examples for MXCHIP AZ3166 > AzureIoT`，然後按一下 `GetStarted`。

![mini-solution-examples](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

如果您剛好關閉窗格而要重新載入，請使用 `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) 以叫用命令選擇區，並輸入 **Arduino** 以尋找並選取 [Arduino: Examples]。

## <a name="provision-azure-services"></a>佈建 Azure 服務

在解決方案視窗中，藉由輸入 'task cloud-provision'，透過 `Ctrl+P` (macOS: `Cmd+P`) 執行您的工作：

在 VS Code 終端機中，互動式命令列將引導您完成佈建所需的 Azure 服務：

![mini-solution-cloud-provision](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-arduino-sketch"></a>組建並上傳 Arduino 草圖

### <a name="install-required-library"></a>安裝必要的程式庫

1. 按下 `F1` 或 `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) 以叫用命令選擇區，並輸入 **Arduino**，然後尋找並選取 [Arduino: Library Manager]。

2. 搜尋 `ArduinoJson` 程式庫，然後按一下 [安裝]

### <a name="build-and-upload-the-device-code"></a>組建並上傳裝置程式碼

使用 `Ctrl+P` (macOS: `Cmd+P`) 以執行 'task device-upload'。 終端機會提示您輸入設定模式。 若要這樣做，請按住按鈕 A，然後按下並放開 [重設] 按鈕。 畫面會顯示「設定」。 此項目是用於設定從 'task cloud-provision' 步驟擷取的連接字串。

然後會開始驗證和上傳 Arduino 草圖：

![mini-solution-device-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit 會重新啟動並開始執行程式碼。

## <a name="test-the-project"></a>測試專案

在 VS Code 中，按一下狀態列上的電源插頭圖示，以開啟「序列監視器」。

當您看到以下結果時，表示範例應用程式已成功執行：

* 「序列監視器」會顯示與下列螢幕擷取畫面內容相同的資訊。
* MXChip IoT DevKit 上的 LED 正在閃爍。

![VS Code 的最終輸出](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>問題與意見反應

如果發生問題，您可以尋找[常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或透過下列管道與我們連絡。

## <a name="next-steps"></a>後續步驟

您已成功將 MXChip IoT DevKit 連線到 IoT 中樞，並將擷取到的感應器資料傳送至 IoT 中樞。

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

- [透過 iothub-explorer 管理雲端裝置傳訊](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [將 IoT 中樞IoT 中樞儲存至 Azure 資料儲存體](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [使用 Power BI 將來自 Azure IoT 中樞的即時感應器資料視覺化](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [使用 Azure Web Apps 將來自 Azure IoT 中樞的即時感應器資料視覺化](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [在 Azure Machine Learning 中使用 IoT 中樞的感應器資料進行天氣預測](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [透過 iothub-explorer進行裝置管理](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [搭配 Logic Apps 進行遠端監視和通知](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
