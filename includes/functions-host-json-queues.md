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
