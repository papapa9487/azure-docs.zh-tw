---
title: "工作流程動作和觸發程序 - Azure Logic Apps | Microsoft Docs"
description: 
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
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: bfde83e1142bf57e02ee458d477a0a70e78c4ad6
ms.contentlocale: zh-tw
ms.lasthandoff: 09/06/2017

---

# <a name="workflow-actions-and-triggers-for-azure-logic-apps"></a>Azure Logic Apps 的工作流程動作和觸發程序

邏輯應用程式是由觸發程序和動作所組成。 觸發程序有六種類型。 每種類型各有不同的介面和行為。 透過查看[工作流程定義語言](logic-apps-workflow-definition-language.md)的詳細資料，您還可以深入了解其他詳細資料。  
  
請繼續閱讀以深入了解觸發程序和動作，以及如何使用它們來建置邏輯應用程式，以改善商務程序和工作流程。  
  
### <a name="triggers"></a>觸發程序  

觸發程序會指定可起始邏輯應用程式工作流程執行的呼叫。 以下是兩種可起始工作流程執行的不同方式︰  
  
-   輪詢觸發程序  

-   推送觸發程序 - 藉由呼叫[工作流程服務 REST API](https://docs.microsoft.com/rest/api/logic/workflows)  
  
所有觸發程序都包含下列最上層元素︰  
  
```json
"<name-of-the-trigger>" : {
    "type": "<type-of-trigger>",
    "inputs": { <settings-for-the-call> },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Week|Month|Year",
        "interval": "<recurrence interval in units of frequency>"
    },
    "conditions": [ <array-of-required-conditions > ],
    "splitOn" : "<property to create runs for>",
    "operationOptions": "<operation options on the trigger>"
}
```

### <a name="trigger-types-and-their-inputs"></a>觸發程序類型和其輸入  

您可以使用下列類型的觸發程序︰
  
-   **要求** \- 讓邏輯應用程式成為可供您呼叫的端點  
  
-   **循環** \- 根據定義的排程來引發  
  
-   **HTTP** \- 輪詢 HTTP Web 端點。 HTTP 端點必須符合特定觸發合約 \- 不論是藉由使用 202\-非同步模式或藉由傳回陣列  
  
-   **ApiConnection** \- 如同 HTTP 觸發程序一般地輪詢，不過，它會利用 [Microsoft 管理的 API](https://docs.microsoft.com/azure/connectors/apis-list)  
  
-   **HTTPWebhook** \- 如同手動觸發程序一般地開啟端點，不過，它也會對指定的 URL 進行呼叫以便註冊和取消註冊  
  
-   **ApiConnectionWebhook** \- 藉由利用 Microsoft 管理的 API 以如同 HTTPWebhook 觸發程序的方式運作       
    每種觸發程序類型都會有一組不同的行為定義**輸入**。  
  
## <a name="request-trigger"></a>要求觸發程序  

此觸發程序可做為端點，供您透過 HTTP 要求進行呼叫，以叫用邏輯應用程式。 要求觸發程序看起來就像下面這個範例︰  
  
```json
"<name-of-the-trigger>" : {
    "type" : "request",
    "kind": "http",
    "inputs" : {
        "schema" : {
            "properties" : {
                "myInputProperty1" : { "type" : "string" },
                "myInputProperty2" : { "type" : "number" }
            },
        "required" : [ "myInputProperty1" ],
        "type" : "object"
        }
    }
} 
```

此外，還有一個稱為**結構描述**的選擇性屬性：  
  
|元素名稱|必要|說明|  
|----------------|------------|---------------|  
|結構描述|否|會驗證連入要求的 JSON 結構描述。 適用於協助後續工作流程步驟了解要參考哪些屬性。|

若要叫用此端點，您必須呼叫 listCallbackUrl API。 請參閱[工作流程服務 REST API](https://docs.microsoft.com/rest/api/logic/workflows)。  
  
## <a name="recurrence-trigger"></a>循環觸發程序  

循環觸發程序是會根據定義的排程來執行的觸發程序。 這類觸發程序看起來可能就像下面這個範例︰  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

如您所見，這是很簡單的工作流程執行方式。  
  
|元素名稱|必要|說明|  
|----------------|------------|---------------|  
|frequency|是|觸發程序的執行頻率。 只能使用下列其中一個可能值︰秒、分鐘、小時、天、週、月或年|  
|interval|是|給定循環頻率的間隔|  
|startTime|否|如果提供 startTime 時未指定 UTC 時差，則會使用此 timeZone。|  
|timeZone|no|如果提供 startTime 時未指定 UTC 時差，則會使用此 timeZone。|  
  
您也可以將觸發程序排程在未來某個時間點開始執行。 例如，如果您想要在每週一啟動每週報告，則可以藉由建立下列觸發程序將邏輯應用程式排程在每週一啟動︰  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2015-06-22T00:00:00Z"
    }
}
```

## <a name="http-trigger"></a>HTTP 觸發程序  

HTTP 觸發程序會輪詢指定端點，然後檢查回應以判斷是否應執行工作流程。 輸入物件會採用一組建構 HTTP 呼叫時所需的參數︰  
  
|元素名稱|必要|說明|類型|  
|----------------|------------|---------------|--------|  
|method|yes|可以是下列其中一種 HTTP 方法︰GET、POST、PUT、DELETE、PATCH 或 HEAD|String|  
|uri|yes|所呼叫的 http 或 https 端點。 最大值為 2 KB。|String|  
|查詢|否|物件，代表要新增至 URL 的查詢參數。 例如，`"queries" : { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。|Object|  
|headers|否|物件，代表傳送至要求的每個標頭。 例如，若要對要求設定語言和類型︰`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|Object|  
|body|否|物件，代表傳送至端點的承載。|Object|  
|RetryPolicy|否|物件，可讓您自訂 4xx 或 5xx 錯誤的重試行為。|Object|  
|驗證|否|代表應該用來驗證要求的方法。 如需此物件的詳細資訊，請參閱[排程器輸出驗證](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)。 除了排程器外，還有一個支援的屬性︰`authority`。根據預設，在未指定時這個值會是 `https://login.windows.net`，但您可以使用不同的受眾，例如 `https://login.windows\-ppe.net`|Object|  
  
HTTP 觸發程序要求 HTTP API 必須符合特定模式，才能與邏輯應用程式良好搭配運作。 它會辨識下列屬性：  
  
|Response|必要|說明|  
|------------|------------|---------------|  
|狀態碼|是|狀態碼 200 \(確定\) 會導致執行。 其他任何狀態碼則不會導致執行。|  
|Retry\-after 標頭|否|邏輯應用程式再次輪詢端點前所需經過的秒數。|  
|位置標頭|否|在下一個輪詢間隔時所要呼叫的 URL。 如果未指定，則會使用原本的 URL。|  
  
以下是不同要求類型所具有之不同行為的一些範例︰  
  
|Response code|Retry\-After|行為|  
|-----------------|----------------|------------|  
|200|無\(\)|非有效觸發程序，必須有 Retry\-After，否則引擎永遠不會輪詢下一個要求。|  
|202|60|不會觸發工作流程。 會在一分鐘內開始下一次嘗試。|  
|200|10|執行工作流程，並在 10 秒後再次檢查是否有其他內容。|  
|400|無\(\)|不正確的要求，不會執行工作流程。 如果未定義任何**重試原則**，則會使用預設原則。 在達到重試次數後，觸發程序就會失效。|  
|500|無\(\)|伺服器錯誤，不會執行工作流程。  如果未定義任何**重試原則**，則會使用預設原則。 在達到重試次數後，觸發程序就會失效。|  
  
HTTP 觸發程序的輸出看起來就像下面這個範例︰  
  
|元素名稱|說明|類型|  
|----------------|---------------|--------|  
|headers|http 回應的標頭。|Object|  
|body|http 回應的主體。|Object|  
  
## <a name="api-connection-trigger"></a>API 連線觸發程序  

API 連線觸發程序和 HTTP 觸發程序的相似之處在於其基本功能。 然而，用於識別動作的參數卻不相同。 下列是一個範例：  
  
```json
"dailyReport" : {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            },
        }
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

|元素名稱|必要|類型|說明|  
|----------------|------------|--------|---------------|  
|主機|是||ApiApp 所裝載的閘道和識別碼。|  
|method|是|String|可以是下列其中一種 HTTP 方法︰**GET**、**POST**、**PUT**、**DELETE**、**PATCH** 或 **HEAD**|  
|查詢|否|Object|代表要新增至 URL 的查詢參數。 例如，`"queries" : { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。|  
|headers|否|Object|代表傳送至要求的每個標頭。 例如，若要對要求設定語言和類型︰`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|否|Object|代表傳送至端點的承載。|  
|RetryPolicy|否|Object|可讓您自訂 4xx 或 5xx 錯誤的重試行為。|  
|驗證|否|Object|代表應該用來驗證要求的方法。 如需此物件的詳細資訊，請參閱[排程器輸出驗證](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)|  
  
主機的屬性如下︰  
  
|元素名稱|必要|說明|  
|----------------|------------|---------------|  
|api runtimeUrl|是|Managed API 的端點。|  
|連線名稱||必須是名為 `$connection` 之參數的參考，並且是工作流程所使用之 Managed API 連線的名稱。|
  
API 連線觸發程序的輸出如下︰
  
|元素名稱|類型|說明|  
|----------------|--------|---------------|  
|headers|Object|http 回應的標頭。|  
|body|Object|http 回應的主體。|  
  
## <a name="httpwebhook-trigger"></a>HTTPWebhook 觸發程序  

HTTPWebhook 觸發程序會如同手動觸發程序一般地開啟端點，不過，HTTPWebhook 觸發程序還會對指定的 URL 進行呼叫以便註冊和取消註冊。 HTTPWebhook 觸發程序看起來可能就像下面這個範例︰  

```json
"myappspottrigger": {
    "type": "httpWebhook",
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

這些區段之中有許多是選擇性的，而且 Webhook 的行為取決於提供或省略了哪些區段。  
Webhook 的屬性如下所示︰  
  
|元素名稱|必要|說明|  
|----------------|------------|---------------|  
|訂閱|否|在觸發程序建立並執行初始註冊時，所呼叫的連出要求。|  
|取消訂閱|否|刪除觸發程序時的連出要求。|  
  
-   **訂閱**是為了開始接聽事件所產生的連出呼叫。 此呼叫會使用一般 HTTP 動作所使用的同一組參數來啟動。 工作流程在任何時候以任何方式進行變更 (例如，每當認證輪替時)，或觸發程序的輸入參數變更時，就會產生連出呼叫。
  
    為了支援這個呼叫，所以有了一個新函式︰`@listCallbackUrl()`。 這個函式會針對此工作流程中的這個特定觸發程序傳回唯一 URL。 它代表使用服務 REST 之端點的唯一識別碼。  
  
-   **取消訂閱**受到呼叫的時機是當作業將此觸發程序轉譯為無效時，包括︰  
  
    -   刪除或停用觸發程序  
  
    -   刪除或停用工作流程  
  
    -   刪除或停用訂用帳戶  
  
    邏輯應用程式會自動呼叫取消訂閱動作。 此函式的參數和 HTTP 觸發程序的相同。  
  
    HTTPWebhook 觸發程序的輸出是連入要求的內容︰  
  
|元素名稱|類型|說明|  
|-----------------|--------|---------------|  
|headers|Object|http 要求的標頭。|  
|body|Object|http 要求的主體。|  

對於 Webhook 動作的限制可透過和 [HTTP 非同步限制](#asynchronous-limits)相同的方式來指定。
  

## <a name="conditions"></a>條件  

對於任何觸發程序，您都可以使用一或多個條件來判斷是否應該執行工作流程。 例如：  

```json
"dailyReport" : {
    "type": "recurrence",
    "conditions": [ {
        "expression": "@parameters('sendReports')"
    } ],
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

在此案例中，報告只會在工作流程的 `sendReports` 參數設定為 true 時觸發。 最後，條件可能會參考觸發程序的狀態碼。 例如，您只能在網站傳回狀態碼 500 時啟動工作流程，如下所示︰
  
```  
"conditions": [  
        {  
          "expression": "@equals(triggers().code, 'InternalServerError')"  
        }  
      ]  
```  
  
> [!NOTE]  
> 若有任何運算式 \(以任何方式\) 參考觸發程序的狀態碼，預設行為 \(只在 200 \(確定\) 時觸發\) 會遭到取代。 例如，如果您想要同時對狀態碼 200 和狀態碼 201 觸發，則需要納入 `@or(equals(triggers().code, 200),equals(triggers().code,201))` 作為條件。  
  
## <a name="start-multiple-runs-for-a-request"></a>為要求啟動多個執行

若要為單一要求啟動多個執行，`splitOn` 會適用於，例如，當您想要輪詢的端點可在輪詢間隔之間具有多個新項目時。
  
透過 `splitOn`，您可在包含項目陣列 (您想要使用其中各項來啟動觸發程序的執行) 的回應承載內指定屬性。 例如，假設您有一個會傳回下列回應的 API︰  
  
```json
{
    "Status" : "success",
    "Rows" : [
        {  
            "id" : 938109380,
            "name" : "mycoolrow"
        },
        {
            "id" : 938109381,
            "name" : "another row"
        }
    ]
}
```
  
您的邏輯應用程式只需要資料列內容，因此您可以如下列範例這樣建構觸發程序︰  
  
```json
"mysplitter" : {
    "type" : "http",
    "recurrence": {
        "frequency": "Minute",
        "interval": "1"
    },
    "intputs" : {
        "uri" : "https://mydomain.com/myAPI",
        "method" : "GET"
    },
    "splitOn" : "@triggerBody()?.Rows"
}
```
  
然後，在工作流程定義中，`@triggerBody().name` 會針對第一個執行傳回 `mycoolrow`，並針對第二個執行傳回 `another row`。 觸發程序的輸出看起來就像下面這個範例︰  
  
```json
{
    "body" : {
        "id" : 938109381,
        "name" : "another row"
    }
}
```

因此，如果您使用 `SplitOn`，就無法取得陣列 (在此案例中是指 `Status` 欄位) 外部的屬性。  
  
> [!NOTE]  
> 在此範例中，我們使用 `?` 運算子，以便可以在 `Rows` 屬性不存在時避免失敗。 
  
## <a name="single-run-instance"></a>單一執行的執行個體

您可以將擁有循環屬性的觸發程序，設定為只在所有作用中執行皆已完成的情況下引發。 如果在仍有進行中執行時發生了排程的循環，觸發程序將會略過並等待，直到下一個排程的循環間隔時才再次檢查。

您可以透過作業選項進行此設定︰

```json
"triggers": {
    "mytrigger": {
        "type": "http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="types-and-inputs"></a>類型和輸入  

動作有許多類型，且各有各的獨特行為。 集合動作可在自身當中包含其他許多動作。

### <a name="standard-actions"></a>標準動作  

-   **HTTP** 這個動作會呼叫 HTTP Web 端點。  
  
-   **ApiConnection** \- 這個動作的行為就像 HTTP 動作，但會使用 Microsoft 管理的 API。  
  
-   **ApiConnectionWebhook** \- 像 HTTPWebhook，但使用 Microsoft 管理的 API。  
  
-   **回應** \- 這個動作會定義連入呼叫的回應。  
  
-   **等候** \- 這個簡單的動作會等候固定時間量或直到某個特定時間。  
  
-   **工作流程** \- 這個動作代表巢狀工作流程。  

-   **函式** \- 這個動作代表 Azure Function。

### <a name="collection-actions"></a>集合動作

-   **範圍** \- 這個動作是其他動作的邏輯群組。

-   **條件** \- 這個動作會評估運算式，並執行對應的結果分支。

-   **ForEach** \- 這個迴圈動作會逐一查看陣列並對每個項目執行內部動作。

-   **直到** \- 這個迴圈動作會執行內部動作，直到有條件的結果為 true。
  
每種動作類型都有一組可定義動作行為的不同**輸入**。  
  
## <a name="http-action"></a>HTTP 動作  

HTTP 動作會呼叫指定端點，然後檢查回應以判斷是否應執行工作流程。 **輸入**物件會採用一組建構 HTTP 呼叫時所需的參數︰  
  
|元素名稱|必要|類型|說明|  
|----------------|------------|--------|---------------|  
|method|是|String|可以是下列其中一種 HTTP 方法︰**GET**、**POST**、**PUT**、**DELETE**、**PATCH** 或 **HEAD**|  
|uri|是|String|所呼叫的 http 或 https 端點。 最大長度為 2 KB。|  
|查詢|否|Object|代表要新增至 URL 的查詢參數。 例如，`"queries" : { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。|  
|headers|否|Object|代表傳送至要求的每個標頭。 例如，若要對要求設定語言和類型︰`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|否|Object|代表傳送至端點的承載。|  
|RetryPolicy|否|Object|可讓您自訂 4xx 或 5xx 錯誤的重試行為。|  
|operationsOptions|否|String|定義一組要覆寫的特殊行為。|  
|驗證|否|Object|代表應該用來驗證要求的方法。 如需此物件的詳細資訊，請參閱[排程器輸出驗證](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)。 除了排程器外，還有一個支援的屬性︰`authority`。 根據預設，在未指定時這會是 `https://login.windows.net`，但您可以使用不同的受眾，例如 `https://login.windows\-ppe.net`|  
  
HTTP 動作 \(和 API 連線\) 動作支援重試原則。 重試原則適用於間歇性失敗，其典型是 HTTP 狀態碼 408、429 與 5xx，以及任何連線例外狀況。 此原則使用所定義的 *retryPolicy* 物件來進行描述，如下所示︰
  
```json
"retryPolicy" : {
    "type": "<type-of-retry-policy>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```
  
重試間隔是以 ISO 8601 格式來指定。 此間隔的最小值 (也是預設值) 為 20 秒，最大值為 1 小時。 最大重試計數 (也是預設值) 為 4 小時。 如果未指定重試原則定義，則會使用 `fixed` 策略與預設重試計數和間隔值。 若要停用重試原則，請將其類型設定為 `None`。  
  
例如，下列動作會在有間歇性失敗時重試擷取最新消息兩次，總共會執行三次，每次嘗試之間會延遲 30 秒︰  
  
```json
"latestNews" : {
    "type": "http",
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
### <a name="asynchronous-patterns"></a>非同步模式

根據預設，所有 HTTP 型動作皆支援標準的非同步作業模式。 因此，如果遠端伺服器指出已接受處理要求，並產生 202 \(已接受\) 回應，Logic Apps 引擎會持續輪詢回應之 location 標頭中指定的 URL，直到到達終止狀態 \(非\-202 回應\)。  
  
若要停用先前所述的非同步行為，請在動作輸入中設定 `DisableAsyncPattern` 選項。 在此案例中，動作的輸出是根據伺服器所傳來的初始 202 回應。  
  
```json
"invokeLongRunningOperation" : {
    "type": "http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

#### <a name="asynchronous-limits"></a>非同步限制

非同步模式可限制在其持續時間內，也可以限制在特定時間間隔內。  如果在時間間隔經過後仍未到達終止狀態，動作的狀態會標示為 `Cancelled`，而狀態碼為 `ActionTimedOut`。  限制逾時是以 ISO 8601 格式來指定。  使用下列語法即可指定限制︰

``` json
"<action-name>": {
    "type": "workflow|webhook|http|apiconnectionwebhook|apiconnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="api-connection"></a>API 連線  

API 連線是參考 Microsoft 管理之連接器的動作。
這個動作需要有效連線的參考，以及關於所需 API 及參數的資訊。

|元素名稱|必要|類型|說明|  
|----------------|------------|--------|---------------|  
|主機|是|Object|代表連接器資訊，例如 runtimeUrl 和連線物件的參考|
|method|是|String|可以是下列其中一種 HTTP 方法︰**GET**、**POST**、**PUT**、**DELETE**、**PATCH** 或 **HEAD**|  
|路徑|是|String|API 作業的路徑。|  
|查詢|否|Object|代表要新增至 URL 的查詢參數。 例如，`"queries" : { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。|  
|headers|否|Object|代表傳送至要求的每個標頭。 例如，若要對要求設定語言和類型︰`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|否|Object|代表傳送至端點的承載。|  
|RetryPolicy|否|Object|可讓您自訂 4xx 或 5xx 錯誤的重試行為。|  
|operationsOptions|否|String|定義一組要覆寫的特殊行為。|  

```json
"Send_Email": {
    "type": "apiconnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "post",
        "body": {
            "Subject": "New Tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
    }
```

## <a name="api-connection-webhook-action"></a>API 連線 Webhook 動作

```json
"Send_approval_email": {
    "type": "apiconnectionwebhook",
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

對於 Webhook 動作的限制可透過和 [HTTP 非同步限制](#asynchronous-limits)相同的方式來指定。
  
## <a name="response-action"></a>回應動作  

這個動作類型包含來自 HTTP 要求的整個回應承載，並納入 statusCode、主體和標頭︰  
  
```json
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "runAfter": {}
}
```
  
回應動作有其他動作所不適用的特殊限制。 具體而言：  
  
-   定義中不能有平行的回應動作，因為連入要求必須有確定性回應。  
  
-   如果連入要求在收到回應後觸達回應動作，系統會將動作視為失敗 \(衝突\)，因此，執行會是 `Failed`。  
  
-   具有回應動作的工作流程在其觸發程序中不能有 `splitOn`，因為一個呼叫就會導致許多個執行。 因此，當流程是 PUT 且造成不正確的要求時，便應就此進行驗證。  
  
## <a name="wait-action"></a>等候動作  

`wait` 動作會在一段指定間隔內暫停工作流程的執行。 例如，若要等候 15 分鐘，您可以使用此程式碼片段︰  
  
```json
"waitForFifteenMinutes" : {
    "type": "wait",
    "inputs": {
        "interval": {
            "unit" : "minute",
            "count" : 15
        }
    }
}
```  
  
或者，若要等候直到特定時間點，您可以使用此範例︰  
  
```json
"waitUntilOctober" : {
    "type": "wait",
    "inputs": {
        "until": {
            "timestamp" : "2016-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> 等候持續時間可使用**間隔**物件或**直到**物件 (但不可同時使用兩者) 來指定。  
  
|名稱|必要|類型|說明|  
|--------|------------|--------|---------------|  
|interval|否|Object|以時間量為基礎的等候持續時間。|  
|間隔單位|是|String|下列其中一個間隔︰秒、分鐘、小時、天、週、月、年。|  
|間隔計數|是|String|以指定內部單位為基礎的持續時間。|  
|直到|否|Object|以時間點為基礎的等候持續時間。|  
|直到時間戳記|是|String|字串 | 等候到期時的 UTC 時間點。|  

## <a name="query-action"></a>查詢動作

`query` 動作可讓您根據條件來篩選陣列。 例如，若要選取大於 2 的數字，您可以使用︰

```json
"FilterNumbers" : {
    "type": "query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

`query` 動作的輸出是一個陣列，其中具有輸入陣列中符合條件的元素。

> [!NOTE]
> 如果沒有任何值符合 `where` 條件，則結果為空白陣列。

|名稱|必要|類型|說明|
|--------|------------|--------|---------------|
|from|是|陣列|來源陣列。|
|其中|是|String|要套用到來源陣列各個元素的條件。|

## <a name="select-action"></a>選取動作

`select` 動作可讓您將陣列的每個元素預測為新的值。
例如，若要將數字的陣列轉換為物件的陣列，您可以使用︰

```json
"SelectNumbers" : {
    "type": "select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

`select` 動作的輸出是基數為與輸入陣列相同的陣列，其中每個轉換的元素均由 `select` 屬性定義。 如果輸入是空的陣列，則輸出也是空的陣列。

|名稱|必要|類型|說明|
|--------|------------|--------|---------------|
|from|是|陣列|來源陣列。|
|選取|是|任意|要套用到來源陣列各個元素的預測。|

## <a name="terminate-action"></a>終止動作

終止動作會停止執行工作流程、中止任何進行中的動作，並略過任何剩餘的動作。 例如，若要終止狀態為**失敗**的執行，您可以使用下列程式碼片段︰

```json
"HandleUnexpectedResponse" : {
    "type": "terminate",
    "inputs": {
        "runStatus" : "failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response.",
        }
    }
}
```

> [!NOTE]
> 已完成的動作不受終止動作所影響。

|名稱|必要|類型|說明|
|--------|------------|--------|---------------|
|runStatus|是|String|目標執行狀態。 **失敗**或**取消**。|
|runError|否|Object|錯誤詳細資料。 在 **runStatus** 設為**失敗**時才支援。|
|runError 代碼|否|String|執行錯誤碼。|
|runError 訊息|否|String|執行錯誤訊息。|

## <a name="compose-action"></a>撰寫動作

撰寫動作可讓您建構任意物件。 撰寫動作的輸出是其輸入的評估結果。 例如，您可以使用撰寫動作合併多個動作的輸出︰

```json
"composeUserRecord" : {
    "type": "compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
        }
    }
}
```

> [!NOTE]
> **撰寫**動作可用來建構任何輸出，包括物件、陣列以及邏輯應用程式原生支援的其他任何類型，例如 XML 和二進位檔。

## <a name="table-action"></a>資料表動作

`table` 可讓您將項目的陣列轉換為 **CSV** 或 **HTML** 資料表。

假設 @triggerBody() 是

```json
[{
  "id": 0,
  "name": "apples"
},{
  "id": 1, 
  "name": "oranges"
}]
```

並且讓動作定義為

```json
"ConvertToTable" : {
    "type": "table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html"
    }
}
```

以上會產生

<table><thead><tr><th>id</th><th>名稱</th></tr></thead><tbody><tr><td>0</td><td>apples</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>"

為了自訂資料表，您可以明確指定資料行。 例如：

```json
"ConvertToTable" : {
    "type": "table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [{
          "header": "produce id",
          "value": "@item().id"
        },{
          "header": "description",
          "value": "@concat('fresh ', item().name)"
        }]
    }
}
```

以上會產生

<table><thead><tr><th>產生識別碼</th><th>說明</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>"

如果 `from` 屬性值為空的陣列，則輸出為空的資料表。

|名稱|必要|類型|說明|
|--------|------------|--------|---------------|
|from|是|陣列|來源陣列。|
|format|是|String|格式，**CSV** 或 **HTML**。|
|columns|否|陣列|資料行。 允許覆寫資料表的預設圖形。|
|資料行標頭|否|String|資料行的標頭。|
|資料行值|是|String|資料行的值。|

## <a name="workflow-action"></a>工作流程動作   

|名稱|必要|類型|說明|  
|--------|------------|--------|---------------|  
|主機識別碼|是|String|您想要呼叫之工作流程的資源識別碼。|  
|主機 triggerName|是|String|您想要叫用之觸發程序的名稱。|  
|查詢|否|Object|代表要新增至 URL 的查詢參數。 例如，`"queries" : { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。|  
|headers|否|Object|代表傳送至要求的每個標頭。 例如，若要對要求設定語言和類型︰`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|否|Object|代表傳送至端點的承載。|  
  
```json
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName " : "mytrigger001"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
    }
```
  
系統會檢查工作流程 \(更具體地說是觸發程序\) 的存取權，也就是說，您需要工作流程的存取權。  
  
`workflow` 動作的輸出是根據您在子工作流程之 `response` 動作中所做的定義。 如果您尚未定義任何 `response` 動作，輸出會是空的。  

## <a name="function-action"></a>函式動作   

|名稱|必要|類型|說明|  
|--------|------------|--------|---------------|  
|函式識別碼|是|String|您想要叫用之函式的資源識別碼。|  
|method|否|String|用來叫用函式的 HTTP 方法。 若未指定，則其預設值為 `POST`。|  
|查詢|否|Object|代表要新增至 URL 的查詢參數。 例如，`"queries" : { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。|  
|headers|否|Object|代表傳送至要求的每個標頭。 例如，若要對要求設定語言和類型︰`"headers" : { "Accept-Language": "en-us" }`。|  
|body|否|Object|代表傳送至端點的承載。|  

```json
"myfunc" : {
    "type" : "Function",
    "inputs" : {
        "function" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Web/sites/myfuncapp/functions/myfunc"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()"
        },
        "method" : "POST",
    "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
}
```

當您儲存邏輯應用程式時，我們會對所參考的函式執行某些檢查︰
-   您必須具有該函式的存取權。
-   僅允許使用標準 HTTP 觸發程序或一般 JSON Webhook 觸發程序。
-   它不應定義任何路由。
-   只允許使用「函式」和「匿名」授權層級。

系統會在執行階段擷取、快取及使用觸發程序 URL。 因此，若有任何作業讓快取的 URL 失效，動作就會在執行階段失敗。 若要解決這個問題，請再次儲存邏輯應用程式，這會讓邏輯應用程式再次擷取及快取觸發程序 URL。

## <a name="collection-actions-scopes-and-loops"></a>集合動作 (範圍和迴圈)

某些動作類型可以在自身當中包含動作。 集合內的參考動作可直接在集合外部參考。 如果您在某個範圍中定義了 `http`，`@body('http')` 仍會在工作流程中的任何位置保持有效。 集合內的動作只能對相同集合內的其他動作執行 `runAfter`。

## <a name="scope-action"></a>範圍動作

`scope` 動作可讓您以邏輯方式將工作流程中的動作群組起來。

|名稱|必要|類型|說明|  
|--------|------------|--------|---------------|  
|動作|是|Object|要在範圍內執行的內部動作|

```json
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

## <a name="foreach-action"></a>ForEach 動作

這個迴圈動作會逐一查看陣列並對每個項目執行內部動作。 根據預設，foreach 迴圈會以平行方式執行 (一次 20 個平行執行)。 您可以使用 `operationOptions` 參數設定執行規則。

|名稱|必要|類型|說明|  
|--------|------------|--------|---------------|  
|動作|是|Object|要在迴圈內執行的內部動作|
|foreach|是|字串|要逐一查看的陣列|
|operationOptions|no|string|行為的任何作業選項。 目前僅支援 `sequential` 來循序執行反覆運算 (預設行為是平行)|

```json
"forEach_email": {
    "type": "foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter":{
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>直到動作

這個迴圈動作會執行內部動作，直到有條件的結果為 true。

|名稱|必要|類型|說明|  
|--------|------------|--------|---------------|  
|動作|是|Object|要在迴圈內執行的內部動作|
|expression|是|string|要在每次反覆運算之後評估的運算式|
|limit|yes|Object|迴圈的限制 - 必須定義至少一項限制|
|計數|no|int|可執行之反覆運算次數的限制|
|timeout|no|字串|此動作應執行迴圈多久的逾時值。  ISO 8601 格式|


```json
 "Until_succeeded": {
    "actions": {
        "Http": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {},
    "type": "Until"
}
```

## <a name="conditions---if-action"></a>條件 - If 動作

`If` 動作可讓您評估條件，並根據運算式是否評估為 `true` 來執行分支。

|名稱|必要|類型|說明|  
|--------|------------|--------|---------------|  
|動作|是|Object|運算式評估為 `true` 時要執行的內部動作|
|expression|是|字串|要評估的運算式|
|else|no|Object|運算式評估為 `false` 時要執行的內部動作|
  
```json
"My_condition": {
    "actions": {
        "If_true": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {},
                "type": "Http"
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {},
    "type": "If"
}
```  
  
下表說明條件如何在動作中使用運算式的範例︰  
  
|JSON 值|結果|  
|--------------|----------|  
|`"expression": "@parameters('hasSpecialAction')"`|會評估為 true 的任何值都會導致此條件成立。 僅支援布林運算式。 若要將其他類型轉換成布林值，請使用函式 `empty`、`equals`。|  
|`"expression": "@greater(actions('act1').output.value, parameters('threshold'))"`|支援比較函式。 對於這裡的範例，只有當 act1 的輸出大於閾值時，才會執行動作。|  
|`"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"`|邏輯函式也支援建立巢狀布林運算式。 在此案例中，當 act1 的輸出高於閾值或低於 100 時，便會執行動作。|  
|`"expression": "@equals(length(actions('act1').outputs.errors), 0))"`|您可以使用陣列函式來檢查陣列是否有任何項目。 在此案例中，當錯誤陣列空白時，便會執行動作。| 
|`"expression": "parameters('hasSpecialAction')"`|錯誤 - 不是有效條件，因為條件必須有 @。|  
  
如果條件評估成功，條件會標示為 `Succeeded`。 `actions` 或 `else` 物件內的動作在執行並成功後會評估為 `Succeeded`，執行但失敗後會評估為 `Failed`，當該分支未執行時會評估為 `Skipped`。

## <a name="next-steps"></a>後續步驟

[工作流程服務 REST API](https://docs.microsoft.com/rest/api/logic/workflows)

