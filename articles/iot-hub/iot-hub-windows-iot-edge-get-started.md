---
title: "開始使用 Azure IoT Edge (Windows) | Microsoft Docs"
description: "如何在 Windows 機器上建置 Azure IoT Edge 閘道，並了解 Azure IoT Edge 中的重要概念，例如模組和 JSON 組態檔。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>在 Windows 上探索 Azure IoT Edge 架構

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>執行範例

**build.cmd** 指令碼會在 **iot-edge** 存放庫本機複本的 **build** 資料夾中產生其輸出。 此輸出包含許多檔案，但本範例將焦點放在其中三個：
- 兩個 IoT Edge 模組：**build\\modules\\logger\\Debug\\logger.dll** 和 **build\\modules\\hello_world\\Debug\\hello\_world.dll**。 
- 一個可執行檔：**build\\samples\\hello\_world\\Debug\\hello\_world\_sample.exe**。 此程序會採用一個 JSON 組態檔作為命令列引數。

您在此範例中使用的第四個檔案並不在組建資料夾中，但在複製 iot-edge 存放庫時已包含：
- 一個 JSON 組態檔：**samples\\hello\_world\\src\\hello\_world\_win.json**。 此檔案包含兩個模組的路徑。 它也會宣告 logger.dll 要將輸出寫至何處。 預設為您目前工作目錄中的 **log.txt** 

   >[!NOTE]
   >如果您移動範例模組，或是新增自己的模組來進行測試，請將組態檔中的 **module.path** 值更新成相符的值。 模組路徑是相對於 **hello\_world\_sample.exe**所在的目錄。 

若要執行範例，請遵循下列步驟：

1. 瀏覽至 **iot-edge** 存放庫的本機複本根中的 **build** 資料夾。

1. 執行以下命令：

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. 以下輸出表示已成功執行：

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. 按 **Enter** 鍵即可停止程序。


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
