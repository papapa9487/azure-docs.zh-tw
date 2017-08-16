---
title: "Azure 容器執行個體教學課程 - 準備 Azure Container Registry | Microsoft Docs"
description: "Azure 容器執行個體教學課程 - 準備 Azure Container Registry"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服務, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 7ec6c7fd2125293ba47a48feb83250eeb667d1a6
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>部署和使用 Azure Container Registry

這是三段式教學課程的第二段。 在[上一個步驟](./container-instances-tutorial-prepare-app.md)中，我們已針對使用 [Node.js](http://nodejs.org) 所編寫的簡單 Web 應用程式建立容器映像。 在本教學課程中，此映像會推送至 Azure Container Registry。 如果您尚未建立容器映像，請回到[教學課程 1 – 建立容器映像](./container-instances-tutorial-prepare-app.md)。 

Azure Container Registry 是以 Azure 為基礎的私人登錄，用於裝載 Docker 容器映像。 本教學課程逐步解說如何部署 Azure Container Registry 執行個體，以及將容器映像推送至該執行個體。 完成的步驟包括：

> [!div class="checklist"]
> * 部署 Azure Container Registry 執行個體
> * 標記 Azure Container Registry 的容器映像
> * 將映像上傳至 Azure Container Registry

在後續教學課程中，您會從私用登錄將容器部署至 Azure 容器執行個體。

## <a name="before-you-begin"></a>開始之前

本教學課程需要您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="deploy-azure-container-registry"></a>部署 Azure Container Registry

部署 Azure Container Registry 時，您必須先有資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯集合。

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 在此範例中，會在 eastus 區域中建立名為 myResourceGroup 的資源群組。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az acr create](/cli/azure/acr#create) 命令來建立 Azure Container Registry。 Container Registry 的名稱**必須是唯一的**。 在下列範例中，我們使用名稱 mycontainerregistry082。

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

在本教學課程的其餘部分，我們使用 `<acrname>` 作為您選擇之容器登錄名稱的預留位置。

## <a name="get-azure-container-registry-information"></a>取得 Azure Container Registry 資訊

建立好容器登錄之後，您可以查詢其登入伺服器和密碼。 下列程式碼會傳回這些值。 請記下登入伺服器和密碼的每個值，因為本教學課程會提及這些值。

容器登錄的登入伺服器 (以登錄名稱來更新)：

```azurecli
az acr show --name <acrName> --query loginServer
```

在本教學課程的其餘部分，我們使用 `<acrLoginServer>` 作為容器登錄之登入伺服器值的預留位置。

容器登錄密碼：

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

在本教學課程的其餘部分，我們使用 `<acrPassword>` 作為容器登錄之密碼值的預留位置。

## <a name="login-to-the-container-registry"></a>登入到容器登錄

您必須先登入容器登錄執行個體，才能將映像推送給它。 使用 [的 docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令來完成此作業。 執行 Docker 登入時，您必須提供登錄的登入伺服器名稱和認證。

```bash
docker login --username=<acrName> --password=<acrPassword> <acrLoginServer>
```

此命令在完成之後會傳回「登入成功」訊息。

## <a name="tag-container-image"></a>標記容器映像

為了從私用登錄部署容器映像，您必須為此映像標記上登錄的 `loginServer` 名稱。

若要查看目前的映像清單，請使用 `docker images` 命令。

```bash
docker images
```

輸出：

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

以容器登錄的 loginServer 標記 aci-tutorial-app 映像。 此外，將 `:v1` 新增至映像名稱的結尾。 此標籤指示映像版本號碼。

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

標記之後，執行 `docker images` 來驗證作業。

```bash
docker images
```

輸出：

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>將映像推送至 Azure Container Registry

將 aci-tutorial-app 映像推送至登錄。

使用下列範例，以您環境中的 loginServer 取代容器登錄 loginServer 名稱。

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

## <a name="list-images-in-azure-container-registry"></a>在 Azure Container Registry 中列出映像

若要傳回已推送至 Azure Container Registry 的映像清單，請使用 [az acr repository list](/cli/azure/acr/repository#list) 命令。 使用容器登錄名稱來更新命令。

```azurecli
az acr repository list --name <acrName> --username <acrName> --password <acrPassword> --output table
```

輸出：

```azurecli
Result
----------------
aci-tutorial-app
```

而後若要查看特定映像的標籤，請使用 [az acr repository show-tags](/cli/azure/acr/repository#show-tags) 命令。

```azurecli
az acr repository show-tags --name <acrName> --username <acrName> --password <acrPassword> --repository aci-tutorial-app --output table
```

輸出：

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，我們準備了 Azure Container Registry 來與 Azure 容器執行個體搭配使用，並已推送容器映像。 已完成下列步驟：

> [!div class="checklist"]
> * 部署 Azure Container Registry 執行個體
> * 標記 Azure Container Registry 的容器映像
> * 將映像上傳至 Azure Container Registry

進入下一個教學課程，來了解如何使用 Azure 容器執行個體將容器部署至 Azure 中。

> [!div class="nextstepaction"]
> [將容器部署至 Azure 容器執行個體](./container-instances-tutorial-deploy-app.md)

