---
title: "資料連線：來自事件資料流的資料流輸入 | Microsoft Docs"
description: "了解如何設定通往串流分析的資料連線 (也就是「輸入」)。 輸入包括來自事件的資料流，以及參考資料。"
keywords: "資料流, 資料連線, 事件資料流"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/05/2017
ms.author: samacha
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 09066927641054acb8c53a3585e111df87893e50
ms.contentlocale: zh-tw
ms.lasthandoff: 07/06/2017

---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>資料連線：了解資料從事件到串流分析的資料流輸入
串流分析作業的資料連線是來自資料來源的事件資料流，稱為作業的「輸入」。 串流分析與 Azure 資料流來源的整合性極佳，來源包括 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)、[Azure IoT 中樞](https://azure.microsoft.com/services/iot-hub/)和 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)。 這些輸入來源可以來自與分析作業相同的 Azure 訂用帳戶，或來自不同的訂用帳戶。

## <a name="data-input-types-data-stream-and-reference-data"></a>資料輸入類型：資料流和參考資料
當資料發送到資料來源時，串流分析作業會即時取用並處理該資料。 輸入分為兩種類型：資料流輸入和參考資料輸入。

### <a name="data-stream-inputs"></a>資料流輸入
資料流是一段時間內發生的無限制事件序列。 串流分析作業必須包含至少一個資料流輸入。 支援將事件中樞、IoT 中樞和 Blob 儲存體當成資料流輸入來源。 事件中樞用於從多個裝置和服務收集事件資料流。 這些資料流可能包括社交媒體活動摘要、股票交易資訊或來自感應器的資料。 IoT 中樞經過最佳化，最適合從物聯網 (IoT) 情節中連接的裝置收集資料。  Blob 儲存體可作為輸入來源，將大量資料內嵌為資料流，例如記錄檔。  

### <a name="reference-data"></a>參考資料
串流分析也支援稱為「參考資料」的輸入。 這是靜態或緩慢變更的輔助資料。 通常用來執行相互關聯和查閱。 比方說，您可能會將資料流輸入中的資料聯結至參考資料中的資料，很像是執行 SQL 聯結來查詢靜態值。 在預覽版本中，Azure Blob 儲存體是目前唯一支援當成參考資料的輸入來源。 參考資料來源 Blob 的大小以 100 MB 為限。

若要了解如何建立參考資料輸入，請參閱[使用參考資料](stream-analytics-use-reference-data.md)。  

## <a name="create-data-stream-input-from-event-hubs"></a>從事件中樞建立資料流輸入

Azure 事件中樞提供高延展性的發佈-訂閱事件擷取器。 事件中樞每秒可收集數百萬個事件，可讓您處理和分析連接的裝置和應用程式所產生的大量資料。 事件中樞搭配串流分析，一起為您提供端對端解決方案來分析即時資料 -- 事件中樞可讓您即時將事件傳送到 Azure，而 Stream Analytics 作業可以即時處理這些事件。 例如，您可以將網頁點擊、感應器讀數或線上記錄事件傳送至事件中樞。 然後，您可以建立串流分析作業，將事件中樞當作輸入資料流來即時篩選、彙總和相互關聯。

在串流分析中，來自事件中樞的事件預設時間戳記是事件抵達事件中樞的時間戳記，也就是 `EventEnqueuedUtcTime`。 若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 關鍵字。

### <a name="consumer-groups"></a>用戶群組
您應該將每一個串流分析事件中樞輸入設定為有自己的取用者群組。 當作業包含自我聯結或有多個輸入時，某些輸入可能由下游的多個讀取器所讀取。 這種情況會影響單一取用者群組中的讀取器數目。 若要避免超出每個分割區的每個取用者群組最多有 5 個讀取器的事件中樞限制，最好為每個串流分析作業指定取用者群組。 另外也限制每一個事件中樞最多有 20 個取用者群組。 如需詳細資訊，請參閱[事件中樞程式設計指南](../event-hubs/event-hubs-programming-guide.md)。

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>將事件中樞設定為資料流輸入
下表說明在 Azure 入口網站中將事件中樞設定為輸入時，[新的輸入] 刀鋒視窗中的每一個屬性。

| 屬性 | 說明 |
| --- | --- |
| **輸入別名** |在作業查詢中用來參考這個輸入的易記名稱。 |
| **服務匯流排命名空間** |Azure 服務匯流排命名空間，是一組訊息實體的容器。 建立新的事件中樞時，也會建立服務匯流排命名空間。 |
| **事件中樞名稱** |作為輸入的事件中樞名稱。 |
| **事件中樞原則名稱** |支援存取事件中樞的共用存取原則。 每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| **事件中樞取用者群組** (選用) |用來從事件中樞內嵌資料的取用者群組。 如果未指定取用者群組，串流分析作業會使用預設取用者群組。 建議讓每一個串流分析作業使用不同的取用者群組。 |
| **事件序列化格式** |傳入資料流的序列化格式 (JSON、CSV 或 Avro)。 |
| **編碼** | UTF-8 是目前唯一支援的編碼格式。 |

當您的資料來自事件中樞時，您可以在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 說明 |
| --- | --- |
| **EventProcessedUtcTime** |資料流分析處理事件的日期與時間。 |
| **EventEnqueuedUtcTime** |事件中樞收到事件的日期與時間。 |
| **PartitionId** |輸入配接器以零起始的資料分割識別碼。 |

例如，您可以使用這些欄位來撰寫類似下列範例的查詢：

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-data-stream-input-from-iot-hub"></a>從 IoT 中樞建立資料流輸入
Azure IoT 中樞是已針對 IoT 案例最佳化的高延展性發佈/訂閱事件擷取器。

在串流分析中，來自 IoT 中樞的事件預設時間戳記是事件抵達 IoT 中樞的時間戳記，也就是 `EventEnqueuedUtcTime`。 若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 關鍵字。

> [!NOTE]
> 傳送的訊息必須附上 `DeviceClient` 屬性才能處理。
> 
> 

### <a name="consumer-groups"></a>用戶群組
您應該將每一個串流分析 IoT 中樞輸入設定為有自己的取用者群組。 當作業包含自我聯結或有多個輸入時，某些輸入可能由下游的多個讀取器所讀取。 這種情況會影響單一取用者群組中的讀取器數目。 若要避免超出每個分割區的每個取用者群組最多有 5 個讀取器的 Azure IoT 中樞限制，最好為每個串流分析作業指定取用者群組。

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>將 IoT 中樞設定為資料流輸入
下表說明在 Azure 入口網站中將 IoT 中樞設定為輸入時，[新的輸入] 刀鋒視窗中的每一個屬性。

| 屬性 | 說明 |
| --- | --- |
| **輸入別名** |在作業查詢中用來參考這個輸入的易記名稱。|
| **IoT 中樞** |作為輸入的 IoT 中樞名稱。 |
| **端點** |IoT 中樞的端點。|
| **共用存取原則名稱** |支援存取 IoT 中樞的共用存取原則。 每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| **共用存取原則金鑰** |用來授權 IoT 中樞存取權的共用存取金鑰。 |
| **取用者群組** (選用) |用來從 IoT 中樞內嵌資料的取用者群組。 如果未指定取用者群組，串流分析作業會使用預設取用者群組。 建議讓每一個串流分析作業使用不同的取用者群組。 |
| **事件序列化格式** |傳入資料流的序列化格式 (JSON、CSV 或 Avro)。 |
| **編碼** |UTF-8 是目前唯一支援的編碼格式。 |

當您的資料來自 IoT 中樞時，您可以在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 說明 |
| --- | --- |
| **EventProcessedUtcTime** |處理事件的日期與時間。 |
| **EventEnqueuedUtcTime** |IoT 中樞收到事件的日期與時間。 |
| **PartitionId** |輸入配接器以零起始的資料分割識別碼。 |
| **IoTHub.MessageId** | IoT 中樞內用於雙向通訊相互關聯的識別碼。 |
| **IoTHub.CorrelationId** |IoT 中樞內用於訊息回應和回饋的識別碼。 |
| **IoTHub.ConnectionDeviceId** |用來傳送此訊息的驗證識別碼。 IoT 中樞會將服務繫結訊息標上此值。 |
| **IoTHub.ConnectionDeviceGenerationId** |用來傳送此訊息之已驗證裝置的世代識別碼。 IoT 中樞會將服務繫結訊息標上此值。 |
| **IoTHub.EnqueuedTime** |IoT 中樞收到訊息的時間。 |
| **IoTHub.StreamId** |傳送者裝置所新增的自訂事件屬性。 |


## <a name="create-data-stream-input-from-blob-storage"></a>從 Blob 儲存體建立資料流輸入
在要於雲端中儲存大量非結構化資料的情節中，Azure Blob 儲存體提供具有成本效益且可擴充的解決方案。 Blob 儲存體中的資料通常被視為待用資料。 但可以由串流分析當作資料流來處理。 記錄處理是透過串流分析來處理 Blob 儲存體輸入的常見情節。 在此情節中，從系統擷取遙測資料之後，需要剖析和處理，才能得到有意義的資料。

在串流分析中，Blob 儲存體事件的預設時間戳記是上次修改 blob 的時間戳記，也就是 `BlobLastModifiedUtcTime`。 若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 關鍵字。

CSV 格式的輸入「需要」標題列來定義資料集的欄位。 此外，所有標題列欄位都必須是唯一的。

> [!NOTE]
> 串流分析不支援將內容加入現有的 blob。 串流分析只會檢視一次 blob，在作業讀取資料之後，blob 中發生的任何變更就不處理。 最佳做法是一次上傳所有資料，然後就不要將事件新增至該 blob 存放區。
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>將 Blob 儲存體設定為資料流輸入

下表說明在 Azure 入口網站中將 Blob 儲存體設定為輸入時，[新的輸入] 刀鋒視窗中的每一個屬性。

| 屬性 | 說明 |
| --- | --- |
| **輸入別名** | 在作業查詢中用來參考這個輸入的易記名稱。 |
| **儲存體帳戶** | Blob 檔案所在的儲存體帳戶名稱。 |
| **儲存體帳戶金鑰** | 與儲存體帳戶相關聯的密碼金鑰。 |
| **容器** | Blob 輸入的容器。 容器提供邏輯分組給儲存在 Microsoft Azure Blob 服務中的 blob。 將 blob 上傳至 Azure Blob 儲存體服務時，您必須指定該 blob 的容器。 |
| **路徑模式** (選用) | 用來在指定的容器中找出 blob 的檔案路徑。 在該路徑內，您可以指定下列三個變數的一個或多個執行個體：`{date}`、`{time}` 或 `{partition}`<br/><br/>範例 1：`cluster1/logs/{date}/{time}/{partition}`<br/><br/>範例 2：`cluster1/logs/{date}`<br/><br/>`*` 字元不是路徑前置詞允許的值。 僅允許有效的 <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob 字元</a>。 |
| **日期格式** (選用) | 在路徑中使用日期變數時，用來組織檔案的日期格式。 範例：`YYYY/MM/DD` |
| **時間格式** (選用) |  在路徑中使用時間變數時，用來組織檔案的時間格式。 目前唯一支援的值為 `HH`。 |
| **事件序列化格式** | 傳入資料流的序列化格式 (JSON、CSV 或 Avro)。 |
| **編碼** | 對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |

當您的資料來自 Blob 儲存體來源時，您可以在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 說明 |
| --- | --- |
| **BlobName** |事件來源的輸入 Blob 名稱。 |
| **EventProcessedUtcTime** |資料流分析處理事件的日期與時間。 |
| **BlobLastModifiedUtcTime** |上次修改 Blob 的時間與日期。 |
| **PartitionId** |輸入配接器以零起始的資料分割識別碼。 |

例如，您可以使用這些欄位來撰寫類似下列範例的查詢：

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
您已經了解自己串流分析工作的資料連線選項。 若要深入了解串流分析，請參閱：

* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

