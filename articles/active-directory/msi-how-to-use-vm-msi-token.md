---
title: "如何使用 Azure 虛擬機器受控服務識別 (MSI) 來取得存取權杖"
description: "使用 Azure 虛擬機器 MSI 取得 OAuth 存取權杖的逐步指示和範例。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 20ab8c9ceeb28c92b02a50ab03c9a7fa3fb7c7b7
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-token-acquisition"></a>如何使用 Azure 虛擬機器受控服務識別 (MSI) 來取得權杖 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)] 本文提供各種取得權杖的程式碼和指令碼，以及處理權杖到期和 HTTP 錯誤等重要主題的指引。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

如果您打算使用本文中的 Azure PowerShell 範例，請務必安裝最新版的 [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM)。


> [!IMPORTANT]
> - 本文中的所有範例程式碼/指令碼都假設用戶端在已啟用 MSI 的虛擬機器上執行。 在 Azure 入口網站中使用虛擬機器「連線」功能，從遠端連線到您的虛擬機器。 如需有關在虛擬機器上啟用 MSI 的詳細資訊，請參閱[使用 Azure 入口網站設定虛擬機器受管理服務識別 (MSI)](msi-qs-configure-portal-windows-vm.md)，或其中一篇變化文章 (使用 PowerShell、CLI、範本或 Azure SDK)。 

## <a name="overview"></a>概觀

用戶端應用程式可以要求用於存取指定資源的 MSI [僅限應用程式存取權杖](develop/active-directory-dev-glossary.md#access-token)。 此權杖是[以 MSI 服務主體為基礎](msi-overview.md#how-does-it-work)。 因此，用戶端不需要自行註冊就能取得本身服務主體下的存取權杖。 權杖在[需要用戶端認證的服務對服務呼叫](active-directory-protocols-oauth-service-to-service.md)中適合作為持有人權杖。

|  |  |
| -------------- | -------------------- |
| [使用 HTTP 取得權杖](#get-a-token-using-http) | MSI 權杖端點的通訊協定詳細資料 |
| [使用 C# 取得權杖](#get-a-token-using-c) | 從 C# 用戶端使用 MSI REST 端點的範例 |
| [使用 Go 取得權杖](#get-a-token-using-go) | 從 Go 用戶端使用 MSI REST 端點的範例 |
| [使用 Azure PowerShell 取得權杖](#get-a-token-using-azure-powershell) | 從 PowerShell 用戶端使用 MSI REST 端點的範例 |
| [使用 CURL 取得權杖](#get-a-token-using-curl) | 從 Bash/CURL 用戶端使用 MSI REST 端點的範例 |
| [處理權杖到期](#handling-token-expiration) | 處理過期存取權杖的指引 |
| [錯誤處理](#error-handling) | 從 MSI 權杖端點傳回 HTTP 錯誤的處理指引 |
| [Azure 服務的資源識別碼](#resource-ids-for-azure-services) | 取得所支援 Azure 服務資源識別碼的地方 |

## <a name="get-a-token-using-http"></a>使用 HTTP 取得權杖 

取得存取權杖的基本介面是以 REST 為基礎，如此可讓用戶端應用程式在可執行 HTTP REST 呼叫的虛擬機器上執行時，可以對其進行存取。 這類似於 Azure AD 的程式設計模型，但用戶端會在虛擬機器上使用 localhost 端點 (對比 Azure AD 端點)。

範例要求：

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 元素 | 說明 |
| ------- | ----------- |
| `GET` | HTTP 指令動詞，指出您想要擷取端點中的資料。 在此案例中是 OAuth 存取權杖。 | 
| `http://localhost:50342/oauth2/token` | MSI 端點，其中 50342 是預設連接埠且可設定。 |
| `resource` | 查詢字串參數，指出目標資源的應用程式識別碼 URI。 也會出現在所核發權杖的 `aud` (對象) 宣告中。 此範例會要求權杖來存取 Azure Resource Manager，其中就包含應用程式識別碼 URI https://management.azure.com/。 |
| `Metadata` | HTTP 要求標頭欄位，MSI 需此元素以減輕伺服器端偽造要求 (SSRF) 攻擊。 此值必須設定為 "true" (全部小寫)。

範例回應：

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 元素 | 說明 |
| ------- | ----------- |
| `access_token` | 所要求的存取權杖。 呼叫受保護的 REST API 時，權杖會內嵌在 `Authorization` 要求標頭欄位中成為「持有人」權杖，以允許 API 驗證呼叫端。 | 
| `refresh_token` | MSI 不使用。 |
| `expires_in` | 存取權杖從發行到過期之前持續有效的秒數。 在權杖的 `iat` 宣告中可找到發行時間。 |
| `expires_on` | 存取權杖到期的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `exp` 宣告)。 |
| `not_before` | 存取權杖生效且可被接受的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `nbf` 宣告)。 |
| `resource` | 要求存取權杖所針對的資源，符合要求的 `resource` 查詢字串參數。 |
| `token_type` | 權杖的類型，即「持有人」存取權杖，表示此權杖的持有人可以存取資源。 |

## <a name="get-a-token-using-c"></a>使用 C# 取得權杖

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-go"></a>使用 Go 取得權杖

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for MSI token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://localhost:50342/oauth2/token")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call MSI /token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>使用 Azure PowerShell 取得權杖

下列範例會示範如何從 PowerShell 用戶端使用 MSI REST 端點來執行下列動作：

1. 取得存取權杖。
2. 使用存取權杖來呼叫 Azure Resource Manager REST API，並取得虛擬機器的相關資訊。 請務必以您的訂用帳戶識別碼、資源群組名稱和虛擬機器名稱各別取代 `<SUBSCRIPTION-ID>`、`<RESOURCE-GROUP>` 和 `<VM-NAME>`。

```azurepowershell
# Get an access token for the MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>使用 CURL 取得權杖

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>處理權杖到期

本機 MSI 子系統會快取權杖。 因此，您可以視需要呼叫它，而只有在下列情況，線上呼叫 Azure AD 才會有結果：
- 由於快取中沒有權杖而發生快取遺漏
- 權杖已過期

如果您在程式碼中快取權杖，則應該妥善處理資源指出權杖過期的情節。

## <a name="error-handling"></a>錯誤處理 

MSI 端點會透過 HTTP 回應訊息標頭的狀態碼欄位 (如 4xx 或 5xx 錯誤) 來發出錯誤通知：

| 狀態碼 | 錯誤原因 | 處理方式 |
| ----------- | ------------ | ------------- |
| 要求中的 4xx 錯誤。 | 一個或多個要求參數不正確。 | 請勿重試。  檢查錯誤詳細資料以取得更多資訊。  4xx 錯誤是設計階段錯誤。|
| 來自服務的 5xx 暫時性錯誤。 | MSI 子系統或 Azure Active Directory 傳回暫時性錯誤。 | 等待至少一秒後即可安全地進行重試。  如果您太快重試或重試太多次，Azure AD 可能傳回速率限制錯誤 (429)。|

如果發生錯誤，對應的 HTTP 回應主體會包含 JSON 格式的錯誤詳細資料：

| 元素 | 說明 |
| ------- | ----------- |
| 錯誤   | 錯誤識別碼。 |
| error_description | 錯誤的詳細資訊描述。 **錯誤描述可以隨時變更。請勿將程式碼撰寫為會針對錯誤描述中的值建立分支。**|

### <a name="http-response-reference"></a>HTTP 回應參考

本節會說明可能的錯誤回應。 「200 確定」狀態是成功的回應，而且存取權杖會包含在回應主體 JSON 中 (在 access_token 元素中)。

| 狀態碼 | 錯誤 | 錯誤說明 | 方案 |
| ----------- | ----- | ----------------- | -------- |
| 400 不正確的要求 | invalid_resource | AADSTS50001：在名為 \<TENANT-ID\> 的租用戶中找不到名為 \<URI\> 的應用程式。 如果租用戶的系統管理員尚未安裝此應用程式或租用戶中的任何使用者尚未同意使用此應用程式，也可能會發生此錯誤。 您可能會將驗證要求傳送至錯誤的租用戶。\ | (僅限 Linux) |
| 400 不正確的要求 | bad_request_102 | 未指定必要的中繼資料標頭 | 要求中遺漏 `Metadata` 要求標頭欄位，或欄位的格式不正確。 值必須指定為 `true` (全部小寫)。 相關範例請參閱[前一節 REST](#rest) 中的「範例要求」。|
| 401 未經授權 | unknown_source | 未知的來源 *\<URI\>* | 請確認 HTTP GET 要求 URI 的格式正確。 `scheme:host/resource-path` 部分必須指定為 `http://localhost:50342/oauth2/token`。 相關範例請參閱[前一節 REST](#rest) 中的「範例要求」。|
|           | invalid_request | 要求遺漏必要參數、包含無效參數值、多次包含某個參數或格式不正確。 |  |
|           | unauthorized_client | 用戶端無權使用此方法要求存取權杖。 | 因為要求並未使用本機回送呼叫延伸模組，或是所在的虛擬機器沒有正確設定 MSI。 如果您需要設定虛擬機器的協助，請參閱[使用 Azure 入口網站設定虛擬機器受控服務識別 (MSI)](msi-qs-configure-portal-windows-vm.md)。 |
|           | access_denied | 資源擁有者或授權伺服器已拒絕要求。 |  |
|           | unsupported_response_type | 授權伺服器不支援使用此方法取得存取權杖。 |  |
|           | invalid_scope | 要求的範圍無效、未知或格式不正確。 |  |
| 500 內部伺服器錯誤 | 未知 | 無法從 Active 目錄擷取權杖。 如需詳細資訊，請參閱\<檔案路徑\>中的記錄 | 請確認虛擬機器上已正確啟用 MSI。 如果您需要設定虛擬機器的協助，請參閱[使用 Azure 入口網站設定虛擬機器受控服務識別 (MSI)](msi-qs-configure-portal-windows-vm.md)。<br><br>也請確認 HTTP GET 要求 URI 的格式正確，尤其是查詢字串中指定的資源 URI。 相關範例請參閱[前一節 REST](#rest) 中的「範例要求」，或請參閱[支援 Azure AD 驗證的 Azure 服務](msi-overview.md#azure-services-that-support-azure-ad-authentication)，以取得服務及其各自資源識別碼的清單。

## <a name="resource-ids-for-azure-services"></a>Azure 服務的資源識別碼

關於支援 Azure AD 且經過 MSI 測試的資源及其各自資源識別碼的清單，請參閱[支援 Azure AD 驗證的 Azure 服務](msi-overview.md#azure-services-that-support-azure-ad-authentication)。


## <a name="related-content"></a>相關內容

- 若要在 Azure 虛擬機器上啟用 MSI，請參閱[使用 Azure 入口網站設定虛擬機器受控服務識別 (MSI)](msi-qs-configure-portal-windows-vm.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。








