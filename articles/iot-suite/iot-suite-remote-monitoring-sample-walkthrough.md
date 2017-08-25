---
title: "遠端監視預先設定解決方案逐步解說 | Microsoft Docs"
description: "說明 Azure IoT 預先設定解決方案遠端監視及其架構。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: c6d76cc741a6d932a506017781e45bc9b8f8c640
ms.contentlocale: zh-tw
ms.lasthandoff: 05/15/2017

---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>遠端監視預先設定解決方案逐步解說

IoT 套件遠端監視[預先設定解決方案][lnk-preconfigured-solutions]是適用於在遠端位置執行的多部機器之端對端監視解決方案實作。 此解決方案結合了主要的 Azure 服務以提供一般的商務案例實作。 您可以將此解決方案做為自己實作的起點，並加以[自訂][lnk-customize]以符合自己特有的商務需求。

本文將逐步介紹遠端監視解決方案的一些重要元素，讓您瞭解它的運作方式。 這項知識能協助您︰

* 在解決方案中進行疑難排解。
* 規劃如何自訂解決方案以滿足您的特定需求。 
* 設計使用 Azure 服務的 IoT 解決方案。

## <a name="logical-architecture"></a>邏輯架構

下圖概述預先設定解決方案的邏輯元件：

![邏輯架構](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="simulated-devices"></a>模擬的裝置

在預先設定解決方案中，模擬的裝置會表示冷卻裝置 (例如建築物空調或設備空氣處理單位)。 當您部署預先設定的解決方案時，也會自動佈建四個在 [Azure WebJob][lnk-webjobs] 中執行的模擬裝置。 模擬裝置讓您可以輕鬆探索解決方案的行為，而不需要部署任何實體裝置。 若要部署真實的實體裝置，請參閱[將裝置連接至遠端監視預先設定解決方案][lnk-connect-rm]教學課程。

### <a name="device-to-cloud-messages"></a>裝置到雲端的訊息

每個模擬裝置可以傳送下列訊息類型至 IoT 中樞︰

| 訊息 | 說明 |
| --- | --- |
| 啟動 |裝置啟動時會將含有本身相關資訊的 **裝置資訊** 訊息傳送至後端。 這項資料包括裝置識別碼以及裝置所支援的命令和方法清單。 |
| 目前狀態 |裝置會定期傳送 **目前狀態** 訊息，以報告該裝置是否可以感應到感應器的目前狀態。 |
| 遙測 |裝置會定期傳送 **遙測** 訊息，以報告從裝置的模擬感應器收集到的溫度和溼度模擬值。 |

> [!NOTE]
> 解決方案會儲存裝置在 Cosmos DB 資料庫 (而非裝置對應項) 中所支援的命令清單。

### <a name="properties-and-device-twins"></a>屬性和裝置對應項

模擬裝置會將下列裝置屬性傳送至 IoT 中樞中的[對應項][lnk-device-twins]做為「報告屬性」。 裝置會在啟動時傳送報告屬性，以回應 [變更裝置狀態] 命令或方法。

| 屬性 | 目的 |
| --- | --- |
| Config.TelemetryInterval | 裝置傳送遙測資料的頻率 (秒) |
| Config.TemperatureMeanValue | 指定模擬溫度遙測的平均值 |
| Device.DeviceID |在解決方案中建立裝置時所提供或指派的識別碼 |
| Device.DeviceState | 裝置所回報的狀態 |
| Device.CreatedTime |在解決方案中建立裝置的時間 |
| Device.StartupTime |啟動裝置的時間 |
| Device.LastDesiredPropertyChange |上次所需屬性變更的版本號碼 |
| Device.Location.Latitude |裝置的緯度位置 |
| Device.Location.Longitude |裝置的經度位置 |
| System.Manufacturer |裝置製造商 |
| System.ModelNumber |裝置的型號 |
| System.SerialNumber |裝置的序號 |
| System.FirmwareVersion |裝置的目前韌體版本 |
| System.Platform |裝置的平台架構 |
| System.Processor |執行裝置的處理器 |
| System.InstalledRAM |在裝置上安裝的 RAM 數量 |

模擬器會以範例值在模擬裝置中植入這些屬性。 模擬器每次初始化模擬的裝置時，裝置會向 IoT 中樞報告預先定義的中繼資料做為報告屬性。 只有裝置可以更新報告屬性。 若要變更報告屬性，您可以在解決方案入口網站中設定所需的屬性。 裝置的責任︰

1. 定期從 IoT 中樞擷取所需的屬性。
2. 使用所需的屬性值更新其組態。
3. 將新的值傳回至中樞做為報告屬性。

在解決方案儀表板中，您可以透過[裝置對應項][lnk-device-twins]，使用*所需屬性*來設定裝置屬性。 一般而言，裝置會從中樞讀取所需的屬性值，以更新其內部狀態並將變更回報為報告屬性。

> [!NOTE]
> 模擬裝置程式碼只會使用 **Desired.Config.TemperatureMeanValue** 和 **Desired.Config.TelemetryInterval** 所需屬性來更新送回 IoT 中樞的報告屬性。 模擬裝置會忽略所有其他所需的屬性變更要求。

### <a name="methods"></a>方法

模擬裝置可以處理透過 IoT 中樞從解決方案入口網站叫用的下列方法 ([直接方法][lnk-direct-methods])：

| 方法 | 說明 |
| --- | --- |
| InitiateFirmwareUpdate |指示裝置執行韌體更新 |
| 重新啟動 |指示裝置重新啟動 |
| FactoryReset |指示裝置執行原廠重設 |

有些方法會使用報告屬性來報告進度。 例如，**InitiateFirmwareUpdate** 方法可以模擬在裝置上以非同步方式執行更新。 此方法會在裝置上立即傳回資料，而非同步工作會使用報告屬性繼續將狀態更新送回解決方案儀表板。

### <a name="commands"></a>命令

模擬裝置可以處理透過 IoT 中樞從解決方案入口網站傳送的下列命令 (雲端到裝置訊息)：

| 命令 | 說明 |
| --- | --- |
| PingDevice |傳送 *ping* 給裝置以檢查是否運作 |
| StartTelemetry |傳送遙測以啟動裝置 |
| StopTelemetry |傳送遙測以停止裝置 |
| ChangeSetPointTemp |變更設定點值，在其周圍會產生隨機資料 |
| DiagnosticTelemetry |觸發裝置模擬器以傳送其他遙測值 (externalTemp) |
| ChangeDeviceState |變更裝置的擴充狀態屬性，並從裝置傳送裝置資訊訊息 |

> [!NOTE]
> 如需這些命令 (雲端到裝置訊息) 和方法 (直接方法) 的比較，請參閱[雲端到裝置通訊指引][lnk-c2d-guidance]。

## <a name="iot-hub"></a>IoT 中樞

[IoT 中樞][lnk-iothub]內嵌從裝置傳送至雲端的資料，並提供給 Azure 串流分析 (ASA) 作業。 每個串流 ASA 作業使用不同的 IoT 中樞取用者群組，以從您的裝置讀取訊息串流。

解決方案中的 IoT 中樞也可︰

- 維護身分識別登錄，以儲存允許連接至入口網站之所有裝置的驗證金鑰。 您可以透過身分識別登錄來啟用和停用裝置。
- 代表解決方案入口網站將命令傳送到您的裝置。
- 代表解決方案入口網站在裝置上叫用方法。
- 維護所有已註冊裝置的裝置對應項。 裝置對應項會儲存裝置所報告的屬性值。 裝置對應項也會儲存在解決方案入口網站中設定的所需屬性，以便裝置在後續連接時擷取。
- 排程作業以設定多個裝置的屬性，或在多個裝置上叫用方法。

## <a name="azure-stream-analytics"></a>Azure 串流分析

在遠端監視解決方案中，[Azure 串流分析][lnk-asa] (ASA) 會將透過 IoT 中樞所收到的裝置訊息分派至其他後端元件進行處理或儲存。 不同的 ASA 作業會根據訊息內容執行特定的功能。

**作業 1：裝置資訊** 會篩選來自傳入訊息串流的裝置資訊訊息，並將它們傳送到事件中樞端點。 裝置會在啟動時傳送裝置資訊訊息，並且回應 **SendDeviceInfo** 命令。 此作業使用下列查詢定義來識別 **裝置資訊** 訊息︰

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

此作業會將其輸出傳送到事件中樞進行進一步的處理。

**作業 2：規則** 會針對每一裝置臨界值評估傳入氣溫和溼度遙測值。 臨界值是在解決方案入口網站中提供的規則編輯器中設定。 每個裝置/值組是依據時間戳記儲存在 blob 中，串流分析會讀取做為 **參考資料**。 工作會針對裝置的設定臨界值比較任何非空白值。 如果超過 '>' 條件，作業會輸出**警示**事件，表示已超過臨界值，並且提供裝置、值和時間戳記值。 此作業會使用下列查詢定義來識別應觸發警示的遙測訊息︰

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

作業會將其輸出傳送至事件中樞供進一步處理，並將每項警示的詳細資料儲存至 blob 儲存體，解決方案入口網站可以從該位置讀取警示資訊。

**作業 3：遙測** 會以兩種方法來操作傳入裝置遙測串流。 第一種方法會將裝置的所有遙測訊息都傳送至持續性 blob 儲存體以供長期儲存。 第二種方法會透過五分鐘滑動視窗計算平均、最小和最大溼度值，並將此資料傳送至 Blob 儲存體。 解決方案入口網站會從 blob 儲存體讀取遙測資料來填入圖表。 此作業會使用下列查詢定義：

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>事件中樞

**裝置資訊**和**規則** ASA 作業將資料輸出至事件中樞，以便可靠地轉送給在 WebJob 中執行的**事件處理器**。

## <a name="azure-storage"></a>Azure 儲存體

此解決方案使用 Azure blob 儲存體來保存解決方案裝置中的所有原始和摘要遙測資料。 入口網站會從 blob 儲存體讀取遙測資料來填入圖表。 若要顯示警示，解決方案入口網站會從 blob 儲存體讀取當遙測值超過設定的臨界值時所記錄的資料。 解決方案也使用 Blob 儲存體記錄您在解決方案入口網站中設定的臨界值。

## <a name="webjobs"></a>WebJobs

除了裝載裝置模擬器，解決方案中的 WebJob 也可裝載在 Azure WebJob 中執行的 **事件處理器**，以便處理命令回應。 其使用命令回應訊息來更新裝置命令歷程記錄 (儲存於 Cosmos DB 資料庫)。

## <a name="cosmos-db"></a>Cosmos DB

解決方案使用 Cosmos DB 資料庫來儲存連線至解決方案的裝置相關資訊。 這項資訊包括從解決方案入口網站傳送至裝置的命令歷程記錄，以及從解決方案入口網站叫用的方法歷程記錄。

## <a name="solution-portal"></a>解決方案入口網站

解決方案入口網站是已部署成為預先設定解決方案一部分的 Web 應用程式。 解決方案入口網站中的主要頁面為儀表板和裝置清單。

### <a name="dashboard"></a>儀表板

Web 應用程式中的此頁面會使用 PowerBI javascript 控制項 (請參閱 [PowerBI 視覺效果儲存機制](https://www.github.com/Microsoft/PowerBI-visuals)) 以視覺化方式呈現裝置的遙測資料。 解決方案會使用 ASA 遙測作業，將遙測資料寫入至 blob 儲存體。

### <a name="device-list"></a>裝置清單

在解決方案入口網站的這個頁面中，您可以：

* 佈建新裝置。 這個動作會設定唯一裝置識別碼並產生驗證金鑰。 它會將裝置相關資訊寫入至 IoT 中樞身分識別登錄以及解決方案特定的 Cosmos DB 資料庫。
* 管理裝置屬性。 這個動作包括檢視現有屬性和使用新的屬性更新。
* 將命令傳送至裝置。
* 檢視裝置的命令歷程記錄。
* 啟用和停用裝置。

## <a name="next-steps"></a>後續步驟

下列 TechNet 部落格文章提供有關遠端監視預先設定之解決方案更多的詳細資料︰

* [IoT 套件 - 幕後 - 遠端監視](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
* [IoT 套件 - 遠端監視 - 新增即時與模擬裝置](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

您可以繼續閱讀下列文章，了解如何開始使用 IoT 套件︰

* [將裝置連接至遠端監視預先設定方案][lnk-connect-rm]
* [azureiotsuite.com 網站的權限][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md

