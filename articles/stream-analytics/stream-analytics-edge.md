---
title: "IoT Edge 上的 Azure 串流分析 (預覽)"
description: "在 Azure 串流分析中建立 Edge 作業，並將其部署至執行 Azure IoT Edge 的裝置中。"
keywords: "資料流, iot, edge"
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/15/2017
ms.author: jeanb
ms.openlocfilehash: 6e94758581bd510e58a709a53e30c11a5c1f1b62
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>IoT Edge 上的 Azure 串流分析 (預覽)

> [!IMPORTANT]
> 這項功能為預覽狀態。 我們不建議用於生產環境。
 
IoT Edge 上的 Azure 串流分析 (ASA) 讓開發人員能夠在更接近 IoT 裝置的地方部署近乎即時的分析智慧，從而使裝置產生的資料充分發揮價值。 針對需要低延遲、復原、有效使用頻寬和相容性的客戶所設計，企業現在可以部署接近產業作業的控制邏輯，並補充在雲端中完成的巨量資料分析。  
IoT Edge 上的 Azure 串流分析會在 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 架構內執行，並且在 ASA 中建立作業之後，可以使用 IoT 中樞完成部署及管理 ASA 作業。
這項功能處於預覽狀態，如果您有任何問題或意見反應，可以使用[本調查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u)連絡產品小組。 

## <a name="scenarios"></a>案例
![高階圖表](media/stream-analytics-edge/ASAedge_highlevel.png) 以下是在 Edge 上執行 ASA 特別有趣的幾個典型情節：
* **低延遲命令和控制**：例如，需要製造安全系統才能回應包含超低延遲的作業資料。 透過 IoT Edge 上的 ASA，您可以近乎即時地分析感應器資料，並在偵測到異常時發出命令來停止電腦或觸發程序的警示。
*   **有限的雲端連線能力**：諸如遠端採礦設備、已連線的容器或海外深入探索等任務關鍵系統都必須分析和回應資料，即使是當雲端連線斷斷續續時也一樣。 利用 ASA，您的串流邏輯就會獨立執行網路連線，從而能夠選擇傳送至雲端進行進一步處理或儲存體的項目。
* **受限頻寬**：噴射引擎或連線的車輛所產生的資料量可能會很大，在將資料傳送到雲端之前，必須先篩選或預先處理。 您可以使用 ASA 來篩選或彙總必須傳送至雲端的資料。
* **合規性**：法規合規性可能會要求某些資料在傳送至雲端之前，先在本機進行匿名或彙總。 透過 ASA，您 

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure 串流分析中的 Edge 作業
### <a name="what-is-an-edge-job"></a>什麼是 "Edge" 作業？

ASA Edge 作業會作為 [Azure IoT Edge 執行階段](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)內的模組執行。 它們是由兩個部分組成：
1.  負責作業定義的雲端部分：使用者在雲端中定義輸入、輸出、查詢和其他設定 (順序錯亂的事件等)。
2.  在本機執行之 IoT Edge 模組上的 ASA。 它包含 ASA 複雜事件處理引擎，並會從雲端接收作業定義。 

ASA 會使用 IoT 中樞將 Edge 作業部署到裝置。 關於 [IoT Edge 部署的詳細資訊可以在這裡看到](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)。

![Edge 作業](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>安裝指示
下表說明高階步驟。 後面幾節提供詳細資訊。
|      |步驟   | 位置     | 注意事項   |
| ---   | ---   | ---       |  ---      |
| 1   | **建立 ASA Edge 作業**   | Azure 入口網站      |  建立新作業，請選取 **Edge** 作為**裝載環境**。 <br> 這些作業會從雲端建立/管理，並在您自己的 IoT Edge 裝置上執行。     |
| 2   | **建立儲存體容器**   | Azure 入口網站       | 儲存體容器是用來儲存您的作業定義，您的 IoT 裝置可在其中加以存取。 <br>  您可以重複使用任何現有的儲存體容器。     |
| 3   | **在您的裝置上設定 IoT Edge 環境**   | 裝置      | 適用於 [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) 的指示。          |
| 4   | **在 IoT Edge 裝置上部署 ASA**   | Azure 入口網站      |  ASA 作業定義已匯出至先前建立的儲存體容器。       |
您可以遵循[此逐步教學課程](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)，在 IoT Edge 在部署您的第一個 ASA 作業。

#### <a name="create-an-asa-edge-job"></a>建立 ASA Edge 作業
1. 從 Azure 入口網站中，建立新的「串流分析作業」。 [在這裡導向連結以建立新的 ASA 作業](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob)。
2. 在 [建立] 畫面中，選取 **Edge** 作為 **裝載環境**(請參閱下圖) ![作業建立](media/stream-analytics-edge/ASAEdge_create.png)
3. 作業定義
    1. **定義輸入資料流**。 定義適用於您作業的一或多個輸入資料流。
    2. 定義參考資料 (選擇性)。
    3. **定義輸出資料流**。 定義適用於您作業的一或多個輸出資料流。 
    4. **定義查詢**。 使用內嵌編輯器在雲端中定義 ASA 查詢。 編譯器會自動檢查針對 ASA Edge 啟用的語法。 您也可以上傳範例資料來測試您的查詢。 
4. 設定選擇性設定
    1. **事件順序**。 您可以在入口網站中設定順序錯亂的事件。 您可以在[這裡](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396)取得文件。
    2. **地區設定**。 設定國際化格式。


#### <a name="create-a-storage-container"></a>建立儲存體容器
需要儲存體容器，才能匯出 ASA 已編譯查詢和作業組態。 它可用來設定包含您特定查詢的 ASA Docker 映像。 
1. 依照[這些指示](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)從 Azure 入口網站建立儲存體帳戶。 您可以保留所有預設選項將此帳戶用於 ASA。
2. 在新建立的儲存體帳戶中，建立 blob 儲存體容器：
    1. 按一下 [Blob]，然後按一下 [+ 容器]。 
    2. 輸入名稱，並將容器保留為「私人」


> [!Note]
> 建立部署時，ASA 會將作業定義匯出至儲存體容器中。 這個作業定義在部署的持續期間都會保持相同。 因此，如果您需要更新 Edge 上執行的作業，就必須在 ASA 中編輯作業，然後在 IoT 中樞中建立新的部署。


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>在您的裝置上設定 IoT Edge 環境
可以在執行 Azure IoT Edge 的裝置上部署 Edge 作業。
若要這麼做，您必須遵循這些步驟：
- 建立 IoT 中樞；
- 在 Edge 裝置上安裝 Docker 和 IoT Edge 執行階段；
- 在 IoT 中樞中將您的裝置設定為「IoT Edge 裝置」。

適用於 [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) 的 IoT Edge 文件中會說明這些步驟。  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>在 IoT Edge 裝置上部署 ASA
##### <a name="add-asa-to-your-deployment"></a>將 ASA 新增至您的部署
- 在 Azure 入口網站中，開啟 IoT 中樞、瀏覽至 IoT Edge Explorer，並開啟您的裝置刀鋒視窗。
- 選取 [設定模組]，然後選取 [匯入 Azure 服務 IoT Edge 模組]。
- 選取訂用帳戶與您建立的 ASA Edge 作業。 然後選取您的儲存體帳戶。 按一下 [儲存]。
![在您的部署中新增 ASA 模組](media/stream-analytics-edge/set_module.png)


> [!Note]
> 在此步驟期間，ASA 會要求存取所選取的儲存體容器，然後會建立名為 "EdgeJobs" 的資料夾。 針對每個部署，會在 "EdgeJobs" 資料夾中建立新的子資料夾。
> 若要將作業部署到您的 Edge 裝置，ASA 會建立作業定義檔的共用存取簽章 (SAS)。 SAS 索引鍵會使用裝置對應項安全地傳輸至 IoT Edge 裝置。 這個索引鍵的到期日是從其建立起的三年。


如需 IoT Edge 部署的詳細資訊，請參閱[本頁](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)。


##### <a name="configure-routes"></a>設定路由
IoT Edge 提供方法以宣告方式在模組之間及模組與 IoT 中樞之間路由訊息。 [這裡](https://docs.microsoft.com/azure/iot-edge/module-composition)說明完整語法。
ASA 作業中建立的輸入和輸出名稱可用來作為路由的端點。  

###### <a name="example"></a>範例
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
此範例會顯示下圖中所述情節的路由。 它包含名為 "**ASA**" 的 Edge 作業，以及名為 "**temperature**" 的輸入和名為 "**alert**" 的輸出。
![路由範例](media/stream-analytics-edge/RoutingExample.png)

這個範例會定義下列路由：
- 來自 **tempSensor** 的每個訊息都會傳送至名為 **ASA** 的模組到名為 **temperature** 的輸入，
- 所有 **ASA** 模組的輸出都會傳送至連結到此裝置 ($upstream) 的 IoT 中樞，
- 所有 **ASA** 模組的輸出都會傳送至 **tempSensor** 的**控制項**端點。


## <a name="technical-information"></a>技術資訊
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>相較於雲端作業，Edge 作業的目前限制
目標是在 Edge 作業和雲端作業之間進行同位檢查。 已支援大部分 SQL 查詢語言的功能。
不過，Edge 作業尚未支援下列功能：
* 使用者定義函式 (UDF) 和使用者定義彙總 (UDA)。
* Azure ML 函式。
* 在單一步驟中使用超過 14 個彙總。
* 輸入/輸出的 AVRO 格式。 目前僅支援 CSV 和 JSON。
* JSON 壓縮的輸入。
* 下列 SQL 運算子：
    * AnomalyDetection
    * 地理空間運算子：
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>執行階段與硬體需求
若要在 IoT Edge 上執行 ASA，您需要可以執行 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 的裝置。 

ASA 和 Azure IoT Edge 會使用 **Docker** 容器來提供可攜式解決方案，能在多個主機作業系統上執行 (Windows、Linux)。

IoT Edge 上的 ASA 可供作為 Windows 和 Linux 映像，能在 x86-64 或 ARM 架構上執行。 


### <a name="input-and-output"></a>輸入和輸出
#### <a name="input-and-output-streams"></a>輸入和輸出資料流
ASA Edge 作業可以從 IoT Edge 裝置上執行的其他模組取得輸入和輸出。 若要連線進出特定模組，您可以在部署期間設定路由組態。 [IoT Edge 模組複合文件](https://docs.microsoft.com/azure/iot-edge/module-composition)中會說明詳細資訊。

輸入與輸出都支援 CSV 和 JSON 格式。

針對您在 ASA 作業中建立的每一個輸入和輸出資料流，在部署的模組上已建立對應的端點。 這些端點可用於部署的路由。



##### <a name="reference-data"></a>參考資料
參考資料 (也稱為查詢資料表) 基本上是靜態或不常變更的有限資料集。 可用來執行查閱或與資料流相互關聯。 若要使用 Azure 串流分析作業中的參考資料，您通常會在查詢中使用[參考資料聯結](https://msdn.microsoft.com/library/azure/dn949258.aspx)。 如需詳細資訊，請參閱[關於參考資料的 API 文件](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data)。

若要使用 IoT Edge 上的 ASA 參考資料，您必須遵循下列步驟： 
1. 建立作業的新輸入
2. 選擇 [參考資料] 作為 [來源類型]。
3. 設定檔案路徑。 檔案路徑在![參考資料建立](media/stream-analytics-edge/ReferenceData.png)裝置上必須是**絕對**的檔案路徑
4. 在 Docker 設定中啟用**共用磁碟機**，並確定在開始部署之前已啟用磁碟機。

如需詳細資訊，請在這裡參閱[適用於 Windows 的 Docker 文件](https://docs.docker.com/docker-for-windows/#shared-drives)。

> [!Note]
> 目前僅支援本機參考資料。




## <a name="license-and-third-party-notices"></a>授權和第三方通知
* [IoT Edge 預覽授權上的 Azure 串流分析](https://go.microsoft.com/fwlink/?linkid=862827). 
* [IoT Edge 預覽上 Azure 串流分析的第三方通知](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>取得說明
如需進一步的協助，請參閱 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。


## <a name="next-steps"></a>後續步驟

* [Azure Iot Edge 的詳細資訊](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA IoT Edge 教學課程](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [使用這個調查將意見反應傳送給小組](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
