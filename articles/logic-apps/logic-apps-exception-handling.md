---
title: "錯誤和例外狀況處理 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps 中的錯誤和例外狀況處理模式"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 4eb6f743479886374692eadcf218b77b4bfcc933
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2017
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>處理 Azure Logic Apps 中的錯誤和例外狀況

Azure Logic Apps 提供了豐富的工具和模式，以協助確保您的整合穩固且具有彈性，不怕故障。 任何整合架構都會帶來挑戰，讓您難以確定是否可適當地處理相依系統的停機或問題。 Logic Apps 讓處理錯誤成為一種頂級享受，賦予您所需工具來處理工作流程內的例外狀況和錯誤。

## <a name="retry-policies"></a>重試原則

重試原則是最基本的例外狀況和錯誤處理類型。 如果初始要求逾時或失敗 (任何造成 429 或 5xx 回應的要求)，此原則會定義是否及如何重試動作。 有三種類型的重試原則，`exponential`、`fixed` 和 `none`。 如果工作流程定義中未提供重試原則，就會使用預設原則。 您可以在特定動作或觸發程序 (如果可重試) 的**輸入**中設定重試原則。 同樣地，在邏輯應用程式設計工具中，可以設定給定區塊之 [設定] 之下的重試原則 (如果適用)。

如需重試原則限制的詳細資訊，請參閱 [Logic Apps 限制與設定](../logic-apps/logic-apps-limits-and-config.md)，如需有關支援語法的詳細資訊，請參閱[「工作流程動作與觸發程序」中的重試原則小節][retryPolicyMSDN]。

### <a name="exponential-interval"></a>指數間隔
在以指數方式成長範圍內的隨機時間間隔之後，`exponential` 原則類型會重試失敗的要求。 每次重試都保證在大於 **minimumInterval** 和小於 **maximumInterval** 的隨機間隔中傳送。 每次重試都會產生下方範圍中的統一隨機變數，最多且含**計數**：
<table>
<tr><th> 隨機變數範圍 </th></tr>
<tr><td>

| 重試數目 | 最小間隔 | 最大間隔 |
| ------------ |  ------------ |  ------------ |
| 1 | 最大值 (0，**minimumInterval**) | 最小值 (時間間隔，**maximumInterval**) |
| 2 | 最大值 (時間間隔，**minimumInterval**) | 最小值 (2 * 間隔，**maximumInterval**) |
| 3 | 最大值 (2 * 間隔，**minimumInterval**) | 最小值 (4 * 間隔，**maximumInterval**) |
| 4 | 最大 (4 * 間隔，**minimumInterval**) | Min (8 * 間隔，**maximumInterval**) |
| ... |

</td></tr></table>

針對 `exponential` 輸入原則，需要**計數**和**間隔**，而 **minimumInterval** 和 **maximumInterval** 則可以選擇性地提供，以分別覆寫 PT5S 和 PT1D 的預設值。

| 元素名稱 | 必要 | 類型 | 說明 |
| ------------ | -------- | ---- | ----------- |
| 類型 | 是 | String | `exponential` |
| 計數 | 是 | Integer | 重試次數必須介於 1 到 90 之間  |
| interval | 是 | String | 重試間隔為 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，必須在 PT5S 與 PT1D 之間 |
| minimumInterval | 否| String | 重試最小間隔為 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，必須介於 PT5S 與**間隔**之間 |
| maximumInterval | 否| String | 重試最小間隔為 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，必須介於**間隔**與 PT1D 之間 |

### <a name="fixed-interval"></a>固定間隔

`fixed` 原則類型在傳送下一個要求之前，會等候所提供的時間間隔，以重試失敗的要求。

| 元素名稱 | 必要 | 類型 | 說明 |
| ------------ | -------- | ---- | ----------- |
| 類型 | 是 | String | `fixed`|
| 計數 | 是 | Integer | 重試次數必須介於 1 到 90 之間 |
| interval | 是 | String | 重試間隔為 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，必須在 PT5S 與 PT1D 之間 |

### <a name="none"></a>None
`none` 原則類型將不會重試失敗的要求。

| 元素名稱 | 必要 | 類型 | 說明 |
| ------------ | -------- | ---- | ----------- |
| 類型 | 是 | String | `none`|

### <a name="default"></a>預設值
如果未指定任何重試原則，則會使用預設原則。 預設原則是指數的間隔原則，會傳送最多 4 次重試，以指數方式依 7.5 秒調整增加間隔，且上限為 5 到 45 秒之間。 這項預設原則 (未定義 **retryPolicy** 時使用) 就相當於此範例 HTTP 工作流程定義中的原則：

```json
"HTTP":
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

## <a name="catch-failures-with-the-runafter-property"></a>使用 RunAfter 屬性來擷取失敗

每個邏輯應用程式動作都會宣告要讓此動作啟動必須先完成哪些動作，就像是將工作流程中的步驟排序。 在動作定義中，此排序稱為 `runAfter` 屬性。 此屬性是會說明哪些動作和動作狀態會執行此動作的物件。 根據預設，所有透過邏輯應用程式設計工具新增的動作都會設定為如果上一個步驟 `Succeeded`，則在上一個步驟之後執行 (`runAfter`)。 不過，您可以自訂此值，以在先前的動作是 `Failed`、`Skipped` 或這兩個值的可能組合時引發動作。 如果您想要在特定動作 `Insert_Row` 失敗之後於指定的服務匯流排主題新增項目，您可以使用下列 `runAfter` 組態︰

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

請注意，`runAfter` 屬性會設定為在 `Insert_Row` 動作是 `Failed` 時引發。 若要在動作狀態是 `Succeeded`、`Failed` 或 `Skipped` 時執行動作，請使用此語法︰

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 前一個動作失敗之後所執行並順利完成的動作會標示為 `Succeeded`。 此行為表示如果您成功擷取到工作流程中的所有失敗，該次執行本身會標示為 `Succeeded`。

## <a name="scopes-and-results-to-evaluate-actions"></a>用以評估動作的範圍和結果

和您可以在個別動作之後進行的執行方式類似，您也可以將多個動作群組到某個[範圍](../logic-apps/logic-apps-loops-and-scopes.md)內，其可做為這些動作的邏輯群組。 範圍適合用來組織邏輯應用程式動作，以及用來對某一範圍的狀態執行彙總評估。 範圍本身會在範圍中的所有動作都已完成之後收到狀態。 範圍狀態會由執行時的相同條件來決定。 如果執行分支中的最後一個動作是 `Failed` 或 `Aborted`，狀態會是 `Failed`。

若要針對範圍內發生的任何失敗引發特定動作，您可以搭配標示為 `Failed` 的範圍使用 `runAfter`。 若範圍內的「任何」動作失敗，在範圍失敗之後執行可讓您建立一項動作以擷取失敗。

### <a name="getting-the-context-of-failures-with-results"></a>透過結果取得失敗的內容

雖然從範圍擷取失敗很實用，但您可能也想要取得內容以協助您了解實際上有哪些動作失敗，以及所傳回的任何錯誤或狀態碼。 `@result()` 工作流程函式會提供有關範圍內所有動作結果的內容。

`@result()` 採用單一參數、範圍名稱，並且會傳回該範圍內產生之所有動作的陣列。 這些動作物件包含和 `@actions()` 物件相同的屬性，包括動作開始時間、動作結束時間、動作狀態、動作輸入、動作相互關聯識別碼和動作輸出。 若要傳送在範圍內失敗之任何動作的內容，您可以輕易地配對 `@result()` 函式與 `runAfter`。

若要對範圍中 `Failed` 的 For each 動作執行動作，篩選失敗動作的結果陣列，您可以配對 `@result()` 與**[篩選陣列](../connectors/connectors-native-query.md)**動作和 **[ForEach](../logic-apps/logic-apps-loops-and-scopes.md)** 迴圈。 您可以取得篩選後的結果陣列，並使用 **ForEach** 迴圈對每個失敗執行動作。 以下範例 (隨後並有詳細說明) 會傳送 HTTP POST 要求，其中含有任何在範圍 `My_Scope` 內失敗之動作的回應本文。

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

以下是說明整個情形的詳細逐步解說︰

1. 若要取得 `My_Scope` 內所有動作的結果，**篩選陣列**動作會篩選 `@result('My_Scope')`。

2. **篩選陣列**的條件是狀態等於 `Failed` 的任何 `@result()` 項目。 此條件會將具有 `My_Scope` 之所有動作結果的陣列，篩選為只剩失敗動作結果的陣列。

3. 對**篩選後陣列**的輸出執行 **For Each** 動作。 此步驟會對之前篩選的每個失敗動作結果執行動作。

    如果範圍中的單一動作失敗，`foreach` 中的動作只會執行一次。 
    許多失敗動作會對每個失敗造成一個動作。

4. 在 `foreach` 項目的回應本文上傳送 HTTP POST，或傳送 `@item()['outputs']['body']`。 `@result()` 項目圖形和 `@actions()` 圖形相同，並可透過相同方式剖析。

5. 包含兩個具有失敗動作名稱 `@item()['name']` 和失敗執行用戶端追蹤識別碼 `@item()['clientTrackingId']` 的自訂標頭。

供您參考，以下範例是單一 `@result()` 項目，會顯示前一個範例中剖析的 `name`、`body` 和 `clientTrackingId` 屬性。 在 `foreach` 之外，`@result()` 會傳回這些物件的陣列。

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

若要執行不同的例外狀況處理模式，您可以使用上述運算式。 您可以選擇在範圍之外執行單一的例外狀況處理動作，以接受整個篩選後的失敗陣列並移除 `foreach`。 您也可以包含上述 `@result()` 回應中的其他有用屬性。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 診斷和遙測

上述模式非常適合處理執行內的錯誤和例外狀況，但您也可以識別和回應與執行本身無關的錯誤。 
[Azure 診斷](../logic-apps/logic-apps-monitor-your-logic-apps.md) 提供了簡單的方法讓您將所有工作流程事件 (包括所有執行和動作狀態) 傳送至 Azure 儲存體帳戶或 Azure 事件中樞。 若要評估執行狀態，您可以監視記錄和度量，或將它們發佈至您偏好使用的任何監視工具。 其中一個可能的選項是透過 Azure 事件中樞將所有事件串流到 [串流分析](https://azure.microsoft.com/services/stream-analytics/)。 在串流分析中，您可以將診斷記錄中任何異常、平均或失敗的即時查詢取消。 串流分析可以輕鬆地輸出至其他資料來源，例如佇列、主題、SQL、Azure Cosmos DB 和 Power BI。

## <a name="next-steps"></a>後續步驟

* [看看客戶如何使用 Azure Logic Apps 建置錯誤處理](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [尋找其他 Logic Apps 範例和案例](../logic-apps/logic-apps-examples-and-scenarios.md)
* [了解如何建立邏輯應用程式的自動化部署](../logic-apps/logic-apps-create-deploy-template.md)
* [使用 Visual Studio 建置和部署邏輯應用程式](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
