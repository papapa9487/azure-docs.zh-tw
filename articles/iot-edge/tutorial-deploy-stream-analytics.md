---
title: "使用 Azure IoT Edge部署 Azure 串流分析 | Microsoft Docs"
description: "將 Azure 串流分析作為模組部署至邊緣裝置"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5a143bbf7abb5304ac51782d517c02ec184a05a2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>將 Azure 串流分析部署為 IoT Edge 模組 - 預覽

IoT 裝置可能會產生大量資料。 有時候，這些資料必須先進行分析或處理，然後才送到雲端，這樣可減少上傳資料的大小，或排除可操作深入解析 (actionable insight) 的往返延遲。

IoT Edge 會利用預先建置的 Azure 服務 IoT Edge 模組，以便快速進行部署，而 [Azure 串流分析][azure-stream] (ASA) 就是這類模組。 您可以從其入口網站中建立 ASA 作業，然後前往 IoT 中樞入口網站將該作業部署為 IoT Edge 模組。  

Azure 串流分析針對雲端中或 IoT Edge 上的資料，提供了豐富結構化的查詢語法。 如需 IoT Edge 上的 ASA 相關資訊，請參閱 [ASA 文件](../stream-analytics/stream-analytics-edge.md)。

本教學課程會引導您建立 Azure 串流分析作業，以及其在 IoT Edge 裝置上的部署，以便直接在裝置上處理本機遙測資料流，並產生警示以在裝置上立即採取動作。  此教學課程中包含兩個模組。 模擬的溫度感應器模組 (tempSensor) 會產生 20 到 120 度的溫度資料 (每隔 5 秒增加 1 度)。 串流分析模組會在平均 30 秒就會達到 70 度時，重設 tempSensor。 在生產環境中，這項功能可能會用來關閉機器，或在溫度達到危險層級時採取預防措施。 

您會了解如何：

> [!div class="checklist"]
> * 在 Edge 上建立 ASA 作業來處理資料
> * 將新的 ASA 作業與其他 IoT Edge 模組連線
> * 將 ASA 作業部署到 IoT Edge 裝置

## <a name="prerequisites"></a>必要條件

* IoT 中樞 
* 您在快速入門中，或是在＜在 [Windows][lnk-tutorial1-win] 和 [Linux][lnk-tutorial1-lin] 上的模擬裝置中部署 Azure IoT Edge＞中建立及設定的裝置。 您必須知道裝置連線金鑰和裝置識別碼。 
* 在 IoT Edge 裝置上執行的 Docker
    * [在 Windows 上安裝 Docker][lnk-docker-windows]
    * [在 Linux 上安裝 Docker][lnk-docker-linux]
* IoT Edge 裝置上的 Python 2.7.x
    * [在 Windows 上安裝 Python 2.7][lnk-python]。
    * 大部分的 Linux 發行版本 (包括 Ubuntu) 都已安裝 Python 2.7。  請使用下列命令確定 pip 已安裝：`sudo apt-get install python-pip`。


## <a name="create-an-asa-job"></a>建立 ASA 作業

在本節中，您可以透過建立 Azure 串流分析作業，從 IoT 中樞取用資料、查詢從您裝置送出的遙測資料，以及將結果轉送至 Azure 儲存體容器 (BLOB)。 如需詳細資訊，請參閱[串流分析文件][azure-stream]的**概觀**一節。 

### <a name="create-a-storage-account"></a>建立儲存體帳戶

Azure 儲存體帳戶必須提供端點，才可作為 ASA 作業中的輸出。 下列範例會使用 BLOB 儲存體類型。  如需詳細資訊，請參閱 [Azure 儲存體文件][azure-storage]中的 **Blob** 一節。

1. 在 Azure 入口網站中，瀏覽至**建立資源**並在搜尋列中輸入 `Storage account`。 選取**儲存體帳戶 - blob、檔案、資料表、佇列**。

2. 輸入儲存體帳戶的名稱，並選取與您 IoT 中樞所在相同的位置。 按一下 [建立] 。 記下名稱以在稍後使用。

    ![新儲存體帳戶][1]

3. 瀏覽至您剛建立的儲存體帳戶。 按一下 [瀏覽 blob]。 
4. 建立新的 ASA 模組容器來儲存資料。 將存取層級設為**容器**。 按一下 [確定] 。

    ![儲存體設定][10]

### <a name="create-a-stream-analytics-job"></a>建立串流分析工作

1. 在 Azure 入口網站中，瀏覽至 [建立資源] > [物聯網]，然後選取 [串流分析作業]。

2. 輸入名稱，選擇 [Edge] 作為主控環境，並使用其餘的預設值。  按一下 [建立] 。

    >[!NOTE]
    >目前，「美國西部 2」區域並不支援 IoT Edge 上的 ASA 作業。 

3. 前往已建立作業。 選取 [輸入] 然後按一下 [新增]。

4. 針對輸入別名，輸入 `temperature`，將來源類型設為 [資料流]，然後使用其他參數的預設值。 按一下 [建立] 。

   ![ASA 輸入](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. 選取 [輸出] 然後按一下 [新增]。

6. 針對輸出別名，輸入 `alert`，然後使用其他參數的預設值。 按一下 [建立] 。

   ![ASA 輸出](./media/tutorial-deploy-stream-analytics/asa_output.png)


7. 選取 [查詢]。
8. 使用下列查詢取代預設文字：

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```
9. 按一下 [儲存] 。

## <a name="deploy-the-job"></a>部署作業

現在您已經準備好要在 IoT Edge 裝置上部署 ASA 作業。

1. 在 Azure 入口網站中，從您的 IoT 中樞內瀏覽至 [IoT Edge (預覽)]，並開啟 IoT Edge 裝置的詳細資料頁面。
1. 選取 [設定模組]。
1. 如果您先前在此裝置上部署過 tempSensor 模組，其可能會自動填入。 如果沒有，請使用下列步驟來新增模組：
   1. 按一下 [新增 IoT Edge 模組]。
   1. 輸入 `tempSensor` 作為名稱，以及 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` 作為影像 URI。 
   1. 其他設定保留不變，然後按一下 [儲存]。
1. 若要新增 ASA Edge 作業，請選取 [匯入 Azure 串流分析 IoT Edge 模組]。
1. 選取訂用帳戶與您建立的 ASA Edge 作業。 
1. 選取訂用帳戶與您建立的儲存體帳戶。 按一下 [儲存] 。

    ![設定模組][6]

1. 複製已自動產生的 ASA 模組名稱。 

    ![溫度模組][11]

1. 按一下 [下一步] 來設定路由。
1. 將下列命令複製到 [路由]。  使用您複製的模組名稱取代 _{moduleName}_：

    ```json
    {
        "routes": {                                                               
          "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream", 
          "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream", 
          "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")", 
          "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")" 
        }
    }
    ```

1. 按一下 [下一步] 。

1. 在 [檢閱範本] 步驟中，按一下 [提交]。

1. 返回裝置的詳細資料頁面，按一下 [重新整理]。  您應該會看到新的串流分析模組正在與 **IoT Edge 代理程式**模組和 **IoT Edge 中樞**一起執行。

    ![模組輸出][7]

## <a name="view-data"></a>檢視資料

現在您可以移至您的 IoT Edge 裝置，確認 ASA 模組和 tempSensor 模組之間的互動。

檢查是否所有模組皆在 Docker 中執行：

   ```cmd/sh
   docker ps  
   ```

   ![Docker 輸出][8]

查看所有系統記錄和計量資料。 使用串流分析模組名稱：

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

您應該能夠看到機器的溫度逐漸上升，直到在 30 秒內到達 70 度。 接著串流分析模組會觸發程序重設，且機器溫度降回為 21 度。 

   ![Docker 記錄][9]


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定了 Azure 儲存體容器與串流分析作業來分析 IoT Edge 裝置中的資料。  接著，您載入了自訂 ASA 模組，透過串流將資料從您的裝置移至用於下載的 BLOB 中。  您可以繼續閱讀其他教學課程，以進一步了解 Azure IoT Edge 可如何為您的企業建立解決方案。

> [!div class="nextstepaction"] 
> [將 Azure Machine Learning 模型部署為模組][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/