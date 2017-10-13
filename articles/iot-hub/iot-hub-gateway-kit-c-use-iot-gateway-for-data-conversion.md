---
title: "在 IoT 閘道上使用 Azure IoT Edge 進行資料轉換 | Microsoft Docs"
description: "使用 IoT 閘道可從 Azure IoT Edge 透過自訂模組轉換感應器資料的格式。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 閘道資料轉換, iot 閘道資料轉換"
ms.assetid: 75f2573d-500b-4405-bff7-61021c4c3500
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: d5c735a4adbc59e9526ec4fd40720c5ec136d63d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-iot-gateway-for-sensor-data-transformation-with-azure-iot-edge"></a>搭配 Azure IoT Edge 使用 IoT 閘道進行感應器資料轉換

> [!NOTE]
> 開始本教學課程之前，請確定您循序完成下列課程：
> * [將 Intel NUC 設定為 IoT 閘道](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
> * [使用 IoT 閘道將裝置連接到雲端 - SensorTag 至 Azure IoT 中樞](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

IoT 閘道的其中一個用途是要在傳送資料至雲端之前處理所收集的資料。 Azure IoT Edge 推出的模組可用來建立和組合以形成資料處理工作流程。 模組會接收訊息、對其執行某個動作，然後移動它，以供其他模組處理。

## <a name="what-you-learn"></a>您學到什麼

您了解如何建立模組，將訊息從 SensorTag 轉換成不同格式。

## <a name="what-you-do"></a>您要做什麼

* 建立模組，將已接收的訊息轉換成 .json 格式。
* 編譯模組。
* 透過 Azure IoT Edge 將模組新增至 BLE 範例應用程式。
* 執行範例應用程式。

## <a name="what-you-need"></a>您需要什麼

* 循序完成下列教學課程：
  * [將 Intel NUC 設定為 IoT 閘道](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
  * [使用 IoT 閘道將裝置連接到雲端 - SensorTag 至 Azure IoT 中樞](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
* 在主機電腦上執行的 SSH 用戶端。 Windows 上建議使用 PuTTY。 Linux 和 macOS 已隨附 SSH 用戶端。
* 從 SSH 用戶端存取閘道所用的 IP 位址和使用者名稱和密碼。
* 網際網路連線。

## <a name="create-a-module"></a>建立模組

1. 在主機電腦上，執行 SSH 用戶端並連線至 IoT 閘道。
1. 執行下列命令，將轉換模組的原始程式檔從 GitHub 複製到 IoT 閘道的主目錄：

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-nuc-gateway-customized-module.git
   ```

   這是以 C 程式設計語言撰寫的原生 Azure Edge 模組。 模組會將所接收訊息的格式轉換成下列其中一個：

   ```json
   {"deviceId": "Intel NUC Gateway", "messageId": 0, "temperature": 0.0}
   ```

## <a name="compile-the-module"></a>編譯模組

若要編譯模組，請執行下列命令：

```bash
cd iot-hub-c-intel-nuc-gateway-customized-module/my_module
# change the build script runnable
chmod 777 build.sh
# remove the invalid windows character
sed -i -e "s/\r$//" build.sh
# run the build shell script
./build.sh
```

編譯完成之後，您會得到 `libmy_module.so` 檔案。 記下此檔案的絕對路徑。

## <a name="add-the-module-to-the-ble-sample-application"></a>將模組新增至 BLE 範例應用程式

1. 執行下列命令來前往範例資料夾：

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples
   ```

1. 執行下列命令來開啟組態檔：

   ```bash
   vi ble_gateway.json
   ```

1. 插入下列程式碼至 `modules` 區段來新增模組。

   ```json
   {
       "name": "MyModule",
       "loader": {
           "name": "native",
           "entrypoint":{
               "module.path": "[Your libmy_module.so path]"
               }
            },
        "args": null
    },
    ```

1. 將程式碼中的 `[Your libmy_module.so path]` 取代為 libmy_module.so 檔案的絕對路徑。
1. 使用下列程式碼取代 `links` 區段中的程式碼：

   ```json
   {
       "source": "SensorTag",
       "sink": "MyModule"
   },
   {
       "source": "MyModule",
       "sink": "mapping"
   }
   ```

1. 按下 `ESC` 然後輸入 `:wq` 儲存檔案。

## <a name="run-the-sample-application"></a>執行範例應用程式

1. 開啟 SensorTag 的電源。
1. 執行下列命令來設定 SSL_CERT_FILE 環境變數：

   ```bash
   export SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
   ```

1. 執行下列命令來使用新增的模組執行範例應用程式：

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>後續步驟

您已成功使用 IoT 閘道將訊息從 SensorTag 轉換為 .json 格式。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
