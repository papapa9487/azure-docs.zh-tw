---
title: "快速入門 - 適用於 Linux 的 Azure Docker Swarm 叢集"
description: "快速了解如何在 Azure Container Service 中使用 Azure CLI 建立適用於 Linux 容器的 Docker Swarm 叢集。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: get-started-article
ms.date: 08/14/2017
ms.author: nepeters
ms.custom: 
ms.openlocfilehash: 79e4fab9501141c78bca4b28eabb3964604be909
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="deploy-docker-swarm-cluster"></a>部署 Docker Swarm 叢集

本快速入門中會使用 Azure CLI 來部署 Docker Swarm 叢集。 接著，在叢集上部署和執行多容器應用程式，其中包含 Web 前端和 Redis 執行個體。 完成後，即可透過網際網路來存取應用程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

本快速入門需要您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯群組。

下列範例會在 westus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

輸出：

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westcentralus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>建立 Docker Swarm 叢集

使用 [az acs create](/cli/azure/acs#create) 命令，在 Azure Container Service 中建立 Docker Swarm 叢集。 

下列範例會建立一個名為 mySwarmCluster 的叢集，其中包含一個 Linux 主要節點和三個 Linux 代理程式節點。

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type Swarm --resource-group myResourceGroup --generate-ssh-keys
```

在有限試用之類的某些情況下，Azure 訂用帳戶只擁有 Azure 資源的有限存取權。 如果部署因可用核心受限而失敗，請將 `--agent-count 1` 加入 [az acs create](/cli/azure/acs#create) 命令來減少預設代理程式的數量。 

幾分鐘之後，此命令就會完成，並以 json 格式傳回叢集的相關資訊。

## <a name="connect-to-the-cluster"></a>連接到叢集

在本快速入門中，您需要 Docker Swarm 主機和 Docker 代理程式集區的 IP 位址。 執行下列命令以傳回這兩個 IP 位址。


```bash
az network public-ip list --resource-group myResourceGroup --query "[*].{Name:name,IPAddress:ipAddress}" -o table
```

輸出：

```bash
Name                                                                 IPAddress
-------------------------------------------------------------------  -------------
swarmm-agent-ip-myswarmcluster-myresourcegroup-d5b9d4agent-66066781  52.179.23.131
swarmm-master-ip-myswarmcluster-myresourcegroup-d5b9d4mgmt-66066781  52.141.37.199
```

建立 Swarm 主機的 SSH 通道。 以 Swarm 主機的 IP 位址取代 `IPAddress`。

```bash
ssh -p 2200 -fNL 2375:localhost:2375 azureuser@IPAddress
```

設定 `DOCKER_HOST` 環境變數。 這可讓您對 Docker Swarm 執行 Docker 命令，而不需指定主機名稱。

```bash
export DOCKER_HOST=:2375
```

您現在已經準備好在 Docker Swarm 上執行 Docker 服務。


## <a name="run-the-application"></a>執行應用程式

建立名為 `docker-compose.yaml` 的檔案，並將下列內容複製到其中。

```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:redis-v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

請執行下列命令來建立 Azure 投票服務。

```bash
docker-compose up -d
```

輸出：

```bash
Creating network "user_default" with the default driver
Pulling azure-vote-front (microsoft/azure-vote-front:redis-v1)...
swarm-agent-EE873B23000005: Pulling microsoft/azure-vote-front:redis-v1...
swarm-agent-EE873B23000004: Pulling microsoft/azure-vote-front:redis-v1... : downloaded
Pulling azure-vote-back (redis:latest)...
swarm-agent-EE873B23000004: Pulling redis:latest... : downloaded
Creating azure-vote-front ... 
Creating azure-vote-back ... 
Creating azure-vote-front
Creating azure-vote-back ...
```

## <a name="test-the-application"></a>測試應用程式

瀏覽至 Swarm 代理程式集區的 IP 位址，以測試 Azure 投票應用程式。

![瀏覽至 Azure 投票的影像](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>刪除叢集
若不再需要叢集，您可以使用 [az group delete](/cli/azure/group#delete) 命令來移除資源群組、容器服務和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>取得程式碼

在本快速入門中，預先建立的容器映像已用來建立 Docker 服務。 在 GitHub 上可取得相關的應用程式程式碼、Dockerfile 和 Compose 檔案。

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 Docker Swarm 叢集，並將多容器應用程式部署到此叢集。

若要了解如何整合 Docker Swarm 與 Visual Studio Team Services，請繼續進行搭配 Docker Swarm 和 VSTS 的 CI/CD。

> [!div class="nextstepaction"]
> [搭配 Docker Swarm 和 VSTS 的 CI/CD](./container-service-docker-swarm-setup-ci-cd.md)