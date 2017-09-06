---
title: "Azure Event Grid 安全性與驗證"
description: "說明 Azure Event Grid 與其概念。"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/14/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: ccef224ef1c2919a3e5469c1bbe0980c6963705b
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---

# <a name="event-grid-security-and-authentication"></a>Event Grid 安全性與驗證 

Azure Event Grid 有三種驗證方法：

* 事件訂閱
* 事件發佈
* WebHook 事件傳遞

## <a name="webhook-event-delivery"></a>WebHook 事件傳遞

Webhook 是即時從 Azure Event Grid 接收事件的眾多方法之一。

每次一有準備好傳遞的新事件，Event Grid 就會在主體內夾帶事件，將 HTTP 要求傳送至您的 WebHook。

當您使用 Event Grid 註冊自己的 WebHook 端點時，Event Grid 會將帶有簡單驗證碼的 POST 要求傳送給您，藉以證明端點的所有權。 您的應用程式需要回傳驗證碼作為回應。 Event Grid 不會將未經驗證的事件傳遞至 WebHook 端點。
 
### <a name="validation-details"></a>驗證詳細資料：

* 在事件訂用帳戶建立/更新時，Event Grid 會將 SubscriptionValidationEvent 事件送至目標端點。
* 此事件的標題值為 Event-Type: Validation。
* 此事件主體之結構描述與其他 Event Grid 事件相同。
* 此事件資料包含一個 “ValidationCode” 屬性與一個隨機產生的字串，例如“ValidationCode: acb13…”。

範例 SubscriptionValidationEvent 如下所示。
```json
[{
  "Id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "Topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "Subject": "",
  "Data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "EventType": "Microsoft.EventGrid/SubscriptionValidationEvent",
  "EventTime": "2017-08-06T22:09:30.740323Z"
}]
```

為了證明端點所有權，請回傳驗證碼，例如 “validation_response: acb13…”，其範例如下所示。

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

為了證明端點所有權，請回傳驗證碼，例如 “ValidationResponse: acb13…”。

最後請務必注意，Azure Event Grid 只支援 HTTPS Webhook 端點。
## <a name="event-subscription"></a>事件訂閱

要訂閱事件，您必須擁有所需資源的 **Microsoft.EventGrid/EventSubscriptions/Write** 授權。 因為您要在該資源的範圍下寫入新的訂閱，所以需要授權。 端看您訂閱的是系統主題或自訂主題，所需資源會有所不同。 本節會說明這兩種類型。

### <a name="system-topics-azure-service-publishers"></a>系統主題 (Azure 服務發行者)

若您訂閱的是系統主題，您需要取得在資源發佈範圍下寫入新事件訂閱的權限。 資源的格式為：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

舉例來說，若要訂閱名為 **myacct** 之儲存體帳戶上的事件，您需要 Microsoft.EventGrid/EventSubscriptions/Write 授予您此權限：`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>自訂主題

若您訂閱的是自訂主題，您需要取得在 Event Grid 主題範圍下寫入新事件訂閱的權限。 資源的格式為：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

舉例來說，若要訂閱名為**mytopic** 之自訂主題，您需要 Microsoft.EventGrid/EventSubscriptions/Write 授予您此權限：`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="topic-publishing"></a>主題發佈

主題使用共用存取簽章 (SAS) 或金鑰驗證。 我們建議使用 SAS，但金鑰驗證提供簡單的程式編寫，並且與許多現有的 Webhook 發佈者相容。 

您要在 HTTP 標題包含驗證值。 若選擇 SAS，請使用 **aeg-sas-token** 作為標題的值。 若選擇金鑰驗證，請使用 **aeg-sas-key** 作為標題的值。

### <a name="key-authentication"></a>金鑰驗證

金鑰驗證是最簡單的驗證方法。 請使用此格式：`aeg-sas-key: <your key>`

舉例來說，您將金鑰與此一起傳遞： 

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS 權杖

Event Grid 的 SAS 權杖包含資源、過期時間及簽章。 SAS 權杖的格式為：`r={resource}&e={expiration}&s={signature}`。

這裡的 resource 是您傳送事件之目標主題的路徑。 以下為一個有效資源路徑的例子：`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

簽章由您從金鑰產生。

以下為一個有效 **aeg-sas-token** 值的例子：

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
``` 

下列範例建立了供 Event Grid 使用的 SAS 權杖：

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    string encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString());

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Management Access Control

Azure Event Grid 讓您能控制給予不同使用者進行各種管理作業的存取層級，這些作業包含列出事件訂閱、建立新訂閱及產生金鑰等等。 Event Grid 為此使用 Azure 的 Role Based Access Check (RBAC)。

### <a name="operation-types"></a>作業類型

Azure Event Grid 支援下列動作：

* Microsoft.EventGrid/*/read 
* Microsoft.EventGrid/*/write 
* Microsoft.EventGrid/*/delete 
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action 
* Microsoft.EventGrid/topics/listKeys/action 
* Microsoft.EventGrid/topics/regenerateKey/action

最後三種作業可能會回傳秘密資訊，這種資訊在一般讀取作業時是會被篩選掉的。 最好的做法是限制這幾項作業的存取。 自訂角色可以使用 [Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)、[Azure 命令列介面 (CLI)](../active-directory/role-based-access-control-manage-access-azure-cli.md) 和 [REST API](../active-directory/role-based-access-control-manage-access-rest.md) 建立。

### <a name="enforcing-role-based-access-check-rbac"></a>強制執行 Role Based Access Check (RBAC)

進行下列步驟以強制不同使用者執行 RBAC：

#### <a name="create-a-custom-role-definition-file-json"></a>建立自訂的角色定義檔案 (.json)

以下是允許使用者進行不同組事件的 Event Grid 角色定義範例。

**EventGridReadOnlyRole.json**：只允許唯讀作業。
```json
{ 
  "Name": "Event grid read only role", 
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856", 
  "IsCustom": true, 
  "Description": "Event grid read only role", 
  "Actions": [ 
    "Microsoft.EventGrid/*/read" 
  ], 
  "NotActions": [ 
  ], 
  "AssignableScopes": [ 
    "/subscriptions/<Subscription Id>" 
  ] 
}
```

**EventGridNoDeleteListKeysRole.json**：允許限制的張貼動作，但不允許刪除。
```json
{ 
  "Name": "Event grid No Delete Listkeys role", 
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174", 
  "IsCustom": true, 
  "Description": "Event grid No Delete Listkeys role", 
  "Actions": [     
    "Microsoft.EventGrid/*/write", 
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action" 
    "Microsoft.EventGrid/topics/listkeys/action", 
    "Microsoft.EventGrid/topics/regenerateKey/action" 
  ], 
  "NotActions": [ 
    "Microsoft.EventGrid/*/delete" 
  ], 
  "AssignableScopes": [ 
    "/subscriptions/<Subscription id>" 
  ] 
}
```

**EventGridContributorRole.json**：允許所有 Grid 動作。  
```json
{ 
  "Name": "Event grid contributor role", 
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514", 
  "IsCustom": true, 
  "Description": "Event grid contributor role", 
  "Actions": [ 
    "Microsoft.EventGrid/*/write", 
    "Microsoft.EventGrid/*/delete", 
    "Microsoft.EventGrid/topics/listkeys/action", 
    "Microsoft.EventGrid/topics/regenerateKey/action", 
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action" 
  ], 
  "NotActions": [], 
  "AssignableScopes": [ 
    "/subscriptions/d48566a8-2428-4a6c-8347-9675d09fb851" 
  ] 
} 
```

#### <a name="install-and-login-to-azure-cli"></a>安裝並登入 Azure CLI

* Azure CLI 0.8.8 版或更新版本。 若要安裝最新版本，並將它與 Azure 訂用帳戶建立關聯，請參閱 [安裝和設定 Azure CLI](../cli-install-nodejs.md)。
* Azure CLI 中的 Azure Resource Manager。 如需詳細資訊，請瀏覽 [搭配使用 Azure CLI 和 Resource Manager](../xplat-cli-azure-resource-manager.md) 。

#### <a name="create-a-custom-role"></a>建立自訂角色

若要建立自訂角色，請使用：

    azure role create --inputfile <file path>

#### <a name="assign-the-role-to-a-user"></a>指派角色給使用者


    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>


## <a name="next-steps"></a>後續步驟

* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)

