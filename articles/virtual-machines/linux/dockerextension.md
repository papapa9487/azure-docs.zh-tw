---
title: "使用 Azure Docker VM 延伸模組 | Microsoft Docs"
description: "了解如何使用 Resource Manager 範本和 Azure CLI 2.0，在 Azure 中使用 Docker VM 延伸模組快速而安全地部署 Docker 環境"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 0cef78edaeec9d45aa733b1912d82d5a058ba289
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>使用 Docker VM 延伸模組在 Azure 中建立 Docker 環境
Docker 是常用的容器管理和映像處理平台，它能讓您在 Linux 上快速地操作容器。 在 Azure 中，您可以根據您的需求使用幾個方式來部署 Docker。 本文著重於搭配 Azure CLI 2.0 使用 Docker VM 延伸模組與 Azure Resource Manager 範本。 您也可以使用 [Azure CLI 1.0](dockerextension-nodejs.md) 來執行這些步驟。

## <a name="azure-docker-vm-extension-overview"></a>Azure Docker VM 延伸模組概觀
Azure Docker VM 擴充功能會在您的 Linux 虛擬機器 (VM) 中安裝並設定 Docker 精靈、Docker 用戶端和 Docker Compose。 相較於只使用 Docker 電腦或自行建立 Docker 主機，您使用 Azure Docker VM 延伸模組會有更多控制權和功能。 這些額外功能，例如 [Docker Compose](https://docs.docker.com/compose/overview/)，讓 Azure Docker VM 延伸模組適用於更穩固的開發人員或生產環境。

如需不同部署方法的詳細資訊，包括使用 Docker 電腦和 Azure Container Service，請參閱下列文章︰

* 如需快速應用程式原型，您可以使用 [Docker 電腦](docker-machine.md)建立單一 Docker 主機。
* 如需建置提供其他排程和管理工具的生產就緒、可調整環境，您可以[在 Azure Container Service 上部署 Docker Swarm 叢集](../../container-service/dcos-swarm/container-service-deployment.md)。

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>使用 Azure Docker VM 擴充功能部署範本
使用現有的快速入門範本建立使用 Azure Docker VM 延伸模組的 Ubuntu VM，以安裝及設定 Docker 主機。 您可以在這裡檢視範本︰ [使用 Docker 簡易部署 Ubuntu VM](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)。 您需要安裝最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，並使用 [az login](/cli/azure/#login) 來登入 Azure 帳戶。

首先，使用 [az group create](/cli/azure/group#create) 建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli
az group create --name myResourceGroup --location eastus
```

接下來，使用 [az group deployment create](/cli/azure/group/deployment#create) 來部署 VM，其中包含來自 [GitHub 上此 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)的 Azure Docker VM 擴充功能。 針對 *newStorageAccountName*、*adminUsername*、*adminPassword* 和 *dnsNameForPublicIP* 提供您自己唯一的值，如下所示：

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

需要幾分鐘的時間才能完成部署。 部署完成後，[移至下一個步驟](#deploy-your-first-nginx-container)以透過 SSH 連接到您的 VM。 

(選擇性) 若要將控制權交回給提示字元，並且讓部署繼續在背景中執行，請將 `--no-wait` 旗標新增至上述命令。 此程序可讓您在部署繼續執行數分鐘時，在 CLI 中執行其他工作。 

您可以接著使用 [az vm show](/cli/azure/vm#show)，檢視有關 Docker 主機狀態的詳細資訊。 下列範例會在名為 myResourceGroup 的資源群組中檢查名為 myDockerVM 的 VM 狀態 (範本的預設名稱 - 請勿變更這個名稱)：

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --query [provisioningState] \
    --output tsv
```

當此命令傳回 Succeeded 時，即表示部署已完成，您可以在下列步驟中透過 SSH 連線到 VM。

## <a name="deploy-your-first-nginx-container"></a>部署您的第一個 NGINX 容器
若要檢視 VM 的詳細資料，包括 DNS 名稱，請使用 [az vm show](/cli/azure/vm#show)：

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

以 SSH 連線到新的 Docker 主機。 請提供您自己的 DNS 名稱，如下所示：

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

一旦登入 Docker 主機後，請執行 NGINX 容器︰

```bash
sudo docker run -d -p 80:80 nginx
```

下載 NGINX 影像和啟動容器時，輸出類似下列的範例︰

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

檢查您的 Docker 主機上執行的容器狀態，如下所示︰

```bash
sudo docker ps
```

輸出類似下列的範例，會顯示 NGINX 容器正在執行，且正在轉送 TCP 連接埠 80 和 443︰

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

若要查看容器實際運作的情況，請開啟網頁瀏覽器，然後輸入 Docker 主機的 DNS 名稱︰

![執行 ngnix 容器](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Azure Docker VM 延伸模組範本參考
前一個範例使用現有的快速入門範本。 您也可以使用您自己的 Resource Manager 範本來部署 Azure Docker VM 延伸模組。 若要這樣做，請將下列項目新增至 Resource Manager 範本，適當地定義 VM 的 `vmName`︰

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.*",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

如需更詳細的 Resource Manager 範本使用逐步解說，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。

## <a name="next-steps"></a>後續步驟
您可能會想要使用 [Docker Compose](https://docs.docker.com/compose/overview/) 來[設定 Docker 精靈 TCP 連接埠](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket)、了解[Docker 安全性](https://docs.docker.com/engine/security/security/)或部署容器。 如需有關 Azure Docker VM 延伸模組本身的詳細資訊，請參閱 [GitHub 專案](https://github.com/Azure/azure-docker-extension/)。

閱讀有關 Azure 中其他 Docker 部署選項的詳細資訊︰

* [使用 Docker 電腦搭配 Azure 驅動程式](docker-machine.md)  
* [在 Azure 虛擬機器上開始使用 Docker 和 Compose 定義並執行多容器應用程式](docker-compose-quickstart.md)。
* [部署 Azure 容器服務叢集](../../container-service/dcos-swarm/container-service-deployment.md)

