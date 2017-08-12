---
title: "Azure Container Service 教學課程 - 更新應用程式 | Microsoft Docs"
description: "Azure Container Service 教學課程 - 更新應用程式"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服務, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 72cdbfe2fe65e5152ca748cbb3989e3ef980ee50
ms.contentlocale: zh-tw
ms.lasthandoff: 07/25/2017

---

# <a name="update-an-application-in-kubernetes"></a>在 Kubernetes 中更新應用程式

在 Kubernetes 中部署應用程式之後，您可以藉由指定新的容器映像或映像版本來進行更新。 當您更新應用程式時，更新會分段推出，所以只有一部分的部署會同時更新。 此分段更新可讓應用程式在更新期間繼續執行，並且在發生部署失敗時提供復原機制。 

在本教學課程 (6/7 部分) 中，已更新範例 Azure Vote 應用程式。 您完成的工作包括：

> [!div class="checklist"]
> * 更新前端應用程式程式碼
> * 建立已更新的容器映像
> * 將容器映像推送至 Azure Container Registry
> * 部署已更新的容器映像

在後續教學課程中，會將 Operations Management Suite 設定為監視 Kubernetes 叢集。

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已將應用程式封裝成容器映像、將這些映像上傳至 Azure Container Registry，並已建立 Kubernetes 叢集。 該應用程式接著便在 Kubernetes 叢集上執行。 

如果您尚未完成這些步驟，而想要跟著做，請回到[教學課程 1 – 建立容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。 

## <a name="update-application"></a>更新應用程式

若要完成本教學課程中的步驟，您必須複製一份 Azure Vote 應用程式。 如有必要，請使用下列命令建立此複製的複本：

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

使用任何程式碼或文字編輯器開啟 `config_file.cfg` 檔案。 您可以在所複製存放庫的下列目錄下找到這個檔案。

```bash
 /azure-voting-app-redis/azure-vote/azure-vote/config_file.cfg
```

變更 `VOTE1VALUE` 和 `VOTE2VALUE` 的值，然後儲存檔案。

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

使用 [docker-compose](https://docs.docker.com/compose/) 重新建立前端映像，並執行已更新的應用程式。

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yaml up --build -d
```

## <a name="test-application-locally"></a>在本機測試應用程式

瀏覽至 `http://localhost:8080` 以查看已更新的應用程式。

![Azure 上 Kubernetes 叢集的影像](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>標記和推送映像

以容器登錄的 loginServer 標記 *azure-vote-front* 映像。

如果您使用 Azure Container Registry，請使用 [az acr list](/cli/azure/acr#list) 命令取得登入伺服器名稱。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 來標記映像。 以您的 Azure Container Registry 登入伺服器名稱或公用登錄主機名稱取代 `<acrLoginServer>`。

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 將映像上傳至您的登錄。 以您的 Azure Container Registry 登入伺服器名稱或公用登錄主機名稱取代 `<acrLoginServer>`。

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>部署更新應用程式

若要確保最大執行時間，則應用程式 pod 必須有多個執行個體正在執行中。 請使用 [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令驗證此組態。

```bash
kubectl get pod
```

輸出：

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

如果您沒有執行 azure-vote-front 映像的多個 pod，請調整 *azure-vote-front* 部署。


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

若要更新應用程式，請使用 [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set) 命令。 以容器登錄的登入伺服器或主機名稱來更新 `<acrLoginServer>`。

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

若要監視部署，請使用 [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令。 部署已更新的應用程式後，您的 pod 會終止並以新的容器映像重建。

```azurecli-interactive
kubectl get pod
```

輸出：

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>測試已更新的應用程式

取得 *azure-vote-front* 服務的外部 IP 位址。

```azurecli-interactive
kubectl get service azure-vote-front
```

瀏覽至此 IP 位址以查看已更新的應用程式。

![Azure 上 Kubernetes 叢集的影像](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已更新應用程式並將此更新推出至 Kubernetes 叢集。 已完成下列工作：

> [!div class="checklist"]
> * 更新了前端應用程式程式碼
> * 建立了已更新的容器映像
> * 已將容器映像推送至 Azure Container Registry
> * 部署了已更新的應用程式

請前進到下一個教學課程，了解如何利用 Operations Management Suite 監視 Kubernetes。

> [!div class="nextstepaction"]
> [透過 OMS 監視 Kubernetes](./container-service-tutorial-kubernetes-monitor.md)
