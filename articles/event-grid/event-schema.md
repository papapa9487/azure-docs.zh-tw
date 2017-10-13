---
title: "Azure Event Grid 事件結構描述"
description: "描述 Azure Event Grid 中事件提供的屬性"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.openlocfilehash: a61357b6ba75566e0ad4d3300cc602333ece0563
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid 事件結構描述

本文提供事件之屬性與結構描述。 事件包含一組五個必要字串屬性和一個必要資料物件。 這些屬性通用於任何發行者的所有事件。 資料物件含有各發行者特有的屬性。 系統主題下的屬性專屬於資源提供者，像是 Microsoft Azure 儲存體或 Azure 事件中樞。

事件會以陣列型態傳送至 Azure Event Grid，陣列中可包含多個事件物件。 如果陣列中只有一個事件，則陣列長度為 1。 陣列可以具有的最多 1 MB 的總計大小。 陣列中的每個事件會限制為 64 KB。
 
## <a name="event-properties"></a>事件屬性

所有事件皆包含下列最高層級資料：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 |
| 主旨 | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 | 資源提供者特有的事件資料。 |

## <a name="available-event-sources"></a>可用的事件來源

下列事件來源透過 Event Grid 來發佈事件：

* 資源群組 (管理作業)
* Azure 訂用帳戶 (管理作業)
* 事件中樞
* 自訂主題

## <a name="azure-subscriptions"></a>Azure 訂用帳戶

Azure 訂用帳戶現在可從 Azure Resource Manager 發出管理事件，像是在虛擬機器建立時或儲存體帳戶刪除時皆可使用此功能。

### <a name="available-event-types"></a>可用的事件類型

- **Microsoft.Resources.ResourceWriteSuccess**：在資源建立或更新作業成功時引發。  
- **Microsoft.Resources.ResourceWriteFailure**：在資源建立或更新作業失敗時引發。  
- **Microsoft.Resources.ResourceWriteCancel**：在資源建立或更新作業取消時引發。  
- **Microsoft.Resources.ResourceDeleteSuccess**：在資源刪除作業成功時引發。  
- **Microsoft.Resources.ResourceDeleteFailure**：在資源刪除作業失敗時引發。  
- **Microsoft.Resources.ResourceDeleteCancel**：在資源刪除作業取消時引發。 這會在範本部署取消時發生。

### <a name="example-event-schema"></a>事件結構描述範例

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
        "httpRequest":"",
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



## <a name="resource-groups"></a>資源群組

資源群組現在可從 Azure Resource Manager 發出管理事件，像是在虛擬機器建立時或儲存體帳戶刪除時皆可使用此功能。

### <a name="available-event-types"></a>可用的事件類型

- **Microsoft.Resources.ResourceWriteSuccess**：在資源建立或更新作業成功時引發。  
- **Microsoft.Resources.ResourceWriteFailure**：在資源建立或更新作業失敗時引發。  
- **Microsoft.Resources.ResourceWriteCancel**：在資源建立或更新作業取消時引發。  
- **Microsoft.Resources.ResourceDeleteSuccess**：在資源刪除作業成功時引發。  
- **Microsoft.Resources.ResourceDeleteFailure**：在資源刪除作業失敗時引發。  
- **Microsoft.Resources.ResourceDeleteCancel**：在資源刪除作業取消時引發。 這會在範本部署取消時發生。

### <a name="example-event"></a>事件範例

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
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



## <a name="event-hubs"></a>事件中樞

事件中樞事件目前只有在透過擷取功能自動傳送檔案至儲存體時發出。

### <a name="available-event-types"></a>可用的事件類型

- **Microsoft.EventHub.CaptureFileCreated**：擷取檔案建立時引發。

### <a name="example-event"></a>事件範例

此範例顯示在擷取功能儲存檔案時引發之事件中樞事件的結構描述： 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```


## <a name="azure-blob-storage"></a>Azure Blob 儲存體

>[!IMPORTANT]
>您必須註冊，Blob 儲存體事件預覽才能使用 Blob 儲存體事件。 如需預覽計畫的詳細資訊，請參閱[Azure Blob 儲存體事件](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview#join-the-preview)。  


### <a name="available-event-types"></a>可用的事件類型

- **Microsoft.Storage.BlobCreated**：blob 建立時引發。
- **Microsoft.Storage.BlobDeleted**：blob 刪除時引發。

### <a name="example-event"></a>事件範例

此事件範例顯示建立 blob 時引發之儲存體事件的結構描述： 

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




## <a name="custom-topics"></a>自訂主題

您自訂事件的資料承載由您定義，可用任何格式正確的 JSON 物件來進行。 最高層級的資料應包含與標準資源定義事件相同的欄位。 在發佈事件至自訂主題時，您應考慮以幫助路由與篩選為目標建立事件的主體。

### <a name="example-event"></a>事件範例

以下範例為自訂主題的事件：
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>後續步驟

* 若要初步了解 Azure Event Grid，請參閱[什麼是 Event Grid？](overview.md)。
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
