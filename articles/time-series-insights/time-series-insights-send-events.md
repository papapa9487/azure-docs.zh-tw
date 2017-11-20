---
title: "如何將事件傳送至 Azure 時間序列深入解析環境 | Microsoft Docs"
description: "本教學課程說明如何建立和設定事件中樞，並執行應用程式範例以將事件推送至 Azure 時間序列深入解析來加以顯示。"
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 543fafac63423ab874c6c8e40d91a1ce0f161987
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>使用事件中樞將事件傳送至 Time Series Insights 環境
本文說明如何建立和設定事件中樞，並執行應用程式範例以推送事件。 如果您現有的事件中樞內含 JSON 格式的事件，請跳過本教學課程，並在 [Time Series Insights](https://insights.timeseries.azure.com) 檢視您的環境。

## <a name="configure-an-event-hub"></a>設定事件中樞
1. 若要建立事件中樞，請遵循事件中樞[文件](../event-hubs/event-hubs-create.md)的指示。

2. 在搜尋列中搜尋**事件中樞**。 在傳回的清單中按一下 [事件中樞]。

3. 按一下事件中樞的名稱來加以選取。

4. 在中間設定視窗的 [實體] 底下，再次按一下 [事件中樞]。

5. 選取事件中樞的名稱來對它進行設定。

  ![選取事件中樞取用者群組](media/send-events/consumer-group.png)

6. 在 [實體] 底下，選取 [取用者群組]。
 
7. 確定您已建立專供 Time Series Insights 事件來源使用的取用者群組。

   > [!IMPORTANT]
   > 確定任何其他服務 (例如串流分析作業或其他 Time Series Insights 環境) 均未使用此取用者群組。 如果有其他服務使用此取用者群組，讀取作業會對此環境和其他服務造成負面影響。 如果您使用 “$Default” 做為取用者群組，有可能會導致其他讀取者重複使用。

8. 在 [設定] 標題底下，選取 [共用存取原則]。

9. 在事件中樞內建立 **MySendPolicy**，以用來在 Csharp 範例中傳送事件。

  ![選取 [共用存取原則]，然後按一下 [新增] 按鈕](media/send-events/shared-access-policy.png)  

  ![新增共用存取原則](media/send-events/shared-access-policy-2.png)  

## <a name="create-time-series-insights-event-source"></a>建立 Time Series Insights 事件來源
1. 如果您尚未建立事件來源，請遵循[這些指示](time-series-insights-how-to-add-an-event-source-eventhub.md)來建立事件來源。

2. 指定 **deviceTimestamp** 做為時間戳記屬性名稱，這個屬性會做為 C# 範例中的實際時間戳記。 以 JSON 形式將時間戳記屬性傳送至事件中樞時，該屬性的名稱區分大小寫，且值的格式必須是 __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__。 如果事件中不存在該屬性，則系統會使用加入事中樞佇列的時間。

  ![建立事件來源](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>用來推送事件的範例程式碼
1. 移至名為 **MySendPolicy** 的事件中樞原則。 複製**連接字串**與原則金鑰。

  ![複製 MySendPolicy 連接字串](media/send-events/sample-code-connection-string.png)

2. 執行下列程式碼，讓三個裝置各傳送 600 個事件。 使用連接字串更新 `eventHubConnectionString`。

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>支援的 JSON 樣貌
### <a name="sample-1"></a>範例 1

#### <a name="input"></a>輸入

簡單的 JSON 物件。

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---1-event"></a>輸出 - 1 個事件

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>範例 2

#### <a name="input"></a>輸入
具有兩個 JSON 物件的 JSON 陣列。 每個 JSON 物件都會轉換成事件。
```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---2-events"></a>輸出 - 2 個事件

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>範例 3

#### <a name="input"></a>輸入

具有巢狀 JSON 陣列的 JSON 物件，此陣列中包含兩個 JSON 物件。
```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---2-events"></a>輸出 - 2 個事件
請注意，系統會將「location」屬性複製到每個事件。

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>範例 4

#### <a name="input"></a>輸入

具有巢狀 JSON 陣列的 JSON 物件，此陣列中包含兩個 JSON 物件。 此輸入示範可由複雜 JSON 物件表示的全域屬性。

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---2-events"></a>輸出 - 2 個事件

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>後續步驟
在[時間序列深入解析總管](https://insights.timeseries.azure.com)中檢視您的環境。
