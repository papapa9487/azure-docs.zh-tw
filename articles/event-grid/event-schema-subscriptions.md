---
title: "Azure 事件格線訂用帳戶的事件結構描述"
description: "描述 Azure 事件格線針對訂用帳戶事件所提供的屬性"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 7dc10d0cc73960fac4759a0cebec8d294cf1b463
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>針對訂用帳戶的 Azure 事件格線事件結構描述

本文提供針對 Azure 訂用帳戶事件的屬性和結構描述。 如需事件結構描述的簡介，請參閱 [Azure 事件格線事件結構描述](event-schema.md)。

Azure 訂用帳戶和資源群組會發出相同的事件類型。 事件類型與資源中的變更相關聯。 主要的差異在於資源群組所發出的事件是針對資源群組內的資源，而 Azure 訂用帳戶發出的事件是針對整個訂用帳戶上的資源。

## <a name="available-event-types"></a>可用的事件類型

Azure 訂用帳戶會從 Azure Resource Manager 發出管理事件，像是建立 VM 或刪除儲存體帳戶。

| 事件類型 | 說明 |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | 在資源建立或是更新作業成功時引發。 |
| Microsoft.Resources.ResourceWriteFailure | 在資源建立或是更新作業失敗時引發。 |
| Microsoft.Resources.ResourceWriteCancel | 在資源建立或是更新作業取消時引發。 |
| Microsoft.Resources.ResourceDeleteSuccess | 在資源刪除作業成功時引發。 |
| Microsoft.Resources.ResourceDeleteFailure | 在資源刪除作業失敗時引發。 |
| Microsoft.Resources.ResourceDeleteCancel | 在資源刪除作業取消時引發。 此事件會於範本部署取消時發生。 |

## <a name="example-event"></a>事件範例

下列範例顯示資源已建立事件的結構描述： 

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```

資源已刪除事件的結構描述也相當類似：

```json
[{
  "topic":"/subscriptions/{subscription-id}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  }
}]
```

## <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 |
| 主旨 | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 | 訂用帳戶事件資料。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| 授權 | 字串 | 作業的所要求授權。 |
| claims | 字串 | 宣告的屬性。 |
| correlationId | 字串 | 用於疑難排解的作業識別碼。 |
| httpRequest | 字串 | 作業的詳細資料。 |
| resourceProvider | 字串 | 執行作業的資源提供者。 |
| resourceUri | 字串 | 作業中資源的 URI。 |
| operationName | 字串 | 已執行的作業。 |
| status | 字串 | 作業狀態。 |
| subscriptionId | 字串 | 資源的訂用帳戶識別碼。 |
| tenantId | 字串 | 資源的租用戶識別碼。 |

## <a name="next-steps"></a>後續步驟

* 若要初步了解 Azure Event Grid，請參閱[什麼是 Event Grid？](overview.md)。
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
