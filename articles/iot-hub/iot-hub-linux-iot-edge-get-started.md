---
title: "開始使用 Azure IoT Edge (Linux) | Microsoft Docs"
description: "如何在 Linux 機器上建置閘道，並了解 Azure IoT Edge 中的重要概念，例如模組和 JSON 組態檔。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: e2f26c1abe09feff77e1e2633d8bfcf4ca527aee
ms.contentlocale: zh-tw
ms.lasthandoff: 09/23/2017

---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>在 Linux 上探索 Azure IoT Edge 架構

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>如何執行範例

**build.sh** 指令碼會在 **iot-edge** 存放庫本機複本的 **build** 資料夾中產生其輸出。 這輸出包含在此範例中使用的兩個 IoT Edge 模組。

build 指令碼會將 **liblogger.so** 放在 **build/modules/logger/** 資料夾中，並將 **libhello\_world.so** 放在 **build/modules/hello_world/** 資料夾中。 使用這些路徑作為**模組路徑**值 (如下列範例 JSON 設定檔所示)。

hello\_world\_sample 程序會採用 JSON 組態檔的路徑作為命令列引數。 下列範例 JSON 檔案提供於 SDK 儲存裝置中 (位於 **samples/hello\_world/src/hello\_world\_lin.json**)。 除非您修改 build 指令碼以將 IoT Edge 模組或範例可執行檔放置在非預設位置，否則此組態檔將保有原始功能。

> [!NOTE]
> 模組路徑是 hello\_world\_sample 可執行檔啟動之目前工作目錄的相對路徑，不是可執行檔所在的目錄。 範例 JSON 組態檔的預設值為在目前的工作目錄中寫入 'log.txt'。

```json
{
    "modules" :
    [
        {
            "name" : "logger",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args" : {"filename":"log.txt"}
        },
        {
            "name" : "hello_world",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/hello_world/libhello_world.so"
            }
            },
            "args" : null
        }
    ],
    "links":
    [
        {
            "source": "hello_world",
            "sink": "logger"
        }
    ]
}
```

1. 瀏覽至 **iot-edge** 存放庫的本機複本根中的 **build** 資料夾。

1. 執行以下命令：

    ```sh
    ./samples/hello_world/hello_world_sample ../samples/hello_world/src/hello_world_lin.json`
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]


