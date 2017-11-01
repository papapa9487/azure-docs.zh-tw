---
title: "快速入門 - 使用 Azure CLI 在 Azure 中建立私用的 Docker 登錄"
description: "快速了解如何使用 Azure CLI 建立私用的 Docker 容器登錄。"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 91f0aa093e0a1f7ed4d54a0cdf5ef53bc41cb6be
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>使用 Azure CLI 建立容器登錄庫

Azure Container Registry 是用於儲存私用 Docker 容器映像的受管理 Docker 容器登錄服務。 本指南詳述如何使用 Azure CLI 建立 Azure Container Registry 執行個體。

本快速入門需要您執行 Azure CLI 2.0.12 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。

您也必須在本機上安裝 Docker。 Docker 提供可輕鬆在 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系統上設定 Docker 的套件。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>建立容器登錄庫

在本快速入門中，我們會建立「基本」登錄。 有數個不同的 SKU 提供 Azure Container Registry，簡略說明於下表。 如需個別項目更詳細的資訊，請參閱[容器登錄 SKU](container-registry-skus.md)。

Azure Container Registry 在數個 SKU 中提供服務：`Basic`、`Managed_Basic`、`Managed_Standard` 和 `Managed_Premium`。 雖然 `Managed_*` SKU 提供諸如受管理存放裝置及 Webhook 等進階功能，但目前在某些 Azure 區域無法透過 Azure CLI 使用它們。 我們在本快速入門中選取 `Basic` SKU 是因為其在所有區域的可用性。

>[!NOTE]
> 受管理登錄目前在部分區域無法使用。 不過，在所有區域中目前版本的 Azure CLI 皆不支援建立受管理的登錄。 下一版的 Azure CLI 將支援。 在這之前的版本，請使用 [Azure 入口網站](container-registry-get-started-portal.md)建立受管理的登錄。

使用 [az acr create](/cli/azure/acr#create) 命令，以建立 ACR 執行個體。

登錄的名稱**必須是唯一的**。 下列範例中使用 *myContainerRegistry007*。 請將此更新為唯一的值。

```azurecli
az acr create --name myContainerRegistry007 --resource-group myResourceGroup --sku Basic
```

建立登錄時，輸出大致如下：

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr223140"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

在本快入入門的其餘部分，我們使用 `<acrname>` 作為容器登錄名稱的預留位置。

## <a name="log-in-to-acr"></a>登入 ACR

發送和提取容器映像之前，您必須登入 ACR 執行個體。 若要這樣做，請使用 [az acr login](/cli/azure/acr#login) 命令。

```azurecli
az acr login --name <acrname>
```

此命令在完成之後會傳回「登入成功」訊息。

## <a name="push-image-to-acr"></a>推送映像到 ACR

若要推送映像到 Azure Container Registry，您必須先有映像。 如有需要，請執行下列命令，以從 Docker Hub 提取預先建立的映像。

```bash
docker pull microsoft/aci-helloworld
```

映像必須加上 ACR 登入伺服器名稱。 執行下列命令，以傳回 ACR 執行個體的登入伺服器名稱。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 命令來標記映像。 將 <acrLoginServer> 取代為 ACR 執行個體的登入伺服器名稱。

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

最後，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 將映像推送到 ACR 執行個體。 將 <acrLoginServer> 取代為 ACR 執行個體的登入伺服器名稱。

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>列出容器映像

下列範例會列出登錄中的存放庫：

```azurecli
az acr repository list -n <acrname> -o table
```

輸出：

```bash
Result
----------------
aci-helloworld
```

下列範例會列出在 **aci-helloworld** 的標籤上。

```azurecli
az acr repository show-tags -n <acrname> --repository aci-helloworld -o table
```

輸出：

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#delete) 命令來移除資源群組、ACR 執行個體和所有容器映像。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用 Azure CLI 建立了 Azure Container Registry。 如果您想要使用 Azure Container Registry 搭配 Azure 容器執行特體，請繼續進行 Azure 容器執行個體教學課程。

> [!div class="nextstepaction"]
> [Azure 容器執行個體教學課程](../container-instances/container-instances-tutorial-prepare-app.md)