在 Cloud Shell 中，使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令來建立 App Service 方案。

[!INCLUDE [app-service-plan](app-service-plan-linux.md)]

下列範例會在**標準**定價層和 Linux 容器中，建立名為 `myAppServicePlan` 的 App Service 方案：

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

建立 App Service 方案後，Azure CLI 會顯示類似下列範例的資訊：

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```