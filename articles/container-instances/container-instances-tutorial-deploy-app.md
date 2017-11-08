---
title: "Azure 容器執行個體教學課程 - 部署應用程式"
description: "Azure 容器執行個體教學課程 - 部署應用程式"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 3b651526f5ee3197e7d04accb6a87e2f10bf0791
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>將容器部署至 Azure 容器執行個體

這是三段式教學課程的最後一段。 在前面幾節中，我們[已建立容器映像](container-instances-tutorial-prepare-app.md)並[推送至 Azure Container Registry](container-instances-tutorial-prepare-acr.md)。 本節會將容器部署至 Azure 容器執行個體，以完成本教學課程。 完成的步驟包括：

> [!div class="checklist"]
> * 使用 Azure CLI 從 Azure Container Registry 部署容器
> * 在瀏覽器中檢視應用程式
> * 檢視容器記錄

## <a name="before-you-begin"></a>開始之前

本教學課程需要您執行 Azure CLI 2.0.20 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。

若要完成本教學課程，您需要 Docker 開發環境。 Docker 提供可輕鬆在 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系統上設定 Docker 的套件。

Azure Cloud Shell 不包括完成本教學課程每個步驟所需的 Docker 元件。 因此，我們建議您本機安裝 Azure CLI 和 Docker 開發環境。

## <a name="deploy-the-container-using-the-azure-cli"></a>使用 Azure CLI 來部署容器

Azure CLI 能夠透過單一命令將容器部署至 Azure 容器執行個體。 由於容器映像裝載在私人 Azure Container Registry 中，因此您必須納入所需的認證才能存取該映像。 如有必要，您可以查詢這些認證，如下所示。

容器登錄的登入伺服器 (以登錄名稱來更新)：

```azurecli
az acr show --name <acrName> --query loginServer
```

容器登錄密碼：

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

若要從容器登錄中使用 1 個 CPU 核心和 1 GB 記憶體的資源要求來部署您的容器映像，請執行下列命令：

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

在幾秒內，您應該就會從 Azure Resource Manager 收到首次的回應。 若要檢視部署的狀態，請使用：

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query state
```

我們可以繼續執行此命令，直到狀態從*擱置*變更為*執行中*。 然後我們可以繼續進行。

## <a name="view-the-application-and-container-logs"></a>檢視應用程式和容器記錄

部署成功後，開啟瀏覽器以進入下列命令的輸出所顯示的 IP 位址：

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

```json
"13.88.176.27"
```

![瀏覽器中的 Hello World 應用程式][aci-app-browser]

您也可以檢視容器的記錄輸出：

```azurecli
az container logs --name aci-tutorial-app -g myResourceGroup
```

輸出：

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成將容器部署至 Azure 容器執行個體的程序。 已完成下列步驟：

> [!div class="checklist"]
> * 使用 Azure CLI 從 Azure Container Registry 部署容器
> * 在瀏覽器中檢視應用程式
> * 檢視容器記錄

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
