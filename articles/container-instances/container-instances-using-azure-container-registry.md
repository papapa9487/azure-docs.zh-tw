---
title: "從 Azure Container Registry 部署至 Azure Container Instances | Azure Docs"
description: "從 Azure Container Registry 部署至 Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: cdac6362f0d51b3144024efd28af09eb6d97515f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---

# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>從 Azure Container Registry 部署至 Azure Container Instances

Azure Container Registry 是以 Azure 為基礎的私人登錄，用於裝載 Docker 容器映像。 本文說明如何將儲存於 Azure Container Registry 的容器映像部署至 Azure Container Instances。

## <a name="using-the-azure-cli"></a>使用 Azure CLI

Azure CLI 包括可用於建立和管理 Azure Container Instances 中容器的命令。 如果您在 `create` 命令中指定私人映像，則也可以指定對容器登錄進行驗證所需的映像登錄密碼。

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --image-registry-password myRegistryPassword --resource-group myresourcegroup
```

`create` 命令也支援指定 `image-registry-login-server` 和 `image-registry-username`。 不過，Azure Container Registry 的登入伺服器永遠會是 registryname.azurecr.io，而預設使用者名稱是 registryname，因此如果未明確提供，您仍可從映像名稱推斷這些值。

## <a name="using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本

您可以在容器群組定義中加入 `imageRegistryCredentials` 屬性，以在 Azure Resource Manager 範本中指定 Azure Container Registry 的屬性：

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

為了避免在範本中直接儲存容器登錄密碼，我們建議您將其儲存為 [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) 中的祕密，並在範本中使用 [Azure Resource Manager 和 Key Vault 之間的原生整合](../azure-resource-manager/resource-manager-keyvault-parameter.md)參考該祕密。

## <a name="using-the-azure-portal"></a>使用 Azure 入口網站

如果您在 Azure Container Registry 中保有容器映像，您就可以使用 Azure 入口網站在 Azure Container Instances 中輕鬆地建立容器。

1. 在 Azure 入口網站中，瀏覽到您的容器登錄。

2. 選擇存放庫。

    ![Azure 入口網站中的 Azure Container Registry 功能表][acr-menu]

3. 選擇您想要從哪個存放庫進行部署。

4. 以滑鼠右鍵按一下您想要部署的容器映像標記。

    ![可供使用 Azure Container Instances 來啟動容器的快顯功能表][acr-runinstance-contextmenu]

5. 分別為容器和資源群組輸入名稱。 如有需要，您也可以變更預設值。

    ![建立 Azure Container Instances 的功能表][acr-create-deeplink]

6. 部署完成之後，您可以從 [通知] 窗格瀏覽至容器群組，以尋找其 IP 位址和其他屬性。

    ![Azure Container Instances 容器群組的詳細資料檢視][aci-detailsview]

## <a name="next-steps"></a>後續步驟

透過[完成教學課程](container-instances-tutorial-prepare-app.md)，了解如何建置容器、將其推送至私人容器登錄和將其部署至 Azure Container Instances。

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
