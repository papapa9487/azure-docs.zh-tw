---
title: "在 Linux 上模擬 Azure IoT Edge | Microsoft Docs"
description: "在 Linux 中的模擬裝置上安裝 Azure IoT Edge 執行階段及部署您的第一個模組"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 10/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 327a959ad97897fd19f45a0599f37492938df104
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux---preview"></a>在 Linux 中的模擬裝置上部署 Azure IoT Edge - 預覽

Azure IoT Edge 可讓您在裝置上執行分析和資料處理，而不必將所有的資料推送至雲端。 IoT Edge 教學課程會示範如何部署不同類型的模組，從 Azure 服務或自訂程式碼建立，但首先您需要能夠測試的裝置。 

在本教學課程中，您了解如何：

1. 建立 IoT 中樞
2. 註冊 IoT Edge 裝置
3. 啟動 IoT Edge 執行階段
4. 部署模組

![教學課程架構][2]

您在本教學課程中建立的模擬裝置是一個監視器，會產生溫度、溼度和壓力資料。 其他 Azure IoT Edge 教學課程會以您在此所做的工作為基礎，並部署可分析商業見解資料的模組。 

## <a name="prerequisites"></a>必要條件

本教學課程假設您使用執行 Linux 的電腦或虛擬機器，來模擬物聯網裝置。 需要下列服務才能成功部署 IoT Edge 裝置：

- [安裝適用於 Linux 的 Docker][lnk-docker-ubuntu] 並確定它正在執行。 
- 大部分的 Linux 發行版本 (包括 Ubuntu) 都已安裝 Python 2.7。 請使用下列命令確定 pip 已安裝：`sudo apt-get install python-pip`。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

本教學課程從建立您的 IoT 中樞開始。
![建立 IoT 中樞][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>註冊 IoT Edge 裝置

向新建立的 IoT 中樞註冊 IoT Edge 裝置。
![註冊裝置][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>安裝並啟動 IoT Edge 執行階段

安裝並啟動您裝置上的 Azure IoT Edge 執行階段。 
![註冊裝置][5]

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其包含兩個模組。 **IoT Edge 代理程式**可協助進行部署及監視 IoT Edge 裝置上的模組。 **IoT Edge 中樞**會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。 當您在新的裝置上設定執行階段時，最初只有 IoT Edge 代理程式會啟動。 當您部署模組時，接著會是 IoT Edge 中樞。 

在您要執行 IoT Edge 裝置的電腦上，下載 IoT Edge 控制指令碼：
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

使用上一節中的 IoT Edge 裝置連接字串來設定執行階段：
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

啟動執行階段：
```cmd
sudo iotedgectl start
```

檢查 Docker 以確認 IoT Edge 代理程式是否正作為模組執行中：
```cmd
sudo docker ps
```

![請參閱 Docker 中的 edgeAgent](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>部署模組

在雲端管理您的 Azure IoT Edge 裝置，以部署會將遙測資料傳送到 IoT 中樞的模組。
![註冊裝置][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>檢視產生的資料

在本教學課程中，您可以建立新的 IoT Edge 裝置，並在其中安裝 IoT Edge 執行階段。 然後，您會使用 Azure 入口網站來推送 IoT Edge 模組，讓其無須變更裝置本身就能在裝置上執行。 在此案例中，您推送的模組會建立可在教學課程中使用的環境資料。 

在執行模擬裝置的電腦上再次開啟命令提示字元。 確認從雲端部署的模組是在 IoT Edge 裝置上執行：

```cmd
sudo docker ps
```

![在您的裝置上檢視三個模組](./media/tutorial-simulate-device-linux/docker-ps2.png)

檢視從 tempSensor 模組傳送至雲端的訊息：

```cmd
sudo docker logs -f tempSensor
```

![從您的模組中檢視資料](./media/tutorial-simulate-device-linux/docker-logs.png)

您也可以使用 [IoT 中樞總管工具][lnk-iothub-explorer]，檢視裝置正在傳送的遙測資料。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立新的 IoT Edge 裝置，也使用了 Azure IoT Edge 雲端介面來將程式碼部署到裝置上。 現在，您已有模擬裝置，可產生其環境的相關原始資料。 

本教學課程是其他所有 IoT Edge 教學課程的必要條件。 您可以繼續閱讀其他任何教學課程，以了解 Azure IoT Edge 可如何協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [開發 C# 程式碼並部署為模組](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
