---
title: "使用 Azure Web App for Containers 進行持續部署 | Microsoft Docs"
description: "如何在 Azure Web App for Containers 中設定持續部署。"
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 27a2c95c09197b3439d3fac7c74d253df2b32b1c
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="continuous-deployment-with-azure-web-app-for-containers"></a>使用 Azure Web App for Containers 進行持續部署

在本教學課程中，您可以從受控的 [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/) 存放庫或 [Docker 中樞](https://hub.docker.com)設定自訂容器映像的連續部署。

## <a name="step-1---sign-in-to-azure"></a>步驟 1 - 登入 Azure

登入 Azure 入口網站 (網址是 http://portal.azure.com)

## <a name="step-2---enable-container-continuous-deployment-feature"></a>步驟 2 - 啟用容器連續部署功能

您可以使用 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) 並執行下列命令來啟用持續部署功能

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
``` 

在 **[Azure 入口網站](https://portal.azure.com/)**中，按一下頁面左側的 [App Service] 選項。

按一下您想要設定 Docker 中樞連續部署的應用程式名稱。

在 [應用程式設定] 中，新增名為 `DOCKER_ENABLE_CI` 且包含 `true` 值的應用程式設定。

![將應用程式設定的影像插入](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>步驟 3 - 準備 Webhook URL

您可以使用 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) 並執行下列命令來取得 Webhook URL

```azurecli-interactive
az webapp deployment container show-cd-url -n sname1 -g rgname
``` 

針對 Webhook URL，您必須擁有下列端點︰`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`。

您可以取得 `publishingusername` 和 `publishingpwd`，方法是使用 Azure 入口網站來下載 Web 應用程式發佈設定檔。

![將新增 webhook 2 的映像插入](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>步驟 4：新增 Web Hook

### <a name="azure-container-registry"></a>Azure Container Registry

在您的登錄入口網站刀鋒視窗中，按一下 [Webhook]，按一下 [新增] 以建立新的 webhook。 在 [建立 webhook] 刀鋒視窗中，提供您的 webhook 名稱。 在 Webhook URI 中，您需要提供從**步驟 3** 取得的 URL。

請確定您將範圍定義為包含您的容器映像的存放庫。

![插入 webhook 的映像](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

當更新映像時，Web 應用程式會以新的映像自動進行更新。

### <a name="docker-hub"></a>Docker 中樞

在您的 [Docker 中樞] 頁面上，依序按一下 [Webhook]、[建立 WEBHOOK]。

![將新增 webhook 1 的映像插入](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

在 Webhook URL 中，您需要提供從**步驟 3** 取得的 URL。

![將新增 webhook 2 的映像插入](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

當更新映像時，Web 應用程式會以新的映像自動進行更新。

## <a name="next-steps"></a>後續步驟

* [什麼是 Azure Web App for Containers？](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/)
* [在 Azure Web App for Containers 中使用 .NET Core](quickstart-dotnetcore.md)
* [在 Azure Web App for Containers 中使用 Ruby](quickstart-ruby.md)
* [如何針對 Azure Web App for Containers 使用自訂 Docker 映像](quickstart-custom-docker-image.md)
* [Azure App Service Web App for Containers 常見問題集](./app-service-linux-faq.md) 
* [使用 Azure CLI 2.0 管理 Web App for Containers](./app-service-linux-cli.md)

