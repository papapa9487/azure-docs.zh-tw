---
title: "Azure 事件格線訂用帳戶的結構描述"
description: "描述訂閱具有 Azure 事件格線之事件的屬性。"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/17/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: cce058e75ebf49f53d11e3b5a87ea4625994db0f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---

# <a name="event-grid-subscription-schema"></a>事件格線訂用帳戶的結構描述

若要建立事件格線訂用帳戶，請將要求傳送至建立事件訂用帳戶作業。 請使用下列格式：

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

例如，若要在名為 `examplegroup` 的資源群組中建立命名為 `examplestorage` 的儲存體帳戶之事件訂用帳戶，請使用下列格式：

```
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

本文描述要求主體的屬性和結構描述。
 
## <a name="event-subscription-properties"></a>事件訂用帳戶屬性

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| 目的地 | 物件 | 定義端點的物件。 |
| filter | 物件 | 篩選事件類型的選擇性欄位。 |

### <a name="destination-object"></a>目的地物件

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| endpointType | 字串 | 訂用帳戶的端點類型 (webhook/HTTP、事件中樞或佇列)。 | 
| endpointUrl | 字串 |  | 

### <a name="filter-object"></a>篩選物件

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| includedEventTypes | array | 事件訊息中的事件類型完全符合這些事件類型名稱其中之一時，即會符合。 事件名稱不符合事件來源的已註冊事件類型名稱時，會引發錯誤。 預設會符合所有事件類型。 |
| subjectBeginsWith | 字串 | 事件訊息中對主體欄位的前置詞符合篩選。 預設值或空字串會符合全部。 | 
| subjectEndsWith | 字串 | 事件訊息中對主體欄位的後置詞符合篩選。 預設值或空字串會符合全部。 |
| subjectIsCaseSensitive | 字串 | 控制篩選的區分大小寫比對。 |


## <a name="example-subscription-schema"></a>範例訂用帳戶的結構描述

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "blobCreated", "blobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "subjectIsCaseSensitive": "true"
    }
  }
}
```

## <a name="next-steps"></a>後續步驟

* 若要初步了解事件格線，請參閱[什麼是事件格線？](overview.md)
