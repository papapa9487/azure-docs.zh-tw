
使用 [az webapp create](/cli/azure/webapp#az_webapp_create) 命令，在 `myAppServicePlan` App Service 方案中建立應用程式。 

Web 應用程式會為您的 API 提供裝載空間，以及提供 URL 來檢視已部署的應用程式。

在下列命令中，使用唯一的名稱取代 *\<app_name>*。 如果 `<app_name>` 不是唯一的，您會收到錯誤訊息「具有指定名稱 <app_name> 的網站已經存在」。 Web 應用程式的預設 URL 是 `https://<app_name>.azurewebsites.net`。 

```azurecli-interactive
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
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```