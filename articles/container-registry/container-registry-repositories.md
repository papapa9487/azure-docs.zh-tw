---
title: "Azure 容器登錄存放庫"
description: "如何使用 Docker 映像的 Azure 容器登錄存放庫"
services: container-registry
author: cristy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 03/24/2017
ms.author: cristyg
ms.openlocfilehash: 3321dd1d8bbd1b8232c26491edd8c374df16b813
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-repositories"></a>Azure 容器登錄存放庫

Azure 容器登錄可讓您將容器映像儲存在存放庫中。 透過將映像儲存在存放庫中，您可以在隔離的環境中擁有映像 (或映像的版本) 的群組。 當您將映像推送到您的登錄時，可以指定這些存放庫。


## <a name="prerequisites"></a>必要條件
* **Azure 容器登錄庫** - 在 Azure 訂用帳戶中建立容器登錄庫。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI 2.0](container-registry-get-started-azure-cli.md)。
* **Docker CLI** - 若要將您的本機電腦設定為 Docker 主機並存取 Docker CLI 命令，安裝 [Docker 引擎](https://docs.docker.com/engine/installation/)。
* **提取映像** - 從公用 Docker Hub 登錄提取映像、標記映像，並將其推送到您的登錄。 如需如何推送和提取映像的指示，請參閱[推送 Docker 映像到 Azure 私人登錄](container-registry-get-started-docker-cli.md)。


## <a name="viewing-repositories-in-the-portal"></a>在入口網站中檢視存放庫

一旦您將映像推送到容器登錄，即可在 Azure 入口網站中看到裝載映像的存放庫清單。

如果您遵循[推送 Docker 映像到 Azure 私人登錄](container-registry-get-started-docker-cli.md)一文中的步驟，您的容器登錄中現在應該有 Nginx 映像。 依指示所述，您應該已為該映像指定命名空間。 在下列範例中，命令將 NGinx 映像推送到 "samples" 存放庫︰

```
docker push myregistry.azurecr.io/samples/nginx
```
 Azure 容器登錄庫支援多層級的儲存機制命名空間。 這項功能可讓您將與特定應用程式相關的映像集合為群組，或將特定開發或作業團隊的應用程式集合為群組。 若要深入了解容器登錄中的存放庫，請參閱 [Azure 中的私人 Docker 容器登錄](container-registry-intro.md)。

檢視容器登錄存放庫：

1. 登入 Azure 入口網站
2. 在 [Azure 容器登錄 (Azure Container Registry)] 刀鋒視窗中，選取您想要檢查的登錄
3. 在登錄刀鋒視窗中，按一下 [儲存機制 (Repositories)] 以查看所有儲存機制和其映像的清單
4. (選擇性) 選取特定的映像以查看標記

![入口網站中的存放庫](./media/container-registry-repositories/container-registry-repositories.png)


## <a name="next-steps"></a>後續步驟
現在您瞭解基本概念了，可以開始使用您的登錄庫！ 例如，開始將容器映像部署到 [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/) 叢集。
