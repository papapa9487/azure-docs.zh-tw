---
title: "Jenkins CI/CD 在 Azure Container Service 中使用 Kubernetes | Microsoft Docs"
description: "如何使用 Jenkins 自動化 CI/CD 程序以在 Azure Container Service 中部署和升級 Kubernetes 上的容器化應用程式"
services: container-service
documentationcenter: 
author: chzbrgr71
manager: johny
editor: 
tags: acs, azure-container-service, jenkins
keywords: "Docker, 容器, Kubernetes, Azure, Jenkins"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: briar
ms.custom: mvc
ms.openlocfilehash: 2078d0694fc4dd6e83ecd2792588b4254980cd78
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Jenkins 與 Azure Container Service 和 Kubernetes 整合 
在本教學課程中，我們逐步解說使用 Jenkins 平台將多容器應用程式的連續整合設定至 Azure Container Service Kubernetes 的程序。 工作流程會更新 Docker 中樞內的容器映像，並使用部署導入升級 Kubernetes Pod。 

## <a name="high-level-process"></a>高階程序
本文章中的基本步驟如下︰ 
- 在 Container Service 中安裝 Kubernetes 叢集
- 設定 Jenkins 和容器服務的存取權
- 建立 Jenkins 工作流程
- 端對端測試 CI/CD 程序

## <a name="install-a-kubernetes-cluster"></a>安裝 Kubernetes 叢集
    
使用下列步驟部署 Azure Container Service 中的 Kubernetes 叢集。 完整文件位於[這裡](container-service-kubernetes-walkthrough.md)。

### <a name="step-1-create-a-resource-group"></a>步驟 1：建立資源群組
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>步驟 2︰部署叢集
> [!NOTE]
> 下列步驟需要本機 SSH 公用金鑰儲存在 ~/.ssh 資料夾中。
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>設定 Jenkins 和容器服務的存取權

### <a name="step-1-install-jenkins"></a>步驟 1：安裝 Jenkins
1. 使用 Ubuntu 16.04 LTS 建立 Azure VM。  因為在稍後的步驟中，您必須使用本機電腦上的 Bash 連線到這個 VM，請將「驗證類型」設定為「SSH 公開金鑰」，並貼上本機儲存在 ~/.ssh 資料夾中的 SSH 公開金鑰。  此外，記下您指定的「使用者名稱」，因為在稍後步驟中，將需要這個使用者名稱才能檢視 Jenkins 儀表板以及連線到 Jenkins VM。
2. 透過這些[指示](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu)安裝 Jenkins。 更詳細的教學課程在 [howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04)。
3. 若要在本機電腦上檢視 Jenkins 儀表板，可新增允許存取連接埠 8080 的輸入規則，從而更新可允許連接埠 8080 的 Azure 網路安全性群組。  或者，您將需要設定連接埠轉送，方法是執行此命令：`ssh -i ~/.ssh/id_rsa -L 8080:localhost:8080 <your_jenkins_user>@<your_jenkins_public_ip`
4. 使用瀏覽器連線到您的 Jenkins 伺服器，方法是瀏覽至公用 IP (http://<your_jenkins_public_ip>:8080)，並在第一次使用時，使用初始管理密碼將 Jenkins 儀表板解除鎖定。  管理密碼會儲存在 Jenkins VM 上的 /var/lib/jenkins/secrets/initialAdminPassword。  取得此密碼的簡單方法是 SSH 至 Jenkins VM 中：`ssh <your_jenkins_user>@<your_jenkins_public_ip>`。  接下來，請執行：`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`。
5. 透過這些[指示](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts)在 Jenkins 機器上安裝 Docker。 這可讓 Docker 命令在 Jenkins 工作中執行。
6. 設定 Docker 權限以允許 Jenkins 存取 Docker 端點。

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. 在 Jenkins 上安裝 `kubectl` CLI。 更多詳細資料位於[安裝和設定 kubectl](https://kubernetes.io/docs/tasks/kubectl/install/)。  Jenkins 作業將使用 'kubectl' 來管理及部署至 Kubernetes 叢集。

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>步驟 2︰設定 Kubernetes 叢集的存取權

> [!NOTE]
> 有多個方式可以完成下列步驟。 使用對您最簡單的方法。
>

1. 將 `kubectl` 組態檔複製到 Jenkins 電腦，讓 Jenkins 作業能夠存取 Kubernetes 叢集。 這些指示假設您是從 Jenkins VM 以外的電腦使用 Bash，且本機 SSH 公開金鑰是儲存在電腦的 ~/.ssh 資料夾。

```bash
export KUBE_MASTER=<your_cluster_master_fqdn>
export JENKINS_USER=<your_jenkins_user>
export JENKINS_SERVER=<your_jenkins_public_ip>
sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir -m 777 /home/$JENKINS_USER/.kube/ \
&& sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir /var/lib/jenkins/.kube/ \
&& sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config $JENKINS_USER@$JENKINS_SERVER:~/.kube/config \
&& sudo ssh -i ~/.ssh/id_rsa $JENKINS_USER@$JENKINS_SERVER sudo cp /home/$JENKINS_USER/.kube/config /var/lib/jenkins/.kube/config \
```
        
2. 從 Jenkins 驗證 Kubernetes 叢集可存取。  若要這樣做，請 SSH 至 Jenkins VM 中：`ssh <your_jenkins_user>@<your_jenkins_public_ip>`。  接下來，請確認 Jenkins 可以成功連線到您的叢集：`kubectl cluster-info`。
    

## <a name="create-a-jenkins-workflow"></a>建立 Jenkins 工作流程

### <a name="prerequisites"></a>必要條件

- 程式碼儲存機制的 GitHub 帳戶。
- 用來儲存和更新映像的 Docker 中樞帳戶。
- 可以重新建立及更新的容器化應用程式。 您可以使用這個以 Golang 撰寫的範例容器應用程式：https://github.com/chzbrgr71/go-web 

> [!NOTE]
> 必須在您自己的 GitHub 帳戶中執行下列步驟。 隨意複製上述的儲存機制，但您必須使用自己的帳戶來設定 webhook 和 Jenkins 存取。
>

### <a name="step-1-deploy-initial-v1-of-application"></a>步驟 1︰部署應用程式的初始 v1
1. 使用下列命令從開發人員機器建置應用程式。 以您自己取代 `myrepo`。
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. 將映像推送至 Docker 中樞。

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. 部署 Kubernetes 叢集。
    
    > [!NOTE] 
    > 編輯 `go-web.yaml` 檔案以更新容器映像和儲存機制。
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>步驟 2︰設定 Jenkins 系統
1. 按一下 [管理 Jenkins] > [設定系統]。
2. 在 [GitHub] 下，選取 [新增 GitHub Server]。
3. 保留 **API URL** 為預設值。
4. 在 [認證] 下，使用 [密碼文字] 新增 Jenkins 認證。 我們建議使用在您 GitHub 使用者帳戶設定中設定的 GitHub 個人存取權杖。 更多與此相關的詳細資料在[這裡。](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
5. 按一下 [測試連接] 以確保正確設定。
6. 在 [全域屬性] 下，新增環境變數 `DOCKER_HUB`，並提供您 Docker 中樞的密碼。 (這在此示範中非常有用，但實際執行案例需要更安全的方法)。
7. 儲存。

![Jenkins GitHub 存取](./media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>步驟 3︰建立 Jenkins 工作流程
1. 建立 Jenkins 項目。
2. 提供名稱 (例如，「go-web」)，然後選取 **Freestyle 專案**。 
3. 檢查 **GitHub 專案**，並提供您 GitHub 儲存機制的 URL。
4. 在**原始程式碼管理**中，提供 GitHub 儲存機制 URL 和認證。 
5. 新增 [執行殼層] 類型的 [建置步驟]，並使用下列文字︰

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. 新增另一個 [執行殼層] 類型的 [建置步驟]，並使用下列文字︰

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Jenkins 建置步驟](./media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. 儲存 Jenkins 項目並以 [立即建置] 測試。

### <a name="step-4-connect-github-webhook"></a>步驟 4︰連線 GitHub webhook
1. 在您建立的 Jenkins 項目中，按一下 [設定]。
2. 在 [建置觸發程序] 下，選取 [GITScm 輪詢的 GitHub 攔截觸發程序] 和 [儲存]。 這會自動設定 GitHub webhook。
3. 在您 go-web 的 GitHub 儲存機制中，按一下 [設定 > Webhook]。
4. 請確認已成功新增 Jenkins webhook URL。 URL 應該以「github-webhook」結束。

![Jenkins webhook 組態](./media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>端對端測試 CI/CD 程序

1. 更新儲存機制的程式碼並使用 GitHub 儲存機制推入/同步處理。
2. 從 Jenkins 主控台中，檢查**建置記錄**並驗證作業已執行。 檢視主控台輸出以查看詳細資料。
3. 從 Kubernetes，檢視已升級部署的詳細資訊︰

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>後續步驟

- 部署 Azure 容器登錄，並將影像儲存在安全的儲存機制。 請參閱[使用 Azure 容器登錄](https://docs.microsoft.com/azure/container-registry)。
- 建置的更複雜工作流程，其包含在 Jenkins 中的並存部署和自動化測試。
- 如需關於 CI/CD 與 Jenkins 和 Kubernetes 的詳細資訊，請參閱 [Jenkins 部落格](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/)。
