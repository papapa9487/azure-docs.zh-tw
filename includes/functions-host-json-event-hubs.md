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
