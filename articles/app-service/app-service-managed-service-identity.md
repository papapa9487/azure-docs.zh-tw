---
title: "App Service 和 Azure Functions 中的受管理服務識別 | Microsoft Docs"
description: "Azure App Service 和 Azure Functions 中受管理服務識別支援的概念參考和設定指南"
services: app-service
author: mattchenderson
manager: cfowler
editor: 
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/13/2017
ms.author: mahender
ms.openlocfilehash: 6b2dcaa4b0e0f59bf8a632b48813ba6a24202ec5
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="how-to-use-azure-managed-service-identity-public-preview-in-app-service-and-azure-functions"></a>如何在 App Service 和 Azure Functions 中使用 Azure 受管理服務身分識別 (公開預覽)

> [!NOTE] 
> 適用於 App Service 和 Azure Functions 的受管理服務識別目前為預覽版本。

本主題示範如何為 App Service 和 Azure Functions 應用程式建立受管理應用程式身分識別，以及如何使用它來存取其他資源。 Azure Active Directory 的受管理服務識別，可讓應用程式輕鬆存取其他受到 AAD 保護的資源 (如 Azure Key Vault)。 身分識別由 Azure 平台負責管理，因此您不需要佈建或輪替任何密碼。 如需受管理服務識別的詳細資訊，請參閱[受管理的服務識別概觀](../active-directory/msi-overview.md)。

## <a name="creating-an-app-with-an-identity"></a>建立採用身分識別的應用程式

若要建立採用身分識別的應用程式，您必須在應用程式上設定額外的屬性。

> [!NOTE] 
> 只有網站的主要位置才會收到身分識別。 尚不支援部署位置的受管理服務識別。


### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

若要在入口網站中設定受管理的服務識別，您需要先像平常一樣建立應用程式，然後再啟用功能。

1. 像平常一樣在入口網站中建立應用程式。 在入口網站中瀏覽至該應用程式。

2. 如果您使用函式應用程式，請瀏覽至 [平台功能]。 若使用類型的應用程式，請在左側導覽列中向下捲動到 [設定]。

3. 選取 [受管理的服務識別]。

4. 將 [向 Azure Active Directory 註冊應用程式] 切換為 [開啟]。 按一下 [儲存] 。

![App Service 中的受管理服務識別](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>使用 Azure CLI

若要使用 Azure CLI 設定受控服務識別，您必須對現有的應用程式使用 `az webapp assign-identity` 命令。 有三個選項可供您執行本節中的範例︰

- 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 請透過下方每個程式碼區塊右上角的 [立即試用] 按鈕，使用內嵌的 Azure Cloud Shell。
- 如果您需要使用本機的 CLI 主控台，請[安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.21 或更新版本)。 

下列步驟將逐步引導您建立 web 應用程式，並使用 CLI 指派身分識別給它：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/#login) 登入 Azure。 使用您要部署應用程式且已與 Azure 訂用帳戶相關聯的帳戶：

    ```azurecli-interactive
    az login
    ```
2. 使用 CLI 建立 web 應用程式。 如需如何使用 CLI 搭配 App Service 的相關範例，請參閱 [App Service CLI 範例](../app-service/app-service-cli-samples.md)：

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myplan --resource-group myResourceGroup --sku S1
    az webapp create --name myapp --resource-group myResourceGroup --plan myplan
    ```

3. 執行 `assign-identity` 命令來建立此應用程式的身分識別：

    ```azurecli-interactive
    az webapp assign-identity --name myApp --resource-group myResourceGroup
    ```

### <a name="using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本

您可以使用 Azure Resource Manager 範本來將 Azure 資源的部署自動化。 若要深入了解如何部署到 App Service 和 Functions，請參閱[在 App Service 中將資源部署自動化](../app-service/app-service-deploy-complex-application-predictably.md)和[在 Azure Functions 中將資源部署自動化](../azure-functions/functions-infrastructure-as-code.md)。

對於所有 `Microsoft.Web/sites` 型別的資源來說，您可以在資源定義中加入以下屬性，以建立採用身分識別的資源：
```json
"identity": {
    "type": "SystemAssigned"
}    
```

如此能告訴 Azure 該如何建立及管理應用程式的身分識別。

例如，Web 應用程式可能與下圖中的範例相似：
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

當網站建立時，它擁有以下額外屬性：
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

其中，`<TENANTID>` 和 `<PRINCIPALID>` 會取代為 GUID。 tenantId 屬性能辨識應用程式隸屬的 AAD 租用戶。 principalId 是應用程式新身分識別的唯一識別碼。 在 AAD 內，應用程式的名稱與您提供給 App Service 或 Azure Functions 執行個體的名稱相同。

## <a name="obtaining-tokens-for-azure-resources"></a>取得 Azure 資源的權杖

應用程式可以使用自己的身分識別來取得權杖，存取其他受 AAD 保護的資源 (如 Azure Key Vault)。 這些權杖代表存取資源的應用程式，而不是任何特定的應用程式使用者。 

> [!IMPORTANT]
> 您可能需要設定目標資源，讓應用程式得以存取。 例如，如果您要求 Key Vault 的權杖，便需要確認是否已新增含有應用程式身分識別的存取原則。 否則即使呼叫含有權杖，依然會遭到拒絕。 若要深入了解哪些資源支援受管理的服務識別權杖，請參閱[支援 Azure AD 驗證的 Azure 服務](../active-directory/msi-overview.md#which-azure-services-support-managed-service-identity)。

有一個簡單的 REST 通訊協定可用來在 App Service 和 Azure Functions 中取得權杖。 對於 .NET 應用程式，Microsoft.Azure.Services.AppAuthentication 程式庫提供讓應用程式透過此通訊協定進行提取的功能，也能支援本機部署經驗。

### <a name="asal"></a>將 Microsoft.Azure.Services.AppAuthentication 程式庫運用在 .NET 上

對於 .NET 應用程式和函式來說，採用受管理服務識別最簡單的方法就是透過 Microsoft.Azure.Services.AppAuthentication 套件。 該程式庫還能讓您使用來自 Visual Studio、[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) 或 Active Directory 整合式驗證的使用者帳戶，在部署機器上以本機方式測試程式碼。 如需使用此程式庫之本機開發選項的詳細資訊，請參閱 [Microsoft.Azure.Services.AppAuthentication 參考]。 本節示範如何在您的程式碼中開始使用程式庫。

1. 將 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 和 [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 套件的參考新增至應用程式。

2.  將下列程式碼新增至應用程式：

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

若要深入了解 Microsoft.Azure.Services.AppAuthentication 和它公開的作業，請參閱 [Microsoft.Azure.Services.AppAuthentication 參考]與[採用 MSI 的 App Service 和 KeyVault .NET 範例](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)。

### <a name="using-the-rest-protocol"></a>使用 REST 通訊協定

採用受管理服務識別的應用程式有兩個已定義的環境變數：
- MSI_ENDPOINT
- MSI_SECRET

**MSI_ENDPOINT** 是應用程式要求權杖的來源本機 URL。 若要取得資源的權杖，請向該端點提出包含以下參數的 HTTP GET 要求：

> [!div class="mx-tdBreakAll"]
> |參數名稱|在|說明|
> |-----|-----|-----|
> |resource|查詢|資源的 AAD 資源 URI，也就是要取得權杖的目標資源。|
> |api-version|查詢|要使用的權杖 API 版本。 目前唯一支援的版本為 "2017-09-01"。|
> |secret|標頭|MSI_SECRET 環境變數的值。|


成功的 200 OK 回應包括含以下屬性的 JSON 本文：

> [!div class="mx-tdBreakAll"]
> |屬性名稱|說明|
> |-------------|----------|
> |access_token|所要求的存取權杖。 呼叫端 Web 服務可以使用此權杖來向接收端 Web 服務進行驗證。|
> |expires_on|存取權杖的到期時間。 日期會表示為從 1970-01-01T0:0:0Z UTC 至到期時間的秒數。 這個值用來判斷快取權杖的存留期。|
> |resource|接收端 Web 服務的應用程式識別碼 URI。|
> |token_type|表示權杖類型值。 Azure AD 唯一支援的類型是 Bearer。 如需持有人權杖的詳細資訊，請參閱 [OAuth 2.0 授權架構︰持有人權杖用法 (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)。|


該回應與 [AAD 服務對服務存取權杖要求的回應](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#service-to-service-access-token-response)。

> [!NOTE] 
> 環境變數會在程序初次啟動時進行設定，因此您在應用程式上啟用受管理的服務識別前，可能需要重新啟動應用程式或重新部署其程式碼，才可讓您的程式碼使用 `MSI_ENDPOINT` 和 `MSI_SECRET`。

### <a name="rest-protocol-examples"></a>REST 通訊協定範例
範例要求如下所示：
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
範例回應如下所示：
```
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>程式碼範例
若要以 C# 提出這項要求：
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> 對於 .NET 語言，您也可以使用 [Microsoft.Azure.Services.AppAuthentication](#asal) 而不需要自行製作要求。

以 Node.JS 表示：
```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

在 PowerShell 中：
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```


[Microsoft.Azure.Services.AppAuthentication 參考]: https://go.microsoft.com/fwlink/p/?linkid=862452
