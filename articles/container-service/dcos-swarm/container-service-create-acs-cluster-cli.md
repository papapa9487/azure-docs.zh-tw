---
title: "部署 Docker 容器叢集 - Azure CLI"
description: "在 Azure Container Service 中使用 Azure CLI 2.0 部署 Kubernetes、DC/OS，或 Docker Swarm 解決方案"
services: container-service
author: sauryadas
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 03/01/2017
ms.author: saudas
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 40d5ea0e7abce165659219db8842ab64ac75fda7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 部署 Docker 容器主控解決方案

在 Azure CLI 2.0 中使用 `az acs` 命令建立及管理 Azure Container Service 中的叢集。 您也可以使用 [Azure 入口網站](container-service-deployment.md) 或 Azure Container Service API 來部署 Azure Container Service 叢集。

如需 `az acs` 命令的說明，請將 `-h` 參數傳遞至任何命令。 例如， `az acs create -h`。



## <a name="prerequisites"></a>必要條件
若要使用 Azure CLI 2.0 建立 Azure Container Service 叢集，您必須︰
* 有一個 Azure 帳戶 ([取得免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/))
* 已安裝及設定 [Azure CLI 2.0](/cli/azure/install-az-cli2)

## <a name="get-started"></a>開始使用 
### <a name="log-in-to-your-account"></a>登入您的帳戶
```azurecli
az login 
```

依照提示以互動方式進行登入。 如需其他登入方法，請參閱[開始使用 Azure CLI 2.0](/cli/azure/get-started-with-az-cli2)。

### <a name="set-your-azure-subscription"></a>設定您的 Azure 訂用帳戶

如果您有一個以上的 Azure 訂用帳戶，請設定預設訂用帳戶。 例如：

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>建立資源群組
建議您建立每個叢集的資源群組。 指定[可使用](https://azure.microsoft.com/en-us/regions/services/) Azure Container Service 的 Azure 區域。 例如：

```azurecli
az group create -n acsrg1 -l "westus"
```
輸出大致如下：

![建立資源群組](./media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>建立 Azure Container Service 叢集

若要建立叢集，請使用 `az acs create`。
上一步中建立的叢集名稱和資源群組名稱都是必要參數。 

其他輸入會設為預設值 (請參閱下方的畫面)，除非使用個別的參數覆寫。 例如，Orchestrator 預設會設定為 DC/OS。 如未指定，則會根據叢集名稱建立 DNS 名稱前置詞。

![az acs 建立使用量](./media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>使用預設值的快速 `acs create`
如果您在預設位置有 SSH RSA 公用金鑰檔案 `id_rsa.pub` (或已針對 [OS X 和 Linux](../../virtual-machines/linux/mac-create-ssh-keys.md) 或 [Windows](../../virtual-machines/linux/ssh-from-windows.md) 建立一個 SSH 公用金鑰)，請使用類似下列的命令︰

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
如果您沒有 SSH 公開金鑰，請使用此第二個命令。 此命令搭配 `--generate-ssh-keys` 參數可為您建立一個 SSH 公開金鑰。

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

輸入上述命令後，等候約 10 分鐘就會建立叢集。 命令輸出包含主要和代理程式節點的完整網域名稱 (FQDN) 以及可連接到第一個主要節點的 SSH 命令。 以下是簡短的輸出︰

![Image ACS create](./media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> [Kubernetes 逐步解說](../kubernetes/container-service-kubernetes-walkthrough.md)示範如何使用 `az acs create` 搭配預設值來建立 Kubernetes 叢集。
>

## <a name="manage-acs-clusters"></a>管理 ACS 叢集

使用其他 `az acs` 命令來管理您的叢集。 以下是一些範例。

### <a name="list-clusters-under-a-subscription"></a>列出訂用帳戶之下的叢集

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>列出資源群組中的叢集

```azurecli
az acs list -g acsrg1 --output table
```

![acs 清單](./media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>顯示容器服務叢集的詳細資料

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![acs 顯示](./media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>調整叢集
允許相應放大和相應縮小代理程式節點。 參數 `new-agent-count` 代表 ACS 叢集中新的代理程式數目。

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![acs 級別](./media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>刪除容器服務叢集
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
此命令不會刪除在建立容器服務時所建立的所有資源 (網路和儲存體)。 若要輕鬆刪除所有資源，建議您在不同的資源群組中部署每個叢集。 然後，不再需要叢集時，才能刪除資源群組。

## <a name="next-steps"></a>後續步驟
既然您有一個可運作的叢集，請參閱這些文件來了解連接和管理的詳細資料：

* [連接到 Azure Container Service 叢集](../container-service-connect.md)
* [使用 Azure Container Service 和 DC/OS](container-service-mesos-marathon-rest.md)
* [使用 Azure Container Service 和 Docker Swarm](container-service-docker-swarm.md)
* [使用 Azure Container Service 和 Kubernetes](../kubernetes/container-service-kubernetes-walkthrough.md)