---
title: "如何使用 Azure VM 受管理服務識別來登入和取得權杖"
description: "提供如何使用 Azure VM MSI 服務主體來登入和取得存取權杖的逐步指示。"
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
ms.date: 10/17/2017
ms.author: bryanla
ms.openlocfilehash: 905e7b0d8a0c45c98a86882a8c8f387be0950f9f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>如何使用 Azure VM 受管理服務識別 (MSI) 來登入和取得權杖 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)] 在 Azure VM 上啟用 MSI 之後，您可以使用 MSI 來登入和要求存取權杖。 本文說明各種方法，讓您使用 MSI [服務主體](develop/active-directory-dev-glossary.md#service-principal-object)來登入，以及取得[僅限應用程式存取權杖](develop/active-directory-dev-glossary.md#access-token)來存取其他資源，包括：

- 從 PowerShell 或 Azure CLI 無訊息/自動登入
- 取得各種用戶端的權杖，包括 .NET、PowerShell、Bash/CURL、REST
- 使用 Azure SDK 來登入，包括 .NET、Java、Node.js、Python、Ruby

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

如果您打算使用本文中的 PowerShell 範例，請務必安裝 [Azure PowerShell 4.3.1 版](https://www.powershellgallery.com/packages/AzureRM)或更新版本。 如果您打算使用本文中的 Azure CLI 範例，您有三個選項可用：
- 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 透過 Azure CLI 程式碼區塊右上角的 [試試看] 按鈕，使用內嵌的 Azure Cloud Shell。
- 如果您想要在本機命令提示字元中使用 CLI，請[安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 或更新版本)。 


> [!IMPORTANT]
> - 本文中的所有範例程式碼/指令碼都假設用戶端在已啟用 MSI 的虛擬機器上執行。 如需有關在虛擬機器上啟用 MSI 的詳細資訊，請參閱[使用 Azure 入口網站設定虛擬機器受管理服務識別 (MSI)](msi-qs-configure-portal-windows-vm.md)，或其中一篇變化文章 (使用 PowerShell、CLI、範本或 Azure SDK)。 
> - 為了防止程式碼/指令碼範例中發生授權錯誤 (403/AuthorizationFailed)，必須提供虛擬機器範圍的「讀取者」存取權給虛擬機器的身分識別，以允許在虛擬機器上執行 Azure Resource Manager 作業。 如需詳細資訊，請參閱[使用 Azure 入口網站將受管理服務識別 (MSI) 存取權指派給資源](msi-howto-assign-access-portal.md)。
> - 繼續閱讀下列各節之前，請使用 Azure 入口網站中的 虛擬機器「連線」功能，從遠端連線至已啟用 MSI 的 VM。

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>如何使用 MSI 從 PowerShell 或 CLI 登入

從前，以指令碼自己的身分識別來執行指令碼意味著：
- 指令碼要向 Azure AD 註冊為機密/web 用戶端應用程式
- 使用應用程式的用戶端識別碼/密碼認證來登入

MSI 可讓指令碼用戶端不需要再向 Azure AD 註冊，也不必再提供認證。 在下列範例中，我們示範如何使用虛擬機器的 MSI 服務主體來登入。

### <a name="azure-powershell"></a>Azure PowerShell

下列指令碼示範如何：

- 取得 Azure VM 的 MSI 存取權杖
- 使用存取權杖在對應的 MSI 服務主體下登入 Azure AD
- 使用 MSI 服務主體呼叫 Azure Resource Manager，以取得服務主體的識別碼

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>Azure CLI

下列指令碼示範如何：

- 在虛擬機器的 MSI 服務主體下登入 Azure AD
- 使用 MSI 服務主體呼叫 Azure Resource Manager，以取得服務主體的識別碼

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>如何從 MSI 取得存取權杖

MSI 可讓用戶端應用程式/指令碼不需要再向 Azure AD 註冊，也不必再出示其用戶端識別碼和密碼以取得存取權杖。 用戶端可直接向本機 MSI 端點要求存取權杖，不必出示應用程式認證。 僅限應用程式存取權杖會核發給 MSI 服務主體，在[需要用戶端認證的服務對服務呼叫](active-directory-protocols-oauth-service-to-service.md)中適合作為持有人權杖。

在下列範例中，我們示範如何使用虛擬機器的 MSI 來取得權杖。

### <a name="net"></a>.NET

> [!IMPORTANT]
> Azure AD 驗證程式庫 (ADAL) 未與 MSI 整合。 若要使用 MSI 取得存取權杖，請對虛擬機器中的本機 MSI 端點提出要求。 如需詳細資訊，請參閱 [MSI 常見問題集和已知問題](msi-known-issues.md#frequently-asked-questions-faqs)。

```csharp
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

### <a name="azure-powershell-token"></a>Azure PowerShell

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST

範例要求：

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 元素 | 說明 |
| ------- | ----------- |
| `GET` | HTTP 指令動詞，指出您想要擷取端點中的資料。 在此案例中是 OAuth 存取權杖。 | 
| `http://localhost:50342/oauth2/token` | MSI 端點，其中 50342 是預設連接埠且可設定。 |
| `resource` | 查詢字串參數，指出目標資源的應用程式識別碼 URI。 也會出現在所核發權杖的 `aud` (對象) 宣告中。 在此範例中，我們要求權杖來存取 Azure Resource Manager，其中就包含應用程式識別碼 URI https://management.azure.com/。 |
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
| `access_token` | 所要求的存取權杖。 呼叫 REST API 時，權杖會內嵌在 `Authorization` 要求標頭欄位中成為「持有人」權杖，以允許 API 驗證呼叫端。 | 
| `refresh_token` | MSI 不使用。 |
| `expires_in` | 存取權杖從發行到過期之前持續有效的秒數。 在權杖的 `iat` 宣告中可找到發行時間。 |
| `expires_on` | 存取權杖到期的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `exp` 宣告)。 |
| `not_before` | 存取權杖生效且可被接受的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `nbf` 宣告)。 |
| `resource` | 要求存取權杖所針對的資源，符合要求的 `resource` 查詢字串參數。 |
| `token_type` | 權杖的類型，即「持有人」存取權杖，表示此權杖的持有人可以存取資源。 |

## <a name="how-to-use-msi-with-azure-sdk-libraries"></a>如何搭配使用 Azure SDK 程式庫和 MSI

Azure 透過一系列的 [Azure SDK](https://azure.microsoft.com/downloads) 來支援多個程式設計平台。 其中許多都已更新來支援使用 MSI 登入，還提供對應的範例來示範用法。 這份清單會隨著新增額外的支援而更新：

| SDK | 範例 |
| --- | ------ | 
| .NET | [使用受管理的服務識別從 Windows VM 部署 ARM 範本](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core | [使用受管理的服務識別從 Linux VM 呼叫 Azure 服務](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js| [使用受管理服務識別 (MSI) 來管理資源](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python | [使用 MSI 直接從虛擬機器內部驗證](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby   | [從已啟用 MSI 的虛擬機器管理資源](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) | 

## <a name="additional-information"></a>其他資訊

### <a name="token-expiration"></a>權杖到期

本機 MSI 子系統會快取權杖。 因此，您可以視需要呼叫它，而只有在下列情況，線上呼叫 Azure AD 才會有結果：
- 由於快取中沒有權杖而發生快取遺漏
- 權杖已過期

如果您在程式碼中快取權杖，則應該妥善處理資源指出權杖過期的情節。

### <a name="resource-ids-for-azure-services"></a>Azure 服務的資源識別碼

關於支援 MSI 的服務及其各自資源識別碼的清單，請參閱[支援 Azure AD 驗證的 Azure 服務](msi-overview.md#azure-services-that-support-azure-ad-authentication)。

## <a name="troubleshooting"></a>疑難排解

### <a name="sign-in-or-token-acquisition-fails"></a>登入或取得權杖失敗

請確認已正確啟用 MSI。 返回 [Azure 入口網站](https://portal.azure.com)中的 Azure VM，然後：

- 查看 [組態] 頁面，並確定已啟用的 MSI = "Yes"。
- 查看 [延伸模組] 頁面，並確定已成功部署 MSI 延伸模組。

如果其中一者不正確，您可能需要再次在資源上重新部署 MSI，或針對部署失敗進行疑難排解。

### <a name="http-request-error-responses"></a>HTTP 要求錯誤回應

- 「bad_request_102：未指定必要的中繼資料標頭」

  要求中遺漏 `Metadata` 要求標頭欄位，或欄位的格式不正確。 值必須指定為 `true` (全部小寫)。 相關範例請參閱[前一節 REST](#rest) 中的「範例要求」。

- unknown：無法從 Active directory 擷取權杖。如需詳細資訊，請參閱\<檔案路徑\>中的記錄

  請確認 HTTP GET 要求 URI 的格式正確，尤其是查詢字串中指定的資源 URI。 相關範例請參閱[前一節 REST](#rest) 中的「範例要求」，或請參閱[支援 Azure AD 驗證的 Azure 服務](msi-overview.md#azure-services-that-support-azure-ad-authentication)，以取得服務及其各自資源識別碼的清單。

- unknown_source：未知的來源 \<URI\>

  請確認 HTTP GET 要求 URI 的格式正確。 `scheme:host/resource-path` 部分必須指定為 `http://localhost:50342/oauth2/token`。 相關範例請參閱[前一節 REST](#rest) 中的「範例要求」。

## <a name="related-content"></a>相關內容

- 如需 MSI 的概觀，請參閱[受管理的服務識別概觀](msi-overview.md)。
- 若要在 Azure VM 上啟用 MSI，請參閱[使用 PowerShell 設定 Azure VM 受管理的服務身分識別 (MSI)](msi-qs-configure-powershell-windows-vm.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

