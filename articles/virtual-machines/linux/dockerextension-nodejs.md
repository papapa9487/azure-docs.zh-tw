---
title: "搭配 Azure CLI 1.0 使用 Azure Docker VM 延伸模組 | Microsoft Docs"
description: "了解如何使用 Resource Manager 範本，在 Azure 中使用 Docker VM 延伸模組快速而安全地部署 Docker 環境。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: a3cbcf63533f4042dcd695e141655c5814bd7068
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>在 Azure 中使用 Docker VM 延伸模組搭配 Azure CLI 1.0 建立 Docker 環境
Docker 是常用的容器管理和映像處理平台，它能讓您在 Linux 上 (和 Windows 上) 快速地操作容器。 在 Azure 中，您可以根據您的需求使用幾個方式來部署 Docker。 本文著重於使用 Docker VM 延伸模組與 Azure Resource Manager 範本。 

如需不同部署方法的詳細資訊，包括使用 Docker 電腦和 Azure Container Service，請參閱下列文章︰

* 如需快速應用程式原型，您可以使用 [Docker 電腦](docker-machine.md)建立單一 Docker 主機。
* 對於較大且更穩定的環境，您可以使用 Azure Docker VM 延伸模組，其也支援 [Docker Compose](https://docs.docker.com/compose/overview/)，以產生一致的容器部署。 本文詳細說明使用 Azure Docker VM 延伸模組。
* 如需建置提供其他排程和管理工具的生產就緒、可調整環境，您可以[在 Azure Container Service 上部署 Docker Swarm 叢集](../../container-service/dcos-swarm/container-service-deployment.md)。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#azure-docker-vm-extension-overview) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](dockerextension.md) - 適用於資源管理部署模型的新一代 CLI 

## <a name="azure-docker-vm-extension-overview"></a>Azure Docker VM 延伸模組概觀
Azure Docker VM 擴充功能會在您的 Linux 虛擬機器 (VM) 中安裝並設定 Docker 精靈、Docker 用戶端和 Docker Compose。 相較於只使用 Docker 電腦或自行建立 Docker 主機，您使用 Azure Docker VM 延伸模組會有更多控制權和功能。 這些額外功能，例如 [Docker Compose](https://docs.docker.com/compose/overview/)，讓 Azure Docker VM 延伸模組適用於更穩固的開發人員或生產環境。

Azure Resource Manager 範本會定義您環境的整個結構。 範本可讓您建立和設定資源，例如 Docker 主機 VM、儲存體、以角色為基礎的存取控制 (RBAC) 以及診斷。 您可以重複使用這些範本，以一致的方式建立其他部署方式。 如需 Azure Resource Manager 和範本的詳細資訊，請參閱 [Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>使用 Azure Docker VM 擴充功能部署範本
使用現有的快速入門範本建立使用 Azure Docker VM 延伸模組的 Ubuntu VM，以安裝及設定 Docker 主機。 您可以在這裡檢視範本︰ [使用 Docker 簡易部署 Ubuntu VM](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)。 

您需要使用 Resource Manager 模式安裝和登入[最新的 Azure CLI](../../cli-install-nodejs.md)，如下所示：

```azurecli
azure config mode arm
```

使用 Azure CLI 部署範本，指定範本 URI。 下列範例會在 westus 位置建立名為 myResourceGroup 的資源群組。 使用您自己的資源群組名稱和位置，如下所示︰

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

請回答提示來為您的儲存體帳戶命名、提供使用者名稱和密碼，並提供 DNS 名稱。 輸出類似於下列範例：

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: azureuser
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicidns
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Azure CLI 僅在幾秒鐘後讓您回到提示，但仍會由 Azure Docker VM 延伸模組建立並設定您的 Docker 主機。 需要幾分鐘的時間才能完成部署。 您可以使用 `azure vm show` 命令檢視關於 Docker 主機狀態的詳細資訊。

下列範例會在名為 myResourceGroup 的資源群組中檢查名為myDockerVM 的 VM 狀態 (範本的預設名稱 - 請勿變更這個名稱)。 輸入您在上一個步驟中建立的資源群組名稱︰

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

`azure vm show` 命令的輸出類似下列範例：

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicdns.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

在接近輸出頂端之處，您會看到 VM 的 ProvisioningState。 當這顯示為 Succeeded 時，即表示部署已完成，您可以透過 SSH 連線到 VM。

在輸出的結尾，FQDN 會顯示 Docker 主機的完整網域名稱。 此 FQDN 就是您在剩餘的步驟中透過 SSH 連線到 Docker 主機時所使用的 FQDN。

## <a name="deploy-your-first-nginx-container"></a>部署您的第一個 nginx 容器
一旦部署完成之後，SSH 會從本機電腦連線到新的 Docker 主機。 請輸入您自己的使用者名稱和 FQDN，如下所示︰

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com
```

一旦登入 Docker 主機後，請執行 nginx 容器︰

```bash
sudo docker run -d -p 80:80 nginx
```

下載 nginx 影像和啟動容器時，輸出類似下列的範例︰

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

輸出類似下列的範例，會顯示 nginx 容器正在執行，且正在轉送 TCP 連接埠 80 和 443︰

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

若要查看容器實際運作的情況，請開啟網頁瀏覽器，然後輸入 Docker 主機的 FQDN 名稱︰

![執行 ngnix 容器](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Azure Docker VM 延伸模組範本參考
前一個範例使用現有的快速入門範本。 您也可以使用您自己的 Resource Manager 範本來部署 Azure Docker VM 延伸模組。 若要這樣做，請將下列項目新增至 Resource Manager 範本，適當地定義 VM 的 vmName︰

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
    "typeHandlerVersion": "1.1",
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

