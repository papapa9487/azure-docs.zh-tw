---
title: "Azure 容器執行個體教學課程 - 準備 Azure Container Registry"
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
ms.date: 10/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 8cb00210ee260383d546be4faf141c133661156b
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>部署和使用 Azure Container Registry

這是三段式教學課程的第二段。 在[上一個步驟](container-instances-tutorial-prepare-app.md)中，我們已針對使用 [Node.js](http://nodejs.org) 所編寫的簡單 Web 應用程式建立容器映像。 在本教學課程中，您會將此映像推送至 Azure Container Registry。 如果您尚未建立容器映像，請回到[教學課程 1 – 建立容器映像](container-instances-tutorial-prepare-app.md)。

Azure Container Registry 是以 Azure 為基礎的私人登錄，用於裝載 Docker 容器映像。 本教學課程逐步解說如何部署 Azure Container Registry 執行個體，以及將容器映像推送至該執行個體。 完成的步驟包括：

> [!div class="checklist"]
> * 部署 Azure Container Registry 執行個體
> * 標記 Azure Container Registry 的容器映像
> * 將映像上傳至 Azure Container Registry

在後續教學課程中，您會從私用登錄將容器部署至 Azure 容器執行個體。

## <a name="before-you-begin"></a>開始之前

本教學課程需要您執行 Azure CLI 2.0.20 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。

若要完成本教學課程，您需要 Docker 開發環境。 Docker 提供可輕鬆在 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系統上設定 Docker 的套件。

Azure Cloud Shell 不包括完成本教學課程每個步驟所需的 Docker 元件。 因此，我們建議您本機安裝 Azure CLI 和 Docker 開發環境。

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

## <a name="container-registry-login"></a>Container Registry 登入

您必須先登入 ACR 執行個體，再將映像推送到它。 使用 [az acr login](/cli/azure/acr#az_acr_login) 命令來完成此作業。 您必須在建立容器登錄時，為它提供唯一名稱。

```azurecli
az acr login --name <acrName>
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

若要取得 loginServer 名稱，請執行下列命令：

```azurecli
az acr show --name <acrName> --query loginServer --output table
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
az acr repository list --name <acrName> --output table
```

輸出：

```azurecli
Result
----------------
aci-tutorial-app
```

而後若要查看特定映像的標籤，請使用 [az acr repository show-tags](/cli/azure/acr/repository#show-tags) 命令。

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
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
> * 已部署 Azure Container Registry 執行個體
> * 標記 Azure Container Registry 的容器映像
> * 將映像上傳至 Azure Container Registry

進入下一個教學課程，來了解如何使用 Azure 容器執行個體將容器部署至 Azure 中。

> [!div class="nextstepaction"]
> [將容器部署至 Azure 容器執行個體](./container-instances-tutorial-deploy-app.md)
