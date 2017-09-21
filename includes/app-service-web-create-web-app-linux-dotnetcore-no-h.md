在 Cloud Shell 中，使用 [az webapp create](/cli/azure/webapp#create) 命令，在 [ App Service 方案中建立 ](../articles/app-service/containers/app-service-linux-intro.md)Web 應用程式`myAppServicePlan`。 別忘了以唯一的應用程式名稱取代 `<app name>`。

下列命令中的執行階段會設定為 `dotnetcore|1.1`。 若要查看所有支援的執行階段，請執行 [az webapp list-runtimes](/cli/azure/webapp#list-runtimes)。 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "dotnetcore|1.1" --deployment-local-git
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的輸出：

```json
Local git is configured with url of 'https://<username>@<app name>.scm.azurewebsites.net/<app name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

您已在 Linux 容器中建立空的新 Web 應用程式，並已啟用 Git 部署。

> [!NOTE]
> Git 遠端的 URL 會顯示在 `deploymentLocalGitUrl` 屬性中，其格式為 `https://<username>@<app name>.scm.azurewebsites.net/<app name>.git`。 儲存此 URL，稍後您將會用到此資訊。
>
