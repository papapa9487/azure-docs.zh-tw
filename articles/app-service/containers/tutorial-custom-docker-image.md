---
title: "針對 Azure Web App for Containers 使用自訂 Docker 映像 | Microsoft Docs"
description: "如何針對 Azure Web App for Containers 使用自訂 Docker 映像。"
keywords: "azure app service, web 應用程式, linux, docker, 容器"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 760772d1d1c79dd4a1114c36971de0b3693ab74f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-custom-docker-image-for-azure-web-app-for-containers"></a>針對 Azure Web App for Containers 使用自訂 Docker 映像

[Web App for Containers](app-service-linux-intro.md) 在 Linux 上提供內建的 Docker 映像，且支援特定的版本，例如 PHP 7.0 和 Node.js 4.5。 Web App for Containers 會利用 Docker 容器技術，來裝載內建映像和自訂映像作為平台即服務。 在此教學課程中，您將學習如何建置用於 Web App for Containers 的自訂 Docker 映像，如果您的語言沒有內建的映像，或您的應用程式需要的特定設定未提供於內建影像，這就是常見的模式。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要：

* [Git](https://git-scm.com/downloads)
* 有效的 [Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)
* [Docker](https://docs.docker.com/get-started/#setup)
* [Docker Hub 帳戶](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下載範例

在終端機視窗中執行下列命令，將範例應用程式存放庫複製到本機電腦，然後變更為包含範例程式碼的目錄。

```bash
git clone https://github.com/Azure-Samples/use-custom-docker-image.git
cd use-custom-docker-image
```

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="build-the-image-from-the-docker-file"></a>從 Docker 檔案建立映像

下列 Docker 檔案會描述執行我們應用程式所需的 Python 環境。 此外，映像會設定 [SSH](https://www.ssh.com/ssh/protocol/) 伺服器，以在容器與主機之間進行安全通訊。

```docker
# Use an official Python runtime as a parent image
FROM python

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Run python's package manager and install the flask package
RUN pip install flask

# Configure ports
EXPOSE 2222 80

# Run apt-get, to install the SSH server
RUN apt-get update \ 
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "root:Docker!" | chpasswd

#Copy the sshd_config file to its new location
COPY sshd_config /etc/ssh/

# Start the SSH service
RUN service ssh start

# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/
    
# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh 

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

若要建置 Docker 映像，請執行 `docker build` 命令，然後提供名稱、`mydockerimage` 和標記、`v1.0.0`。 將 `<docker-id>` 取代為 Docker Hub 帳戶識別碼。

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

命令會產生類似下列的輸出：

```bash
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  23.04kB
Step 1/13 : FROM python
 ---> 968120d8cbe8
Step 2/13 : WORKDIR /app
 ---> Using cache
 ---> dd6fdca5aa65
Step 3/13 : ADD . /app
 ---> e05c8f4beeae
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [145 kB]
Get:3 http://deb.debian.org jessie Release.gpg [2373 B]
Fetched 9988 kB in 7s (1266 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
The following extra packages will be installed:
  init-system-helpers libwrap0 openssh-sftp-server
Suggested packages:
  ssh-askpass rssh molly-guard ufw monkeysphere
Recommended packages:
  tcpd xauth ncurses-term
The following NEW packages will be installed:
  init-system-helpers libwrap0 openssh-server openssh-sftp-server
0 upgraded, 4 newly installed, 0 to remove and 3 not upgraded.
Need to get 442 kB of archives.
After this operation, 1138 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian/ jessie/main libwrap0 amd64 7.6.q-25 [58.5 kB]
Creating SSH2 RSA key; this may take some time ...
2048 f0:e9:fb:69:de:62:a4:5c:a3:7c:b3:41:e9:2e:96:a3 /etc/ssh/ssh_host_rsa_key.pub (RSA)
Creating SSH2 DSA key; this may take some time ...
1024 4a:5e:89:bd:aa:2d:71:bb:0e:3a:32:94:fb:c0:b1:4d /etc/ssh/ssh_host_dsa_key.pub (DSA)
Processing triggers for systemd (215-17+deb8u7) ...
 ---> 5b416a7dcdca
Removing intermediate container 283b3b4623d7
Step 13/13 : CMD python app.py
 ---> Running in 1c776e5e0772
 ---> 1bfc1bbc968d
Removing intermediate container 1c776e5e0772
Successfully built 1bfc1bbc968d
Successfully tagged <docker-id>/myDockerImage:v1.0.0
```

測試組建運作的方式是執行 Docker 容器。 發出 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令，並將映像的名稱和標記傳遞給它。 您也必須使用 `-p` 引數來指定連接埠。

```bash
docker run -p 80:2222 <docker-ID>/mydockerimage:v1.0.0
```

驗證 web 應用程式和容器是否會正常運作，方法是在本機瀏覽 web 應用程式。

![在本機測試 Web 應用程式](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-a-docker-image-to-docker-hub"></a>將 Docker 映像推送至 Docker Hub

登錄是裝載映像並提供服務映像和容器服務的應用程式。 您必須將映像推送到登錄，才能共用您的映像。 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> 推送到私人 Docker 登錄嗎？ 請參閱選擇性指示以[將 Docker 映像推送至私人登錄](#push-a-docker-image-to-private-registry-optional)。

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub 是 Docker 映像的登錄，可讓您裝載自己的公用或私人存放庫。 若要將自訂的 Docker 映像推送到公用 Docker Hub，請使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令，並提供完整的映像名稱和標記。 完整的映像名稱和標記看起來像下列範例：

```bash
<docker-id>/image-name:tag
```

如果您尚未登入 Docker Hub，請在嘗試推送映像之前，先使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令。

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

「登入成功」訊息會確認您已登入。 一旦登入之後，您可以使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令將映像推送到 Docker Hub。

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

檢查命令的輸出來確認已成功推送。

```bash
The push refers to a repository [docker.io/<docker-id>/python-flask]
e9aa2c6d0f34: Pushed
0fdcb490aeec: Pushed
08ae61c7869c: Pushed
2548e7db2a94: Mounted from library/python
325b9d6f2920: Pushed
815acdffadff: Mounted from library/python
97108d083e01: Mounted from library/python
5616a6292c16: Mounted from library/python
f3ed6cb59ab0: Mounted from library/python
654f45ecb7e3: Mounted from library/python
2c40c66f7667: Mounted from library/python
v1: digest: sha256:a910d5b77e6960c01745a87c35f3d1a13ba73231ac9a4664c5011b1422d59b60 size: 2632
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

## <a name="create-web-app-for-containers"></a>建立 Web App for Containers

您可以使用 Azure Web 應用程式在雲端中裝載原生 Linux 應用程式。 若要建立 Web App for Containers，您必須執行 Azure CLI 命令，可建立群組、然後是服務方案，以及最後是 web 應用程式本身。 首先，執行 [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) 命令，並傳入位置和唯一名稱。

```azurecli-interactive
az group create --location "West Europe" --name myResourceGroup
```

您會看到類似以下顯示的範例：

```json
{
  "id": "/subscriptions/432849d3e4-4f90-a782-87c11e-5e59d6dd/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

使用 [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) 命令，透過群組名稱來協助建立應用程式服務方案。 您也需要以唯一的名稱為其命名，並設定 `--is-linux` 旗標。

```azurecli-interactive
az appservice plan create --name myServicePlan --resource-group myResourceGroup --is-linux
```

建立服務方案會產生類似下列範例的結果：

```json
  {- Starting...
  "adminSiteName": null,
  "appServicePlanName": "myServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/resourceGroups/myResourceGroup/provide
rs/Microsoft.Web/serverfarms/myServicePlan",
  "kind": "linux",
  "location": "West Europe", 
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "tier": "Basic"
  },
  "status": "Ready",
  "subscription": "",
  "tags": null,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

現在，您已建立資源群組和服務方案，可以執行 [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) 命令來建立 web 應用程式。 請注意，執行階段堆疊為 Python 3.4，且 web 應用程式會使用先前步驟中的資源群組及服務方案安裝程式。

```azurecli-interactive
az webapp create -g myResourceGroup -p myServicePlan -n <web-app-name> --runtime "python|3.4" 
```

要建立 web 應用程式的命令會產生如下所示的輸出：

```json
{- Starting ..
  "availabilityState": "Normal",
   "enabled": true,
  "enabledHostNames": [
    "<web-app-name>.azurewebsites.net",
    "<web-app-name>.scm.azurewebsites.net"
  ],
  "ftpPublishingUrl": "ftp://waws-prod-am2-085.ftp.azurewebsites.windows.net/site/wwwroot",
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<web-app-name>.azurewebsites.net",
    },
  ],
  "hostNames": [
    "<web-app-name>.azurewebsites.net"
  ],
  "hostNamesDisabled": false,
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/5e59d6dd-d3e4-4f90-a782-43284987c11e/resourceGroups/myResourceGroup/providers/Microsoft.
Web/sites/<web-app-name>",
  "lastModifiedTimeUtc": "2017-08-08T21:09:33.693333",
  "location": "West Europe",
  "name": "<web-app-name>",
  "outboundIpAddresses": "13.81.108.99,52.232.76.83,52.166.73.203,52.233.173.39,52.233.159.48",
  "resourceGroup": "myResourceGroup"
}

```

大部分 web 應用程式的應用程式設定都需要設定。 如果您使用的是其他人所建置的現有 Docker 映像，映像可能需要連接埠 80 以外的連接埠給應用程式。 若要設定 `WEBSITES_PORT`，請執行 [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config/appsettings) 命令，如下列程式碼範例所示：

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <web-app-name> --settings WEBSITES_PORT=2222
```

> [!NOTE]
> 應用程式設定會區分大小寫。
>

瀏覽 Web 應用程式以確認它可運作。 請記得連接埠號碼。

![測試 web 應用程式連接埠設定](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="configure-web-app-to-use-docker-container-from-docker-hub"></a>將 Web 應用程式設定成從 Docker Hub 使用 Docker 容器

[az webapp config](https://docs.microsoft.com/cli/azure/webapp/config) 命令可讓您使用自訂的 Docker 映像。

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> 從私人 Docker 登錄部署？ 請參閱選擇性指示以[設定 Web 應用程式，從私人登錄使用 Docker 容器](#configure-web-app-to-use-docker-container-from-a-private-registry-optional)。

<!-- # [Docker Hub](#tab/docker-hub)-->

若要將 web 應用程式設為使用公用 Docker 登錄，請將應用程式、資源群組，以及映像名稱和 URL 的名稱傳遞至 [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set) 命令。

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage
--docker-registry-server-url <docker-id>/myContainerRegistry
```

設定變更成功會傳回容器的相關一般資訊。

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "{docker-id}/mydockerimage:v1.0.0"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{docker-id}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

## <a name="test-the-application-in-azure"></a>在 Azure 中測試應用程式

在測試之前，您必須使用 [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart) 重新啟動 web 應用程式，設定變更才會生效。

```azurecli-interactive
az webapp restart --name <web-app-name> --resource-group myResourceGroup
```

重新啟動命令會以無訊息模式重新啟動 web 應用程式，因此您在終端機中不會看到任何意見反應。 一旦 Web 應用程式執行之後，可瀏覽 URL (網址為 `http://<username>.azurewebsites.net`) 來測試 web 應用程式。 確認應用程式會顯示新的歡迎訊息。

![在 Azure 中測試 web 應用程式](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>變更 Web 應用程式並重新部署

名為 `app.py` 的 Python 檔案存在於 `using-custom-docker-image` 目錄中。 這個檔案包含的程式碼會顯示 `Hello World!` 訊息。 將行變更讓它顯示 `Hello World of Web Apps running in Docker Containers!` 訊息。

```python
return "Hello World of Web Apps running in Docker Containers!"
```

一旦您修改了 Python 檔案並加以儲存後，必須重建並推送新的 Docker 映像。 接著，重新啟動 Web 應用程式，變更才會生效。 使用您先前已在本教學課程中使用的相同命令。 您可以參考[從 Docker 檔案建立映像](#build-the-image-from-the-docker-file)和[推送 Docker 映像](#push-docker-image)區段。 遵循[在 Azure 中測試應用程式](#tTest-the-application-in-azure)中的指示來測試 web 應用程式

## <a name="connect-to-web-app-for-containers-using-ssh"></a>使用 SSH 連線到 Web App for Containers

SSH 可讓容器和用戶端之間進行安全通訊。 您必須將自訂的 Docker 映像建置到 Dockerfile，它才能支援 SSH。 您要在 Docker 檔案本身啟用 SSH。 已將 SSH 指示新增至範例 Dockerfile 中，因此您可以使用自己的自訂映像來遵循這些指示：

* [執行](https://docs.docker.com/engine/reference/builder/#run)指示會呼叫 `apt-get`，然後將根帳戶的密碼設為 `"Docker!"`。

    ```docker
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

    > [!NOTE]
    > 此設定不允許容器的外部連線。 SSH 只能透過 Kudu/SCM 站台提供。 Kudu/SCM 站台會向發佈認證進行驗證。

* [複製](https://docs.docker.com/engine/reference/builder/#copy)指示會指示 Docker 引擎將 [sshd_config](http://man.openbsd.org/sshd_config) 檔案複製到 /etc/ssh/ 目錄。 設定檔需以我們 Azure-App-Service GitHub 存放庫中的 [sshd_config 檔案](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config)作為基礎。

    > [!NOTE]
    > sshd_config 檔案必須包含下列項目︰ 
    > * `Ciphers` 必須在此清單中包含至少一個項目：`aes128-cbc,3des-cbc,aes256-cbc`。
    > * `MACs` 必須在此清單中包含至少一個項目：`hmac-sha1,hmac-sha1-96`。

    ```docker
    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/
    ```

* [公開](https://docs.docker.com/engine/reference/builder/#expose)指示會在容器中公開連接埠 2222。 雖然已知根密碼，但無法從網際網路存取連接埠 2222。 它是供內部使用的連接埠，只有私人虛擬網路之橋接網路內的容器可以存取。 在這之後，命令會複製 SSH 設定詳細資料並啟動 `ssh` 服務。

    ```docker
    # Configure ports
    EXPOSE 2222 80

    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/

    # Start the SSH service
    RUN service ssh start
    ```

範例程式碼中的 `init_container.sh` 檔案包含如何在容器執行時加以初始化的指示。 Dockerfile 會使用[複製](https://docs.docker.com/engine/reference/builder/#copy)、[執行](https://docs.docker.com/engine/reference/builder/#run)和 [CMD](https://docs.docker.com/engine/reference/builder/#cmd) 指示正確啟動 `init_container.sh` 指令碼。

```docker
# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/

# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

您需要確認特定應用程式正在容器中執行。 若要檢查容器及驗證執行程序，請開啟瀏覽器並瀏覽至 `https://<app name>.scm.azurewebsites.net/webssh/host` 來啟動。 然後，系統會將您重新導向至一個顯示互動式主控台的頁面。 在提示字元提出 `top` 命令。

```bash
top
```

`top` 命令會公開容器中所有執行中的程序。

```bash
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

恭喜！ 您已針對 Azure Web App for Containers 設定自訂 Docker 映像。

## <a name="push-a-docker-image-to-private-registry-optional"></a>將 Docker 映像推送至私人登錄 (選擇性)

Azure Container Registry 是 Azure 中用來裝載私人映像的受控 Docker 服務。 部署可能是任何類型，包括 [Docker Swarm](https://docs.docker.com/engine/swarm/)、[Kubernetes](https://kubernetes.io/) 和 Azure 應用程式服務容器。 使用 Azure Container Registry 就如同使用任何私人登錄，因此如果您需要使用自己的私人登錄，完成這項工作的步驟將會類似。

使用 [az acr create](https://docs.microsoft.com/cli/azure/acr#az_acr_create) 命令來建立 Azure Container Registry。 將 SKU 的名稱、資源群組和 `Basic` 傳入。 可用的 SKU 為 `Classic`、`Basic`、`Standard` 和 `Premium`。

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

建立容器時，會產生下列輸出：

```bash
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/{azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

您必須提供認證讓登錄接受推送，才能將映像推送至登錄。 您可以使用 [az acr show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) 命令來擷取這些認證。 

```azurecli-interactive
az acr credential show --name {azure-container-registry-name}
```

此命令所顯示的兩個密碼可與使用者名稱搭配使用。

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<azure-container-registry-name>"
}
```

現在，您已具備所需的認證，可使用 `docker login` 命令登入 Azure Container Registry。 需要伺服器名稱才能登入。 請使用此格式：`{azure-container-registry-name>.azurecr.io`。

```bash
docker login <azure-container-registry-name>.azurecr.io --username <azure-container-registry-name> --password <password> 
```

確認登入成功。 使用 `docker push` 命令，並以登錄名稱再接著映像名稱和標籤來標記映像，然後推送映像。

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

列出 ACR 存放庫以確認推送已成功將容器新增至登錄。 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

在登錄中列出映像會確認 `mydockerimage` 位於登錄中。

```json
[
  "mydockerimage"
]
```

## <a name="configure-web-app-to-use-docker-container-from-a-private-registry-optional"></a>設定 Web 應用程式，從私人登錄使用 Docker 容器 (選擇性)

您可以在 Linux 上設定 web 應用程式，讓它們執行儲存在 Azure Container Registry 中的容器。 使用 Azure Container Registry 就如同使用任何私人登錄，因此如果您需要使用自己的私人登錄，完成這項工作的步驟將會類似。

[az acr credential show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) 命令會顯示容器登錄的密碼。 複製使用者名稱和其中一個密碼，因此您可以使用它來設定下一個步驟中的 web 應用程式。

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<azure-container-registry-name>"
}
```

執行 [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set) 命令。 此命令會將自訂的 Docker 映像指派給 web 應用程式。 請注意，您需要格式為 `https://{your-registry-username}.azurecr.io` 的 URL。 此外，web 應用程式需要上一個步驟中取得的使用者名稱和密碼，才能存取容器登錄。

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

> [!NOTE]
> 登錄的 URL 中必須有 `https`。
>

此命令顯示的輸出類似下列的 JSON 字串，顯示設定變更成功：

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "mycontainerregistry.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{azure-container-registry-name}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>後續步驟

[Azure App Service Web App for Containers 常見問題集](app-service-linux-faq.md)
