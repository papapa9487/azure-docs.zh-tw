---
title: "Azure Functions 的 host.json 參考"
description: "Azure Functions host.json 檔案的參考文件。"
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: 96103e7014212ecaa3e4e9238ae3b9c7a851cca9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="hostjson-reference-for-azure-functions"></a>Azure Functions 的 host.json 參考

*host.json* 中繼資料檔案所包含的全域設定選項會影響函式應用程式的所有函式。 本文列出可用的設定。 JSON 結構描述位在 http://json.schemastore.org/host。

在[應用程式設定](functions-app-settings.md)和 [local.settings.json](functions-run-local.md#local-settings-file) 檔案中，還有其他全域設定選項。

## <a name="sample-hostjson-file"></a>範例 host.json 檔案

下列範例 *host.json* 檔案已指定所有可能的選項。

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
    "functionTimeout": "00:05:00",
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

本文的下列各節說明每個最上層屬性。 除非另有說明，否則全部都是選擇項目。

## <a name="aggregator"></a>aggregator

指定[計算 Application Insights 的計量](functions-monitoring.md#configure-the-aggregator)時彙總多少函式引動過程。 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|batchSize|1000|要彙總的要求數目上限。| 
|flushTimeout|00:00:30|要彙總的最長期間。| 

達到兩個限制的第一個限制時，會彙總函式引動過程。

## <a name="applicationinsights"></a>applicationInsights

控制 [Application Insights 中的取樣功能](functions-monitoring.md#configure-sampling)。

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|isEnabled|false|啟用或停用取樣。| 
|maxTelemetryItemsPerSecond|5|取樣的開始臨界值。| 

## <a name="eventhub"></a>eventHub

[事件中樞觸發程序和繫結](functions-bindings-event-hubs.md)的組態設定。

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|maxBatchSize|64|每個接收迴圈接收到的事件計數上限。|
|prefetchCount|n/a|基礎 EventProcessorHost 將使用的預設 PrefetchCount。| 
|batchCheckpointFrequency|1|要在建立 EventHub 資料指標檢查點之前處理的事件批次數目。| 

## <a name="functions"></a>functions

工作主機將執行的函式清單。  空陣列表示已執行所有函式。  預定只能在[本機執行](functions-run-local.md)時使用。 在函式應用程式中，使用 *function.json* `disabled` 屬性，而不是 *host.json* 中的這個屬性。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

指出所有函式的逾時持續期間。 在取用量方案中，有效範圍是從 1 秒到 10 分鐘，而預設值是 5 分鐘。 在 App Service 方案中，沒有限制，而且預設值為 Null，這指出沒有逾時。

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="http"></a>http

[HTTP 觸發程序和繫結](functions-bindings-http-webhook.md)的組態設定。

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 
        "dynamicThrottlesEnabled": false
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|routePrefix|api|適用於所有路由的路由前置詞。 若要移除預設前置詞，請使用空字串。 |
|maxOutstandingRequests|-1|將在任何指定時間保留的未完成要求數目上限 (-1 表示無限制)。 限制包括已排入佇列但尚未開始執行的要求，以及任何進行中的執行。 會以 429「忙碌」回應來拒絕任何超過此限制的連入要求。 呼叫端可以使用該回應，來採用以時間為基礎的重試策略。 此設定只會控制在作業主機執行路徑內發生的佇列處理。 其他佇列 (例如 ASP.NET 要求佇列) 不受此設定的影響。 |
|maxConcurrentRequests|-1|將平行執行的 HTTP 函式數目上限 (-1 表示無限制)。 例如，並行太高時，如果 HTTP 函式使用太多系統資源，則可以設定限制。 或者，如果函式對第三方服務發出傳出要求，可能需要限制這些呼叫的速率。|
|dynamicThrottlesEnabled|false|讓要求處理管線以定期檢查系統效能計數器。 計數器包含連線、執行緒、流程、記憶體和 CPU。 如果有任何計數器超出內建臨界值 (80%)，則除非計數器回復正常等級，否則會以 429「忙碌」回應來拒絕要求。|

## <a name="id"></a>id

作業主機的唯一識別碼。 可以是已移除虛線的小寫 GUID。 在本機執行時為必要項目。 在 Azure Functions 中執行時，如果省略 `id`，則會自動產生識別碼。

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

控制 [ILogger 物件](functions-monitoring.md#write-logs-in-c-functions)或 [context.log](functions-monitoring.md#write-logs-in-javascript-functions) 所寫入記錄的篩選。

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|categoryFilter|n/a|指定依類別的篩選| 
|defaultLevel|資訊|針對 `categoryLevels` 陣列中未指定的任何類別，會將這個層級和以上層級的記錄傳送至 Application Insights。| 
|categoryLevels|n/a|一個類別陣列，指定針對每個類別傳送至 Application Insights 的最小記錄層級。 這裡指定的類別控制所有開頭為相同值的類別，但會優先使用較長的值。 在上述範例 *host.json* 檔案中，所有開頭為 "Host.Aggregator" 的類別都會記錄在 `Information` 層級。 所有開頭為 "Host" 的其他類別 (例如 "Host.Executor") 都會記錄於 `Error` 層級。| 

## <a name="queues"></a>queues

[儲存體佇列觸發程序和繫結](functions-bindings-storage-queue.md)的組態設定。

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|maxPollingInterval|60000|佇列輪詢之間的間隔上限 (毫秒)。| 
|visibilityTimeout|0|處理訊息失敗時，重試之間的時間間隔。| 
|batchSize|16|要平行取出和處理的佇列訊息數目。 最大值為 32。| 
|maxDequeueCount|5|將訊息移至有害佇列之前，嘗試處理訊息的次數。| 
|newBatchThreshold|batchSize/2|提取新批次之訊息的臨界值。| 

## <a name="servicebus"></a>serviceBus

[服務匯流排觸發程序和繫結](functions-bindings-service-bus.md)的組態設定。

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|maxConcurrentCalls|16|訊息幫浦應該起始之回呼的並行呼叫數上限。 | 
|prefetchCount|n/a|基礎 MessageReceiver 將使用的預設 PrefetchCount。| 
|autoRenewTimeout|00:05:00|將自動更新訊息鎖定的最大持續時間。| 

## <a name="singleton"></a>singleton

Singleton 鎖定行為的組態設定。 如需詳細資訊，請參閱[單一支援的 GitHub 問題](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)。

```json
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|lockPeriod|00:00:15|取得函式層級鎖定的期間。 鎖定會自動更新。| 
|listenerLockPeriod|00:01:00|接聽程式鎖定所需的期間。| 
|listenerLockRecoveryPollingInterval|00:01:00|啟動時無法取得接聽程式鎖定時，用於接聽程式鎖定復原的時間間隔。| 
|lockAcquisitionTimeout|00:01:00|執行階段將嘗試取得鎖定的時間量上限。| 
|lockAcquisitionPollingInterval|n/a|鎖定取得嘗試之間的間隔。| 

## <a name="tracing"></a>tracing

使用 `TraceWriter` 物件所建立記錄的組態設定。 請參閱 [C# 記錄](functions-reference-csharp.md#logging)和 [Node.js 記錄](functions-reference-node.md#writing-trace-output-to-the-console)。 

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|consoleLevel|info|主控台記錄的追蹤層級。 選項為：`off`、`error`、`warning`、`info` 和 `verbose`。|
|fileLoggingMode|debugOnly|檔案記錄的追蹤層級。 選項為 `never`、`always`、`debugOnly`。| 

## <a name="watchdirectories"></a>watchDirectories

應該監視其變更的一組[共用程式碼目錄](functions-reference-csharp.md#watched-directories)。  請確定，這些目錄中的程式碼變更時，函式會反映變更。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="durabletask"></a>durableTask

[長期函式](durable-functions-overview.md)的[工作中樞](durable-functions-task-hubs.md)名稱。

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

工作中樞名稱必須以字母開頭，僅包含字母和數字。 如果未指定，函式應用程式的預設工作中樞名稱是 **DurableFunctionsHub**。 如需詳細資訊，請參閱[工作中樞](durable-functions-task-hubs.md)。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何更新 host.json 檔案](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [請參閱環境變數中的全域設定](functions-app-settings.md)
