---
title: "Azure 活動記錄事件結構描述 | Microsoft Docs"
description: "了解發出至活動記錄之資料的事件結構描述"
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: johnkem
ms.openlocfilehash: a4ceb822e0ec3e1c1dc31ece1db761834e795f6c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-activity-log-event-schema"></a>Azure 活動記錄事件結構描述
透過「Azure 活動記錄」，您可深入了解 Azure 中發生的任何訂用帳戶層級事件。 本文說明每個資料類別的事件結構描述。

## <a name="administrative"></a>管理
透過 Resource Manager 執行的所有建立、更新、刪除和動作作業皆記錄在此類別中。 您可能會在此類別中看到的事件類型範例包括「建立虛擬機器」和「刪除網路安全性群組」。使用者或應用程式使用 Resource Manager 所執行的每個動作，都會成為特定資源類型上的作業模型。 如果作業類型為「寫入」、「刪除」或「動作」，則該作業的啟動及成功或失敗記錄皆會記錄在「系統管理」類別。 「系統管理」類別也包含訂用帳戶中角色型存取控制的所有變更。

### <a name="sample-event"></a>範例事件
```json
{
  "authorization": {
    "action": "microsoft.support/supporttickets/write",
    "role": "Subscription Admin",
    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
  },
  "caller": "admin@contoso.com",
  "channels": "Operation",
  "claims": {
    "aud": "https://management.core.windows.net/",
    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
    "iat": "1421876371",
    "nbf": "1421876371",
    "exp": "1421880271",
    "ver": "1.0",
    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
    "puid": "20030000801A118C",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
    "name": "John Smith",
    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
    "appidacr": "2",
    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
    "http://schemas.microsoft.com/claims/authnclassreference": "1"
  },
  "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "description": "",
  "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
  "eventName": {
    "value": "EndRequest",
    "localizedValue": "End request"
  },
  "httpRequest": {
    "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
    "clientIpAddress": "192.168.35.115",
    "method": "PUT"
  },
  "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
  "level": "Informational",
  "resourceGroupName": "MSSupportGroup",
  "resourceProviderName": {
    "value": "microsoft.support",
    "localizedValue": "microsoft.support"
  },
  "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
  "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "operationName": {
    "value": "microsoft.support/supporttickets/write",
    "localizedValue": "microsoft.support/supporttickets/write"
  },
  "properties": {
    "statusCode": "Created"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": "Created",
    "localizedValue": "Created (HTTP Status Code: 201)"
  },
  "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
  "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
  "subscriptionId": "s1"
}
```

### <a name="property-descriptions"></a>屬性描述
| 元素名稱 | 說明 |
| --- | --- |
| 授權 |事件的 RBAC 屬性的 blob。 通常包括 action、role 和 scope 屬性。 |
| 呼叫者 |已執行作業的使用者的電子郵件地址，根據可用性的 UPN 宣告或 SPN 宣告。 |
| 通道 |為下列其中一個值：Admin、Operation |
| claims |Active Directory 用來驗證使用者或應用程式以在 Resource Manager 中執行此作業的 JWT 權杖。 |
| correlationId |通常是字串格式的 GUID。 具有相同 correlationId、屬於同一 uber 動作的事件。 |
| 說明 |事件的靜態文字描述。 |
| eventDataId |事件的唯一識別碼。 |
| httpRequest |描述 HTTP 要求的 blob。 通常包括 “clientRequestId”、“clientIpAddress”和 “method” (HTTP 方法。 例如，PUT)。 |
| 層級 |事件的層級。 下列其中一個值：重大、錯誤、警告、資訊和詳細資訊 |
| resourceGroupName |受影響資源的資源群組的名稱。 |
| resourceProviderName |受影響資源的資源提供者的名稱。 |
| resourceId |受影響資源的資源識別碼。 |
| operationId |對應至單一作業的事件共用的 GUID。 |
| operationName |作業名稱。 |
| properties |描述事件詳細資料的一組 `<Key, Value>` 配對 (也就是字典)。 |
| status |字串，描述作業的狀態。 常見的值包括︰Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| 子狀態 |通常包含對應 REST 呼叫的 HTTP 狀態碼，但也可以包含其他描述子狀態的字串，常見的值包括：確定 (HTTP 狀態碼︰200)，已建立 (HTTP 狀態碼︰201)、接受 (HTTP 狀態碼︰202)、沒有內容 (HTTP 狀態碼︰204)、不正確的要求 (HTTP 狀態碼︰400)、找不到 (HTTP 狀態碼︰404)，衝突 (HTTP 狀態碼︰409)、內部伺服器錯誤 (HTTP 狀態碼︰500)、服務無法使用 (HTTP 狀態碼︰503)、閘道逾時 (HTTP 狀態碼︰504)。 |
| eventTimestamp |處理與事件對應之要求的Azure 服務產生事件時的時間戳記。 |
| submissionTimestamp |當事件變成可供查詢時的時間戳記。 |
| subscriptionId |Azure 訂用帳戶識別碼。 |

## <a name="service-health"></a>服務健康情況
所有在 Azure 中發生的服務健康情況事件皆記錄在此類別中。 您可能會在此類別中看到的事件類型範例為「美國東部的 SQL Azure 發生停機事件」。 服務健康情況事件有五個種類：「需要採取動作」、「協助復原」、「事件」、「維護」、「資訊」或「安全性」，這些事件只會在訂用帳戶中有可能會受該事件影響的資源時顯示。

### <a name="sample-event"></a>範例事件
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```

### <a name="property-descriptions"></a>屬性描述
元素名稱 | 說明
-------- | -----------
通道 | 為下列其中一個值：Admin、Operation
correlationId | 通常是字串格式的 GUID。 屬於相同 uber 動作的事件通常會共用同一個 correlationId。
說明 | 事件的描述。
eventDataId | 事件的唯一識別碼。
eventName | 事件的標題。
層級 | 事件的層級。 下列其中一個值：重大、錯誤、警告、資訊和詳細資訊
resourceProviderName | 受影響資源的資源提供者名稱。 如果未知，此參數會是 null。
resourceType| 受影響資源的資源類型。 如果未知，此參數會是 null。
子狀態 | 針對服務健康情況事件通常為 null。
eventTimestamp | 記錄事件產生並提交至活動記錄時的時間戳記。
submissionTimestamp |   事件在活動記錄中可供查詢時的時間戳記。
subscriptionId | 記錄此事件的 Azure 訂用帳戶。
status | 字串，描述作業的狀態。 某些常見的值為：Active、Resolved。
operationName | 作業名稱。 通常是 Microsoft.ServiceHealth/incident/action。
category | "ServiceHealth"
resourceId | 受影響資源的資源識別碼 (如果知道)。 否則會提供訂用帳戶識別碼。
Properties.title | 此通訊的當地語系化標題。 預設語言為英文。
Properties.communication | 與 HTML 標記通訊的詳細資料 (已當地語系化)。 預設語言為英文。
Properties.incidentType | 可能的值：AssistedRecovery、ActionRequired、Information、Incident、Maintenance、Security
Properties.trackingId | 識別與此事件 (event) 相關聯的附帶事件 (Incident)。 可用此屬性讓與附帶事件 (Incident) 有關的事件 (event) 相關聯。
Properties.impactedServices | 逸出的 JSON blob，描述受到附帶事件 (Incident) 影響的服務和區域。 Services 清單 (每一份都有 ServiceName) 和 ImpactedRegions 清單 (每一份都有 RegionName)。
Properties.defaultLanguageTitle | 英文的通訊
Properties.defaultLanguageContent | 英文的通訊，如 html 標記或純文字
Properties.stage | AssistedRecovery、ActionRequired、Information、Incident、Security 的可能值：Active、Resolved。 Maintenance 的可能值︰Active、Planned、InProgress、Canceled、Rescheduled、Resolved、Complete
Properties.communicationId | 與此事件相關聯的通訊。

## <a name="alert"></a>警示
此類別包含所有 Azure 警示的啟用記錄。 您可能會在此類別中看到的事件類型範例為「myVM 上的 CPU 百分比在過去 5 分鐘內已超過 80」。 各種 Azure 系統都有警示概念，您可以定義某種類型的規則，並在條件符合該規則時接收通知。 每次支援的 Azure 警示類型「啟動」時，或產生通知的條件符合時，該啟用記錄會也會推送至此類別的活動記錄。

### <a name="sample-event"></a>範例事件

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>屬性描述
| 元素名稱 | 說明 |
| --- | --- |
| 呼叫者 | 一律是 Microsoft.Insights/alertRules |
| 通道 | 一律是 “Admin, Operation” |
| claims | 具有警示引擎的 SPN (服務主體名稱) 或資源類型的 JSON Blob。 |
| correlationId | 字串格式的 GUID。 |
| 說明 |警示事件的靜態文字描述。 |
| eventDataId |警示事件的唯一識別碼。 |
| 層級 |事件的層級。 下列其中一個值：重大、錯誤、警告、資訊和詳細資訊 |
| resourceGroupName |如果為計量警示，這是受影響資源的資源群組名稱。 針對其他警示類型，這是包含警示本身的資源群組名稱。 |
| resourceProviderName |如果為計量警示，這是受影響資源的資源提供者名稱。 針對其他警示類型，這是警示本身的資源提供者名稱。 |
| resourceId | 如果為計量警示，這是受影響資源的資源識別碼名稱。 針對其他警示類型，這是警示資源本身的資源識別碼。 |
| operationId |對應至單一作業的事件共用的 GUID。 |
| operationName |作業名稱。 |
| properties |描述事件詳細資料的一組 `<Key, Value>` 配對 (也就是字典)。 |
| status |字串，描述作業的狀態。 常見的值包括︰Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| 子狀態 | 針對警示通常為 null。 |
| eventTimestamp |處理與事件對應之要求的Azure 服務產生事件時的時間戳記。 |
| submissionTimestamp |當事件變成可供查詢時的時間戳記。 |
| subscriptionId |Azure 訂用帳戶識別碼。 |

### <a name="properties-field-per-alert-type"></a>每個警示類型的屬性欄位
屬性欄位將依據警示事件來源包含不同的值。 兩個常見的警示事件提供者為活動記錄警示和計量警示。

#### <a name="properties-for-activity-log-alerts"></a>活動記錄警示的屬性
| 元素名稱 | 說明 |
| --- | --- |
| properties.subscriptionId | 導致啟用此活動記錄警示規則之活動記錄事件的訂用帳戶識別碼。 |
| properties.eventDataId | 導致啟用此活動記錄警示規則之活動記錄事件的事件資料識別碼。 |
| properties.resourceGroup | 導致啟用此活動記錄警示規則之活動記錄事件的資源群組。 |
| properties.resourceId | 導致啟用此活動記錄警示規則之活動記錄事件的資源識別碼。 |
| properties.eventTimestamp | 導致啟用此活動記錄警示規則之活動記錄事件的事件時間戳記。 |
| properties.operationName | 導致啟用此活動記錄警示規則之活動記錄事件的作業名稱。 |
| properties.status | 導致啟用此活動記錄警示規則之活動記錄事件的狀態。|

#### <a name="properties-for-metric-alerts"></a>計量警示屬性
| 元素名稱 | 說明 |
| --- | --- |
| properties.RuleUri | 計量警示規則本身的資源識別碼。 |
| properties.RuleName | 計量警示規則的名稱。 |
| properties.RuleDescription | 計量警示規則的描述 (如警示規則中所定義)。 |
| properties.Threshold | 用於評估計量警示規則的臨界值。 |
| properties.WindowSizeInMinutes | 用於評估計量警示規則的視窗大小。 |
| properties.Aggregation | 定義於計量警示規則中的彙總類型。 |
| properties.Operator | 用於評估計量警示規則的條件運算子。 |
| properties.MetricName | 用於評估計量警示規則之計量的計量名稱。 |
| properties.MetricUnit | 用於評估計量警示規則之計量的計量單位。 |

## <a name="autoscale"></a>Autoscale
所有與自動調整引擎 (以訂用帳戶中定義的自動調整設定為基礎) 作業相關的所有事件皆記錄在此類別。 您可能會在此類別中看到的事件類型範例為「自動調整相應增加動作失敗」。 自動調整可讓您使用自動調整設定，依據每日時間和/或負載 (計量) 資料，自動相應放大或縮小受支援資源類型中的執行個體數目。 符合相應增加或相應減少的條件時，啟動及成功或失敗事件將會記錄在此類別中。

### <a name="sample-event"></a>範例事件
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>屬性描述
| 元素名稱 | 說明 |
| --- | --- |
| 呼叫者 | 一律是 Microsoft.Insights/autoscaleSettings |
| 通道 | 一律是 “Admin, Operation” |
| claims | 具有自動調整引擎的 SPN (服務主體名稱) 或資源類型的 JSON Blob。 |
| correlationId | 字串格式的 GUID。 |
| 說明 |自動調整事件的靜態文字描述。 |
| eventDataId |自動調整事件的唯一識別碼。 |
| 層級 |事件的層級。 下列其中一個值：重大、錯誤、警告、資訊和詳細資訊 |
| resourceGroupName |自動調整設定的資源群組名稱。 |
| resourceProviderName |自動調整設定的資源提供者名稱。 |
| resourceId |自動調整設定的資源識別碼。 |
| operationId |對應至單一作業的事件共用的 GUID。 |
| operationName |作業名稱。 |
| properties |描述事件詳細資料的一組 `<Key, Value>` 配對 (也就是字典)。 |
| properties.Description | 自動調整引擎所執行之作業的詳細描述。 |
| properties.ResourceName | 受影響資源 (執行調整動作的資源) 的資源識別碼 |
| properties.OldInstancesCount | 自動調整動作生效之前的執行個體數目。 |
| properties.NewInstancesCount | 自動調整動作生效之後的執行個體數目。 |
| properties.LastScaleActionTime | 自動調整動作發生時的時間戳記。 |
| status |字串，描述作業的狀態。 常見的值包括︰Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| 子狀態 | 針對自動調整通常為 null。 |
| eventTimestamp |處理與事件對應之要求的Azure 服務產生事件時的時間戳記。 |
| submissionTimestamp |當事件變成可供查詢時的時間戳記。 |
| subscriptionId |Azure 訂用帳戶識別碼。 |

## <a name="next-steps"></a>後續步驟
* [深入了解活動記錄檔 (之前的稽核記錄檔)](monitoring-overview-activity-logs.md)
* [將 Azure 活動記錄檔串流至事件中樞](monitoring-stream-activity-logs-event-hubs.md)
