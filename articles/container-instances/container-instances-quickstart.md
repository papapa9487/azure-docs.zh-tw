---
title: "快速入門 - 建立您的第一個 Azure Container Instances 容器"
description: "部署和開始使用 Azure Container Instances"
services: container-instances
author: seanmck
manager: timlt
editor: mmacy
ms.service: container-instances
ms.topic: quickstart
ms.date: 11/29/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: f6c809698bed4f91a7121c4515b4375b3cad6d70
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>在 Azure Container Instances 中建立您的第一個容器
Azure Container Instances 能讓您在 Azure 中輕鬆建立及管理 Docker 容器，不需要佈建虛擬機器或採用高階服務即可完成。 在本快速入門中，您會在 Azure 中建立容器，並使用公用 IP 位址向網際網路公開此容器。在本快速入門中，您會在 Azure 中建立容器，並使用公用 IP 位址向網際網路公開此容器。 只需要一個命令就能完成這項作業。 只在幾秒內，您就能在瀏覽器中看到下列結果：

![在瀏覽器中檢視使用 Azure Container Instances 所部署的應用程式][aci-app-browser]

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成此快速入門。 如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.21 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

Azure Container Instances 是 Azure 資源，必須放在 Azure 資源群組中，這是可用來部署和管理 Azure 資源的邏輯集合。

使用 [az group create][az-group-create] 命令來建立資源群組。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>建立容器

您可以向 [az container create][az-container-create] 命令提供名稱、Docker 映像和 Azure 資源群組來建立容器。 您可以選擇使用公用 IP 位址向網際網路公開容器。 在本快速入門中，您會部署一個裝載以 [Node.js](http://nodejs.org)撰寫之小型 Web 應用程式的容器。

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public --ports 80
```

在幾秒內，您就能取得要求的回應。 一開始，容器會處於**建立中**狀態，但應該會在幾秒鐘內啟動。 您可以使用 [az container show][az-container-show] 命令來檢查狀態：

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

在輸出的底部，您會看到容器的佈建狀態和其 IP 位址：

```json
...
"ipAddress": {
      "ip": "13.88.176.27",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

一旦容器轉為**成功**狀態時，您就可以在瀏覽器中使用提供的 IP 位址來連線至容器。

![在瀏覽器中檢視使用 Azure Container Instances 所部署的應用程式][aci-app-browser]

## <a name="pull-the-container-logs"></a>提取容器記錄

您可以使用 [az container logs][az-container-logs] 命令來提取您所建立之容器的記錄：

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

輸出：

```bash
listening on port 80
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://52.224.178.107/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
```

## <a name="delete-the-container"></a>刪除容器

當容器使用完畢後，您可以使用 [az container delete][az-container-delete] 命令來移除容器：

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

若要確認否已將容器刪除，請執行 [az container list](/cli/azure/container#az_container_list) 命令：

```azurecli-interactive
az container list --resource-group myResourceGroup -o table
```

**mycontainer** 不應該出現在命令的輸出中。 如果您的資源群組中沒有任何其他容器，則不會顯示任何輸出。

## <a name="next-steps"></a>後續步驟

本快速入門中所使用之容器的所有程式碼，連帶其 Dockerfile，都可以[在 GitHub 上][app-github-repo]取得。 如果您想要自行建置它，並使用 Azure Container Registry 將它部署至 Azure Container Instances，請繼續進行 Azure Container Instances 教學課程。

> [!div class="nextstepaction"]
> [Azure Container Instances 教學課程](./container-instances-tutorial-prepare-app.md)

若要試用在 Azure 上的協調流程系統中執行容器的選項，請參閱 [Service Fabric][service-fabric] 或 [Azure Container Service (ACS)][container-service] 快速入門。

<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
[container-service]: ../aks/kubernetes-walkthrough.md


<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
