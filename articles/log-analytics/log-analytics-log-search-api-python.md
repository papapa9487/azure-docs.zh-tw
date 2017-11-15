---
title: "用於從 Azure Log Analytics 擷取記錄的 Python 指令碼 | Microsoft Docs"
description: "Log Analytics 記錄搜尋 API 可讓任何 REST API 用戶端從 Log Analytics 工作區擷取資料。  本文提供使用記錄搜尋 API 的範例 Python 指令碼。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: bwren
ms.openlocfilehash: a8a4ec7a6ddf2daeca6ead11460fa076a7eb5c94
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="retrieve-data-from-log-analytics-with-a-python-script"></a>使用 Python 指令碼從 Log Analytics 擷取資料
[Log Analytics 記錄搜尋 API](log-analytics-log-search-api.md) 可讓任何 REST API 用戶端從 Log Analytics 工作區擷取資料。  本文呈現可使用 Log Analytics 記錄搜尋 API 的範例 Python 指令碼。  

>[!NOTE]
> 本文在 Log Analytics 中使用記錄檔搜尋 API 作為舊版查詢語言。  本文將針對已經升級到 [ 新 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)的工作區提供更新。

## <a name="authentication"></a>驗證
此指令碼會在 Azure Active Directory 中使用服務主體來驗證工作區。  服務主體可讓用戶端應用程式要求服務驗證帳戶，即使用戶端沒有帳戶名稱亦然。 執行此指令碼之前，您必須使用[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../azure-resource-manager/resource-group-create-service-principal-portal.md)中的程序來建立服務主體。  您必須將應用程式識別碼、租用戶識別碼及驗證金鑰提供給指令碼。 

> [!NOTE]
> 當您[建立 Azure 自動化帳戶](../automation/automation-create-standalone-account.md)時，會建立適合搭配此指令碼使用的服務主體。  如果您已擁有 Azure 自動化所建立的服務主體，您應該能夠使用它，而不是建立一個新的服務主體，雖然您可能需要[建立驗證金鑰](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) (如果它還沒有的話)。

## <a name="script"></a>指令碼
``` python
import adal
import requests
import json
import datetime
from pprint import pprint

# Details of workspace.  Fill in details for your workspace.
resource_group = 'xxxxxxxx'
workspace = 'xxxxxxxx'

# Details of query.  Modify these to your requirements.
query = "Type=Event"
end_time = datetime.datetime.utcnow()
start_time = end_time - datetime.timedelta(hours=24)
num_results = 100  # If not provided, a default of 10 results will be used.

# IDs for authentication.  Fill in values for your service principal.
subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
tenant_id = 'xxxxxxxx-xxxx-xxxx-xxx-xxxxxxxxxxxx'
application_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx'
application_key = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

# URLs for authentication
authentication_endpoint = 'https://login.microsoftonline.com/'
resource  = 'https://management.core.windows.net/'

# Get access token
context = adal.AuthenticationContext('https://login.microsoftonline.com/' + tenant_id)
token_response = context.acquire_token_with_client_credentials('https://management.core.windows.net/', application_id, application_key)
access_token = token_response.get('accessToken')

# Add token to header
headers = {
    "Authorization": 'Bearer ' + access_token,
    "Content-Type":'application/json'
}

# URLs for retrieving data
uri_base = 'https://management.azure.com'
uri_api = 'api-version=2015-11-01-preview'
uri_subscription = 'https://management.azure.com/subscriptions/' + subscription_id
uri_resourcegroup = uri_subscription + '/resourcegroups/'+ resource_group
uri_workspace = uri_resourcegroup + '/providers/Microsoft.OperationalInsights/workspaces/' + workspace
uri_search = uri_workspace + '/search'

# Build search parameters from query details
search_params = {
        "query": query,
        "top": num_results,
        "start": start_time.strftime('%Y-%m-%dT%H:%M:%S'),
        "end": end_time.strftime('%Y-%m-%dT%H:%M:%S')
        }

# Build URL and send post request
uri = uri_search + '?' + uri_api
response = requests.post(uri,json=search_params,headers=headers)

# Response of 200 if successful
if response.status_code == 200:

    # Parse the response to get the ID and status
    data = response.json()
    search_id = data["id"].split("/")
    id = search_id[len(search_id)-1]
    status = data["__metadata"]["Status"]

    # If status is pending, then keep checking until complete
    while status == "Pending":

        # Build URL to get search from ID and send request
        uri_search = uri_search + '/' + id
        uri = uri_search + '?' + uri_api
        response = requests.get(uri,headers=headers)

        # Parse the response to get the status
        data = response.json()
        status = data["__metadata"]["Status"]

else:

    # Request failed
    print (response.status_code)
    response.raise_for_status()

print ("Total records:" + str(data["__metadata"]["total"]))
print ("Returned top:" + str(data["__metadata"]["top"]))
pprint (data["value"])
```
## <a name="next-steps"></a>後續步驟
- 深入了解 [Log Analytics 記錄搜尋 API](log-analytics-log-search-api.md)。