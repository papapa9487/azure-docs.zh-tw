---
title: "使用 Azure IoT Edge部署 Azure Machine Learning | Microsoft Docs"
description: "將 Azure Machine Learning 作為模組部署至邊緣裝置"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9b8475dcc51fb24fadd1faa4a2008b25a4464080
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>將 Azure Machine Learning 部署為 IoT Edge 模組 - 預覽

您可以使用 IoT Edge 模組來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程會逐步引導您部署 Azure Machine Learning 模組，以根據模擬 IoT Edge 裝置上的感應器資料預測裝置失效的時間 (模擬裝置是在＜在 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 上的模擬裝置中部署 Azure IoT Edge＞教學課程中所建立)。 您會了解如何： 

> [!div class="checklist"]
> * 將 Azure Machine Learning 模組部署至 IoT Edge 裝置
> * 檢視產生的資料

如果您想要在方案中使用您自己的 [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/)模模組，您將會為 IoT Edge [部署模型](https://aka.ms/aml-iot-edge-doc)，並將其裝載在容器登錄中，例如 [Azure Container Registry](../container-registry/index.yml) 或 Docker。

## <a name="prerequisites"></a>必要條件

* 您在快速入門或第一份教學課程中所建立的 Azure IoT Edge 裝置。
* IoT 中樞 (連接您的 IoT Edge 裝置) 的 IoT 中樞連接字串。
* Azure ML 容器

## <a name="create-the-azure-ml-container"></a>建立 Azure ML 容器
若要建立您的 Azure ML 容器，請遵循 [適用於 Azure IoT Edge 的 AI 工具組](https://aka.ms/aml-iot-edge-anomaly-detection)中的指示。

## <a name="run-the-solution"></a>執行方案

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 IoT 中樞。
1. 移至 [IoT Edge (預覽)] 並選取您的 IoT Edge 裝置。
1. 選取 [設定模組]。
1. 選取 [新增 IoT Edge 模組]。
1. 在 [名稱] 欄位中，輸入 `tempSensor`。
1. 在 [映像 URI] 欄位中，輸入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
1. 其他設定保留不變，然後選取 [儲存]。
1. 一樣在 [新增模組] 步驟中，再次選取 [新增 IoT Edge 模組]。
1. 在 [名稱] 欄位中，輸入您在上一節中創造的容器名稱。 請參閱[適用於 Azure IoT Edge 的 AI 工具組](https://aka.ms/aml-iot-edge-anomaly-detection)來協助尋找該名稱。
1. 在 [映像] 欄位中，輸入您在上一節中創造的容器映像 URI。 請參閱[適用於 Azure IoT Edge 的 AI 工具組](https://aka.ms/aml-iot-edge-anomaly-detection)來協助尋找該映像。
1. 按一下 [儲存] 。
1. 回到 [新增模組] 步驟中，選取 [下一步]。
1. 更新模組的路由：
1. 在 [指定路由] 步驟中，將下列 JSON 複製到文字方塊。 模組會將所有訊息發佈到 Edge 執行階段。 執行階段中的宣告式規則會定義這些訊息的流向。 在本教學課程中，您需要兩個路由。 第一個路由會透過所有 Azure Machine Learning 使用的 mlInput 端點，將訊息從溫度感應器傳輸到機器學習模組。 第二個路由會將訊息從機器學習模組傳輸到 IoT 中樞。 在此路由，''mlOutput'' 是所有 Azure Machine Learning 模組用來輸出資料的 endput，而 ''upstream'' 是告知 Edge 中樞將訊息傳送至 IoT 中樞的特殊目的地。 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. 按一下 [下一步] 。 
1. 在 [檢閱範本] 步驟中，按一下 [提交]。 
1. 返回裝置的詳細資料頁面，按一下 [重新整理]。  您應該會看到新的 ''machinelearningmodule'' 正在與「tempSensor 模組」和「IoT Edge 執行階段」一起執行。

## <a name="view-generated-data"></a>檢視產生的資料

 在 VS Code 中，使用**檢視 |命令選擇區...| IoT：開始監視 D2C 訊息**功能表命令來監視抵達 IoT 中樞的資料。 

## <a name="next-steps"></a>後續步驟

在此教學課程中，您可以部署受到 Azure Machine Learning 支援的 IoT Edge 模組。 您可以繼續閱讀其他任何教學課程，以了解 Azure IoT Edge 有什麼其他方法可協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [將 Azure Functions 部署為模組](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md