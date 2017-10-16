---
title: "針對 Azure Web App for Containers 執行自訂 Docker Hub 映像 | Microsoft Docs"
description: "如何針對 Azure Web App for Containers 使用自訂 Docker 映像。"
keywords: "azure app service, web 應用程式, linux, docker, 容器"
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 28b0b50c68fa7c16a354fd16b7dfd930436c6b4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-custom-docker-hub-image-in-azure-web-app-for-containers"></a>針對 Azure Web App for Containers 執行自訂 Docker Hub 映像 #

App Service 在 Linux 上提供預先定義的應用程式堆疊，且支援特定的版本，例如 PHP 7.0 和 Node.js 4.5。 您也可以使用自訂 Docker 映像，將您的 Web 應用程式部署到尚未定義在 Azure 中的應用程式堆疊。 本快速入門示範如何建立 Web 應用程式，並將 Python 的映像部署到其中。 [使用 Azure CLI 建立 Web 應用程式](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)] 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)] 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)] 

## <a name="create-a-web-app"></a>建立 Web 應用程式

使用 [az webapp create](/cli/azure/webapp#create) 命令，在 `myAppServicePlan` App Service 方案中建立 [Web 應用程式](../app-service-web-overview.md)。 別忘了以唯一的應用程式名稱取代 `<app name>`。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name elnably/dockerimagetest
```

在上述命令中，`--deployment-container-image-name` 指向公用 Docker Hub 映像 [https://hub.docker.com/r/elnably/dockerimagetest/](https://hub.docker.com/r/elnably/dockerimagetest/)。 您可以在[https://github.com/rachelappel/docker-image](https://github.com/rachelappel/docker-image) 檢查其內容。

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的輸出：

```json
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

## <a name="browse-to-the-app"></a>瀏覽至應用程式

使用網頁瀏覽器瀏覽至下列 URL。

```bash
http://<app_name>.azurewebsites.net
```

![在 Azure 中執行的範例應用程式](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**恭喜！** 您已部署自訂 Docker Hub 映像至 Azure Web App for Containers。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 Azure 中建置 Docker Python 和 PostgreSQL Web 應用程式](tutorial-docker-python-postgresql-app.md)
