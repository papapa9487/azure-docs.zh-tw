---
title: "提供者資源使用狀況 API | Microsoft Docs"
description: "資源使用狀況 API (用以擷取 Azure Stack 使用狀況資訊) 的參考。"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: b6055923-b6a6-45f0-8979-225b713150ae
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: alfredop
ms.openlocfilehash: c54dca9d734cf909cf20d5235a90b9b46f0af11c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="provider-resource-usage-api"></a>提供者資源使用狀況 API
「提供者」一詞適用於服務管理員和任何委派的提供者。 Azure Stack 操作員和委派的提供者可使用提供者使用狀況 API，檢視其直接租用戶的使用狀況。 例如，P0 可以呼叫提供者 API，以取得 P1 和 P2 直接使用的使用狀況資訊；而 P1 可呼叫以取得 P3 和 P4 的使用狀況資訊。

![提供者階層的概念模型](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API 呼叫參考
### <a name="request"></a>要求
要求會取得所要求的訂用帳戶在要求的時間範圍內的耗用量詳細資料。 沒有要求主體。

此使用狀況 API 是提供者 API，因此必須將提供者訂用帳戶中的「擁有者」、「參與者」或「讀者」角色指派給呼叫者。

| **方法** | **要求 URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>引數
| **引數** | **說明** |
| --- | --- |
| *armendpoint* |您 Azure Stack 環境的 Azure Resource Manager 端點。 依 Azure Stack 慣例，Azure Resource Manager 端點名稱的格式是 `https://adminmanagement.{domain-name}`。 例如，如果是開發套件，則網域名稱會是 local.azurestack.external，而 Resource Manager 端點會是 `https://adminmanagement.local.azurestack.external`。 |
| *subId* |正在進行呼叫的使用者訂用帳戶 ID。 |
| *reportedStartTime* |查詢的開始時間。 *DateTime* 值應該以 UTC 格式及小時開始時的時間呈現，例如 13:00。 對於每日彙總，請將這個值設定為 UTC 午夜。 格式是*逸出的* ISO 8601，例如 2015-06-16T18%3a53%3a11%2b00%3a00Z，其中冒號會逸出為 %3a 而加號會逸出為 %2b，使其符合 URI 規範。 |
| *reportedEndTime* |查詢的結束時間。 適用於 *reportedStartTime* 的限制也適用於此引數。 *reportedEndTime* 的值不得為未來或目前的日期。 如果是，結果會設為「處理未完成。」 |
| *aggregationGranularity* |這是選擇性引數，它可以有兩個截然不同的可能值 (每日及每小時)。 如同以上兩個值所暗示，一個會每日傳回資料，另一個則會每小時傳回資料。 預設值為 [每日] 選項。 |
| *subscriberId* |訂用帳戶 ID。 若要取得篩選的資料，需要提供者的直接租用戶訂用帳戶 ID。 如果未指定訂用帳戶 ID 參數，呼叫會傳回所有提供者直接租用戶的使用狀況資料。 |
| *api-version* |用來提出此要求的通訊協定版本。 此值會設定為 2015-06-01-preview。 |
| *continuationToken* |從上次呼叫使用狀況 API 提供者所擷取的權杖。 回應大於 1000 行時就需要這個權杖，可作為進度的書籤。 若無此權杖，則會從一天或小時開始時的時間擷取資料，取決於所傳遞的細微性。 |

### <a name="response"></a>Response
GET /subscriptions/sub1/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&subscriberId=sub1.1&api-version=1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>回應詳細資料
| **引數** | **說明** |
| --- | --- |
| *id* |使用情況彙總的唯一識別碼 |
| *name* |使用情況彙總的名稱 |
| *type* |資源定義 |
| *subscriptionId* |Azure Stack 使用者訂用帳戶識別碼 |
| *usageStartTime* |此使用情況彙總所屬的使用情況貯體 UTC 開始時間 |
| *usageEndTime* |此使用情況彙總所屬的使用情況貯體 UTC 結束時間 |
| *instanceData* |執行個體詳細資料的機碼值組 (新格式)：<br> *resourceUri*：完整資源 ID，包含資源群組和執行個體名稱 <br> *location*：此服務執行所在的區域 <br> *tags*：使用者所指定的資源標記 <br> *additionalInfo*：更多關於所耗用資源的詳細資料 (例如，作業系統版本或映像類型) |
| *quantity* |此時間範圍內發生的資源取用數量 |
| *meterId* |所取用資源的唯一識別碼 ( *ResourceID*) |

## <a name="next-steps"></a>後續步驟
[租用戶資源使用狀況 API 參考](azure-stack-tenant-resource-usage-api.md)

[使用狀況相關常見問題集](azure-stack-usage-related-faq.md)

