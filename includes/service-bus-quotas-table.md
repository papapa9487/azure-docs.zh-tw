下表列出服務匯流排訊息的特定配額資訊。 如需有關服務匯流排的價格及其他配額的詳細資訊，請參閱 [服務匯流排價格](https://azure.microsoft.com/pricing/details/service-bus/) 概觀。

| 配額名稱 | Scope | 類型 | 超出時的行為 | 值 |
| --- | --- | --- | --- | --- |
| 每個 Azure 訂用帳戶的基本/標準命名空間數目上限 |命名空間 |靜態 |入口網站會拒絕後續對於更多基本/標準命名空間的要求。 |100|
| 每個 Azure 訂用帳戶的進階命名空間數目上限 |命名空間 |靜態 |入口網站會拒絕後續對於更多進階命名空間的要求。 |10 |
| 佇列/主題大小 |實體 |在建立佇列/主題時定義。 |內送訊息將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 |1、2、3、4 或 5 GB。<br /><br />如果啟用[分割](../articles/service-bus-messaging/service-bus-partitioning.md)，則佇列/主題大小上限為 80 GB。 |
| 命名空間上的並行連線數目 |命名空間 |靜態 |後續對更多連接的要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 REST 作業不會計入並行 TCP 連線內。 |NetMessaging: 1,000<br /><br />AMQP: 5,000 |
| 佇列/主題/訂用帳戶實體上的並行接收要求數目 |實體 |靜態 |後續接收要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 這個配額套用至一個主題的所有訂用帳戶的並行接收作業數目合計。 |5,000 |
| 每個服務命名空間的主題/佇列數目 |全系統 |靜態 |後續要求在服務命名空間上建立新主題或佇列將會遭到拒絕。 因此，如果透過 [Azure 入口網站][Azure portal]設定，將會產生錯誤訊息。 如果從管理 API 進行呼叫，則呼叫端程式碼將會收到例外狀況。 |10,000<br /><br />服務命名空間中主題和佇列的總數必須小於或等於 10,000。<br/>這不適用於進階層，因為所有實體都已分割。 |
| 每個服務命名空間的分割主題/佇列數目 |全系統 |靜態 |後續要求在服務命名空間上建立新分割主題或佇列將會遭到拒絕。 因此，如果透過 [Azure 入口網站][Azure portal]設定，將會產生錯誤訊息。 如果從管理 API 進行呼叫，則呼叫端程式碼將會收到 **QuotaExceededException**例外狀況。 |基本層和標準層 - 100<br />[進階](../articles/service-bus-messaging/service-bus-premium-messaging.md) - 1,000 (每個傳訊單位)<br/><br />每個分割佇列或主題的配額計數為每個命名空間 10,000 個實體。 |
| 任何傳訊實體路徑的大小上限︰佇列或主題 |實體 |靜態 |- |260 個字元 |
| 任何傳訊實體名稱的大小上限︰命名空間、訂用帳戶或訂用帳戶規則 |實體 |靜態 |- |50 個字元 |
| 佇列/主題/訂用帳戶實體的訊息大小 |全系統 |靜態 |超出這些配額的內送訊息將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 |訊息大小上限︰256KB ([標準層](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1MB ([進階層](../articles/service-bus-messaging/service-bus-premium-messaging.md))。 <br /><br />**注意** 由於有系統額外負擔，所以這項限制通常會小一點。<br /><br />標頭大小上限︰64KB<br /><br />屬性包中的標頭屬性數目上限︰**byte/int.MaxValue**<br /><br />屬性包中的屬性大小上限︰沒有明確限制。 受限於標頭大小上限。 |
| 佇列/主題/訂用帳戶實體的訊息屬性大小 |全系統 |靜態 |已產生 **SerializationException** 例外狀況。 |每個屬性的訊息屬性大小上限為 32K。 所有屬性的累計大小不得超過 64K。 這適用於 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 的整個標頭，其中同時包含使用者屬性和系統屬性 (例如 [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber)、[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)、[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid) 等等)。 |
| 每個主題的訂用帳戶數目 |全系統 |靜態 |後續要求建立主題的其他訂用帳戶將會遭到拒絕。 因此，如果透過入口網站設定，將會顯示錯誤訊息。 如果從管理 API 進行呼叫，則呼叫端程式碼將會收到例外狀況。 |2,000 |
| 每個主題的 SQL 篩選器數目 |全系統 |靜態 |後續要求在主題上建立其他篩選器都會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 |2,000 |
| 每個主題的相互關聯篩選器數目 |全系統 |靜態 |後續要求在主題上建立其他篩選器都會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 |100,000 |
| SQL 篩選器/動作的大小 |全系統 |靜態 |後續要求建立其他篩選器都會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 |篩選條件字串的長度上限︰1024 (1K)。<br /><br />規則動作字串的長度上限︰1024 (1K)。<br /><br />每個規則動作的運算式數目上限︰32。 |
| 每個命名空間、佇列或主題的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 規則數目 |實體、命名空間 |靜態 |後續要求建立其他規則都會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 |規則數目上限︰12。 <br /><br /> 在服務匯流排命名空間上設定的規則可套用到該命名空間中的所有佇列和主題。 |

[Azure portal]: https://portal.azure.com