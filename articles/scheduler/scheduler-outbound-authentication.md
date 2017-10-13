---
title: "排程器輸出驗證"
description: "排程器輸出驗證"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2016
ms.author: deli
ms.openlocfilehash: e345b2e22daae5b24c23645f7d2636f66df630ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-outbound-authentication"></a>排程器輸出驗證
排程器工作可能需要對外呼叫需要驗證的服務。 如此一來，被呼叫的服務可以判定排程器工作是否可以存取它的資源。 其中某些服務包括其他 Azure 服務、Salesforce.com、Facebook 和安全的自訂網站。

## <a name="adding-and-removing-authentication"></a>新增和移除驗證
將驗證加入至排程器工作輕而易舉 – 建立或更新工作時，將 JSON 子元素 `authentication` 加入至 `request` 元素。 回應中永不傳回 PUT、PATCH 或 POST 要求中傳送給排程器服務的密碼 (屬於 `authentication` 物件)。 在回應中，密碼資訊會設定為 null，或可能具有一個代表已驗證之實體的公用權杖。

若要移除驗證，明確地 PUT 或 PATCH 工作，同時將 `authentication` 物件設定為 null。 您將不會看到回應中傳回的任何驗證屬性。

目前，唯一支援的驗證類型為 `ClientCertificate` 模型 (適用於使用 SSL/TLS 用戶端憑證)、`Basic` 模型 (適用於基本驗證)，和 `ActiveDirectoryOAuth` 模型 (適用於 Active Directory OAuth 驗證)。

## <a name="request-body-for-clientcertificate-authentication"></a>ClientCertificate 驗證的要求本文
加入驗證時，請使用 `ClientCertificate` 模型，在要求本文中指定下列其他元素。  

| 元素 | 說明 |
|:--- |:--- |
| *authentication (父元素)* |使用 SSL 用戶端憑證的驗證物件。 |
| *type* |必要。 驗證類型。若為 SSL 用戶端憑證，值必須是 `ClientCertificate`。 |
| *pfx* |必要。 Base64 編碼的 PFX 檔案內容。 |
| *password* |必要。 存取 PFX 檔案的密碼。 |

## <a name="response-body-for-clientcertificate-authentication"></a>ClientCertificate 驗證的回應本文
當傳送要求與驗證資訊時，回應包含下列驗證相關的元素。

| 元素 | 說明 |
|:--- |:--- |
| *authentication (父元素)* |使用 SSL 用戶端憑證的驗證物件。 |
| *type* |驗證類型。 若為 SSL 用戶端憑證，值為 `ClientCertificate`。 |
| *certificateThumbprint* |憑證的指紋。 |
| *certificateSubjectName* |憑證的主旨辨別名稱。 |
| *certificateExpiration* |憑證的到期日 |

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>ClientCertificate 驗證的範例 REST 要求
```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>ClientCertificate 驗證的範例 REST 回應
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="request-body-for-basic-authentication"></a>基本驗證的要求本文
加入驗證時，請使用 `Basic` 模型，在要求本文中指定下列其他元素。

| 元素 | 說明 |
|:--- |:--- |
| *authentication (父元素)* |使用基本驗證的驗證物件。 |
| *type* |必要。 驗證類型。 若為基本驗證，值必須是 `Basic`。 |
| *username* |必要。 要驗證的使用者名稱。 |
| *password* |必要。 要驗證的密碼。 |

## <a name="response-body-for-basic-authentication"></a>基本驗證的回應本文
當傳送要求與驗證資訊時，回應包含下列驗證相關的元素。

| 元素 | 說明 |
|:--- |:--- |
| *authentication (父元素)* |使用基本驗證的驗證物件。 |
| *type* |驗證類型。 若為基本驗證，值為 `Basic`。 |
| *username* |已驗證的使用者名稱。 |

## <a name="sample-rest-request-for-basic-authentication"></a>基本驗證的範例 REST 要求
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>基本驗證的範例 REST 回應
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 驗證的要求本文
加入驗證時，請使用 `ActiveDirectoryOAuth` 模型，在要求本文中指定下列其他元素。

| 元素 | 說明 |
|:--- |:--- |
| *authentication (父元素)* |使用 ActiveDirectoryOAuth 驗證的驗證物件。 |
| *type* |必要。 驗證類型。 若為 ActiveDirectoryOAuth 驗證，值必須是 `ActiveDirectoryOAuth`。 |
| *tenant* |必要。 Azure AD 租用戶的租用戶識別碼。 |
| *audience* |必要。 這是設為 https://management.core.windows.net/。 |
| *clientId* |必要。 提供 Azure AD 應用程式的用戶端識別碼。 |
| *secret* |必要。 要求權杖之用戶端的密碼。 |

### <a name="determining-your-tenant-identifier"></a>判斷您的租用戶識別碼
您也可以透過在 Azure PowerShell 中執行 `Get-AzureAccount` ，找到 Azure AD 租用戶的租用戶識別碼。

## <a name="response-body-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 驗證的回應本文
當傳送要求與驗證資訊時，回應包含下列驗證相關的元素。

| 元素 | 說明 |
|:--- |:--- |
| *authentication (父元素)* |使用 ActiveDirectoryOAuth 驗證的驗證物件。 |
| *type* |驗證類型。 若為 ActiveDirectoryOAuth 驗證，值為 `ActiveDirectoryOAuth`。 |
| *tenant* |Azure AD 租用戶的租用戶識別碼。 |
| *audience* |這是設為 https://management.core.windows.net/。 |
| *clientId* |Azure AD 應用程式的用戶端識別碼。 |

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 驗證的範例 REST 要求
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 驗證的範例 REST 回應
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>另請參閱
 [排程器是什麼？](scheduler-intro.md)

 [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)

 [在 Azure 入口網站中開始使用排程器](scheduler-get-started-portal.md)

 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)

 [Azure 排程器 REST API 參考](https://msdn.microsoft.com/library/mt629143)

 [Azure 排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)

