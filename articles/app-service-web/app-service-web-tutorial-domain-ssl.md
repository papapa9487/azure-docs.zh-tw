---
title: "將自訂網域和 SSL 新增至 Azure Web 應用程式 | Microsoft Docs"
description: "了解如何新增公司商標以準備將 Azure Web 應用程式移至生產環境。 將自訂網域名稱 (虛名網域) 對應至 Web 應用程式，並使用自訂 SSL 憑證保護它。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 03/29/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 535a7dbe24a1badc8539b61d34c09bdeda41ad40
ms.contentlocale: zh-tw
ms.lasthandoff: 05/25/2017

---
# <a name="add-custom-domain-and-ssl-to-an-azure-web-app"></a>將自訂網域和 SSL 新增至 Azure Web 應用程式

本教學課程示範如何快速地將自訂網域名稱對應至 Azure Web 應用程式，然後使用自訂 SSL 憑證保護它。 

## <a name="before-you-begin"></a>開始之前

執行此範例之前，請在本機安裝 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)。

對於您各自網域提供者的 DNS 設定頁面，您也需要有系統管理存取權。 例如，若要新增 `www.contoso.com`，您必須能夠設定 `contoso.com` 的 DNS 項目。

稍後，對於您想要上傳和繫結的 SSL 憑證，您需要具備有效的 .PFX 檔案_和_其密碼。 此 SSL 憑證應該設定為保護您想要的自訂網域名稱。 在上述範例中，您的 SSL 憑證應該保護 `www.contoso.com`。 

## <a name="step-1---create-an-azure-web-app"></a>步驟 1 - 建立 Azure Web 應用程式

### <a name="log-in-to-azure"></a>登入 Azure

我們現在會在終端機視窗中使用 Azure CLI 2.0 ，以建立在 Azure 中裝載 Node.js 應用程式所需的資源。  使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>建立資源群組   
使用 [az group create](/cli/azure/group#create) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源 (如 Web 應用程式、資料庫和儲存體帳戶) 的邏輯容器。 

```azurecli
az group create --name myResourceGroup --location westeurope 
```

若要查看您可用於 `---location` 的可能值，請使用 `az appservice list-locations` Azure CLI 命令。

## <a name="create-an-app-service-plan"></a>建立應用程式服務方案

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令來建立 App Service 方案。 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

下列範例使用**基本**定價層，建立名為 `myAppServicePlan` 的 App Service 方案。

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1

建立 App Service 方案後，Azure CLI 會顯示類似下列範例的資訊。 

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 1, 
    "family": "B", 
    "name": "B1", 
    "tier": "Basic" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

## <a name="create-a-web-app"></a>建立 Web 應用程式

現已建立 App Service 方案，請在 `myAppServicePlan` App Service 方案中建立 Web 應用程式。 此 Web 應用程式會為您提供裝載空間來部署程式碼，也會提供 URL 讓您檢視已部署的應用程式。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令建立 Web 應用程式。 

在下列命令中，請將 `<app_name>` 預留位置替換成您自己的唯一應用程式名稱。 這個唯一名稱將用來作為 Web 應用程式預設網域名稱的一部分，因此，這個名稱在 Azure 的所有應用程式中必須是唯一的。 您稍後先將任何自訂 DNS 項目對應至 Web 應用程式，再將它公開給使用者。 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan 
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的資訊。 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

從 JSON 輸出中，`defaultHostName` 會顯示 Web 應用程式的預設網域名稱。 在瀏覽器中，瀏覽至此位址。

```
http://<app_name>.azurewebsites.net 
``` 
 
![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-created.png)  

## <a name="step-2---configure-dns-mapping"></a>步驟 2 - 設定 DNS 對應

在此步驟中，您將新增從自訂網域至 Web 應用程式預設網域名稱 (`<app_name>.azurewebsites.net`) 的對應。 一般而言，您可以在網域提供者的網站中執行此步驟。 每個網域註冊機構的網站稍有不同，請查閱您的提供者文件。 不過，以下有幾個一般方針。 

### <a name="navigate-to-to-dns-management-page"></a>瀏覽至 DNS 管理頁面

首先，登入網域註冊機構的網站。  

然後，尋找 DNS 記錄的管理頁面。 在網站中尋找標示為**網域名稱**、**DNS** 或**名稱伺服器管理**的連結或區域。 通常可透過檢視您的帳戶資訊，然後尋找 **我的網域**之類的連結來找到此連結。

找到此頁面之後，尋找可讓您新增或編輯 DNS 記錄的連結。 這可能是 [區域檔案]、[DNS 記錄] 連結，或 [進階設定] 連結。

### <a name="create-a-cname-record"></a>建立 CNAME 記錄

新增 CNAME 記錄，將所需的子網域名稱對應至 Web 應用程式的預設網域名稱 (`<app_name>.azurewebsites.net`，其中 `<app_name>` 是應用程式的唯一名稱)。

在 `www.contoso.com` 範例中，建立 CNAME 將 `www` 主機名稱對應至 `<app_name>.azurewebsites.net`。

## <a name="step-3---configure-the-custom-domain-on-your-web-app"></a>步驟 3 - 在 Web 應用程式上設定自訂網域

當您在網域提供者的網站中完成設定主機名稱對應時，就可以準備在 Web 應用程式上設定自訂網域。 使用 [az appservice web config hostname add](/cli/azure/appservice/web/config/hostname#add) 命令新增這項設定。 

在下列命令，請將 `<app_name>` 替換成唯一的應用程式名稱，將 <your_custom_domain> 替換成完整的自訂網域名稱 (例如 `www.contoso.com`)。 

```azurecli
az appservice web config hostname add --webapp <app_name> --resource-group myResourceGroup --name <your_custom_domain>
```

自訂網域現在已完全對應至您的 Web 應用程式。 在瀏覽器中，瀏覽至自訂網域名稱。 例如：

```
http://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-custom-domain.png)  

## <a name="step-4---bind-a-custom-ssl-certificate-to-your-web-app"></a>步驟 4 - 將自訂 SSL 憑證繫結至 Web 應用程式

您現在有 Azure Web 應用程式，在瀏覽器的網址列中也有您要的網域名稱。 但是，如果您現在就瀏覽至 `https://<your_custom_domain>`，則會發生憑證錯誤。 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-cert-error.png)  

發生此錯誤是因為 Web 應用程式尚無符合自訂網域名稱的 SSL 憑證繫結。 不過，如果瀏覽至 `https://<app_name>.azurewebsites.net`，則不會發生錯誤。 這是因為預設會使用 `*.azurewebsites.net` 萬用字元網域的 SSL 憑證，保護您的應用程式和所有 Azure App Service 應用程式。 

若要以自訂網域名稱存取 Web 應用程式的，您需要將自訂網域的 SSL 憑證繫結至 Web 應用程式。 您將在此步驟中這樣做。 

### <a name="upload-the-ssl-certificate"></a>上傳 SSL 憑證

使用 [az appservice web config ssl upload](/cli/azure/appservice/web/config/ssl#upload) 命令，將自訂網域的 SSL 憑證上傳至您的 Web 應用程式。

在下列命令中，請將 `<app_name>` 替換成唯一的應用程式名稱，將 `<path_to_ptx_file>` 替換成 .PFX 檔案的路徑，將 `<password>` 替換成憑證的密碼。 

```azurecli
az appservice web config ssl upload --name <app_name> --resource-group myResourceGroup --certificate-file <path_to_pfx_file> --certificate-password <password> 
```

上傳憑證之後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "cerBlob": null,
  "expirationDate": "2018-03-29T14:12:57+00:00",
  "friendlyName": "",
  "hostNames": [
    "www.contoso.com"
  ],
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/cert
ificates/9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "issueDate": "2017-03-29T14:12:57+00:00",
  "issuer": "www.contoso.com",
  "keyVaultId": null,
  "keyVaultSecretName": null,
  "keyVaultSecretStatus": "Initialized",
  "kind": null,
  "location": "West Europe",
  "name": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "password": null,
 "pfxBlob": null,
  "publicKeyHash": null,
  "resourceGroup": "myResourceGroup",
  "selfLink": null,
  "serverFarmId": null,
  "siteName": null,
  "subjectName": "www.contoso.com",
  "tags": null,
  "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00",
  "type": "Microsoft.Web/certificates",
  "valid": null
}
```

從 JSON 輸出中，`thumbprint` 會顯示您已上傳之憑證的指紋。 複製它的值供下一個步驟使用。

### <a name="bind-the-uploaded-ssl-certificate-to-the-web-app"></a>將上傳的 SSL 憑證繫結至 Web 應用程式

Web 應用程式現在有您想要的自訂網域名稱，也有可保護該自訂網域的 SSL 憑證。 剩下的工作就是將已上傳的憑證繫結至 Web 應用程式。 您可以使用 [az appservice web config ssl bind](/cli/azure/appservice/web/config/ssl#bind) 命令這樣做。

在下列命令中，請將 `<app_name>` 替換成唯一的應用程式名稱，將 `<thumbprint-from-previous-output>` 替換成您從上一個命令取得的憑證指紋。 

az appservice web config ssl bind --name <app_name> --resource-group myResourceGroup --certificate-thumbprint <thumbprint-from-previous-output> --ssl-type SNI

憑證繫結至 Web 應用程式之後，Azure CLI 會顯示類似下列範例的資訊：

{ "availabilityState": "Normal", "clientAffinityEnabled": true, "clientCertEnabled": false, "cloningInfo": null, "containerSize": 0, "dailyMemoryTimeQuota": 0, "defaultHostName": "<app_name>.azurewebsites.net", "enabled": true, "enabledHostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net", "<app_name>.scm.azurewebsites.net" ], "gatewaySiteName": null, "hostNameSslStates": [ { "hostType": "Standard", "name": "<app_name>.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Repository", "name": "<app_name>.scm.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Standard", "name": "www.contoso.com", "sslState": "SniEnabled", "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00", "toUpdate": null, "virtualIp": null } ], "hostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net" ], "hostNamesDisabled": false, "hostingEnvironmentProfile": null, "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/site s/<app_name>", "isDefaultContainer": null, "kind": "WebApp", "lastModifiedTimeUtc": "2017-03-29T14:36:18.803333", "location": "West Europe", "maxNumberOfWorkers": null, "microService": "false", "name": "<app_name>", "outboundIpAddresses": "13.94.143.57,13.94.136.57,40.68.199.146,13.94.138.55,13.94.140.1", "premiumAppDeployed": null, "repositorySiteName": "<app_name>", "reserved": false, "resourceGroup": "myResourceGroup", "scmSiteAlsoStopped": false, "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsof t.Web/serverfarms/myAppServicePlan", "siteConfig": null, "slotSwapStatus": null, "state": "Running", "suspendedTill": null, "tags": null, "targetSwapSlot": null, "trafficManagerHostNames": null, "type": "Microsoft.Web/sites", "usageState": "Normal" }

在瀏覽器中，瀏覽至自訂網域名稱的 HTTPS 端點。 例如：

```
https://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-ssl-success.png)  

## <a name="more-resources"></a>其他資源

[購買自訂網域名稱並在 Azure App Service 中設定](custom-dns-web-site-buydomains-web-app.md)
[購買並設定 Azure App Service 的 SSL 憑證](web-sites-purchase-ssl-web-site.md)

