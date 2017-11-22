---
title: "Azure Event Grid 事件結構描述"
description: "描述 Azure Event Grid 中事件提供的屬性"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: babanisa
ms.openlocfilehash: caa709fdc2a59472ee812bde91f7300396aa5755
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid 事件結構描述

本文說明出現在所有事件中的屬性和結構描述。 事件包含一組五個必要字串屬性和一個必要資料物件。 這些屬性通用於任何發行者的所有事件。 資料物件含有各發行者特有的屬性。 系統主題下的屬性專屬於資源提供者，像是 Microsoft Azure 儲存體或 Azure 事件中樞。

事件會以陣列型態傳送至 Azure Event Grid，陣列中可包含多個事件物件。 如果陣列中只有一個事件，則陣列長度為 1。 陣列可以具有的最多 1 MB 的總計大小。 陣列中的每個事件會限制為 64 KB。

## <a name="event-schema"></a>結構描述

下列範例顯示所有事件發行者會使用的屬性：

```json
[
  {
    "topic": string,
    "subject": string,    
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    }
  }
]
```

例如，針對 Azure Blob 儲存體事件發行的結構描述為：

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```
 
## <a name="event-properties"></a>事件屬性

所有事件皆包含下列相同的最高層級資料：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 |
| 主旨 | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 | 資源提供者特有的事件資料。 |

若要了解資料物件中的屬性，請參閱事件來源：

* [Azure 訂用帳戶 (管理作業)](event-schema-subscriptions.md)
* [Blob 儲存體](event-schema-blob-storage.md)
* [事件中樞](event-schema-event-hubs.md)
* [資源群組 (管理作業)](event-schema-resource-groups.md)

針對自訂主題，事件發行者會決定資料物件。 最高層級的資料應包含與標準資源定義事件相同的欄位。 在發佈事件至自訂主題時，您應考慮以幫助路由與篩選為目標建立事件的主體。

## <a name="next-steps"></a>後續步驟

* 如需 Azure 事件格線的簡介，請參閱[什麼是事件格線？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
