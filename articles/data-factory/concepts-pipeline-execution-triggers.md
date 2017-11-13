---
title: "Azure Data Factory 中的管道執行和觸發程序 | Microsoft Docs"
description: "本文提供相關資訊來說明如何在 Azure Data Factory 中依需求或建立觸發程序來執行管道。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: 6f4c0b11039bbdaf29c90ec2358934dc1c24af90
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Azure Data Factory 中的管道執行和觸發程序 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式運作](v1/data-factory-scheduling-and-execution.md)
> * [第 2 版 - 預覽](concepts-pipeline-execution-triggers.md)

**管道執行**是 Azure Data Factory 第 2 版中的詞彙，定義管道執行的執行個體。 例如，假設您的管道會在上午 8 點、9 點和 10 點執行。 在此情況下，管道會有三個個別的執行 (管道執行)。 每個管道執行都有唯一的管道執行識別碼，這是唯一定義該特定管道執行的 GUID。 通常是將引數傳遞給管道中定義的參數，以具現化管道執行。 有兩種方式可執行管道：**手動**或透過**觸發程序**。 本文提供兩種管道執行方式的詳細資料。 

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式運作版本 (GA))，請參閱 [Data Factory V1 中的排程和執行](v1/data-factory-scheduling-and-execution.md)。

## <a name="run-pipeline-on-demand"></a>依需求執行管道
在此方法中，您會手動執行管道。 這也視為管道的隨選執行。 

例如，假設您想要執行名為 **copyPipeline** 的管道。 此管道很簡單，只有一個活動會從 Azure Blob 儲存體中的來源資料夾，複製到相同儲存體中的目的資料夾。 以下是範例管道定義： 

```json
{
  "name": "copyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "sourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```
此管道接受兩個參數：sourceBlobContainer 和 sinkBlobContainer，如 JSON 定義中所示。 您可以在執行階段將值傳遞給這些參數。 

若要手動執行管道，您可以使用下列方法之一：.NET、PowerShell、REST 和 Python。 

### <a name="rest-api"></a>REST API
以下是範例 REST 命令：  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```
如需完整範例，請參閱[快速入門：使用 REST API 來建立資料處理站](quickstart-create-data-factory-rest-api.md)。

### <a name="powershell"></a>PowerShell
以下是範例 PowerShell 命令： 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

您可以在要求承載的本文中傳遞參數。 在 .NET、Powershell 和 Python 中，您可以在當作引數傳給呼叫的字典中傳遞值。

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

回應承載是管道執行的唯一識別碼：

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```


如需完整範例，請參閱[快速入門：使用 PowerShell 來建立資料處理站](quickstart-create-data-factory-powershell.md)。

### <a name="net"></a>.NET 
以下是範例 .NET 呼叫： 

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

如需完整範例，請參閱[快速入門：使用 .NET 來建立資料處理站](quickstart-create-data-factory-dot-net.md)。

> [!NOTE]
> .NET API 可讓您從 Azure Functions、您自己的 Web 服務等，叫用 Data Factory 管道。

## <a name="triggers"></a>觸發程序
觸發程序提供第二種執行管道的方式。 觸發程序代表一個處理單位，用來決定何時需要啟動管道執行。 目前，Data Factory 支援一個可依時鐘排程來叫用管道的觸發程序。 稱為**排程器觸發程序**。 目前，Data Factory 不支援以事件為基礎的觸發程序，例如在檔案到達時所執行之管道的觸發程序。

管道和觸發程序具有 "n-m" 關聯性。 多個觸發程序可以啟動單一管道，而相同的觸發程序可以啟動多個管道。 在下列觸發程序 JSON 定義中，**pipelines** 屬性會參考由特定觸發程序所觸發的管道清單，以及管道參數的值。

### <a name="basic-trigger-definition"></a>基本的觸發程序定義： 
```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
        },
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                          "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
            }
        ]
    }
```

## <a name="scheduler-trigger"></a>排程器觸發程序
排程器觸發程序會依時鐘排程來執行管道。 這個觸發程序支援定期和進階行事曆選項 (每週、星期一下午 5 點和星期四下午 9 點)。 很有彈性，不但與資料集模式無關，也不區別時間序列和非時間序列的資料。

### <a name="scheduler-trigger-json-definition"></a>排程器觸發程序 JSON 定義
當您建立的排程器觸發程序時，您可以使用 JSON 來指定排程與週期，如本節中的範例所示。 

若要讓排程器觸發程序啟動管道執行，請在觸發程序定義中包含特定管道的管道參考。 管道和觸發程序具有 "n-m" 關聯性。 多個觸發程序可以啟動單一管道。 相同的觸發程序可以啟動多個管道。

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  **parameters** 屬性是**管線**內的必要屬性。 即使您的管線不接受任何參數，屬性仍需包含空的 json，因為參數必須存在。


### <a name="overview-scheduler-trigger-schema"></a>概觀：排程器觸發程序結構描述
下表提供與觸發程序中的週期和排程相關之主要元素的高階概觀：

JSON 屬性 |     說明
------------- | -------------
startTime | startTime 是日期時間。 在簡單的排程中，startTime 是第一次執行。 在複雜的排程中，觸發程序一到 startTime 就會啟動。
EndTime | 指定觸發程序的結束日期時間。 觸發程序在此時間之後不會執行。 已過去的 endTime 無效。
timeZone | 目前支援僅 UTC。 
週期 | recurrence 物件指定觸發程序的週期規則。 recurrence 物件支援的元素：frequency、interval、endTime、count 和 schedule。 如果定義 recurrence，則 frequency 為必要元素，而 recurrence 的其他元素則為選用元素。
frequency | 代表觸發程序一再執行的頻率單位。 支援的值為：`minute`、`hour`、`day`、`week` 或 `month`。
interval | interval 是正整數。 代表用來決定觸發程序執行頻率的頻率間隔。 比方說，如果 interval 為 3，而 frequency 為 "week"，則觸發程序每隔 3 週重複執行一次。
schedule | 具有指定頻率的觸發程序會根據週期排程來改變其週期。 schedule 包含根據分鐘數、小時數、星期幾數、月日數和週數的修改。


### <a name="schedule-trigger-example"></a>排程觸發程序範例

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="overview-scheduler-trigger-schema-defaults-limits-and-examples"></a>概觀：排程觸發程序結構描述的預設值、限制和範例

JSON 名稱 | 值類型 | 必要？ | 預設值 | 有效值 | 範例
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | String | 是 | None | ISO 8601 日期時間 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
週期 | Object | 是 | None | Recurrence 物件 | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | 數字 | 否 | 1 | 1 到 1000。 | ```"interval":10```
EndTime | String | 是 | None | 代表未來時間的日期時間值 | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Object | 否 | None | Schedule 物件 | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>深入探討：startTime
下表擷取 startTime 如何控制觸發程序的執行方式：

startTime 值 | 週期性無排程 | 週期性有排程
--------------- | --------------------------- | ------------------------
開始時間已過去 | 計算開始時間之後的第一個未來執行時間，並在該時間執行。<p>根據從上次執行時間算出的時間執行後續的執行作業。</p><p>請參閱本表後面的範例。</p> | 觸發程序「一到」指定的開始時間即啟動。 第一次執行是根據從開始時間計算的排程。 <p>根據週期排程執行後續的執行作業</p>
開始時間在未來或現在 | 在指定的開始時間執行一次。 <p>根據從上次執行時間算出的時間執行後續的執行作業。</p> | 觸發程序「一到」指定的開始時間即啟動。 第一次執行是根據從開始時間計算的排程。<p>根據週期排程執行後續的執行作業。</p>

我們來看看一個範例情況：startTime 已過去，週期性，但無排程。 假設目前的時間是 `2017-04-08 13:00`，startTime 是 `2017-04-07 14:00`，而 recurrence 是每兩天 (以 frequency: day 和 interval: 2 定義)。請注意，startTime 在目前時間之前，已過去。

根據這些條件，第一次執行是在 `2017-04-09 at 14:00`。 排程器引擎會從開始時間計算執行週期。 過去的任何執行個體都會遭到捨棄。 引擎會使用下一個在未來發生的執行個體。 因此，在此情況下，startTime 為 `2017-04-07 at 2:00pm`，而下一個執行個體是在該時間的 2 天後，即 `2017-04-09 at 2:00pm`。

第一次執行時間相同，即使 startTime 為 `2017-04-05 14:00` 或 `2017-04-01 14:00` 也一樣。 第一次執行之後，就使用排程來計算後續執行作業。 因此，分別是在 `2017-04-11 at 2:00pm`、接著是 `2017-04-13 at 2:00pm`，然後在 `2017-04-15 at 2:00pm` 等等。

最後，當觸發程序具有排程時，如果未在排程中設定小時及/或分鐘，則會分別預設為第一次執行的小時及/或分鐘。

### <a name="deep-dive-schedule"></a>深入探討：排程
一方面，排程可以限制觸發程序的執行次數。 例如，如果設有 "month" 頻率的觸發程序具有只在月底 31 日執行的排程，則此觸發程序只會在有第 31 天的月份執行。

另一方面，排程也可以增加觸發程序的執行次數。 例如，如果搭配 "month" 頻率的觸發程序具有在當月 1 日及 2 日執行的排程，則此觸發程序會在當月 1 日和 2 日執行，而不是一個月只執行一次。

如果指定多個 schedule 元素，則評估的順序是從最大到最小 – 週數、月日、星期幾、小時和分鐘。

下表詳細說明 schedule 元素：


JSON 名稱 | 說明 | 有效值
--------- | ----------- | ------------
minutes | 一小時內觸發程序執行的分鐘數。 | <ul><li>Integer</li><li>一連串整數</li></ul>
hours | 一天內觸發程序執行的小時數。 | <ul><li>Integer</li><li>一連串整數</li></ul>
weekDays | 觸發程序執行的星期幾。 只能搭配 weekly 頻率指定。 | <ul><li>Monday、Tuesday、Wednesday、Thursday、Friday、Saturday 或 Sunday</li><li>任何值的陣列 (最大陣列大小為 7)</li></p>不區分大小寫</p>
monthlyOccurrences | 決定在當月哪幾天執行觸發程序。 只能搭配 monthly 頻率指定。 | monthlyOccurence 物件的陣列︰`{ "day": day,  "occurrence": occurence }`。 <p> day 是觸發程序執行的星期幾，例如，`{Sunday}` 是當月的每個星期日。 必要。<p>Occurrence 是當月第幾個星期幾，例如 `{Sunday, -1}` 是當月的最後一個星期日。 選用。
monthDays | 觸發程序執行的月日。 只能搭配 monthly 頻率指定。 | <ul><li><= -1 和 >= -31 的任何值</li><li>>= 1 和 <= 31 任何值</li><li>值的陣列</li>


## <a name="examples-recurrence-schedules"></a>範例：週期排程
本節提供週期排程的範例 – 焦點放在 schedule 物件和其子元素。

範例排程假設 interval 設為 1。 另外也根據排程中的設定來假設正確頻率 – 例如，您在排程中不能使用頻率 "day"，也不能有 "monthDays" 修改。 上一節的表格已提及這些限制。 

範例 | 說明
------- | -----------
`{"hours":[5]}` | 在每天上午 5 點執行
`{"minutes":[15], "hours":[5]}` | 在每天上午 5:15 執行
`{"minutes":[15], "hours":[5,17]}` | 在每天上午 5:15 和下午 5:15 執行
`{"minutes":[15,45], "hours":[5,17]}` | 在每天上午 5:15、上午 5:45、下午 5:15 和下午 5:45 執行
`{"minutes":[0,15,30,45]}` | 每隔 15 分鐘執行一次
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 每小時執行一次。 此觸發程序每小時執行一次。 如果指定 startTime，由它控制分鐘，如果未指定，則由建立時間控制。 比方說，如果開始時間或建立時間 (無論套用哪一個) 為下午 12:25，則觸發程序會在 00:25、01:25、02:25、…、23:25 執行。 排程相當於 frequency 為 "hour"、interval 為 1 且沒有 schedule 的觸發程序。 差別在於此排程也可以搭配不同的 frequency 和 interval 使用，以建立其他觸發程序。 例如，如果 frequency 為 "month"，則排程只會一個月執行一次，而不是每天執行 (如果 frequency 為 "day")。
`{"minutes":[0]}` | 在每小時整點執行。 此觸發程序也會每小時執行一次，但在整點執行 (例如上午 12 點、上午 1 點、上午 2 點等等)。 這相當於 frequency 為 "hour"、startTime 為零分鐘且沒有 schedule (如果 frequency 為 "day") 的觸發程序，但如果 frequency 為 "week" 或 "month"，則排程會分別在一週某一天或一個月某一天執行。
`{"minutes":[15]}` | 在每小時 15 分執行。 每小時執行，開始於上午 00:15、上午 1:15、上午 2:15 等等，並結束於下午 10:15 和下午 11:15。
`{"hours":[17], "weekDays":["saturday"]}` | 在每週星期六下午 5 點執行
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每週星期一、星期三、星期五下午 5 點執行
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每週星期一、星期三、星期五下午 5:15 和 5:45 執行
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日每隔 15 分鐘執行一次
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日上午 9 點與下午 4:45 之間每隔 15 分鐘執行一次
`{"weekDays":["tuesday", "thursday"]}` | 在星期二和星期四指定的開始時間執行。
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | 在每個月的第 28 天上午 6 點執行 (假設 frequency 為 month)
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 在當月最後一天上午 6 點執行。 如果您想要在當月最後一天執行觸發程序，請使用 -1，而不是 28、29、30 或 31。
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 在每個月第一天和最後一天上午 6 點執行
`{monthDays":[1,14]}` | 在每個月第一天和第十四天指定的開始時間執行。
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每個月第一個星期五上午 5 點執行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每個月第一個星期五指定的開始時間執行。
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 在每個月倒數第三個星期五的開始時間執行
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每個月第一個和最後一個星期五上午 5:15 執行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每個月第一個和最後一個星期五指定的開始時間執行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 在每個月第五個星期五的開始時間執行。 如果一個月沒有第五個星期五，則管道不會執行，因為它已排定只在第五個星期五執行。  如果您想要在當月最後一個出現的星期五執行觸發程序，請考慮在 occurrence 中使用 -1 而不是 5。
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 在當月最後一個星期五每隔 15 分鐘執行一次。
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 在每個月第三個星期三上午 5:15、上午 5:45、下午 5:15 和下午 5:45 執行。




## <a name="next-steps"></a>後續步驟
請參閱下列教學課程： 

- [快速入門：使用 .NET 來建立資料處理站](quickstart-create-data-factory-dot-net.md)
