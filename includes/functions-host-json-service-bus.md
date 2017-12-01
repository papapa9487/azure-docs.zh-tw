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
|maxConcurrentCalls|16|訊息幫浦應該起始之回呼的並行呼叫數上限。 Functions 執行階段預設會並行處理多個訊息。 若要指示執行階段一次只處理一個佇列或主題訊息，請將 `maxConcurrentCalls` 設定為 1。 | 
|prefetchCount|n/a|基礎 MessageReceiver 將使用的預設 PrefetchCount。| 
|autoRenewTimeout|00:05:00|將自動更新訊息鎖定的最大持續時間。| 
