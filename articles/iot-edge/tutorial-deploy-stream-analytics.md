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
ms.openlocfilehash: 6cf8e2469a6fe6bac0db6caf9acb182a6349096f
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>將 Azure 串流分析部署為 IoT Edge 模組 - 預覽

IoT 裝置可能會產生大量資料。 若要減少上傳資料的大小，或排除可操作深入解析的來回行程延遲，則這些資料有時候必須先進行分析或處理，然後才送到雲端。

Azure IoT Edge 會利用預先建置的 Azure 服務 IoT Edge 模組，以便快速進行部署。 [Azure 串流分析][azure-stream]屬於此類模組。 您可以從 Azure 串流分析入口網站中建立其作業，然後移至 Azure IoT 中樞入口網站將它部署為 IoT Edge 模組。 

Azure 串流分析針對雲端中或 IoT Edge 上的資料，提供了豐富結構化的查詢語法。 如需 IoT Edge 上串流分析的詳細資訊，請參閱 [Azure 串流分析文件](../stream-analytics/stream-analytics-edge.md)。

本教學課程逐步引導您建立 Azure 串流分析作業，並將它部署在 IoT Edge 裝置上。 這樣做可讓您直接在該裝置上處理本機遙測資料流，並產生在裝置上驅動立即動作的警示。 

教學課程示範兩個模組： 
* 模擬的溫度感應器模組 (tempSensor) 會產生 20 到 120 度的溫度資料 (每隔 5 秒增加 1 度)。 
* 串流分析模組會在 30 秒的平均值達到 70 度時重設 tempSensor。 在生產環境中，您可能會使用此功能用來關閉機器，或在溫度達到危險程度時採取預防措施。 

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立 Azure 串流分析作業以在邊緣上處理資料。
> * 將新的 Azure 串流分析作業與其他 IoT Edge 模組連結。
> * 將 Azure 串流分析作業部署到 IoT Edge 裝置。

## <a name="prerequisites"></a>先決條件

* IoT 中樞。 
* 您在快速入門中，或是有關在 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 中的模擬裝置上部署 Azure IoT Edge 的文章中建立及設定的裝置。 您必須知道裝置連線金鑰和裝置識別碼。 
* 在 IoT Edge 裝置上執行的 Docker。
    * [在 Windows 上安裝 Docker][lnk-docker-windows]。
    * [在 Linux 上安裝 Docker][lnk-docker-linux]。
* IoT Edge 裝置上的 Python 2.7.x。
    * [在 Windows 上安裝 Python 2.7][lnk-python]。
    * 大部分的 Linux 發行版本 (包括 Ubuntu) 都已安裝 Python 2.7。 若要確定 pip 已安裝，請使用下列命令：`sudo apt-get install python-pip`。

## <a name="create-an-azure-stream-analytics-job"></a>建立 Azure 串流分析作業

在本節中，您可以透過建立 Azure 串流分析作業，從 IoT 中樞取用資料、查詢從您裝置送出的遙測資料，然後將結果轉送至 Azure Blob 儲存體容器。 如需詳細資訊，請參閱[串流分析文件][azure-stream]的＜概觀＞一節。 

### <a name="create-a-storage-account"></a>建立儲存體帳戶

需要有 Azure 儲存體帳戶，才能提供端點作為 Azure 串流分析作業中的輸出。 本節中的範例使用 Blob 儲存體類型。 如需詳細資訊，請參閱 [Azure 儲存體文件][azure-storage]的＜Blob＞一節。

1. 在 Azure 入口網站中，移至 [建立資源]，在搜尋方塊中輸入**儲存體帳戶**，然後選取 [儲存體帳戶 - Blob、檔案、資料表、佇列]。

2. 在 [建立儲存體帳戶] 窗格中，輸入您的儲存體帳戶名稱，再選取儲存您 IoT 中樞的相同位置，然後選取 [建立]。 記下名稱以在稍後使用。

    ![建立儲存體帳戶][1]

3. 移至您剛才建立的儲存體帳戶，然後選取 [瀏覽 Blob]。 

4. 為 Azure 串流分析模組建立用來儲存資料的新容器，將存取層級設定為 [容器]，然後選取 [確定]。

    ![儲存體設定][10]

### <a name="create-a-stream-analytics-job"></a>建立串流分析工作

1. 在 Azure 入口網站中，移至 [建立資源] > [物聯網]，然後選取 [串流分析工作]。

2. 在 [新的串流分析工作] 窗格中，執行下列動作：

    a. 在 [工作名稱] 方塊中，輸入作業名稱。
    
    b. 在 [主控環境] 下，選取 [Edge]。
    
    c. 在剩餘的欄位中，使用預設值。

    > [!NOTE]
    > 目前，在「美國西部 2」區域中並不支援 IoT Edge 上的 Azure 串流分析作業。 

3. 選取 [建立]。

4. 在已建立的作業中，於 [工作拓撲] 下選取 [輸入]，然後選取 [新增]。

5. 在 [新的輸入] 窗格中，執行下列動作：

    a. 在 [輸入別名] 方塊中，輸入**溫度**。
    
    b. 在 [來源類型] 方塊中，選取 [資料流]。
    
    c. 在剩餘的欄位中，使用預設值。

   ![Azure 串流分析輸入](./media/tutorial-deploy-stream-analytics/asa_input.png)

6. 選取 [建立]。

7. 在 [工作拓撲] 下選取 [輸出]，然後選取 [新增]。

8. 在 [新的輸出] 窗格中，執行下列動作：

    a. 在 [輸出別名] 方塊中，輸入**警示**。
    
    b. 在剩餘的欄位中，使用預設值。 
    
    c. 選取 [建立]。

   ![Azure 串流分析輸出](./media/tutorial-deploy-stream-analytics/asa_output.png)


9. 在下 [工作拓撲] 下選取 [查詢]，並以下列查詢取代預設文字：

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

10. 選取 [儲存]。

## <a name="deploy-the-job"></a>部署作業

現在您已經準備好要在 IoT Edge 裝置上部署 Azure 串流分析作業。

1. 在 Azure 入口網站中，從您的 IoT 中樞內移至 [IoT Edge (預覽)]，然後開啟 IoT Edge 裝置的詳細資料頁面。

2. 選取 [設定模組]。  
    如果您先前在此裝置上部署過 tempSensor 模組，它可能會自動填入。 如果沒有，請執行下列動作來新增模組：

   a. 選取 [新增 IoT Edge 模組]。

   b. 針對名稱，輸入 **tempSensor**。
    
   c. 針對映像 URI，輸入 **microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview**。 

   d. 其他設定保留不變。
   
   e. 選取 [儲存]。

3. 若要新增 Azure 串流分析 Edge 作業，請選取 [匯入 Azure 串流分析 IoT Edge 模組]。

4. 選取訂用帳戶與您建立的 Azure 串流分析 Edge 作業。 

5. 選取訂用帳戶與您建立的儲存體帳戶，然後選取 [儲存]。

    ![設定模組][6]

6. 複製已自動產生的 Azure 串流分析模組名稱。 

    ![溫度模組][11]

7. 若要設定路由，請選取 [下一步]。

8. 將下列程式碼複製到 [路由]。 使用您複製的模組名稱取代 _{moduleName}_：

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

9. 選取 [下一步] 。

10. 在 [檢閱範本] 步驟中，選取 [提交]。

11. 返回裝置的詳細資料頁面，然後選取 [重新整理]。  
    您應該會看到新的串流分析模組正在與 IoT Edge 代理程式模組和 IoT Edge 中樞一起執行。

    ![模組輸出][7]

## <a name="view-data"></a>檢視資料

現在您可以移至您的 IoT Edge 裝置，確認 Azure 串流分析模組和 tempSensor 模組之間的互動。

1. 檢查是否所有模組皆在 Docker 中執行：

   ```cmd/sh
   docker ps  
   ```

   ![Docker 輸出][8]

2. 檢視所有系統記錄和計量資料。 使用串流分析模組名稱：

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

您應該能夠看到機器的溫度逐漸上升，直到在 30 秒內到達 70 度。 接著串流分析模組會觸發程序重設，且機器溫度降回為 21 度。 

   ![Docker 記錄][9]


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定了 Azure 儲存體容器與串流分析作業來分析 IoT Edge 裝置的資料。 接著，您載入了自訂 Azure 串流分析模組，透過串流將資料從您的裝置移至用於下載的 Blob 中。 若要進一步了解 Azure IoT Edge 可如何為您的企業建立解決方案，請繼續閱讀其他教學課程。

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
