---
title: "Azure Container Registry 教學課程 - 將更新的映像推送至區域部署"
description: "將修改的 Docker 映像推送至您進行地理複寫的 Azure Container Registry，然後查看變更自動部署至多個區域中執行的 Web 應用程式。 這是三段式教學課程的第三段。"
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: acr, azure-container-registry, geo-replication
keywords: "Docker, 容器, 登錄, Azure"
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 76e6e1b826f37bfea7a8463808566191753e4f2d
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2017
---
# <a name="push-an-updated-image-to-regional-deployments"></a>將更新的映像推送至區域部署

這是三段式教學課程系列的第三段。 [上一個教學課程](container-registry-tutorial-deploy-app.md)是針對兩個不同區域的 Web 應用程式部署設定地理複寫。 在本教學課程中，您要先修改應用程式，然後建立新的容器映像，再將它推送至您進行地理複寫的登錄中。 最後，您要檢視由 Azure Container Registry Webhook 在兩個 Web 應用程式執行個體中自動部署的變更。

在本教學課程中，系列的最後一段：

> [!div class="checklist"]
> * 修改 Web 應用程式 HTML
> * 建置和標記 Docker 映像
> * 將變更推送至 Azure Container Registry
> * 檢視兩個不同區域中更新後的應用程式

如果您尚未設定兩個*用於容器的 Web 應用程式*區域部署，請先返回系列的上一個教學課程[從 Azure Container Registry 部署 Web 應用程式](container-registry-tutorial-deploy-app.md)。

## <a name="modify-the-web-application"></a>修改 Web 應用程式

在此步驟中，請對 Web 應用程式進行一項在將更新的容器映像推送至 Azure Container Registry 之後很容易發現的變更。

在上一個教學課程中，[從 GitHub 複製的](container-registry-tutorial-prepare-registry.md#get-application-code)應用程式來源中，找出 `AcrHelloworld/Views/Home/Index.cshtml` 檔案，並在慣用的文字編輯器中開啟檔案。 在 `<img>` 行上面新增下列一行：

```html
<h1>MODIFIED</h1>
```

您修改過的 `Index.cshtml` 應該類似：

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<h1>MODIFIED</h1>
<img width="700" src="~/images/@ViewData["MAPIMAGE"]" />
<ul>
<li>Registry URL: @ViewData["REGISTRYURL"]</li>
<li>Registry IP: @ViewData["REGISTRYIP"]</li>
<li>HostEntry: @ViewData["HOSTENTRY"]</li>
<li>Region: @ViewData["REGION"]</li>
<li>Map: @ViewData["MAPIMAGE"]</li>
</ul>
```

## <a name="rebuild-the-image"></a>重建映像

因為您更新了 Web 應用程式，所以請重建其容器映像。 像之前一樣，為標記使用完整的映像名稱，包括登入伺服器 URL：

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="run-the-container-locally"></a>在本機執行容器

部署至 Azure Container Registry 之前，請先在本機執行映像以確認建置成功。

```bash
docker run -d -p 8080:80 <acrName>.azurecr.io/acr-helloworld:v1
```

在您的網頁瀏覽器中瀏覽至 http://localhost:8080，確認容器已啟動且正在執行，而且顯示出您的修改。

![本機容器映像][local-container-01]

## <a name="push-image-to-azure-container-registry"></a>將映像推送至 Azure Container Registry

現在，將更新的 *acr-helloworld* 容器映像推送至您進行地理複寫的登錄。 此時，您要執行一個 `docker push` 命令，將更新的映像同時部署至「美國西部」和「美國東部」兩個區域中的登錄複本。

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="view-the-webhook-logs"></a>檢視 Webhook 記錄

複寫映像時，您會看到觸發的 Azure Container Registry Webhook。

若要查看您在上一個教學課程中將容器部署至*用於容器的 Web 應用程式*時所建立的區域 Webhook，請在 Azure 入口網站中瀏覽至您的容器登錄，然後在 [服務] 下選取 [Webhook]。

![Azure 入口網站中的容器登錄 Webhook][tutorial-portal-01]

選取每個 Webhook 可檢視其呼叫及回應的歷程記錄。 您應該會在兩個 Webhook 記錄中看到**推送**動作的資料列。 此時，位於「美國西部」區域的 Webhook 記錄顯示在上一個步驟中由 `docker push` 觸發的**推送**動作：

![Azure 入口網站中的容器登錄 Webhook 記錄 (美國西部)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>檢視更新後的 Web 應用程式

Webhook 會通知 Web 應用程式新的映像已推送至登錄，此作業會自動將更新的容器部署至兩個區域的 Web 應用程式。

在您的網頁瀏覽器中，瀏覽至這兩個區域的 Web 應用程式部署，確認兩個部署中的應用程式都已更新。 請注意，您可以在每個 App Service 概觀 索引標籤右上方找到已部署 Web 應用程式的 URL。

![Azure 入口網站中的 App Service 概觀][tutorial-portal-03]

若要查看更新過的應用程式，請選取 App Service 概觀中的連結。 以下是在「美國西部」中執行之應用程式的範例檢視：

![在美國西部區域中執行之已修改 Web 應用程式的瀏覽器檢視][deployed-app-westus-modified]

在您的瀏覽器中檢視，確認更新的容器映像也已部署至「美國東部」部署。

![在美國東部區域中執行之已修改 Web 應用程式的瀏覽器檢視][deployed-app-eastus-modified]

使用一個 `docker push`，您就更新了兩個區域的 Web 應用程式部署，Azure Container Registry 也從網路位置接近的存放庫提供容器映像。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您更新了 Web 應用程式容器，並將新版推送到您進行地理複寫的登錄。 Azure Container Registry 中的 Webhook 通知應用程式服務有更新，此作業觸發了本機從複寫的登錄提取。

在系列的最後一段教學課程中，您：

> [!div class="checklist"]
> * 更新了 Web 應用程式 HTML
> * 建置並標記了 Docker 映像
> * 將變更推送至 Azure Container Registry
> * 檢視兩個不同區域中更新後的應用程式

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png