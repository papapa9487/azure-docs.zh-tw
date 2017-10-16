---
title: "將 Docker 映像推送至私人 Azure 登錄 |Microsoft Docs"
description: "使用 Docker CLI 推送和提取 Docker 映像至 Azure 中的私人容器登錄"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 10f01e4e8c86bbbfa17cf2559caca645ff13bdcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>使用 Docker CLI 將您的第一個映像推送至私人 Docker 容器登錄
Azure 容器登錄庫儲存和管理私人 [Docker](http://hub.docker.com) 容器映像，其方式類似於 [Docker 中樞](https://hub.docker.com/)儲存公用 Docker 映像。 使用 [Docker 命令列介面](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) 進行[登入](https://docs.docker.com/engine/reference/commandline/login/)、[推送](https://docs.docker.com/engine/reference/commandline/push/)、[提取](https://docs.docker.com/engine/reference/commandline/pull/)和其他容器登錄庫作業。

如需相關背景和概念，請參閱[概觀](container-registry-intro.md)



## <a name="prerequisites"></a>必要條件
* **Azure 容器登錄庫** - 在 Azure 訂用帳戶中建立容器登錄庫。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI 2.0](container-registry-get-started-azure-cli.md)。
* **Docker CLI** - 若要將您的本機電腦設定為 Docker 主機並存取 Docker CLI 命令，安裝 [Docker 引擎](https://docs.docker.com/engine/installation/)。

## <a name="log-in-to-a-registry"></a>登入登錄庫
使用您的[登錄認證](container-registry-authentication.md)執行 `docker login` 登入容器登錄庫。

下列範例會傳遞 Azure Active Directory [service principal](../active-directory/active-directory-application-objects.md) 的識別碼和密碼。 例如，您可能基於自動化案例已指派服務主體到您的登錄庫。

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> 請務必指定完整的登錄庫名稱 (全部小寫)。 在此範例中為 `myregistry.azurecr.io`。

## <a name="steps-to-pull-and-push-an-image"></a>提取和推送映像的步驟
下列範例會從公用 Docker 中樞登錄下載 Nginx 映像，將其標記為私人 Azure 容器登錄，推送到您的登錄，然後再次將其提取出來。

**1.提取 Nginx 的 Docker 官方映像**

第一次提取公用 Nginx 映像至您的本機電腦。

```
docker pull nginx
```
**2.啟動 Nginx 容器**

下列命令會以互動方式在連接埠 8080 上啟動本機 Nginx 容器，讓您查看 Nginx 的輸出。 它會移除已停止的執行中容器。

```
docker run -it --rm -p 8080:80 nginx
```

瀏覽至 [http://localhost:8080/](http://localhost:8080) 以檢視執行中的容器。 將會看到類似以下的畫面。

![本機電腦上的 Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

若要停止執行中的容器，請按 [CTRL] + [C]。

**3.在登錄庫中建立映像的別名**

下列命令會建立映像的別名，包含登錄庫的完整路徑。 這個範例會指定 `samples` 命名空間，以避免登錄庫根目錄雜亂。

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```  

**4.將映像推送至您的登錄庫**

```
docker push myregistry.azurecr.io/samples/nginx
```

**5.從您的登錄庫提取映像**

```
docker pull myregistry.azurecr.io/samples/nginx
```

**6.從您的登錄庫啟動 Nginx 容器**

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

瀏覽至 [http://localhost:8080/](http://localhost:8080) 以檢視執行中的容器。

若要停止執行中的容器，請按 [CTRL] + [C]。

**7.(選用) 移除映像**

```
docker rmi myregistry.azurecr.io/samples/nginx
```

##<a name="concurrent-limits"></a>並行限制
在某些情況下，同時執行呼叫可能會導致錯誤。 下表包含在 Azure 容器登錄上同時呼叫「推送」和「提取」作業的限制︰

| 作業  | 限制                                  |
| ---------- | -------------------------------------- |
| PULL       | 每個登錄最多 10 個並行提取 |
| PUSH       | 每個登錄最多 5 個並行推送 |

## <a name="next-steps"></a>後續步驟
現在您瞭解基本概念了，可以開始使用您的登錄庫！ 例如，開始將容器映像部署到 [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/) 叢集。
