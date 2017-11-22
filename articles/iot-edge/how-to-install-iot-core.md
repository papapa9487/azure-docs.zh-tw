---
title: "在 IoT 核心版上安裝 Azure IoT Edge | Microsoft Docs"
description: "在 Windows IoT 核心版裝置上安裝 Azure IoT Edge 執行階段"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: be2a80645d23e709d6c5cfb3978498bbe85eca34
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>在 Windows IoT 核心版上安裝 IoT Edge 執行階段 - 預覽

Azure IoT Edge 執行階段即使在微型單板電腦 (SBC) 裝置上也能執行，這類裝置在 IoT 產業中非常普遍。 本文將逐步解說如何在執行 Windows IoT 核心版的 [MinnowBoard Turbot][lnk-minnow] \(英文\) 開發板上佈建執行階段。

## <a name="install-the-runtime"></a>安裝執行階段

1. 在主機系統上安裝 [Windows 10 IoT 核心版儀表板][lnk-core]。
1. 依照[設定裝置][lnk-board]中的步驟，使用 MinnowBoard Turbot/MAX 組建 16299 映像來設定您的開發板。 
1. 開啟裝置電源，然後[使用 PowerShell 從遠端登入][lnk-powershell]。
1. 在 PowerShell 主控台中，[安裝 Docker 二進位檔][lnk-docker-install]。
1. 在 PowerShell 主控台中，執行下列命令來安裝 IoT Edge 執行階段，並確認您的組態：

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   此指令碼提供下列項目： 
   * Python 3.6
   * IoT Edge 控制指令碼 (iotedgectl.exe)

您可能會在遠端 PowerShell 視窗中，看見來自 iotedgectl.exe 工具且以紅色顯示的資訊輸出。 這不一定代表錯誤。 

## <a name="next-steps"></a>後續步驟

現在，您的裝置正在執行 IoT Edge 執行階段，請了解如何[大規模部署和監視 IoT Edge 模組][lnk-deploy]。

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers