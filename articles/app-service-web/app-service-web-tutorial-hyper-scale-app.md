---
title: "在 Azure 中建置超大規模應用程式 | Microsoft Docs"
description: "了解如何使用不同的 Azure 服務，將 Azure 中的 ASP.NET 應用程式效能最大化。"
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: erikre
editor: 
ms.assetid: a4d49ac7-0f97-4997-84c5-cdb9c4465757
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2017
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 17d046f4f5291233cc5dc29c96e2b68d767d5750
ms.contentlocale: zh-tw
ms.lasthandoff: 09/09/2017

---
# <a name="build-a-hyper-scale-web-app-in-azure"></a>在 Azure 中建置超大規模 Web 應用程式

本教學課程示範如何相應放大 Azure 中的 ASP.NET Web 應用程式，以處理最多的使用者要求。

開始本教學課程之前，請確認您的電腦上[已安裝 Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)。 此外，本機電腦上還需要 [Visual Studio](https://www.visualstudio.com/vs/) 以執行範例應用程式。

## <a name="step-1---get-sample-application"></a>步驟 1 - 取得範例應用程式
在此步驟中，您要設定本機的 ASP.NET 專案。

### <a name="clone-the-application-repository"></a>複製應用程式存放庫

開啟您選擇的命令列終端機，並 `CD` 到工作目錄。 然後，執行下列命令來複製範例應用程式。 

```powershell
git clone https://github.com/cephalin/HighScaleApp.git
```

### <a name="run-the-sample-application-in-visual-studio"></a>在 Visual Studio 中執行範例應用程式

在 Visual Studio 中開啟解決方案。

```powershell
cd HighScaleApp
.\HighScaleApp.sln
```

輸入 `F5` 執行應用程式。

此範例 ASP.NET Web 應用程式來自預設範本，可保存使用者工作階段，且使用輸出快取。 請看 `HighScaleApp\Controllers\HomeController.cs`。 `Index()` 方法將一份資料新增至工作階段。

```csharp
Session.Add("visited", "true"); 
```

`About()` 和 `Contact()` 方法會快取其輸出。

```csharp
[OutputCache(Duration = 60)]
```

## <a name="step-2---deploy-to-azure"></a>步驟 2 - 部署至 Azure
在此步驟中，您將建立 Azure Web 應用程式，然後在其中部署範例 ASP.NET 應用程式。

### <a name="create-a-resource-group"></a>建立資源群組   
使用 [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) 在西歐區域建立[資源群組](../azure-resource-manager/resource-group-overview.md)。 您想要一起管理的所有 Azure 資源都放在資源群組中，例如 Web 應用程式和任何 SQL Database 後端。

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

若要查看可用於 `---location` 的可能值，請使用 [az appservice list-locations](https://docs.microsoft.com/en-us/cli/azure/appservice#az_appservice_list_locations) 命令。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案
使用 [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#az_appservice_plan_create) 建立 "B1" [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1
```

App Service 方案是縮放單位，可包含您想在相同 App Service 基礎結構上一起相應增加或相應放大的應用程式，不限數目。 每個方案也被指派[定價層](https://azure.microsoft.com/en-us/pricing/details/app-service/)。 較高層包括更好的硬體和更多功能，例如更多相應放大的執行個體。

本教學課程中，B1 是可相應放大至三個執行個體的最低層。 稍後，您隨時可以執行 [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_update)，在定價層上移或下移您的應用程式。 

### <a name="create-a-web-app"></a>建立 Web 應用程式
使用 [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_create)，以 `$appName` 中的唯一名稱建立 Web 應用程式。

```azurecli
$appName = "<replace-with-a-unique-name>"
az appservice web create --name $appName --resource-group myResourceGroup --plan myAppServicePlan
```

### <a name="set-deployment-credentials"></a>設定部署認證
使用 [az appservice web deployment user set](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/user#az_appservice_web_deployment_user_set) 為 App Service 設定您的帳戶等級部署認證。

```azurecli
az appservice web deployment user set --user-name <letters-numbers> --password <mininum-8-char-captital-lowercase-letters-numbers>
```

### <a name="configure-git-deployment"></a>設定 Git 部署
使用 [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config_local_git) 設定本機 Git 部署。

```azurecli
az appservice web source-control config-local-git --name $appName --resource-group myResourceGroup
```

此命令會產生如下的輸出︰

```json
{
  "url": "https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git"
}
```

使用傳回的 URL 來設定 Git 遠端。 下列命令使用上述的輸出範例。

```powershell
git remote add azure https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-the-sample-application"></a>部署範例應用程式
您現在已準備好部署範例應用程式。 執行 `git push`。

```powershell
git push azure master
```

提示您輸入密碼時，請使用您執行 `az appservice web deployment user set` 時所指定的密碼。

### <a name="browse-to-azure-web-app"></a>瀏覽至 Azure Web 應用程式
使用 [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_browse)查看您正在 Azure 中執行的應用程式，請執行這個命令。

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-3---connect-to-redis"></a>步驟 3 - 連線至 Redis
在此步驟中，您要將 Azure Redis 快取設定為外部、共置的快取給 Azure Web 應用程式。 您可以快速利用 Redis 來快取頁面輸出。 此外，當您稍後相應放大 Web 應用程式時，Redis 會協助您跨多個執行個體可靠地保存使用者工作階段。

### <a name="create-an-azure-redis-cache"></a>建立 Azure Redis 快取
使用 [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_create) 建立 Azure Redis 快取和儲存 JSON 輸出。 在 `$cacheName` 中使用唯一名稱。

```powershell
$cacheName = "<replace-with-a-unique-cache-name>"
$redis = (az redis create --name $cacheName --resource-group myResourceGroup --location "West Europe" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

### <a name="configure-the-application-to-use-redis"></a>將應用程式設定為使用 Redis
格式化快取的連接字串。

```powershell
$connstring = "$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False"
$connstring 
```

第二行應該會產生如下的輸出：

```
mycachename.redis.cache.windows.net:6380,password=/rQP/TLz1mrEPpmh9b/gnfns/t9vBRXqXn3i1RwBjGA=,ssl=True,abortConnect=False
```

在 Visual Studio 中，在專案根目錄中建立名為 `redis.config` 的 Web 組態檔並貼入下列程式碼。 在 `value` 中，使用 PowerShell 輸出的連接字串。

```xml
<appSettings>
  <add key="RedisConnection" value="your-azure-redis-cache-connection-string"/>
</appSettings>
```

如果您查看 Git 存放庫中的 `.gitignore` 檔案，您會看到這個檔案已自原始檔控制中排除。 於是，您的機密資訊獲得安全保障。 

開啟 `Web.config`。 請注意 `<appSettings file="redis.config">` 元素，它可取得您在 `redis.config` 中建立的設定。 

尋找包含 `<sessionState>` 和 `<caching>` 的註解區段。 將此區段取消註解。

![](./media/app-service-web-tutorial-hyper-scale-app/redisproviders.png)

此程式碼會尋找您在 `RedisConnection` 中定義的 Redis 連接字串。 

您的應用程式現在會使用 Redis 管理工作階段和快取。 輸入 `F5` 執行應用程式。 喜歡的話，您可以下載 Redis 管理用戶端，以視覺化方式檢視現在已儲存至快取的資料。

### <a name="configure-the-connection-string-in-azure"></a>在 Azure 中設定連接字串。

若要讓應用程式在 Azure 中運作，您需要在 Azure Web 應用程式中設定相同的 Redis 連接字串。 因為 `redis.config` 不是在原始檔控制中維護，它不會在您執行 Git 部署時部署至 Azure。

使用 [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#az_appservice_web_config_appsettings_update) 新增相同名稱的連接字串 (`RedisConnection`)。

az appservice web config appsettings update --settings "RedisConnection=$connstring" --name $appName --resource-group myResourceGroup

請記住，`$connstring` 包含格式化的連接字串。

### <a name="redeploy-the-application-to-azure"></a>將應用程式重新部署至 Azure
使用 Git 命令將您的變更推送至 Azure

```bash
git add .
git commit -m "now use Redis providers"
git push azure master
```

提示您輸入密碼時，請使用您執行 `az appservice web deployment user set` 時所指定的密碼。

### <a name="browse-to-the-azure-web-app"></a>瀏覽至 Azure Web 應用程式
使用 [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_browse) 查看 Azure 中生效的變更。

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-4---scale-to-multiple-instances"></a>步驟 4 - 擴充至多個執行個體
App Service 方案是 Azure Web 應用程式的縮放單位。 若要相應放大的 Web 應用程式，您可以擴充 App Service 方案。

使用 [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_update) 將 App Service 方案相應放大至三個執行個體，這是 B1 定價層允許的最大數目。 請記住，B1 是您稍早建立 App Service 方案時選擇的定價層。 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --number-of-workers 3 
```

## <a name="step-5---scale-geographically"></a>步驟 5 - 異地擴充
異地擴充時，您會在 Azure 雲端的多個區域執行應用程式。 這項設定會根據地理位置，進一步平衡應用程式的負載，並將應用程式放在更靠近用戶端瀏覽器的位置，以縮短回應時間。

在此步驟中，您可以使用 [Azure 流量管理員](https://docs.microsoft.com/en-us/azure/traffic-manager/)，將 ASP.NET Web 應用程式調整至第二個區域。 此步驟結束時，您會有一個在西歐執行的 Web 應用程式 (已建立) 和一個在東南亞執行的 Web 應用程式 (尚未建立)。 兩個應用程式都從相同的流量管理員 URL 供應。

### <a name="scale-up-the-europe-app-to-standard-tier"></a>將歐洲應用程式相應增加至標準層
在 App Service 中，需要標準定價層才能與 Azure 流量管理員整合。 使用 [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_update) 將 App Service 方案相應增加至 S1。 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --sku S1
```
### <a name="create-a-traffic-manager-profile"></a>建立流量管理員設定檔 
使用 [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) 建立流量管理員設定檔，並新增至資源群組。 在 $dnsName 中使用唯一的 DNS 名稱。

```azurecli
$dnsName = "<replace-with-unique-dns-name>"
az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name $dnsName
```

> [!NOTE]
> `--routing-method Performance` 指定此設定檔[將使用者流量路由傳送至最靠近的端點](../traffic-manager/traffic-manager-routing-methods.md)。

### <a name="get-the-resource-id-of-the-europe-app"></a>取得歐洲應用程式的資源識別碼
使用 [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_show) 取得 Web 應用程式的資源識別碼。

```azurecli
$appId = az appservice web show --name $appName --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-europe-app"></a>新增歐洲應用程式的流量管理員端點
使用 [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) 將端點新增至流量管理員設定檔，並使用 Web 應用程式的資源識別碼作為目標。

```azurecli
az network traffic-manager endpoint create --name myWestEuropeEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appId
```

### <a name="get-the-traffic-manager-endpoint-url"></a>取得流量管理員端點 URL
流量管理員設定檔現在有一個端點指向現有的 Web 應用程式。 使用 [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_show) 取得其 URL。 

```azurecli
az network traffic-manager profile show --name myTrafficManagerProfile --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
```

將輸出複製到瀏覽器。 您應該會再次看到您的 Web 應用程式。

### <a name="create-an-azure-redis-cache-in-asia"></a>在亞洲建立 Azure Redis 快取
現在，您要將 Azure Web 應用程式複寫至東南亞區域。 若要開始，請使用 [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_create) 在東南亞建立第二個 Azure Redis 快取。 此快取和您的應用程式必須共置於亞洲。

```powershell
$redis = (az redis create --name $cacheName-asia --resource-group myResourceGroup --location "Southeast Asia" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

`--name $cacheName-asia` 將快取命名為西歐快取的名稱，尾碼是 `-asia`。

### <a name="create-an-app-service-plan-in-asia"></a>在亞洲建立 App Service 方案
使用 [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create)，以西歐方案使用的相同 S1 層，在東南亞區域建立第二個 App Service 方案。

```azurecli
az appservice plan create --name myAppServicePlanAsia --resource-group myResourceGroup --location "Southeast Asia" --sku S1
```

### <a name="create-a-web-app-in-asia"></a>在亞洲建立 Web 應用程式
使用 [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_create)，建立第二個 Web 應用程式。

```azurecli
az appservice web create --name $appName-asia --resource-group myResourceGroup --plan myAppServicePlanAsia
```

`--name $appName-asia` 將應用程式命名為西歐應用程式的名稱，尾碼是 `-asia`。

### <a name="configure-the-connection-string-for-redis"></a>設定 Redis 的連接字串
使用 [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#az_appservice_web_config_appsettings_update) 將東南亞快取的連接字串新增至 Web 應用程式。

az appservice web config appsettings update --settings "RedisConnection=$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False" --name $appName-asia --resource-group myResourceGroup

### <a name="configure-git-deployment-for-the-asia-app"></a>設定亞洲應用程式的 Git 部署。
使用 [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config_local_git) 設定第二個 Web 應用程式的本機 Git 部署。

```azurecli
az appservice web source-control config-local-git --name $appName-asia --resource-group myResourceGroup
```

此命令會產生如下的輸出︰

```json
{
  "url": "https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git"
}
```

使用傳回的 URL 來設定本機存放庫的第二個 Git 遠端。 下列命令使用上述的輸出範例。

```bash
git remote add azure-asia https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-your-sample-application"></a>部署範例應用程式
執行 `git push` 將範例應用程式部署至第二個 Git 遠端。 

```bash
git push azure-asia master
```

提示您輸入密碼時，請使用您執行 `az appservice web deployment user set` 時所指定的密碼。

### <a name="browse-to-the-asia-app"></a>瀏覽至亞洲應用程式
使用 [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_browse) 驗證您的應用程式正在 Azure 中執行。

```azurecli
az appservice web browse --name $appName-asia --resource-group myResourceGroup
```

### <a name="get-the-resource-id-of-the-asia-app"></a>取得亞洲應用程式的資源識別碼
使用 [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_show) 取得 Web 應用程式在東南亞的資源識別碼。

```azurecli
$appIdAsia = az appservice web show --name $appName-asia --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-asia-app"></a>新增亞洲應用程式的流量管理員端點
使用 [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) 將第二個端點新增至流量管理員設定檔。

```azurecli
az network traffic-manager endpoint create --name myAsiaEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appIdAsia
```

### <a name="add-region-identifier-to-web-apps"></a>將區域識別碼新增至 Web 應用程式
使用 [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#az_appservice_web_config_appsettings_update) 新增區域特定的環境變數。

```azurecli
az appservice web config appsettings update --settings "Region=West Europe" --name $appName --resource-group myResourceGroup
az appservice web config appsettings update --settings "Region=Southeast Asia" --name $appName-asia --resource-group myResourceGroup
```

您的應用程式碼已使用此應用程式設定。 請看 `HighScaleApp\Views\Home\Index.cshtml`。

### <a name="complete"></a>完成！

現在，嘗試從不同地理區域的瀏覽器，存取流量管理員設定檔的 URL。 來自歐洲的用戶端瀏覽器應該會顯示「ASP.NET 西歐」，來自亞洲的用戶端瀏覽器應該會顯示「ASP.NET 東南亞」。

## <a name="more-resources"></a>其他資源

