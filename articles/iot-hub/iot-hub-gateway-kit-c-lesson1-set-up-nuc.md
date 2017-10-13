---
title: "SensorTag 裝置與 Azure IoT 閘道 - 第 1 課：設定 Intel NUC | Microsoft Docs"
description: "將 Intel NUC 設定為在感應器和 Azure IoT 中樞之間做為 IoT 閘道器，以收集感應器資訊，並將資訊傳送至 IoT 中樞。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 閘道, intel nuc, nuc 電腦, DE3815TYKE"
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 1a3a92ab8d08c6ed6f047208217c46022027157e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>將 Intel NUC 設定為 IoT 閘道器
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

## <a name="what-you-will-do"></a>將執行的作業

- 將 Intel NUC 設定為 IoT 閘道器。
- 在 Intel NUC 上安裝 Azure IoT Edge 套件。
- 在 Intel NUC 上執行 "hello_world" 範例應用程式，以確認閘道功能。

  > 如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標

在這一課，您將了解：

- 如何連接 Intel NUC 與週邊設備。
- 如何使用智慧型套件管理員 (Smart Package Manager) 安裝及更新 Intel NUC 上的所需套件。
- 如何執行 "hello_world" 範例應用程式以確認閘道器的功能。

## <a name="what-you-need"></a>您需要什麼

- 預先安裝的 Intel NUC 套件 DE3815TYKE 與 Intel IoT 閘道器軟體套件 (Wind River Linux *7.0.0.13)。 [按一下這裡以購買 Groove IoT 商業閘道套件 (英文)](https://www.seeedstudio.com/Grove-IoT-Commercial-Gateway-Kit-p-2724.html)。
- 乙太網路連接線。
- 鍵盤。
- HDMI 或 VGA 連接線。
- 具有 HDMI 或 VGA 連接埠的螢幕。
- 選擇性︰[Texas Instruments 的感應器標籤 (CC2650STK) (英文)](http://www.ti.com/tool/cc2650stk)

![閘道器套件](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>連接 Intel NUC 與週邊設備

下圖是 Intel NUC 與各種週邊設備連接的範例︰

1. 連接到鍵盤。
2. 使用 VGA 連接線或 HDMI 連接線連接到螢幕。
3. 透過乙太網路連接線連接到有線網路。
4. 利用電源線連接到電源供應器。

![連接到週邊設備的 Intel NUC](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>從主機電腦透過安全殼層 (SSH) 連線至 Intel NUC 系統

您將需要鍵盤和螢幕來取得 Intel NUC 裝置的 IP 位址。 如果您已經知道 IP 位址，可以直接跳至本節的步驟 3。

1. 按下電源按鈕開啟 Intel NUC，然後登入。

   預設的使用者名稱和密碼都是 `root`。

       > Hit the enter key on your keyboard if you see either of the following errors when you boot: 'A TPM error (7) occurred attempting to read a pcr value.' or 'Timeout, No TPM chip found, activating TPM-bypass!'

2. 在 Intel NUC 裝置上執行 `ifconfig` 命令，以取得 Intel NUC 的 IP 位址。

   以下是命令的範例輸出。

   ![顯示 Intel NUC IP 的 ifconfig 輸出](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   在此範例中，`inet addr:` 後的值是 IP 位址，當您從主機電腦連線至 Intel NUC 時需要此位址。

3. 使用下列其中一個 SSH 用戶端，從主機電腦連線至 Intel NUC。

    - [PuTTY](http://www.putty.org/) 適用於 Windows。
    - Ubuntu 或 macOS 上內建的 SSH 用戶端。

   從主機電腦操作 Intel NUC 會更有效率且更有生產力。 您將需要 NUC 的 IP 位址、使用者名稱和密碼，才能透過 SSH 用戶端與它連線。 以下是在 macOS 上使用 SSH 用戶端的範例。
   ![在 macOS 上執行的 SSH 用戶端](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-edge-package"></a>安裝 Azure IoT Edge 套件

Azure IoT Edge 套件包含預先編譯的 IoT Edge 二進位檔及其相依性。 這些二進位檔是 Azure IoT Edge、Azure IoT SDK 和對應的工具。 套件中也有 "hello_world" 範例應用程式，用以確認閘道器的功能。 IoT Edge 是閘道的核心部分。 

請遵循下列步驟來安裝此封裝。

1. 在終端機視窗中執行下列命令，新增 IoT 雲端存放庫：

   ```bash
   rpm --import https://iotdk.intel.com/misc/iot_pub2.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
   ```

   > 若系統顯示 [包括此通道?] 提示，請輸入 'y'。
   
   如果您收到 `import read failed(-1)` 錯誤，請使用下列命令來解決此問題：
   ```bash
   wget http://iotdk.intel.com/misc/iot_pub2.key 
   rpm --import iot_pub2.key  
   ```

   `rpm` 命令會匯入 rpm 索引鍵。 `smart channel` 命令會將 rpm 通道新增至智慧型套件管理員。 執行 `smart update` 命令前，您將會看到如下的輸出。

   ![rpm 和 smart channel 命令的輸出](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

2. 執行智慧更新命令：

   ```bash
   smart update
   ```

3. 執行下列命令來安裝 Azure IoT 閘道封裝：

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` 是套件的名稱。 `smart install` 命令是用於安裝套件。

    > 如果您看到此錯誤，請執行下列命令：「無法使用公用金鑰」

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```
    > 若您看到下列錯誤，請將 Intel NUC 重新開機：「沒有任何套件提供 util-linux-dev」

   安裝封裝之後，Intel NUC 就已準備好當做閘道運作。

## <a name="run-the-azure-iot-edge-helloworld-sample-application"></a>執行 Azure IoT Edge "hello_world" 範例應用程式

下列範例應用程式會從 `hello_world.json` 檔案建立閘道，並使用 Azure IoT Edge 架構的基礎元件，每隔 5 秒將 hello world 訊息記錄到檔案 (log.txt) 中。

您可以藉由執行下列命令來執行 Hello World 範例：

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

讓 Hello World 應用程式執行幾分鐘，然後按 Enter 鍵來停止它。
![應用程式的輸出](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

> 您可以忽略您按下 Enter 之後出現的任何「無效的引數控制代碼 (NULL)」錯誤。

您可以藉由開啟目前已位於 hello_world 資料夾中的 log.txt 檔案，來確認閘道已成功執行︰![log.txt 目錄檢視](media/iot-hub-gateway-kit-lessons/lesson1/logtxtdir.png)

使用下列命令開啟 log.txt：

```bash
vim log.txt
```

您接著將會看見 log.txt 的內容，這將是具備 JSON 格式的記錄訊息輸出，閘道 Hello World 模組每 5 秒便會寫入這類訊息。
![log.txt 目錄檢視](media/iot-hub-gateway-kit-lessons/lesson1/logtxtview.png)

如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-troubleshooting.md)尋求解決方案。

## <a name="summary"></a>摘要

恭喜！ 您已完成將 Intel NUC 設定為閘道器。 現在您可以開始進行下一課，來設定主機電腦、建立 Azure IoT 中樞，並註冊您的 Azure IoT 中樞邏輯裝置。

## <a name="next-steps"></a>後續步驟
[使用 IoT 閘道將裝置連線到 Azure IoT 中樞](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

