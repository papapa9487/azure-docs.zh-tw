---
title: "Azure Container Instances 教學課程 - 準備您的應用程式"
description: "準備應用程式以便部署至 Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 3ae8eb53843e31ec66af52be9b04fbb626093f5b
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>建立容器以部署至 Azure Container Instances

Azure Container Instances 能夠將 Docker 容器部署至 Azure 基礎結構，而不需要佈建任何虛擬機器，或採用較高層級的任何服務。 在本教學課程中，您將以 Node.js 建置小型 Web 應用程式，然後封裝在可使用 Azure Container Instances 來執行的容器中。 我們會說明︰

> [!div class="checklist"]
> * 從 GitHub 複製應用程式來源
> * 從應用程式來源建立容器映像
> * 在本機 Docker 環境中測試映像

在後續教學課程中，您會將映像上傳至 Azure Container Registry，然後部署至 Azure Container Instances。

## <a name="before-you-begin"></a>開始之前

本教學課程需要您執行 Azure CLI 2.0.21 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。

本教學課程假設使用者對核心 Docker 概念有基本認識，例如容器、容器映像和基本 `docker` 命令。 如有需要，請參閱[開始使用 Docker]( https://docs.docker.com/get-started/)以取得容器基本概念入門。

若要完成本教學課程，您需要 Docker 開發環境。 Docker 提供可輕鬆在 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系統上設定 Docker 的套件。

Azure Cloud Shell 不包括完成本教學課程每個步驟所需的 Docker 元件。 因此，我們建議您本機安裝 Azure CLI 和 Docker 開發環境。

## <a name="get-application-code"></a>取得應用程式程式碼

本教學課程中的範例包含一個以 [Node.js](http://nodejs.org) 建置的簡單 Web 應用程式。 應用程式提供一個 HTML 靜態網頁，外觀如下所示：

![在瀏覽器中顯示的教學課程應用程式][aci-tutorial-app]

使用 git 來下載範例：

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>建置容器映像

範例儲存庫中提供的 Dockerfile 會示範如何建置容器。 首先會使用以 [Alpine Linux](https://alpinelinux.org/) 為基礎的[官方 Node.js 映像][dockerhub-nodeimage]，這是一個很適合用於容器的小型散發。 接著會將應用程式檔案複製到容器、使用節點封裝管理員來安裝相依性，最後就啟動應用程式。

```Dockerfile
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

使用 `docker build` 命令來建立容器映像，並標記成 *aci-tutorial-app*：

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

`docker build` 命令的輸出與以下類似 (為便於閱讀已將輸出內容截斷)：

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.2.0-alpine
8.2.0-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.2.0-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

使用 `docker images` 查看已建置的映像：

```bash
docker images
```

輸出：

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>在本機執行容器

在嘗試將容器部署至 Container Instances 之前，請先在本機執行，以確認可以運作。 `-d` 參數可讓容器在背景中執行，而 `-p` 可讓您將電腦上的任意連接埠對應至容器中的連接埠 80。

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

開啟瀏覽器來移至 http://localhost:8080/，以確認容器正在執行。

![在本機瀏覽器中執行應用程式][aci-tutorial-app-local]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立可部署至 Azure Container Instances 的容器映像。 已完成下列步驟：

> [!div class="checklist"]
> * 從 GitHub 複製應用程式來源
> * 從應用程式來源建立容器映像
> * 在本機測試容器

前往下一個教學課程，了解如何在 Azure Container Registry 中儲存容器映像。

> [!div class="nextstepaction"]
> [將映像推送至 Azure Container Registry](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://store.docker.com/images/node

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png