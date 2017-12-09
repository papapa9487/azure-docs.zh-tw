---
title: "在 Azure 中建置 Docker Python 和 PostgreSQL Web 應用程式 | Microsoft Docs"
description: "了解如何取得在 Azure 中運作的 Docker Python 應用程式，並連線至 PostgreSQL 資料庫。"
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2017
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 161d9fda75caa7836e012e6e1ff79df576281137
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>在 Azure 中建置 Docker Python 和 PostgreSQL Web 應用程式

用於容器的 Web 應用程式提供可高度擴充、自我修復的 Web 主機服務。 本教學課程會示範如何在 Azure 中建立基本的 Docker Python Web 應用程式。 您會將此應用程式連線至 PostgreSQL 資料庫。 完成之後，您在 [Linux 上的 App Service](app-service-linux-intro.md) 上就會有 Docker 容器內執行的 Python Flask 應用程式。

![Linux 上的 App Service 中的 Docker Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

您可以在 Mac OS 上依照下列步驟進行。 Linux 和 Windows 指示在大部分情況下都相同，本教學課程對差異不加詳述。
 
## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

1. [安裝 Git](https://git-scm.com/)
1. [安裝 Python](https://www.python.org/downloads/)
1. [安裝及執行 PostgreSQL](https://www.postgresql.org/download/)
1. [安裝 Docker Community 版本](https://www.docker.com/community-edition)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="test-local-postgresql-installation-and-create-a-database"></a>測試本機 PostgreSQL 安裝並建立資料庫

開啟終端機視窗，然後執行 `psql postgres` 來連線至本機 PostgreSQL 伺服器。

```bash
psql postgres
```

如果連線成功，則您的 PostgreSQL 資料庫就已在執行中。 如果沒有，請確定您的本機 PostgresQL 資料庫已遵循[下載 - PostgresQL 核心散發](https://www.postgresql.org/download/)中的步驟來啟動。

建立名為 eventregistration 的資料庫，並且設定名為 manager、密碼為 supersecretpass 的個別資料庫使用者。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
輸入 \q 來結束 PostgreSQL 用戶端。 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>建立本機 Python Flask 應用程式

在此步驟中，您要設定本機 Python Flask 專案。

### <a name="clone-the-sample-application"></a>複製範例應用程式

開啟終端機視窗，然後 `CD` 至工作目錄。

執行下列命令來複製範例存放庫，然後前往 0.1-initialapp 版本。

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

此範例存放庫包含 [Flask](http://flask.pocoo.org/) 應用程式。 

### <a name="run-the-application"></a>執行應用程式

> [!NOTE] 
> 在稍後步驟中簡化這個程序，方法是建立可與生產環境資料庫搭配使用的 Docker 容器。

安裝必要的封裝，然後啟動應用程式。

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

當應用程式完全載入時，您會看到類似下列的訊息：

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在瀏覽器中，瀏覽至 `http://127.0.0.1:5000`。 按一下 [註冊!] 並且建立測試使用者。

![在本機執行的 Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/local-app.png)

Flask 範例應用程式會將使用者資料儲存於資料庫中。 如果您成功註冊使用者，您的應用程式會將資料寫入本機 PostgreSQL 資料庫。

如需隨時停止 Flask 伺服器，請在終端機上輸入 Ctrl+C。 

## <a name="create-a-production-postgresql-database"></a>建立生產環境 PostgreSQL 資料庫

在此步驟中，您要在 Azure 中建立 PostgreSQL 資料庫。 當您的應用程式部署至 Azure 時，它會使用此雲端資料庫。

### <a name="log-in-to-azure"></a>登入 Azure

您即將使用 Azure CLI 2.0，來建立在用於容器的 Web 應用程式中裝載 Python 應用程式所需的資源。  使用 [az login](/cli/azure/#az_login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```azurecli
az login
```

### <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](../../azure-resource-manager/resource-group-overview.md) 來建立[資源群組](/cli/azure/group#az_group_create)。

[!INCLUDE [Resource group intro](../../../includes/resource-group.md)]

下列範例會在美國西部區域中建立一個資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location "West US"
```

使用 [az appservice list-locations](/cli/azure/appservice#az_appservice_list_locations) Azure CLI 命令以列出可用的位置。

### <a name="create-an-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器

使用 [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) 命令來建立 PostgreSQL 伺服器。

在下列命令中，使用唯一的伺服器名稱取代 \<postgresql_name> 預留位置，以及用使用者名稱取代 \<admin_username> 預留位置。 這個伺服器名稱會用來作為 PostgreSQL 端點 (`https://<postgresql_name>.postgres.database.azure.com`) 的一部分，所以在 Azure 的所有伺服器中必須是唯一的名稱。 使用者名稱是用於初始資料庫管理使用者帳戶。 系統會提示您選取此使用者的密碼。

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>
```

建立適用於 PostgreSQL 的 Azure 資料庫伺服器後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則

執行下列 Azure CLI 命令，允許從所有 IP 位址存取資料庫。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

Azure CLI 確認防火牆規則建立，具有類似下列範例的輸出：

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>將 Python Flask 應用程式連線至資料庫

在此步驟中，您要將 Python Flask 範例應用程式連線至適用於您所建立之適用於 PostgreSQL 的 Azure 資料庫伺服器。

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>建立空的資料庫並設定新的資料庫應用程式使用者

建立資料庫使用者，並僅提供單一資料庫的存取權。 您會使用這些認證以避免將伺服器的完整存取權給予應用程式。

連線至資料庫 (系統會提示您輸入管理員密碼)。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

從 PostgreSQL CLI 建立資料庫和使用者。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

輸入 \q 來結束 PostgreSQL 用戶端。

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>針對 Azure PostgreSQL 資料庫本機測試應用程式

現在回到複製的 Github 存放庫 app 資料夾，您只要更新資料庫環境變數，就可以執行 Python Flask 應用程式。

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

當應用程式完全載入時，您會看到類似下列的訊息：

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在瀏覽器中，瀏覽至 http://127.0.0.1:5000 。 按一下 [註冊!] 並且建立測試註冊。 現在您要將資料寫入 Azure 中的資料庫。

![在本機執行的 Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>從 Docker 容器執行應用程式

建置 Docker 容器映像。

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker 會顯示已成功建立容器的確認。

```bash
Successfully built 7548f983a36b
```

將資料庫環境變數新增至環境變數檔案 db.env。 應用程式會連線到「適用於 PostgreSQL 的 Azure 資料庫」生產資料庫。

```text
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

從 Docker 容器內執行應用程式。 下列命令會指定環境變數檔案，並將預設 Flask 連接埠 5000 對應至本機連接埠 5000。

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

輸出類似您稍早所見的範例。 不過，不再需要執行初始資料庫移轉，因此會予以略過。

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

資料庫已包含您先前建立的註冊。

![以 Docker 容器為基礎在本機執行的 Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>將 Docker 容器上傳至容器登錄

在此步驟中，將 Docker 容器上傳至容器登錄。 使用 Azure Container Registry，但是您也可以使用 Docker Hub 等其他熱門的項目。

### <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry

在下列命令中，建立容器登錄，將 \<registry_name> 取代為您選擇的唯一 Azure Container Registry 名稱。

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

輸出

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>擷取用來推送及提取 Docker 映像的登錄認證

若要顯示登錄認證，請先啟用管理員模式。

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

您會看到兩個密碼。 請記下使用者名稱和第一個密碼。

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>將 Docker 容器上傳至 Azure Container Registry

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>將 Docker Python Flask 應用程式部署至 Azure

在此步驟中，您可以將以 Docker 容器為基礎的 Python Flask 應用程式部署至 Azure App Service。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

使用 [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) 命令來建立 App Service 方案。

[!INCLUDE [app-service-plan](../../../includes/app-service-plan-linux.md)]

下列範例會使用 S1 定價層，建立名為 myAppServicePlan 之以 Linux 為基礎的 App Service 方案：

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

建立 App Service 方案後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

### <a name="create-a-web-app"></a>建立 Web 應用程式

使用 [az webapp create](/cli/azure/webapp#az_webapp_create) 命令，在 myAppServicePlan App Service 方案中建立 Web 應用程式。

Web 應用程式會為您提供裝載空間來部署程式碼，以及提供 URL 讓您能夠檢視已部署的應用程式。 用來建立 Web 應用程式。

在下列命令中，將 \<app_name> 預留位置取代為唯一的應用程式名稱。 這個名稱是 Web 應用程式預設 URL 的一部分，因此，這個名稱在 Azure App Service 的所有應用程式中必須是唯一的。

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-the-database-environment-variables"></a>設定資料庫環境變數

稍早在本教學課程中，您定義了環境變數來連線至 PostgreSQL 資料庫。

在 App Service 中，您可以使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 命令將環境變數設定為「應用程式設定」。

下列範例會指定資料庫連線詳細資料作為應用程式設定。 它也會使用 PORT 變數，將 Docker 容器上的連接埠 5000 對應至接收連接埠 80 上的 HTTP 流量。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>設定 Docker 容器部署

AppService 會自動下載及執行 Docker 容器。

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

每當您更新 Docker 容器或變更設定時，重新啟動應用程式。 重新啟動可確保套用所有設定，以及從登錄提取最新容器。

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>瀏覽至 Azure Web 應用程式 

使用 Web 瀏覽器，瀏覽至已部署的 Web 應用程式。 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> Web 應用程式載入的時間較久，因為在容器設定變更之後，必須下載及啟動容器。

您會看到先前已註冊的來賓，儲存至上一個步驟中的 Azure 生產環境資料庫。

![以 Docker 容器為基礎在本機執行的 Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**恭喜！** 您要在 Azure App Service 中執行以 Docker 容器為基礎的 Python Flask 應用程式。

## <a name="update-data-model-and-redeploy"></a>更新資料模型並重新部署

在此步驟中，您會更新來賓模式，將出席者的人數新增至每個事件註冊。

請使用下列 git 命令查看 0.2-migration 版本：

```bash
git checkout tags/0.2-migration
```

此版本已對檢視、控制器及模型進行必要變更。 它也會包含透過 alembic (`flask db migrate`) 產生的資料庫移轉。 您可以看到透過下列 git 命令所進行的所有變更：

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>本機測試您的變更

透過執行 Flask 伺服器，可執行下列命令在本機測試您的變更。

Mac / Linux：
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

在瀏覽器中瀏覽至 http://127.0.0.1:5000 可檢視變更。 建立測試註冊。

![以 Docker 容器為基礎在本機執行的 Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>將變更發佈至 Azure

建立新的 Docker 映像、將其推送至容器登錄，並重新啟動應用程式。

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

瀏覽至 Azure Web 應用程式，然後再次嘗試執行新功能。 建立另一個事件註冊。

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure App Service 中的 Docker Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>管理您的 Azure Web 應用程式

請移至 [Azure 入口網站](https://portal.azure.com)，以查看您所建立的 Web 應用程式。

按一下左側功能表中的 [應用程式服務]，然後按一下 Azure Web 應用程式的名稱。

![入口網站瀏覽至 Azure Web 應用程式](./media/tutorial-docker-python-postgresql-app/app-resource.png)

根據預設，入口網站會顯示 Web 應用程式的 [概觀] 分頁。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 分頁左側的索引標籤會顯示您可開啟的各種設定分頁。

![Azure 入口網站中的 App Service 頁面](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>後續步驟

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至 Web 應用程式。

> [!div class="nextstepaction"]
> [將現有的自訂 DNS 名稱對應至 Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
