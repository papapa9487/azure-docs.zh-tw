---
title: "在 Windows 上模擬 Azure IoT Edge | Microsoft Docs"
description: "在 Windows 中的模擬裝置上安裝 Azure IoT Edge 執行階段及部署您的第一個模組"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0207418cf71902ce9bc9d2911124d1d46889d893
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>在 Windows 中的模擬裝置上部署 Azure IoT Edge -  預覽

Azure IoT Edge 可讓您在裝置上執行分析和資料處理，而不必將所有的資料推送至雲端。 IoT Edge 教學課程會示範如何部署不同類型的模組，從 Azure 服務或自訂程式碼建立，但首先您需要能夠測試的裝置。 

在本教學課程中，您了解如何：

1. 建立 IoT 中樞
2. 註冊 IoT Edge 裝置
3. 啟動 IoT Edge 執行階段
4. 部署模組

![教學課程架構][2]

您在本教學課程中建立的模擬裝置是一個監視器，安裝在會產生溫度、溼度和壓力資料的風力渦輪發電機上。 您之所以需要這些資料，是因為您的渦輪發電機會依據不同天氣條件，產生不同的效能水平。 其他 Azure IoT Edge 教學課程會以您在此所做的工作為基礎，並部署可分析商業見解資料的模組。 

## <a name="prerequisites"></a>必要條件

本教學課程假設您使用執行 Windows 的電腦或虛擬機器，來模擬物聯網裝置。 

>[!TIP]
>如果您在虛擬機器中執行 Windows，請啟用[巢狀虛擬化][lnk-nested]並配置至少 2GB 的記憶體。 

1. 請確定您使用的是受支援的 Windows 版本：
   * Windows 10 
   * Windows Server
2. 安裝[適用於 Windows 的 Docker][lnk-docker] 並確定它正在執行。
3. 在 [Windows 上安裝 Python 2.7][lnk-python]，並確定您可以使用 pip 命令。
4. 執行下列命令以下載 IoT Edge 控制指令碼。

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge 可以執行 Windows 容器或 Linux 容器。 如果您執行下列其中一個 Windows 版本，即可以使用 Windows 容器：
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (組建 16299)
>    * x64 型裝置上的 Windows IoT 核心版 (組建 16299)
>
> 針對 Windows IoT 核心版，請遵循[在 Windows IoT 核心版上安裝 IoT Edge 執行階段][lnk-install-iotcore]中的指示。 否則，只需[設定 Docker 以使用 Windows 容器][lnk-docker-containers]。 使用下列命令來驗證您的必要條件：
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>建立 IoT 中樞

本教學課程從建立您的 IoT 中樞開始。
![建立 IoT 中樞][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>註冊 IoT Edge 裝置

向新建立的 IoT 中樞註冊 IoT Edge 裝置。
![註冊裝置][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>設定 IoT Edge 執行階段

安裝並啟動您的裝置上的 Azure IoT Edge 執行階段。 
![註冊裝置][5]

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其包含兩個模組。 **IoT Edge 代理程式**可協助進行部署及監視 IoT Edge 裝置上的模組。 **IoT Edge 中樞**會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。 當您在新的裝置上設定執行階段時，最初只有 IoT Edge 代理程式會啟動。 當您部署模組時，接著會是 IoT Edge 中樞。 


使用上一節中的 IoT Edge 裝置連接字串來設定執行階段。

```cmd
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

啟動執行階段。

```cmd
iotedgectl start
```

檢查 Docker 以確認 IoT Edge 代理程式是否正作為模組執行中。

```cmd
docker ps
```

![請參閱 Docker 中的 edgeAgent](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>部署模組

在雲端管理您的 Azure IoT Edge 裝置，以部署會將遙測資料傳送到 IoT 中樞的模組。
![註冊裝置][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>檢視產生的資料

在本教學課程中，您可以建立新的 IoT Edge 裝置，並在其中安裝 IoT Edge 執行階段。 然後，您會使用 Azure 入口網站來推送 IoT Edge 模組，讓其無須變更裝置本身就能在裝置上執行。 在此案例中，您推送的模組會建立可在教學課程中使用的環境資料。 

在執行模擬裝置的電腦上再次開啟命令提示字元。 確認從雲端部署的模組是在 IoT Edge 裝置上執行。 

```cmd
docker ps
```

![在您的裝置上檢視三個模組](./media/tutorial-simulate-device-windows/docker-ps2.png)

檢視從 tempSensor 模組傳送至雲端的訊息。 

```cmd
docker logs -f tempSensor
```

![從您的模組中檢視資料](./media/tutorial-simulate-device-windows/docker-logs.png)

您也可以使用 [IoT 中樞總管工具][lnk-iothub-explorer]，檢視裝置正在傳送的遙測資料。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立新的 IoT Edge 裝置，也使用了 Azure IoT Edge 雲端介面來將程式碼部署到裝置上。 現在，您已有模擬裝置，可產生其環境的相關原始資料。 

本教學課程是其他所有 IoT Edge 教學課程的必要條件。 您可以繼續閱讀其他任何教學課程，以了解 Azure IoT Edge 可如何協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [開發 C# 程式碼並部署為模組](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md