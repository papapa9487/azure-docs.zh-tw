---
title: "建立 Azure Service Fabric 的容器映像 | Microsoft Docs"
description: "了解如何建立多容器 Service Fabric 應用程式的容器映像。"
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker、容器、微服務、Service Fabric、Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e84a0a05cbe1952d8325a9aed1c1df411bfcb877
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="create-container-images-for-service-fabric"></a>建立 Service Fabric 的容器映像

本教學課程是一個教學課程系列的第一部分，示範如何在 Linux Service Fabric 叢集中使用容器。 在本教學課程中，多容器應用程式已準備好可與 Service Fabric 搭配使用。 在後續的教學課程中，這些映像可用來作為 Service Fabric 應用程式的一部分。 在本教學課程中，您了解如何： 

> [!div class="checklist"]
> * 從 GitHub 複製應用程式來源  
> * 從應用程式來源建立容器映像
> * 部署 Azure Container Registry (ACR) 執行個體
> * 標記 ACR 的容器映像
> * 將映像上傳至 ACR

在本教學課程系列中，您將了解如何： 

> [!div class="checklist"]
> * 建立 Service Fabric 的容器映像
> * [建置與執行含容器的 Service Fabric 應用程式](service-fabric-tutorial-package-containers.md)
> * [如何在 Service Fabric 中處理容錯移轉和調整](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>必要條件

- 已針對 Service Fabric 設定的 Linux 開發環境。 請依照[這裡](service-fabric-get-started-linux.md)的指示來設定 Linux 環境。 
- 本教學課程需要您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 
- 此外，它需要您具有可用的 Azure 訂用帳戶。 如需免費試用版的詳細資訊，請移至[這裡](https://azure.microsoft.com/free/)。

## <a name="get-application-code"></a>取得應用程式程式碼

本教學課程中使用的範例應用程式是投票應用程式。 應用程式是由前端 Web 元件和後端 Redis 執行個體所組成。 元件已封裝為容器映像。 

使用 git 將應用程式的複本下載至您的開發環境。

```bash
git clone https://github.com/Azure-Samples/service-fabric-dotnet-containers.git

```

從複製的目錄中，使用下列命令進入工作目錄。 

```bash 
cd Linux/container-tutorial/
```

'container-tutorial' 目錄包含名為 'azure-vote' 的資料夾。 這個 'azure-vote' 資料夾包含前端原始程式碼和用來建置前端的 Dockerfile。 'container-tutorial' 目錄也包含 'redis' 目錄，其中含有用來建置 redis 映像的 Dockerfile。 這些目錄包含本教學課程組所需的資產。 

## <a name="create-container-images"></a>建立容器映像

在 'azure-vote' 目錄內部，執行下列命令以建置前端 Web 元件的映像。 此命令會使用此目錄中的 Dockerfile 來建置映像。 

```bash
docker build -t azure-vote-front .
```

在 'redis' 目錄內部，執行下列命令以建置 redis 後端的映像。 此命令會使用目錄中的 Dockerfile 來建置映像。 

```bash
docker build -t azure-vote-back .
```

完成時，使 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 命令來查看所建立的映像。

```bash
docker images
```

請注意，已下載或建立四個映像。 azure-vote-front 映像包含應用程式。 它衍生自 Docker Hub 的 python 映像。 已從 Docker Hub 下載 Redis 映像。

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>部署 Azure Container Registry

先執行 [az login](/cli/azure/login) 命令來登入您的 Azure 帳戶。 

接下來，使用 [az account](/cli/azure/account#set) 命令，選擇您的訂用帳戶來建立 Azure 容器登錄。 

```bash
az account set --subscription <subscription_id>
```

部署 Azure Container Registry 時，您必須先有資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 在此範例中，會在 westus 區域中建立名為 myResourceGroup 的資源群組。 請選擇接近您所在區域中的資源群組。 

```bash
az group create --name myResourceGroup --location westus
```

使用 [az acr create](/cli/azure/acr#create) 命令來建立 Azure Container Registry。 Container Registry 的名稱**必須是唯一的**。

```bash
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

在本教學課程的其餘部分，我們使用 "acrname" 作為您選擇之容器登錄名稱的預留位置。

## <a name="log-in-to-your-container-registry"></a>登入您的容器登錄

請登入您的 ACR 執行個體，再將映像推送到它。 使用 [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) 命令來完成此作業。 在建立容器登錄時，為它提供唯一名稱。

```bash
az acr login --name <acrName>
```

此命令在完成之後會傳回「登入成功」訊息。

## <a name="tag-container-images"></a>標記容器映像

每個容器映像都必須標記登錄的 loginServer 名稱。 將容器映像推送到映像登錄時，此標籤可用於路由傳送。

若要查看目前的映像清單，請使用 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 命令。

```bash
docker images
```

輸出：

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

若要取得 loginServer 名稱，請執行下列命令：

```bash
az acr show --name <acrName> --query loginServer --output table
```

現在，以容器登錄的 loginServer 標記 azure-vote-front 映像。 此外，將 `:v1` 新增至映像名稱的結尾。 此標籤指示映像版本。

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

接下來，以容器登錄的 loginServer 標記 azure-vote-back 映像。 此外，將 `:v1` 新增至映像名稱的結尾。 此標籤指示映像版本。

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

標記之後，執行 'docker images' 來驗證作業。


輸出：

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                        latest              bf9a858a9269        22 minutes ago      107MB
<acrName>.azurecr.io/azure-vote-back    v1                  bf9a858a9269        22 minutes ago      107MB
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf        23 minutes ago      708MB
redis                                  latest              9813a7e8fcc0        2 days ago          107MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>將映像推送到登錄

將 *azure-vote-front* 映像推送到登錄。 

使用下列範例，以您環境中的 loginServer 取代 ACR loginServer 名稱。

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

將 azure-vote-back 映像推送到登錄。 

使用下列範例，以您環境中的 loginServer 取代 ACR loginServer 名稱。

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

Docker push 命令需要數分鐘才能完成。

## <a name="list-images-in-registry"></a>列出登錄中的映像

若要傳回已推送至 Azure Container Registry 的映像清單，請使用 [az acr repository list](/cli/azure/acr/repository#list) 命令。 以 ACR 執行個體名稱更新命令。

```bash
az acr repository list --name <acrName> --output table
```

輸出：

```bash
Result
----------------
azure-vote-back
azure-vote-front
```

當教學課程完成時，私人 Azure Container Registry 執行個體中已儲存容器映像。 在後續的教學課程中，會將此映像從 ACR 部署到 Service Fabric 叢集。

## <a name="next-steps"></a>後續步驟

本教學課程會從 Github 提取應用程式，然後建立容器映像並推送到登錄。 已完成下列步驟：

> [!div class="checklist"]
> * 從 GitHub 複製應用程式來源  
> * 從應用程式來源建立容器映像
> * 部署 Azure Container Registry (ACR) 執行個體
> * 標記 ACR 的容器映像
> * 將映像上傳至 ACR

前進到下一個教學課程，以了解如何使用 Yeoman，將容器封裝到 Service Fabric 應用程式。 

> [!div class="nextstepaction"]
> [封裝和部署容器作為 Service Fabric 應用程式](service-fabric-tutorial-package-containers.md)

