---
title: Resource Manager REST API | Microsoft Docs
description: "資源管理員 REST API 驗證和使用方式範例的概觀"
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: e8d7a1d2-1e82-4212-8288-8697341408c5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: navale;tomfitz;
ms.openlocfilehash: 2f7ba23775545637de865f9ef63680ae22c62164
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="resource-manager-rest-apis"></a>資源管理員 REST API
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [Azure CLI](xplat-cli-azure-resource-manager.md)
> * [入口網站](resource-group-portal.md) 
> * [REST API](resource-manager-rest-api.md)
> 
> 

在每次呼叫 Azure Resource Manager 時、每個部署的範本及每個設定的儲存體帳戶背後，都會呼叫 Azure Resource Manager 的 REST API 一次或多次。 本主題專門討論這些 API 以及如何在完全不使用任何 SDK 的情況下呼叫它們。 如果您想要完全控制對 Azure 的要求、或您慣用的語言沒有適用的 SDK 或該 SDK 不支援您需要的作業，這種做法很有用。

本文並不會逐一介紹 Azure 中公開的每個 API，而是以某些 API 為例，說明如何連接至這些 API。 了解基本概念後，接著您可以閱讀 [Azure Resource Manager REST API 參考](https://docs.microsoft.com/rest/api/resources/)，找出如何使用其餘 API 的詳細資訊。

## <a name="authentication"></a>驗證
Resource Manager 的驗證由 Azure Active Directory (AD) 處理。 若要連接至任何 API，您必須先向 Azure AD 進行驗證，以接收可以傳遞給每個要求的驗證權杖。 因為我們要說明的是單純地呼叫直接 REST API，因此以您不想遵照提示輸入使用者名稱和密碼的假設來進行驗證。 我們也假設您不使用雙重要素驗證機制。 因此，我們將建立用來登入的 Azure AD 應用程式和服務主體。 但請記住，Azure AD 支援數個驗證程序，而這些程序全都可以用來擷取後續 API 要求所需的驗證權杖。
請依照[建立 Azure AD 應用程式和服務主體](resource-group-create-service-principal-portal.md)的逐步指示。

### <a name="generating-an-access-token"></a>產生存取權杖
向外呼叫位於 login.microsoftonline.com 的 Azure AD，集合完成對 Azure AD 驗證。 若要驗證，您必須具有下列資訊︰

* Azure AD 租用戶識別碼 (您用來登入的 Azure AD 名稱，通常與您的公司名稱相同，但不一定如此)
* 應用程式識別碼 (在 Azure AD 應用程式建立步驟期間取得)
* 密碼 (在建立 Azure AD 應用程式時選取）

在下列 HTTP 要求中，務必將 "Azure AD Tenant ID"、"Application ID" 和 "Password" 換成正確的值。

**一般 HTTP 要求︰**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... 將 (如果驗證成功) 產生類似下列的回應：

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(已縮短先前回應中的 access_token，以提高可讀性)

**使用 Bash 產生存取權杖：**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net/&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**使用 PowerShell 產生存取權杖：**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

此回應包含存取權杖、該權杖有效期間的相關資訊，以及您可以將該權杖用於哪項資源的相關資訊。
您在先前 HTTP 呼叫中收到的存取權杖，必須傳給對 Resource Manager API 提出的所有要求。 您透過標頭值 "Authorization" 來傳遞它，值為 "Bearer YOUR_ACCESS_TOKEN"。 請注意 "Bearer" 與存取權杖之間的空格。

從上面的 HTTP 結果可以看出，權杖在一段特定時間內保持有效，您在這段期間內應該快取並重複使用該相同權杖。 即使可以對 Azure AD 進行每個 API 呼叫的驗證，但是會很沒效率。

## <a name="calling-resource-manager-rest-apis"></a>呼叫 Resource Manager REST API
本主題只使用幾個 API 來說明 REST 作業的基本用法。 如需所有作業的相關資訊，請參閱 [Azure Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/)。

### <a name="list-all-subscriptions"></a>列出所有訂用帳戶
您可以執行的最簡單作業之一，就是列出您可以存取的可用訂用帳戶。 在以下要求中，您會看到如何以標頭形式傳入存取權杖：

(以您實際的存取權杖取代 YOUR_ACCESS_TOKEN。)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

...因此，您會取得允許此服務主體存取的訂用帳戶清單

(為了便於閱讀，已縮短訂用帳戶識別碼)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>列出特定訂用帳戶中的所有資源群組
適用於 Resource Manager API 的所有資源都放在資源群組中。 您可以使用下列 HTTP GET 要求，向 Resource Manager 查詢訂用帳戶中的現有資源群組。 這次，請注意訂用帳戶識別碼如何作為 URL 的一部分而傳入。

(以實際的存取權杖和訂用帳戶識別碼取代 YOUR_ACCESS_TOKEN 和 SUBSCRIPTION_ID)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

您收到的回應取決於是否已定義任何資源群組以及群組數量 (若已定義)。

(為了便於閱讀，已縮短訂用帳戶識別碼)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>建立資源群組
到目前為止，我們只向 Resource Manager API 查詢資訊。 現在該建立某些資源，讓我們從最簡單的開始，也就是資源群組。 下列 HTTP 要求會在您選擇的區域/位置建立資源群組，並新增它的標籤。

(以您實際的存取權杖、訂用帳戶識別碼和您要建立的資源群組名稱取代 YOUR_ACCESS_TOKEN、SUBSCRIPTION_ID、RESOURCE_GROUP_NAME)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

如果成功，您會收到類似下列的回應：

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

您已在 Azure 中成功建立資源群組。 恭喜！

### <a name="deploy-resources-to-a-resource-group-using-a-resource-manager-template"></a>使用 Resource Manager 範本將資源部署至資源群組
Resource Manager 可讓您使用範本來部署資源。 範本定義數個資源及其相依性。 在本節中，我們假設您熟悉 Resource Manager 範本，我們只示範如何進行 API 呼叫以開始部署。 如需有關建構範本的詳細資訊，請參閱[編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。

部署範本和呼叫其他 API 沒有多大差異。 較特別的是部署範本可能需要很長的時間。 API 呼叫只是傳回資料，身為開發人員的您要負責查詢部署的狀態，以查明部署何時完成。 如需詳細資訊，請參閱[追蹤非同步 Azure 作業](resource-manager-async-operations.md)。

在此範例中，我們使用 [GitHub](https://github.com/Azure/azure-quickstart-templates) 上提供的公開範本。 我們使用的範本會將 Linux VM 部署至美國西部區域。 雖然此範例使用 GitHub 等公開存放庫中提供的範本，您還是可以在要求中傳遞完整範本。 請注意，我們在要求中提供的參數值會用在部署的範本內。

(將 SUBSCRIPTION_ID、RESOURCE_GROUP_NAME、DEPLOYMENT_NAME、YOUR_ACCESS_TOKEN、GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME、ADMIN_USER_NAME、ADMIN_PASSWORD 和 DNS_NAME_FOR_PUBLIC_IP 換成適合要求的值)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

為了提高本文件的可讀性，已省略此要求較長的 JSON 回應。 回應會包含您建立之樣板化部署的相關資訊。

## <a name="next-steps"></a>後續步驟

- 若要了解處理非同步 REST 作業，請參閱[追蹤非同步 Azure 作業](resource-manager-async-operations.md)。
