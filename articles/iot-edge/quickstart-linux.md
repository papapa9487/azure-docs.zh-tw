---
title: "快速入門 Azure IoT Edge + Linux | Microsoft Docs"
description: "在模擬的 Edge 裝置上執行分析以試用 Azure IoT Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb93efcf00cb7b165c497d7ef38685f80bce84c0
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-linux-device---preview"></a>快速入門：從 Azure 入口網站將您的第一個 IoT Edge 模組部署至 Linux 裝置 - 預覽

Azure IoT Edge 會將雲端的強大功能移至您的物聯網裝置。 在本主題中，了解如何使用雲端介面將預先建置的程式碼 IoT 從遠端部署到 IoT Edge 裝置。

如果您沒有使用中的 Azure 訂用帳戶，請在開始前建立[免費帳戶][lnk-account]。

## <a name="prerequisites"></a>必要條件

若要完成這項工作，請使用您的電腦或虛擬機器來模擬物聯網裝置。 需要下列服務才能成功部署 IoT Edge 裝置：

- [在 Linux 上安裝 Docker][lnk-docker-ubuntu] 並確定它正在執行。 
- 大部分的 Linux 發行版本 (包括 Ubuntu) 都已安裝 Python 2.7。 請使用下列命令確定 pip 已安裝：`sudo apt-get install python-pip`。

## <a name="create-an-iot-hub-with-azure-cli"></a>使用 Azure CLI 建立 IoT 中樞

在您的 Azure 訂用帳戶中建立 IoT 中樞。 IoT 中樞的可用層級適用於本快速入門。 如果您在過去已使用過 IoT 中樞，並已建立可用的中樞，就可以跳過本節並移至[註冊 IoT Edge 裝置][anchor-register]。 每個訂用帳戶只能有一個免費的 IoT 中樞。 

1. 登入 [Azure 入口網站][lnk-portal]。 
1. 選取 [Cloud Shell] 按鈕。 

   ![[Cloud Shell] 按鈕][1]

1. 建立資源群組。 下列程式碼在**美國西部**區域中會建立一個名為 **IoTEdge** 的資源群組：

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. 在您的新資源群組中建立 IoT 中樞。 下列程式碼會在資源群組 **IoTEdge** 中建立一套名為 **MyIotHub** 的免費 **F1** 中樞：

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>註冊 IoT Edge 裝置

建立模擬裝置的裝置身分識別，以便與 IoT 中樞通訊。 由於 IoT Edge 裝置的行為和管理方式不同於典型 IoT 裝置，您從開頭就要將此宣告為 IoT Edge 裝置。 

1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞。
1. 選取 [IoT Edge (預覽)]。
1. 選取 [新增 IoT Edge 裝置]。
1. 為您的模擬裝置提供唯一的裝置識別碼。
1. 選取 [儲存] 來新增您的裝置。
1. 從裝置清單中選取您的新裝置。 
1. 複製 [連接字串 - 主索引鍵] 的值並加以儲存。 您將在下一節中使用此值設定 IoT Edge 執行階段。 

## <a name="install-and-start-the-iot-edge-runtime"></a>安裝並啟動 IoT Edge 執行階段

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其包含兩個模組。 首先，IoT Edge 代理程式可協助進行部署及監視 IoT Edge 裝置上的模組。 第二，IoT Edge 中樞會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。 

在您要執行 IoT Edge 裝置的電腦上，下載 IoT Edge 控制指令碼：
```python
sudo pip install -U azure-iot-edge-runtime-ctl
```

使用上一節中的 IoT Edge 裝置連接字串來設定執行階段：
```python
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

啟動執行階段：
```python
sudo iotedgectl start
```

檢查 Docker 以確認 IoT Edge 代理程式是否正作為模組執行中：
```python
sudo docker ps
```

## <a name="deploy-a-module"></a>部署模組

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>檢視產生的資料

在此快速入門中，您可以建立新的 IoT Edge 裝置，並在其中安裝 IoT Edge 執行階段。 然後，您會使用 Azure 入口網站來推送 IoT Edge 模組，讓其無須變更裝置本身就能在裝置上執行。 在此案例中，您推送的模組會建立可在教學課程中使用的環境資料。 

檢視從 tempSensor 模組送出的訊息：

```cmd/sh
sudo docker logs -f tempSensor
```

您也可以使用 [IoT 中樞總管工具][lnk-iothub-explorer]，檢視裝置正在傳送的遙測資料。 

## <a name="clean-up-resources"></a>清除資源

當您不再需要您所建立的 IoT 中樞時，可以使用 [az iot hub delete][lnk-delete] 命令來移除資源及任何與其相關聯的裝置：

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>後續步驟

您已了解如何將 IoT Edge 模組部署到 IoT Edge 裝置。 現在，請嘗試部署不同類型的 Azure 服務作為模組，以便您可以在 Edge 分析資料。 

* [將您自己的程式碼部署為模組](tutorial-csharp-module.md)
* [將 Azure Functions 部署為模組](tutorial-deploy-function.md)
* [將 Azure 串流分析部署為模組](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
