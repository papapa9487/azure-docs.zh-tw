在 Cloud Shell 中，使用 [az webapp create](/cli/azure/webapp#create) 命令，在 [ App Service 方案中建立 ](../articles/app-service/app-service-web-overview.md)Web 應用程式`myAppServicePlan`。 

Web 應用程式會為您的程式碼提供裝載空間，以及提供 URL 來檢視已部署的應用程式。

在下列命令中，使用唯一的名稱 (有效的字元為 `a-z`、`0-9` 和 `-`) 取代 *\<app_name>*。 如果 `<app_name>` 不是唯一的，您會收到錯誤訊息「具有指定名稱 <app_name> 的網站已經存在」。 Web 應用程式的預設 URL 是 `https://<app_name>.azurewebsites.net`。 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的資訊：

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

您已建立空的 Web 應用程式，其中已啟用 Git 部署。

> [!NOTE]
> Git 遠端的 URL 會顯示在 `deploymentLocalGitUrl` 屬性中，其格式為 `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`。 儲存此 URL，稍後您將會用到此資訊。
>

瀏覽至網站以查看您剛建立的 Web 應用程式。

```bash
http://<app_name>.azurewebsites.net
```
