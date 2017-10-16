---
title: "從 Azure CLI 建立您的第一個函式 | Microsoft Docs"
description: "了解如何使用 Azure CLI 來建立您的第一個 Azure 函式以進行無伺服器執行。"
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 08/22/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: ab35963dc9d10134799270e6ab3e6593be0e601a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-function-using-the-azure-cli"></a>在 Azure CLI 建立您的第一個函式

本快速入門教學課程會逐步解說如何使用 Azure Functions 來建立您的第一個函式。 您會使用 Azure CLI 來建立函式應用程式，此應用程式是主控函式的無伺服器基礎結構。 函式程式碼本身是從 GitHub 的範例存放庫部署而來的。    

您可以使用 Mac、Windows 或 Linux 電腦，依照下面步驟操作。 

## <a name="prerequisites"></a>必要條件 

在執行此範例之前，您必須具備下列項目︰

+ 作用中的 [GitHub](https://github.com) 帳戶。 
+ 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源 (如函式應用程式、資料庫和儲存體帳戶) 的邏輯容器。

下列範例會建立名為 `myResourceGroup` 的資源群組。  
如果您未使用 Cloud Shell，請先使用 `az login` 登入。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```


## <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

函式會使用 Azure 儲存體帳戶來維護函式的狀態和其他資訊。 在使用 [az storage account create](/cli/azure/storage/account#create) 命令所建立的資源群組中建立儲存體帳戶。

在下列命令中，使用全域唯一儲存體帳戶名稱來替代您看見 `<storage_name>` 預留位置的地方。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

建立儲存體帳戶後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```

## <a name="create-a-function-app"></a>建立函數應用程式

您必須擁有函式應用程式以便主控函式的執行。 函式應用程式會提供環境來讓您的函式程式碼進行無伺服器執行。 它可讓您將多個函式群組為邏輯單位，以方便您管理、部署和共用資源。 使用 [az functionapp create](/cli/azure/functionapp#create) 命令來建立函式應用程式。 

在下列命令中，使用唯一函式應用程式名稱來替代您看見 `<app_name>` 預留位置的地方，並使用儲存體帳戶名稱來替代 `<storage_name>`。 `<app_name>` 會作為函式應用程式的預設 DNS 網域，所以此名稱在 Azure 的所有應用程式中都必須是唯一的名稱。 

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--consumption-plan-location westeurope
```
根據預設，所建立的函式應用程式會使用「取用」主控方案，也就是說，您的函式會根據需要來動態新增資源，並且只有在函式執行時才會產生費用。 如需詳細資訊，請參閱[選擇正確的主控方案](functions-scale.md)。 

建立函式應用程式後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

您已經擁有函式應用程式，接下來您可以從 GitHub 的範例存放庫來部署實際的函式程式碼。

## <a name="deploy-your-function-code"></a>部署函式程式碼  

有多種方式可以在新的函式應用程式中建立函式程式碼。 本主題連結到 GitHub 中的範例存放庫。 和先前一樣，請在下列程式碼中將 `<app_name>` 預留位置改為您所建立之函式應用程式的名稱。 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --branch master \
--repo-url https://github.com/Azure-Samples/functions-quickstart \
--manual-integration 
```
設定部署來源之後，Azure CLI 會顯示類似下列範例的資訊 (已移除 Null 值以方便閱讀)︰

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

## <a name="test-the-function"></a>測試函式

在 Mac 或 Linux 電腦上使用 cURL 來測試已部署的函式，在 Windows 上則請使用 Bash。 執行下列 cURL 命令時，但請將其中的 `<app_name>` 預留位置改為函式應用程式的名稱。 將查詢字串 `&name=<yourname>` 附加至 URL。

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![瀏覽器顯示的函式回應。](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

如果您的命令列無法使用 cURL，在網頁瀏覽器的位址中輸入相同 URL 即可。 同樣地，請將 `<app_name>` 預留位置改為函式應用程式的名稱，然後對 URL 附加查詢字串 `&name=<yourname>` 並執行要求。 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![瀏覽器顯示的函式回應。](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門或教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列命令來刪除本快速入門建立的所有資源：

```azurecli-interactive
az group delete --name myResourceGroup
```
在出現提示時輸入 `y`。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
