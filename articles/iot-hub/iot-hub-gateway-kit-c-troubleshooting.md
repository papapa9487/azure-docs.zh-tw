---
title: "SensorTag 裝置與 Azure IoT 閘道 - 疑難排解 | Microsoft Docs"
description: "Intel NUC 閘道器的疑難排解頁面"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 問題, 物聯網問題"
ROBOTS: NOINDEX
ms.assetid: 5f742c38-0e33-465a-9a0d-1e41e8d17187
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 7e80951de55ade6b5140608dcff8ebb064f942ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting"></a>疑難排解

## <a name="hardware-issues"></a>硬體問題

### <a name="ti-sensortag-cannot-be-connected"></a>無法連線至 TI SensorTag

若要針對 SensorTag 連線問題進行疑難排解，請使用 [SensorTag 應用程式](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide)。

### <a name="have-an-issue-with-intel-nuc"></a>Intel NUC 有問題

若要疑難排解啟動問題，請參閱[針對 Intel NUC 沒有開機的問題進行疑難排解](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html)。

若要疑難排解作業系統問題，請參閱[針對 Intel NUC 的作業系統問題進行疑難排解](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html)。

若要疑難排解其他問題，請參閱 [Intel NUC 的閃爍代碼和嗶聲代碼](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html)。

## <a name="nodejs-package-issues"></a>Node.js 套件問題

### <a name="no-response-during-gulp-tasks"></a>gulp 工作期間沒有回應

如果您遇到執行 gulp 工作的問題，您可以新增 `--verbose` 選項以進行偵錯。 嘗試使用 `Ctrl + C` 終止目前的 gulp 工作，然後在主控台視窗中執行下列命令查看偵錯訊息。 您可能會在主控台輸出中看到詳細的錯誤訊息。

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>裝置探索問題

如需使用 `discover-sensortag` 命令對常見問題進行疑難排解的說明，請參閱 [wiki 頁面](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl)。

### <a name="npm-issues"></a>npm 問題

嘗試執行下列命令更新您的 npm 套件：

```bash
npm install -g npm
```

如果問題仍然存在，在本文結尾處留下您的意見，或在我們的[範例儲存機制](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started)建立 GitHub 問題。

## <a name="remote-debugging"></a>遠端偵錯
> 下面的指示是用於針對本教學課程中使用的 node.js 指令碼偵錯。
### <a name="run-the-sample-application-in-debug-mode"></a>在偵錯模式中執行範例應用程式

執行下列命令在偵錯模式中執行範例應用程式：

```bash
gulp run --debug
```

偵錯引擎就緒時，您應該會在主控台輸出中看到`Debugger listening on port 5858`。

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>設定 Visual Studio Code 以連接到遠端裝置

1. 開啟左側的 [偵錯] 面板。
2. 按一下綠色的 [開始偵錯] \(F5) 按鈕。 Visual Studio Code 會開啟 `launch.json` 檔案。
3. 更新 `launch.json` 檔案的下列內容。 將 `[device hostname or IP address]` 取代為實際裝置 IP 位址或主機名稱。

   ``` json
   {
     "version": "0.2.0",
     "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![遠端偵錯組態](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>附加至遠端應用程式

按一下綠色的 [開始偵錯] \(F5) 按鈕，以開始偵錯。

請參閱 [VS Code 中的 JavaScript](https://code.visualstudio.com/docs/languages/javascript#_debugging) 以深入了解偵錯工具。

![偵錯 BLE 範例](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Azure CLI 問題

Azure 命令列介面 (Azure CLI) 是預覽組建。 若要尋求解決方案，您可以使用[預覽安裝指南](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md)。

如果您遇到任何與工具有關的錯誤，請在 GitHub 儲存機制的**問題**一節中提出[問題](https://github.com/Azure/azure-cli/issues)。

如需針對常見問題進行疑難排解的說明，請參閱[讀我檔案](https://github.com/Azure/azure-cli/blob/master/README.rst)。

如果您看到「找不到符合需求的版本」，請執行下列命令將 pip 升級至最新版本。

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Python 安裝問題

### <a name="legacy-installation-issues-macos"></a>舊版安裝問題 (macOS)

安裝 pip 時，如果以 **su** 權限安裝舊版套件，則會擲回權限錯誤。 之所以發生這個情況，是因為先前使用 brew (macOS) 所安裝的 Python 未完整解除安裝。 先前安裝的某些 pip 套件是由根目錄建立，這會導致權限錯誤。 解決方案是移除根目錄安裝的這些套件。 使用下列步驟以完成此工作：

1. 移至 `/usr/local/lib/python2.7/site-packages`。
2. 列出根目錄建立的套件：`ls -l | grep root`
3. 從步驟 2 解除安裝套件：`sudo rm -rf {package name}`
4. 重新安裝 Python。

## <a name="azure-iot-hub-issues"></a>Azure IoT 中樞問題

如果您已使用 Azure CLI 成功佈建 Azure IoT 中樞，而且您需要工具來管理連接到 IoT 中樞的裝置，請嘗試下列工具。

### <a name="device-explorer"></a>裝置總管

[裝置總管](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)會在您的 Windows 本機機器上執行，並且連接至 Azure 中的 IoT 中樞。 它會與下列 [IoT 中樞端點](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/)通訊：

- 裝置身分識別管理，以佈建和管理 IoT 中樞已登錄的裝置。
- 接收裝置到雲端，以便可以監視從裝置傳送到 IoT 中樞的訊息。
- 傳送裝置到雲端，以便可以從 IoT 中樞將訊息傳送至裝置。

在此工具內設定 IoT 中樞連接字串以使用它的所有功能。

### <a name="iothub-explorer"></a>iothub-explorer

[iothub-explorer](https://github.com/Azure/iothub-explorer) 是範例多平台 CLI 工具，可以管理裝置用戶端。 您可以使用此工具管理身分識別登錄中的裝置、監視裝置到雲端訊息，以及傳送雲端到裝置命令。

若要安裝最新 (發行前版本) 版本的 iothub-explorer 工具，執行下列命令︰

```bash
npm install -g iothub-explorer@latest
```

若要取得所有 iothub-explorer 命令及其參數的額外說明，執行下列命令：

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>Azure 入口網站

完整的 CLI 體驗可協助您建立及管理您所有的 Azure 資源。 您可能也會想要使用 [Azure 入口網站](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/)以協助佈建、管理和偵錯您的 Azure 資源。

## <a name="azure-storage-issues"></a>Azure 儲存體問題

[Microsoft Azure 儲存體總管 (預覽)](http://storageexplorer.com/) 是 Microsoft 所提供的獨立應用程式，可供您在 Windows、macOS 和 Linux 上處理 Azure 儲存體資料。 透過使用此工具，您可以連接到資料表，並查看其中的資料。 您可以使用這項工具，對 Azure 儲存體問題進行疑難排解。
