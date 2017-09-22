---
title: "使用 Docker 電腦在 Azure 中建立 Linux 主機 | Microsoft Docs"
description: "說明如何使用 Docker 電腦在 Azure 中建立 Docker 主機。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 7772381e9796ddc2e0db215bab4f230473eaa462
ms.contentlocale: zh-tw
ms.lasthandoff: 09/13/2017

---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>如何使用 Docker 電腦在 Azure 中建立主機
這篇文章說明如何使用 [Docker 電腦](https://docs.docker.com/machine/)在 Azure 中建立主機。 `docker-machine` 命令會在 Azure 中建立 Linux 虛擬機器 (VM)，然後安裝 Docker。 接著，您可以使用相同本機工具和工作流程，在 Azure 中管理您的 Docker 主機。 若要在 Windows 10 中使用 docker-machine，您必須使用 Linux bash。

## <a name="create-vms-with-docker-machine"></a>使用 Docker 電腦建立 VM
首先，使用 [az account show](/cli/azure/account#show) 取得您的 Azure 訂用帳戶識別碼，如下所示：

```azurecli
sub=$(az account show --query "id" -o tsv)
```

您使用 `docker-machine create`，將 *azure* 指定為驅動程式，在 Azure 中建立 Docker 主機 VM。 如需詳細資訊，請參閱 [Docker Azure 驅動程式文件](https://docs.docker.com/machine/drivers/azure/)

下列範例會根據 "Standard D2 v2" 方案建立名為 *myVM* 的 VM、建立名為 *azureuser* 的使用者帳戶，並且在主機 VM 上開啟連接埠 *80*。 依照任何提示登入您的 Azure 帳戶，並且授與 Docker 電腦權限以建立及管理資源。

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_D2_v2 \
    myvm
```

輸出大致如下列範例所示：

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvmdocker) Completed machine pre-create checks.
Creating machine...
(myvmdocker) Querying existing resource group.  name="docker-machine"
(myvmdocker) Creating resource group.  name="docker-machine" location="westus"
(myvmdocker) Configuring availability set.  name="docker-machine"
(myvmdocker) Configuring network security group.  name="myvmdocker-firewall" location="westus"
(myvmdocker) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvmdocker) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvmdocker) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvmdocker) Creating public IP address.  name="myvmdocker-ip" static=false
(myvmdocker) Creating network interface.  name="myvmdocker-nic"
(myvmdocker) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvmdocker) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvmdocker"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvmdocker
```

## <a name="configure-your-docker-shell"></a>設定您的 Docker 殼層
若要連線到 Azure 中的 Docker 主機，請定義適當的連線設定。 如輸出結尾所述，檢視 Docker 主機的連線資訊，如下所示： 

```bash
docker-machine env myvmdocker
```

輸出類似於下列範例：

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvmdocker)
```

若要定義連線設定，您可以執行建議的設定命令 (`eval $(docker-machine env myvmdocker)`)，或手動設定環境變數。 

## <a name="run-a-container"></a>執行容器
為了查看作用中的容器，讓我們執行基本 NGINX 網頁伺服器。 使用 `docker run` 建立容器，並且為 Web 流量公開連接埠 80，如下所示：

```bash
docker run -d -p 80:80 --restart=always nginx
```

輸出類似於下列範例：

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

使用 `docker ps` 檢視執行中容器。 下列範例輸出顯示使用已公開連接埠 80 執行的 NGINX 容器：

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>測試容器
取得 Docker 主機的公用 IP 位址，如下所示：


```bash
docker-machine ip myvmdocker
```

若要查看作用中的容器，開啟網頁瀏覽器並輸入上述命令輸出中所述的公用 IP 位址：

![執行 ngnix 容器](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>後續步驟
您也可以使用 [Docker VM 延伸模組](dockerextension.md)建立主機。 如需使用 Docker Compose 的範例，請參閱[在 Azure 中開始使用 Docker 與 Compose](docker-compose-quickstart.md)。

