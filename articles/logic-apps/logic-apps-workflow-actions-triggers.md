---
title: "工作流程觸發程序和動作 - Azure Logic Apps | Microsoft Docs"
description: "深入了解可以與 Azure Logic Apps 搭配使用來建立及自動執行工作流程與程序的觸發程序和動作類型"
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/17/2016
ms.author: LADocs; mandia
ms.openlocfilehash: 9f95c0c486401e0d709829ce8d560f030932eea7
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2017
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>適用於邏輯應用程式工作流程的觸發程序和動作

所有邏輯應用程式都是以觸發程序為開頭，後面再接著動作。 本主題說明您可以藉由建置邏輯應用程式，用於建立系統整合及自動執行業務工作流程或程序的觸發程序和動作類型。 
  
## <a name="triggers-overview"></a>觸發程序概觀 

所有邏輯應用程式都是以觸發程序為開頭，此觸發程序會指定可以啟動邏輯應用程式回合的呼叫。 以下是兩種可以起始工作流程回合的方式︰  

* 輪詢觸發程序  
* 推送觸發程序 - 此觸發程序會呼叫[工作流程服務 REST API](https://docs.microsoft.com/rest/api/logic/workflows)  
  
所有觸發程序都包含下列最上層元素︰  
  
```json
"trigger-name": {
    "type": "trigger-type",
    "inputs": { call-settings },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Day|Week|Month",
        "interval": recurrence-interval-based-on-frequency
    },
    "conditions": [ array-of-required-conditions ],
    "splitOn": "property-used-for-creating-separate-workflows",
    "operationOptions": "operation-options-for-trigger"
}
```

### <a name="trigger-types-and-inputs"></a>觸發程序類型和輸入  

每一種觸發程序類型都有不同的介面和定義其行為的不同「輸入」。 

| 觸發程序類型 | 描述 | 
| ------------ | ----------- | 
| **週期性** | 根據已定義的排程來引發。 您可以設定一個未來的日期和時間來引發此觸發程序。 您也可以根據頻率來指定工作流程的執行時間和日子。 | 
| **要求**  | 讓邏輯應用程式進入一個您可以呼叫的端點，也稱為「手動」觸發程序。 | 
| **HTTP** | 檢查或「輪詢」HTTP Web 端點。 HTTP 端點必須藉由使用 "202" 非同步模式或藉由傳回陣列，來遵守特定觸發合約。 | 
| **ApiConnection** | 輪詢方式與 HTTP 觸發程序類似，但使用 [Microsoft Managed API](../connectors/apis-list.md)。 | 
| **HTTPWebhook** | 與 Request (要求) 觸發程序類似，會讓您的邏輯應用程式進入可呼叫的端點，但會呼叫指定的 URL 來進行註冊及取消註冊。 |
| **ApiConnectionWebhook** | 運作方式與 **HTTPWebhook** 觸發程序類似，但使用 Microsoft Managed API。 | 
||| 

如需有關其他詳細資料的資訊，請參閱[工作流程定義語言](../logic-apps/logic-apps-workflow-definition-language.md)。 
  
## <a name="recurrence-trigger"></a>循環觸發程序  

此觸發程序會根據您指定的週期和排程來執行，提供一個定期執行工作流程的簡單方式。 以下是一個每天執行的週期觸發程序範例：

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```
您也可以排定一個開始日期和時間來引發觸發程序。 例如，若要在每個星期一啟動每週報告，您可以排定讓邏輯應用程式在特定的星期一啟動，如以下範例所示︰ 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2017-09-18T00:00:00Z"
    }
}
```

以下是此觸發程序的定義： 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": recurrence-interval-based-on-frequency,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ one-or-more-hour-marks ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ one-or-more-minute-marks ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "start-date-time-with-format-YYYY-MM-DDThh:mm:ss",
        "timeZone": "specify-time-zone"
    }
}
```

| 元素名稱 | 必要 | 類型 | 描述 | 
| ------------ | -------- | ---- | ----------- | 
| frequency | 是 | String | 觸發程序引發頻率的時間單位。 只能使用下列其中一個值︰"second"、"minute"、"hour"、"day"、"week" 或 "month" | 
| interval | 是 | Integer | 描述工作流程根據 frequency 多久執行一次的正整數。 <p>以下是最小和最大間隔： <p>- 月：1-16 個月 </br>- 天：1-500 天 </br>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果 interval 是 6，而 frequency 是 "month"，則週期為每隔 6 個月。 | 
| timeZone | 否 | String | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 指定您要套用的時區。 | 
| startTime | 否 | String | 請使用以下格式來指定開始日期和時間： <p>YYYY-MM-DDThh:mm:ss (如果您指定時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未指定時區) <p>因此，舉例來說，如果您想要的是 2017 年 9 月 18 日下午 2:00，則請指定 "2017-09-18T14:00:00"，然後指定一個時區，例如 "Pacific Standard Time"。 或是指定 "2017-09-18T14:00:00Z"，但不指定時區。 <p>**注意：**這個開始時間必須依照 [UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)中的 [ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但不含 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 如果您不指定時區，就必須在結尾加上字母 "Z"，其中不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 <p>就簡單排程來說，開始時間係指第一次發生的時間，而就複雜排程來說，觸發程序會在開始時間一到就立即引發。 如需有關開始日期和時間的詳細資訊，請參閱[建立及排定定期執行的工作](../connectors/connectors-native-recurrence.md)。 | 
| weekDays | 否 | 字串或字串陣列 | 當您想要執行工作流程時，如果針對 `frequency` 指定 "Week"，便可指定一或多天 (以逗號分隔)："Monday"、"Tuesday"、"Wednesday"、"Thursday"、"Friday"、"Saturday" 及 "Sunday" | 
| hours | 否 | 整數或整數陣列 | 當您想要執行工作流程時，如果針對 `frequency` 指定 "Day" 或 "Week"，便可指定從 0 到 23 的一或多個整數 (以逗號分隔) 來表示一天中的哪幾個整點。 <p>例如，如果您指定 "10"、"12" 及 "14"，就會得出上午 10 點、下午 12 點及下午 2 點作為整點標記。 | 
| minutes | 否 | 整數或整數陣列 | 當您想要執行工作流程時，如果針對 `frequency` 指定 "Day" 或 "Week"，便可指定從 0 到 59 的一或多個整數 (以逗號分隔) 來表示小時中的哪幾個分鐘。 <p>例如，您可以指定 "30" 作為分鐘標記，然後使用上個範例代表一天中的整點，這樣就會得出上午 10:30、下午 12:30 及下午 2:30。 | 
|||||| 

例如，這個週期觸發程序會指定邏輯應用程式在太平洋標準時間 2017 年 9 月 9 日下午 2:00 起，於每週星期一早上 10:30、下午 12:30 及下午 2:30 執行：

``` json
{
    "triggers": {
        "myRecurrenceTrigger": {
            "type": "Recurrence",
            "recurrence": {
                "frequency": "Week",
                "interval": 1,
                "schedule": {
                    "hours": [
                        10,
                        12,
                        14
                    ],
                    "minutes": [
                        30
                    ],
                    "weekDays": [
                        "Monday"
                    ]
                },
               "startTime": "2017-09-07T14:00:00",
               "timeZone": "Pacific Standard Time"
            }
        }
    }
}
```

如需有關此觸發程序的週期和開始時間範例，請參閱[建立及排定定期執行的工作](../connectors/connectors-native-recurrence.md)。

## <a name="request-trigger"></a>要求觸發程序

此觸發程序可作為可供您用來透過 HTTP 要求呼叫邏輯應用程式的端點。 要求觸發程序看起來就像下面這個範例︰  
  
```json
"myRequestTrigger": {
    "type": "Request",
    "kind": "Http",
    "inputs": {
        "schema": {
            "type": "Object",
            "properties": {
                "myInputProperty1": { "type" : "string" },
                "myInputProperty2": { "type" : "number" }
            },
            "required": [ "myInputProperty1" ]
        }
    }
} 
```

此觸發程序有一個稱為 *schema* 的選擇性屬性：
  
| 元素名稱 | 必要 | 類型 | 描述 |
| ------------ | -------- | ---- | ----------- |
| 結構描述 | 否 | Object | 會驗證連入要求的 JSON 結構描述。 適用於協助後續工作流程步驟了解要參考哪些屬性。 | 
||||| 

若要叫用此端點，您必須呼叫 listCallbackUrl API。 請參閱[工作流程服務 REST API](https://docs.microsoft.com/rest/api/logic/workflows)。

## <a name="http-trigger"></a>HTTP 觸發程序  

HTTP 觸發程序會輪詢指定的端點並檢查回應，以決定是否應該執行工作流程。 這裡的 `inputs` 物件會採用下列建構 HTTP 呼叫時所需的參數︰  

| 元素名稱 | 必要 | 類型 | 描述 | 
| ------------ | -------- | ---- | ----------- | 
| method | 是 | String | 使用下列其中一種 HTTP 方法︰"GET"、"POST"、"PUT"、"DELETE"、"PATCH" 或 "HEAD" | 
| uri | 是| String | 觸發程序所檢查的 HTTP 或 HTTPS 端點。 字串大小上限：2 KB | 
| 查詢 | 否 | Object | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | Object | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | Object | 代表傳送至端點的承載。 | 
| RetryPolicy | 否 | Object | 請使用此物件來自訂 4xx 或 5xx 錯誤的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md)。 | 
| 驗證 | 否 | Object | 代表要求應用來進行驗證的方法。 如需詳細資訊，請參閱[排程器輸出驗證](../scheduler/scheduler-outbound-authentication.md)。 <p>除了排程器之外，還有一個支援的屬性︰`authority`。 根據預設，若未指定，此值會是 `https://login.windows.net`，但您可以使用不同的值，例如 `https://login.windows\-ppe.net`。 | 
||||| 
 
為了與邏輯應用程式良好搭配運作，HTTP 觸發程序會要求 HTTP API 必須符合特定模式。 此觸發程序可辨識下列屬性：  
  
| 回應 | 必要 | 描述 | 
| -------- | -------- | ----------- |  
| 狀態碼 | 是 | 狀態碼 200 (代表「正常」) 會促使執行。 其他任何狀態碼則不會導致執行。 | 
| Retry-after 標頭 | 否 | 邏輯應用程式再次輪詢端點前所需經過的秒數。 | 
| 位置標頭 | 否 | 在下一個輪詢間隔時所要呼叫的 URL。 如果未指定，則會使用原本的 URL。 | 
|||| 

以下是不同要求類型的一些範例行為︰
  
| Response code | 多久之後重試 | 行為 | 
| ------------- | ----------- | -------- | 
| 200 | {無} | 執行工作流程，然後在所定義的週期之後再次檢查是否有其他資料。 | 
| 200 | 10 秒 | 執行工作流程，然後在 10 秒之後再次檢查是否有其他資料。 |  
| 202 | 60 秒 | 不觸發工作流程。 下一次嘗試會依據所定義的週期，在一分鐘內開始。 如果所定義的週期不超過一分鐘，則 retry-after 標頭的優先順序較高。 否則會採用所定義的週期。 | 
| 400 | {無} | 不正確的要求，不執行工作流程。 如果未定義任何 `retryPolicy`，則會使用預設原則。 達到重試次數之後，觸發程序會在所定義的週期之後再次檢查是否還有資料。 | 
| 500 | {無}| 伺服器錯誤，不執行工作流程。 如果未定義任何 `retryPolicy`，則會使用預設原則。 達到重試次數之後，觸發程序會在所定義的週期之後再次檢查是否還有資料。 | 
|||| 

以下是 HTTP 觸發程序輸出： 
  
| 元素名稱 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| headers | Object | HTTP 回應的標頭 | 
| body | Object | HTTP 回應的主體 | 
|||| 

## <a name="api-connection-trigger"></a>API 連線觸發程序  

API 連線觸發程序和 HTTP 觸發程序的相似之處在於其基本功能。 然而，用於識別動作的參數卻不相同。 下列是一個範例：  
  
```json
"myDailyReportTrigger": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            }
        },
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "awesomest"
    }
}
```

| 元素名稱 | 必要 | 類型 | 描述 | 
| ------------ | -------- | ---- | ----------- | 
| 主機 | 是 | Object | API App 的所裝載閘道和識別碼 | 
| method | 是 | String | 使用下列其中一種 HTTP 方法︰"GET"、"POST"、"PUT"、"DELETE"、"PATCH" 或 "HEAD" | 
| 查詢 | 否 | Object | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | Object | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | Object | 代表傳送至端點的承載。 | 
| RetryPolicy | 否 | Object | 請使用此物件來自訂 4xx 或 5xx 錯誤的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md)。 | 
| 驗證 | 否 | Object | 代表要求應用來進行驗證的方法。 如需詳細資訊，請參閱[排程器輸出驗證](../scheduler/scheduler-outbound-authentication.md)。 | 
||||| 

以下是 `host` 物件的屬性：  
  
| 元素名稱 | 必要 | 描述 | 
| ------------ | -------- | ----------- | 
| api runtimeUrl | 是 | Managed API 的端點 | 
| 連線名稱 |  | 工作流程所使用之 Managed API 連線的名稱。 必須參考名為 `$connection` 的參數。 |
|||| 

以下是 API 連線觸發程序的輸出︰
  
| 元素名稱 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| headers | Object | HTTP 回應的標頭 | 
| body | Object | HTTP 回應的主體 | 
|||| 
  
## <a name="httpwebhook-trigger"></a>HTTPWebhook 觸發程序  

HTTPWebhook 觸發程序與 Request (要求) 觸發程序類似，會提供一個端點，但 HTTPWebhook 觸發程序還會呼叫指定的 URL 來進行註冊和取消註冊。 HTTPWebhook 觸發程序看起來可能就像下面這個範例︰  

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": { },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": { },
            "retryPolicy": { }
        },
        "unsubscribe": {
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "method": "POST",
            "authentication": { }
        }
    },
    "conditions": [ ]
}
```

這些區段之中有許多是選擇性的，而 HTTPWebhook 觸發程序的行為會取決於您所提供或省略的區段。 以下是 HTTPWebhook 觸發程序的屬性：
  
| 元素名稱 | 必要 | 描述 | 
| ------------ | -------- | ----------- |  
| 訂閱 | 否 | 指定建立觸發程序時要呼叫的連出要求，並執行初始註冊。 | 
| 取消訂閱 | 否 | 指定刪除觸發程序時要呼叫的連出要求。 | 
|||| 

您可以採用與 [HTTP 非同步限制](#asynchronous-limits)相同的方式，來對 Webhook 動作指定限制。 以下是有關 `subscribe` 和 `unsubscribe` 動作的詳細資訊：

* 系統會呼叫 `subscribe`，如此觸發程序才能開始接聽事件。 這個連出呼叫的開頭使用與標準 HTTP 動作相同的參數。 當工作流程以任何方式發生變更時 (例如變換認證或觸發程序的輸入參數變更時)，就會進行此呼叫。 
  
  為了支援此呼叫，`@listCallbackUrl()` 函式會針對工作流程中的這個特定觸發程序傳回唯一 URL。 此 URL 代表使用服務 REST API 之端點的唯一識別碼。
  
* 當作業使這個觸發程序變成無效時，就會自動呼叫 `unsubscribe`，包括的作業如下：

  * 刪除或停用觸發程序。 
  * 刪除或停用工作流程。 
  * 刪除或停用訂用帳戶。 
  
  此函式的參數與 HTTP 觸發程序的參數相同。

以下是 HTTPWebhook 觸發程序的輸出，並且是連入要求的內容︰
  
| 元素名稱 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| headers | Object | HTTP 回應的標頭 | 
| body | Object | HTTP 回應的主體 | 
|||| 

## <a name="conditions"></a>條件  

對於任何觸發程序，您都可以使用一或多個條件來判斷是否應該執行工作流程。 例如：  

```json
"myDailyReportTrigger": {
    "type": "Recurrence",
    "conditions": [ 
        {
            "expression": "@parameters('sendReports')"
        } 
    ],
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

在此案例中，報告只會在工作流程的 `sendReports` 參數設定為 true 時觸發。 最後，條件可以參考觸發程序的狀態碼。 例如，您可以只在網站傳回狀態碼 500 時啟動工作流程，如下所示︰
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  
  
> [!NOTE]  
> 當任何運算式以任何方式參考觸發程序的狀態碼時，就會取代預設行為 - 只在狀態碼為 200 (代表「良好」) 時觸發。 例如，如果您想要同時對狀態碼 200 和狀態碼 201 觸發，則需要納入 `@or(equals(triggers().code, 200),equals(triggers().code,201))` 作為條件。
  
## <a name="start-multiple-runs-for-a-request"></a>為要求啟動多個執行

若要為單一要求啟動多個執行，`splitOn` 會適用於，例如，當您想要輪詢的端點可在輪詢間隔之間具有多個新項目時。
  
透過 `splitOn`，您可在包含項目陣列 (您想要使用其中各項來啟動觸發程序的執行) 的回應承載內指定屬性。 例如，假設您有一個會傳回以下回應的 API：  
  
```json
{
    "status": "Succeeded",
    "rows": [
        {  
            "id" : 938109380,
            "name" : "myFirstRow"
        },
        {
            "id" : 938109381,
            "name" : "mySecondRow"
        }
    ]
}
```
  
您的邏輯應用程式只需要 `rows` 內容，因此您可以如下列範例這樣建構觸發程序︰  

```json
"mySplitterTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "minute",
        "interval": 1
    },
    "intputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.rows"
}
```
> [!NOTE]  
> 如果您使用 `SplitOn` 命令，就無法取得陣列外的屬性，因此就此範例而言，您會無法取得 API 所傳回之回應中的 `status` 屬性。
> 此外，在此範例中，我們使用 `?` 運算子，以便可以在 `rows` 屬性不存在時避免發生失敗。 

因此，在工作流程定義中，`@triggerBody().name` 會針對第一個回合傳回 `myFirstRow`，然後針對第二個回合傳回 `mySecondRow`。 觸發程序的輸出看起來就像下面這個範例︰  

```json
{
    "body": {
        "id": 938109380,
        "name": "mySecondRow"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "mySecondRow"
    }
}
```
  
## <a name="single-run-instance"></a>單一執行的執行個體

您可以設定週期觸發程序，讓它們只有在所有作用中回合都已完成時才引發。 如果排定的週期在工作流程執行個體正在執行中時發生，觸發程序就會略過，等到下一個排定的週期間隔時才再次檢查。
若要設定這個設定，請將 `operationOptions` 屬性設定為 `singleInstance`：

```json
"triggers": {
    "myHTTPTrigger": {
        "type": "Http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="actions-overview"></a>動作概觀

動作有許多類型，且各有各的獨特行為。 每種動作類型都有定義動作行為的不同輸入。 集合動作可在自身當中包含許多其他動作。 

### <a name="standard-actions"></a>標準動作  

| 動作類型 | 描述 | 
| ----------- | ----------- | 
| **HTTP** | 呼叫 HTTP Web 端點。 | 
| **ApiConnection**  | 運作方式與 HTTP 動作類似，但使用 [Microsoft Managed API](https://docs.microsoft.com/azure/connectors/apis-list)。 | 
| **ApiConnectionWebhook** | 運作方式與 HTTPWebhook 類似，但使用 Microsoft Managed API。 | 
| **回應** | 定義連入呼叫的回應。 | 
| **Function** | 代表 Azure 函式。 | 
| **Wait** | 等候一段固定的時間，或直到某個特定時間為止。 | 
| **Workflow** | 代表巢狀工作流程。 | 
| **撰寫** | 從動作的輸入建構任意的物件。 | 
| **查詢** | 根據條件來篩選陣列。 | 
| **選取** | 將陣列的每個元素投射成一個新的值。 例如，您可以將數字陣列轉換成物件陣列。 | 
| **資料表** | 將項目的陣列轉換為 CSV 或 HTML 資料表。 | 
| **Terminate** | 停止執行工作流程。 | 
||| 

### <a name="collection-actions"></a>集合動作

| 動作類型 | 描述 | 
| ----------- | ----------- | 
| **Condition** | 評估運算式，然後根據結果來執行對應的分支。 | 
| **範圍** | 用於將其他動作以邏輯方式分組。 | 
| **ForEach** | 這個迴圈動作會逐一查看陣列，並對每個陣列項目執行內部動作。 | 
| **Until** | 這個迴圈動作會執行內部動作，直到條件的結果為 true 為止。 | 
||| 

## <a name="http-action"></a>HTTP 動作  

HTTP 動作會呼叫指定端點，然後檢查回應以判斷是否應執行工作流程。 例如：
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

這裡的 `inputs` 物件會採用下列建構 HTTP 呼叫時所需的參數︰ 

| 元素名稱 | 必要 | 類型 | 描述 | 
| ------------ | -------- | ---- | ----------- | 
| method | 是 | String | 使用下列其中一種 HTTP 方法︰"GET"、"POST"、"PUT"、"DELETE"、"PATCH" 或 "HEAD" | 
| uri | 是| String | 觸發程序所檢查的 HTTP 或 HTTPS 端點。 字串大小上限：2 KB | 
| 查詢 | 否 | Object | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | Object | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | Object | 代表傳送至端點的承載。 | 
| RetryPolicy | 否 | Object | 請使用此物件來自訂 4xx 或 5xx 錯誤的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md)。 | 
| operationsOptions | 否 | String | 定義一組要覆寫的特殊行為。 | 
| 驗證 | 否 | Object | 代表要求應用來進行驗證的方法。 如需詳細資訊，請參閱[排程器輸出驗證](../scheduler/scheduler-outbound-authentication.md)。 <p>除了排程器之外，還有一個支援的屬性︰`authority`。 根據預設，若未指定，此值會是 `https://login.windows.net`，但您可以使用不同的值，例如 `https://login.windows\-ppe.net`。 | 
||||| 

這個範例 HTTP 動作會在有間歇性失敗時重新嘗試擷取最新的消息兩次，總共的執行次數為三次，每次嘗試之間會延遲 30 秒︰
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

重試間隔會以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)來指定。 此間隔的最小值 (也是預設值) 為 20 秒，最大值為 1 小時。 最大重試計數 (也是預設值) 為 4 小時。 如果未指定重試原則定義，則會使用 `fixed` 策略搭配預設的重試計數和間隔值。 若要停用重試原則，請將其類型設定為 `None`。

### <a name="asynchronous-patterns"></a>非同步模式

根據預設，所有 HTTP 型動作皆支援標準的非同步作業模式。 因此，如果遠端伺服器藉由「202 已接受」回應指出已接受要求來進行處理，Logic Apps 引擎就會持續輪詢回應之 location 標頭中所指定的 URL，直到達到中止狀態 (亦即非 202 回應) 為止。
  
若要停用先前所述的非同步行為，請在動作輸入中將 `operationOptions` 設定為 `DisableAsyncPattern`。 在此案例中，動作的輸出是根據伺服器所傳來的初始 202 回應。 例如：
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```
<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>非同步限制

您可以將非同步模式的持續時間限制在特定的時間間隔內。 如果在經過時間間隔後仍未達到終止狀態，動作的狀態就會標示為 `Cancelled`，而代碼會是 `ActionTimedOut`。 限制逾時是以 ISO 8601 格式來指定。 您可以如以下所示指定限制：

``` json
"action-name": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>APIConnection 動作

APIConnection 動作會參考 Microsoft Managed 連接器。 這個動作需要對有效連線的參考，以及 API 與參數的相關資訊。
以下是一個範例 APIConnection 動作：

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| 元素名稱 | 必要 | 類型 | 描述 | 
| ------------ | -------- | ---- | ----------- | 
| 主機 | 是 | Object | 代表連接器資訊，例如 `runtimeUrl` 和對連線物件的參考。 | 
| method | 是 | String | 使用下列其中一種 HTTP 方法︰"GET"、"POST"、"PUT"、"DELETE"、"PATCH" 或 "HEAD" | 
| 路徑 | 是 | String | API 作業的路徑 | 
| 查詢 | 否 | Object | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | Object | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | Object | 代表傳送至端點的承載。 | 
| RetryPolicy | 否 | Object | 請使用此物件來自訂 4xx 或 5xx 錯誤的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md)。 | 
| operationsOptions | 否 | String | 定義一組要覆寫的特殊行為。 | 
| 驗證 | 否 | Object | 代表要求應用來進行驗證的方法。 如需詳細資訊，請參閱[排程器輸出驗證](../scheduler/scheduler-outbound-authentication.md)。 |
||||| 

## <a name="apiconnection-webhook-action"></a>APIConnectionWebhook 動作

APIConnectionWebhook 動作會參考 Microsoft Managed 連接器。 這個動作需要對有效連線的參考，以及 API 與參數的相關資訊。 您可以採用與 [HTTP 非同步限制](#asynchronous-limits)相同的方式，來對 Webhook 動作指定限制。

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| 元素名稱 | 必要 | 類型 | 描述 | 
| ------------ | -------- | ---- | ----------- | 
| 主機 | 是 | Object | 代表連接器資訊，例如 `runtimeUrl` 和對連線物件的參考。 | 
| 路徑 | 是 | String | API 作業的路徑 | 
| 查詢 | 否 | Object | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | Object | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | Object | 代表傳送至端點的承載。 | 
| RetryPolicy | 否 | Object | 請使用此物件來自訂 4xx 或 5xx 錯誤的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md)。 | 
| operationsOptions | 否 | String | 定義一組要覆寫的特殊行為。 | 
| 驗證 | 否 | Object | 代表要求應用來進行驗證的方法。 如需詳細資訊，請參閱[排程器輸出驗證](../scheduler/scheduler-outbound-authentication.md)。 |
||||| 

## <a name="response-action"></a>回應動作  

這個動作包含來自 HTTP 要求的整個回應承載，也包括 `statusCode`、`body` 及 `headers`：
  
```json
"myResponseAction": {
    "type": "response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

回應動作有其他動作所不適用的特殊限制，具體而言就是：  
  
* 邏輯應用程式定義內的平行分支中不能有回應動作，因為連入要求必須有確定性的回應。
  
* 如果工作流程在連入要求已收到回應之後才達到回應動作，系統就會將該回應動作視為失敗或發生衝突。 如此一來，邏輯應用程式回合就會標示為 `Failed`。
  
* 含有回應動作的工作流程無法使用觸發程式定義中的 `splitOn` 命令，因為該呼叫會建立多個回合。 因此，當工作流程作業為 PUT 時，請檢查是否有此情況，並傳回「不正確的要求」回應。

## <a name="function-action"></a>函式動作   

此動作可讓您表示及呼叫 [Azure 函式](../azure-functions/functions-overview.md)，例如：

```json
"my-Azure-Function-name": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/sites/{your-Azure-function-app-name}/functions/{your-Azure-function-name}"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```
| 元素名稱 | 必要 | 類型 | 描述 | 
| ------------ | -------- | ---- | ----------- |  
| 函式識別碼 | 是 | String | 您想要呼叫之 Azure 函式的資源識別碼。 | 
| method | 否 | String | 用來呼叫函式的 HTTP 方法。 若未指定，則預設方法為 "POST"。 | 
| 查詢 | 否 | Object | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | Object | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | Object | 代表傳送至端點的承載。 | 
|||||

當您儲存邏輯應用程式時，Azure Logic Apps 會對所參考的函式執行檢查︰

* 您必須要能夠存取該函式。
* 您只能使用標準 HTTP 觸發程序或一般 JSON Webhook 觸發程序。
* 函式不應該有任何已定義的路由。
* 只允許使用「函式」和「匿名」授權層級。

系統會在執行階段擷取、快取及使用觸發程序 URL。 因此，若有任何作業讓快取的 URL 失效，動作就會在執行階段失敗。 若要解決這個問題，請再次儲存邏輯應用程式，這會讓邏輯應用程式再次擷取及快取觸發程序 URL。

## <a name="wait-action"></a>等候動作  

此動作會在一段指定間隔內暫停工作流程的執行。 以下範例會讓工作流程等候 15 分鐘：
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
或者，若要等候直到特定時間點，您可以使用此範例︰
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> 您可以使用 `until` 物件或 `interval` 物件 (但不可同時使用兩者) 來指定等候持續時間。
  
| 元素名稱 | 必要 | 類型 | 描述 | 
| ------------ | -------- | ---- | ----------- | 
| 直到 | 否 | Object | 以時間點為基礎的等候持續時間 | 
| 直到時間戳記 | 是 | String | 等候時間到期時的時間點 (以 [UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)指定) | 
| interval | 否 | Object | 以間隔單位和計數為基礎的等候持續時間 | 
| 間隔單位 | 是 | String | 時間單位。 只能使用下列其中一個值︰"second"、"minute"、"hour"、"day"、"week" 或 "month" | 
| 間隔計數 | 是 | Integer | 一個正整數，此正整數代表用於表示等候持續時間的間隔單位數 | 
||||| 

## <a name="workflow-action"></a>工作流程動作   

此動作代表另一個工作流程。 Logic Apps 會對工作流程 (或更具體地說是觸發程序) 執行存取權檢查，這意謂著您必須具備工作流程的存取權。

此動作的輸出會根據您在子工作流程之 `response` 動作中所做的定義。 如果您尚未定義 `response` 動作，輸出便會空白。

```json
"myNestedWorkflowAction": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| 元素名稱 | 必要 | 類型 | 描述 | 
| ------------ | -------- | ---- | ----------- |  
| 主機識別碼 | 是 | String| 您想要呼叫之工作流程的資源識別碼 | 
| 主機 triggerName | 是 | String | 您想要叫用之觸發程序的名稱 | 
| 查詢 | 否 | Object | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | Object | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | Object | 代表傳送至端點的承載。 | 
|||||   

## <a name="compose-action"></a>撰寫動作

此動作可讓您建構任意的物件，而輸出則為評估動作之輸入的結果。 

> [!NOTE]
> 您可以使用 `Compose`動作來建構任何輸出，包括物件、陣列以及邏輯應用程式原生支援的任何其他類型，例如 XML 和二進位檔。

例如，您可以使用撰寫動作來合併多個動作的輸出︰

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="select-action"></a>選取動作

此動作可讓您將陣列的每個元素投射成一個新的值。
例如，若要將數字的陣列轉換為物件的陣列，您可以使用︰

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| 名稱 | 必要 | 類型 | 描述 | 
| ---- | -------- | ---- | ----------- | 
| from | 是 | 陣列 | 來源陣列 |
| 選取 | 是 | 任意 | 套用至來源陣列中每個元素的投射 |
||||| 

`select` 動作的輸出是一個基數與輸入陣列相同的陣列。 每個元素會依據 `select` 屬性的定義進行轉換。 如果輸入是空的陣列，則輸出也是空的陣列。

## <a name="query-action"></a>查詢動作

此動作可讓您根據條件來篩選陣列。 以下範例會選取大於 2 的數字：

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

`query` 動作的輸出是一個陣列，其中具有輸入陣列中符合條件的元素。

> [!NOTE]
> 如果沒有任何值符合 `where` 條件，則結果為空白陣列。

| 名稱 | 必要 | 類型 | 描述 | 
| ---- | -------- | ---- | ----------- | 
| from | 是 | 陣列 | 來源陣列 |
| 其中 | 是 | String | 套用至來源陣列中各個元素的條件 |
||||| 

## <a name="table-action"></a>資料表動作

此動作可讓您將項目陣列轉換成 **CSV** 或 **HTML** 資料表。 例如，假設您有一個含有以下陣列的 `@triggerBody()`

```json
[ 
    {
      "id": 0,
      "name": "apples"
    },
    {
      "id": 1, 
      "name": "oranges"
    }
]
```

而您定義一個如以下範例的資料表動作：

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html"
    }
}
```

此範例的結果會看起來像這個 HTML 資料表： 

<table><thead><tr><th>id</th><th>名稱</th></tr></thead><tbody><tr><td>0</td><td>apples</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>

為了自訂此資料表，您可以明確指定資料行，例如：

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

此範例的結果會看起來像這個 HTML 資料表： 

<table><thead><tr><th>產生識別碼</th><th>描述</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>

| 名稱 | 必要 | 類型 | 描述 | 
| ---- | -------- | ---- | ----------- | 
| from | 是 | 陣列 | 來源陣列。 如果 `from` 屬性值為空的陣列，則輸出為空的資料表。 | 
| format | 是 | String | 您想要的資料表格式 (**CSV** 或 **HTML**) | 
| columns | 否 | 陣列 | 您想要的資料表資料行。 用來覆寫預設資料表圖形。 | 
| 資料行標頭 | 否 | String | 資料行標頭 | 
| 資料行值 | 是 | String | 資料行值 | 
||||| 

## <a name="terminate-action"></a>終止動作

此動作會停止工作流程執行、取消任何進行中的動作，並略過任何剩餘的動作。 終止動作不會影響任何已完成的動作。

例如，若要停止狀態為 "Failed" (失敗) 的執行，您可以使用此範例：

```json
"handleUnexpectedResponseAction": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response"
        }
    }
}
```

| 名稱 | 必要 | 類型 | 描述 | 
| ---- | -------- | ---- | ----------- | 
| runStatus | 是 | String | 目標執行的狀態 (`Failed` 或 `Cancelled`) |
| runError | 否 | Object | 錯誤詳細資料。 只有當 `runStatus` 已設定為 `Failed`時才支援。 |
| runError 代碼 | 否 | String | 執行的錯誤碼 |
| runError 訊息 | 否 | String | 執行的錯誤訊息 |
||||| 

## <a name="collection-actions-overview"></a>集合動作概觀

某些動作可以在自身當中包含動作。 您可以直接在集合外部參考集合內的參考動作。 例如，如果您在某個 `scope` 中定義 `Http`，則 `@body('http')` 在工作流程中的任何位置仍然保持有效。 您可以讓集合內的動作只與相同集合內的其他動作搭配執行 `runAfter`。

## <a name="condition-if-action"></a>條件：If 動作

此動作可讓您評估條件，並根據運算式是否評估為 `true` 來執行分支。 
  
```json
"myCondition": {
    "type": "If",
    "actions": {
        "if_true": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {}
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {}
}
``` 

| 名稱 | 必要 | 類型 | 描述 | 
| ---- | -------- | ---- | ----------- | 
| 動作 | 是 | Object | `expression` 評估為 `true` 時要執行的內部動作 | 
| expression | 是 | String | 要評估的運算式 |
| else | 否 | Object | `expression` 評估為 `false` 時要執行的內部動作 |
||||| 

如果條件評估成功，條件就會標示為 `Succeeded`。 `actions` 或 `else` 物件中的動作會評估為： 

* `Succeeded`：當動作執行且成功時
* `Failed`：當動作執行但失敗時
* `Skipped`：當個別的分支並未實行時

以下是說明條件如何在動作中使用運算式的範例︰
  
| JSON 值 | 結果 | 
| ---------- | -------| 
| `"expression": "@parameters('hasSpecialAction')"` | 任何會評估為 true 的值都會促使此條件成立。 僅支援布林運算式。 若要將其他類型轉換成布林值，請使用函式：`empty` 和 `equals` | 
| `"expression": "@greater(actions('act1').output.value, parameters('threshold'))"` | 支援比較函式。 就此範例而言，只有當 `act1` 的輸出大於臨界值時，才會執行動作。 | 
| `"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"` | 支援使用邏輯函式來建立巢狀布林運算式。 就此範例而言，當 `act1` 的輸出超出臨界值或低於 100 時，就會執行動作。 | 
| `"expression": "@equals(length(actions('act1').outputs.errors), 0))"` | 若要檢查陣列是否有任何項目，您可以使用陣列函式。 就此範例而言，當 `errors` 陣列空白時，就會執行動作。 | 
| `"expression": "parameters('hasSpecialAction')"` | 錯誤，不是有效條件，因為條件必須有 @。 |  
|||

## <a name="scope-action"></a>範圍動作

此動作可讓您以邏輯方式將工作流程中的動作分組。

```json
"myScope": {
    "type": "Scope",
    "actions": {
        "call_bing": {
            "type": "Http",
             "inputs": {
                "url": "http://www.bing.com"
            }
        }
    }
}
```

| 名稱 | 必要 | 類型 | 描述 | 
| ---- | -------- | ---- | ----------- |  
| 動作 | 是 | Object | 要在範圍內執行的內部動作 |
||||| 

## <a name="foreach-action"></a>ForEach 動作

這個迴圈動作會逐一查看陣列，並對每個陣列項目執行內部動作。 `foreach` 迴圈預設會以平行方式執行，並且一次可以平行執行 20 個執行項目。 若要設定執行規則，請使用 `operationOptions` 參數。

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

| 名稱 | 必要 | 類型 | 描述 | 
| ---- | -------- | ---- | ----------- | 
| 動作 | 是 | Object | 要在迴圈內執行的內部動作 | 
| foreach | 是 | String | 要逐一查看的陣列 | 
| operationOptions | 否 | String | 指定任何作業選項來自訂行為。 目前僅支援 `Sequential` 來循序執行反覆運算，而預設行為是平行。 |
||||| 

## <a name="until-action"></a>直到動作

這個迴圈動作會執行內部動作，直到條件的結果為 true 為止。

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| 名稱 | 必要 | 類型 | 描述 | 
| ---- | -------- | ---- | ----------- | 
| 動作 | 是 | Object | 要在迴圈內執行的內部動作 | 
| expression | 是 | String | 要在每次反覆運算之後評估的運算式 | 
| limit | 是 | Object | 迴圈的限制。 必須至少定義一個限制。 | 
| 計數 | 否 | Integer | 要執行之反覆運算次數的限制 | 
| timeout | 否 | String | 指定迴圈應該執行多久的逾時限制 (以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601) 指定) |
||||| 

## <a name="next-steps"></a>後續步驟

* [工作流程定義語言](../logic-apps/logic-apps-workflow-definition-language.md)
* [工作流程 REST API](https://docs.microsoft.com/rest/api/logic/workflows) \(英文\)
