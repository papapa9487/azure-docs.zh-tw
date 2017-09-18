---
title: "使用 Jenkins 外掛程式來部署到 Azure App Service | Microsoft Docs"
description: "了解如何在 Jenkins 中使用 Azure App Service Jenkins 外掛程式將 Java Web 應用程式部署到 Azure"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: b2035d6bc0d323f2497a1db9b88d3ed015235b16
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-to-azure-app-service-with-jenkins-plugin"></a>使用 Jenkins 外掛程式來部署到 Azure App Service 
若要將 Java Web 應用程式部署到 Azure，您可以在 [Jenkins 管線](/azure/jenkins/execute-cli-jenkins-pipeline)中使用 Azure CLI，或者，也可以利用 [Azure App Service Jenkins 外掛程式](https://plugins.jenkins.io/azure-app-service)。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 將 Jenkins 設定為透過 FTP 部署至 Azure App Service 
> * 將 Jenkins 設定為透過 Docker 部署至 Linux 上的 Azure App Service 

## <a name="create-and-configure-jenkins-instance"></a>建立及設定 Jenkins 執行個體
如果您還沒有 Jenkins Master，請從[解決方案範本](install-jenkins-solution-template.md)開始著手，此範本包含 JDK8 和下列必要的外掛程式：

* [Jenkins Git 用戶端外掛程式](https://plugins.jenkins.io/git-client) v.2.4.6 
* [Docker Commons 外掛程式](https://plugins.jenkins.io/docker-commons) v.1.4.0
* [Azure 認證](https://plugins.jenkins.io/azure-credentials) v.1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-server) v.0.1

您可以使用 App Service 外掛程式，透過 Azure App Service 所支援的所有語言 (例如，C#、PHP、Java 和 node.js 等等) 來部署 Web 應用程式。 在此教學課程中，我們會使用[適用於 Azure 的簡單 Java Web 應用程式](https://github.com/azure-devops/javawebappsample)這個 Java 應用程式範例。 為了將存放庫分支至您自己的 GitHub 帳戶，按一下右上角的 [分支] 按鈕。  

必須要有 Java JDK 和 Maven 才能建置 Java 專案。 如果您使用 Jenkins 主要伺服器或 VM 代理程式來進行持續整合，請確定您已在其中安裝 Java JDK 和 Maven 元件。 

若要安裝，請使用 SSH 來登入 Jenkins 執行個體，然後執行下列命令：

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

若要部署至 Linux 上的 App Service，您還需要在 Jenkins 主要伺服器或 VM 代理程式上安裝用於建置的 Docker。 若要安裝 Docker，請參閱這篇文章：https://docs.docker.com/engine/installation/linux/ubuntu/。

## <a name="add-azure-service-principal-to-jenkins-credential"></a>將 Azure 服務主體新增到 Jenkins 認證

需要有 Azure 服務主體才能部署至 Azure。 

<ol>
<li>使用 [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) 或 [Azure 入口網站](/azure/azure-resource-manager/resource-group-create-service-principal-portal)來建立 Azure 服務主體</li>
<li>在 Jenkins 儀表板中，按一下 [Credentials] \(認證\) -> [System] \(系統\) -> 。 按一下 [Global credentials(unrestricted)] \(全域認證 (不受限)\)。</li>
<li>按一下 [Add Credentials] \(新增認證\) 來填寫 [Subscription ID] \(訂用帳戶 ID\)、[Client ID] \(用戶端識別碼\)、[Client Secret] \(用戶端祕密\) 及 [OAuth 2.0 Token Endpoint] \(OAuth 2.0 權杖端點\)，以新增 Microsoft Azure 服務主體。 請提供要在後續步驟中使用的識別碼 (**mySp**)。</li>
</ol>

## <a name="azure-app-service-plugin"></a>Azure App Service 外掛程式

Azure App Service 外掛程式 v1.0 支援透過下列方式來持續部署到 Azure Web 應用程式：

* Git 和 FTP
* 適用於 Linux 上 Web 應用程式的 Docker

## <a name="configure-jenkins-to-deploy-web-app-through-ftp-using-the-jenkins-dashboard"></a>使用 Jenkins 儀表板將 Jenkins 設定為透過 FTP 來部署 Web 應用程式

若要將專案部署到 Azure Web 應用程式，您可以使用 Git 或 FTP 上傳您的建置構件 (例如，採用 Java 的 .war 檔案)。

於 Jenkins 中設定作業之前，您需要有 Azure App Service 方案和 Web 應用程式以便執行 Java 應用程式。


1. 使用 [az appservice plan create](/cli/azure/appservice/plan#create) CLI 命令，建立搭配**免費**定價層的 Azure App Service 方案。 Appservice 方案會定義用來託管應用程式的實體資源。 所有指派給 Appservice 方案的應用程式都會共用這些資源，從而讓您節省託管多個應用程式的成本。
2. 建立 Web 應用程式。 您可以使用 [Azure 入口網站](/azure/app-service-web/web-sites-configure)或使用下列 Az CLI 命令：
```azurecli-interactive 
az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
```

3. 確定您已設定好應用程式所需的 Java 執行階段組態。 下列 Azure CLI 命令會將 Web 應用程式設定為在最新的 Java 8 JDK 和 [Apache Tomcat](http://tomcat.apache.org/) 8.0 上執行。
```azurecli-interactive
az webapp config set \
--name <myAppName> \
--resource-group <myResourceGroup> \
--java-version 1.8 \
--java-container Tomcat \
--java-container-version 8.0
```

### <a name="set-up-the-jenkins-job"></a>設定 Jenkins 作業


1. 在 Jenkins 儀表板中建立新的**自由樣式**專案
2. 藉由提供**存放庫 URL**，將**原始程式碼管理**設定為使用[適用於 Azure 的簡單 Java Web 應用程式](https://github.com/azure-devops/javawebappsample)的本機分支。 例如：http://github.com/&lt;yourID>/javawebappsample。
3. 新增建置步驟以使用 Maven 來建置專案。 新增**執行殼層**即可達到此目的。 在此範例中，我們另外需要一個步驟來將目標資料夾中的 *.war 檔案重新命名為 ROOT.war。   
```bash
mvn clean package
mv target/*.war target/ROOT.war
```

4. 選取 [發行 Azure Web 應用程式] 來新增建置後動作。
5. 提供上一個步驟所儲存的 Azure 服務主體「mySp」。
6. 在 [應用程式組態] 區段中，選擇訂用帳戶中的資源群組和 Web 應用程式。 此外掛程式會自動偵測 Web 應用程式是採用 Windows 架構還是 Linux 架構。 若為 Windows 架構的 Web 應用程式，系統會顯示 [發行檔案] 選項。
7. 填入您想要部署的檔案 (例如，如果您使用 Java，則填入 war 套件)。[來源目錄] 和 [目標目錄] 是選擇性欄位。 參數可讓您在上傳檔案時指定來源和目標資料夾。 Azure 上的 Java Web 應用程式會在 Tomcat 伺服器中執行。 因此，您會將 war 套件上傳到 webapps 資料夾。 在此範例中，請將 [來源目錄] 設定為「target」，並對 [目標目錄] 提供「webapps」。
8. 如果您想要部署至生產環境以外的位置，您也可以設定**位置**名稱。
9. 儲存專案並建置該專案。 建置完成時，您的 Web 應用程式就會部署至 Azure。

### <a name="deploy-web-app-through-ftp-using-jenkins-pipeline"></a>使用 Jenkins 管線來透過 FTP 部署 Web 應用程式

外掛程式已隨時可供管線使用。 您可以參考 GitHub 存放庫中的範例。

1. 在 GitHub Web UI 中，開啟 **Jenkinsfile_ftp_plugin** 檔案。 按一下鉛筆圖示來編輯此檔案，更新您 Web 應用程式的資源群組和名稱 (分別位於第 11 行和第 12 行)。    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. 變更第 14 行以更新您 Jenkins 執行個體中的認證識別碼。    
```java
withCredentials([azureServicePrincipal('<mySp>')]) {
```

### <a name="create-a-jenkins-pipeline"></a>建立 Jenkins 管線

1. 在網頁瀏覽器中開啟 Jenkins，按一下 [New Item] \(新增項目\)。
2. 為作業提供一個名稱，然後選取 [Pipeline] \(管線\)。 按一下 [確定] 。
3. 接著，按一下 [Pipeline] \(管線\) 索引標籤。
4. 針對 [Definition] \(定義\)，選取 [Pipeline script from SCM] \(來自 SCM 的管線指令碼\)。
5. 針對 [SCM]，選取 [Git]。 輸入您分支存放庫的 GitHub URL：https:&lt;您的分支存放庫>.git
6. 將**指令碼路徑**更新為「Jenkinsfile_ftp_plugin」
7. 按一下 [儲存] 並執行作業。

## <a name="configure-jenkins-to-deploy-web-app-on-linux-through-docker"></a>將 Jenkins 設定為透過 Docker 在 Linux 上部署 Web 應用程式

除了透過 Git/FTP，Linux 上的 Web 應用程式還支援使用 Docker 來進行部署。 若要使用 Docker 來進行部署，您必須提供一個 Dockerfile，此檔案會將您的 Web 應用程式與服務執行階段封裝成 Docker 映像。 接著，外掛程式會建置該映像，將它推送到 Docker 登錄，然後將該映像部署到您的 Web 應用程式。

Linux 上的 Web 應用程式也支援 Git 和 FTP 等傳統方式，但這些方式只適用於內建語言 (.NET Core、Node.js、PHP 和 Ruby)。 若為其他語言，則需要將您的應用程式程式碼和服務執行階段一起封裝到 Docker 映像，然後使用 Docker 來部署。

於 Jenkins 中設定作業之前，您必須在 Linux 上擁有 Azure 應用程式服務。 您還需要有容器登錄，以便儲存和管理您的私人 Docker 容器映像。 您可以使用 DockerHub；我們會在此範例中使用 Azure Container Registry。

* 您可以遵循[這裡](../app-service/containers/quickstart-nodejs.md)的步驟在 Linux 上建立 Web 應用程式 
* Azure Container Registry 是受管理的 [Docker 登錄] (https://docs.docker.com/registry/) 服務，並以開放原始碼的 Docker Registry 2.0 為基礎。 如需如何操作的詳細指引，請遵循 [這裡] (/azure/container-registry/container-registry-get-started-azure-cli) 的步驟。 您也可以使用 DockerHub。

### <a name="to-deploy-using-docker"></a>若要使用 Docker 來進行部署：

1. 在 Jenkins 儀表板中建立新的自由樣式專案。
2. 藉由提供**存放庫 URL**，將**原始程式碼管理**設定為使用[適用於 Azure 的簡單 Java Web 應用程式](https://github.com/azure-devops/javawebappsample)的本機分支。 例如：http://github.com/&lt;yourid>/javawebappsample。
新增建置步驟以使用 Maven 來建置專案。 新增**執行殼層**來達到此目的，並於**命令**中新增下面這行：    
```bash
mvn clean package
```

3. 選取 [發行 Azure Web 應用程式] 來新增建置後動作。
4. 提供上一個步驟中儲存作為 Azure 認證的 Azure 服務主體 **mySp**。
5. 在 [應用程式組態] 區段中，選擇訂用帳戶中的資源群組和 Linux Web 應用程式。
6. 選擇透過 Docker 來發行。
7. 填寫 **Dockerfile** 路徑。 如果您使用 Azure Container Registry，您可以保留 **Docker 登錄 URL** (以 https://&lt;myRegistry>.azurecr.io 的格式提供) 的預設「/Dockerfile」。 如果您使用 DockerHub，請讓它保持空白。
8. 在 [登錄認證] 中，新增 Azure Container Registry 的認證。 您可以在 Azure CLI 中執行下列命令，以取得使用者識別碼和密碼。 第一個命令會啟用系統管理員帳戶。    
```azurecli-interactive
az acr update -n <yourRegistry> --admin-enabled true
az acr credential show -n <yourRegistry>
```

9. [進階] 索引標籤中的 Docker 映像名稱和標記是選擇性欄位。 根據預設，映像名稱會取自您在 Azure 入口網站 (在 [Docker 容器] 設定中) 中所設定的映像名稱。標記會從 $BUILD_NUMBER 產生。 請確定您有在 Azure 入口網站中指定映像名稱，或是在 [進階] 索引標籤的 [Docker 映像] 中提供值。在此範例中，請在 [Docker 映像] 中提供「&lt;yourRegistry>.azurecr.io/calculator」，並讓 [Docker 映像標記] 保持空白。
10. 請注意，如果您使用內建的 Docker 映像設定，部署將會失敗。 請確定您有將 Docker 設定變更為在 Azure 入口網站的 [Docker 容器] 設定中使用自訂映像。 若要使用內建映像，請使用檔案上傳方法來進行部署。
11. 和檔案上傳方法類似，您也可以選擇生產環境以外的不同位置。
12. 儲存並建置專案。 您會看到您的容器映像已推送至您的登錄中，而且 Web 應用程式已部署好。

### <a name="deploy-to-web-app-on-linux-through-docker-using-jenkins-pipeline"></a>使用 Jenkins 管線透過 Docker 部署至 Linux 上的 Web 應用程式

1. 在 GitHub Web UI 中，開啟 **Jenkinsfile_container_plugin** 檔案。 按一下鉛筆圖示來編輯此檔案，更新您 Web 應用程式的資源群組和名稱 (分別位於第 11 行和第 12 行)。    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. 將第 13 行變更為您的容器登錄伺服器    
```java
def registryServer = '<registryURL>'
```    

3. 變更第 16 行以更新您 Jenkins 執行個體中的認證識別碼    
```java
azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
```    
### <a name="create-jenkins-pipeline"></a>建立 Jenkins 管線    

1. 在網頁瀏覽器中開啟 Jenkins，按一下 [New Item] \(新增項目\)。
2. 為作業提供一個名稱，然後選取 [Pipeline] \(管線\)。 按一下 [確定] 。
3. 接著，按一下 [Pipeline] \(管線\) 索引標籤。
4. 針對 [Definition] \(定義\)，選取 [Pipeline script from SCM] \(來自 SCM 的管線指令碼\)。
5. 針對 [SCM]，選取 [Git]。
6. 輸入您分支存放庫的 GitHub URL：https:&lt;您的分支存放庫>.git</li>
7. 將**指令碼路徑**更新為「Jenkinsfile_container_plugin」
8. 按一下 [儲存] 並執行作業。

## <a name="verify-your-web-app"></a>確認您的 Web 應用程式

1. 若要確認 WAR 檔案是否已順利部署到您的 Web 應用程式， 請開啟網頁瀏覽器。
2. 移至 http://&lt;app_name>.azurewebsites.net/api/calculator/ping。您會看到：    
     歡迎使用 Java Web 應用程式！ 此應用程式已更新！
   Sun Jun 17 16:39:10 UTC 2017
3. 移至 http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (以任何數字取代 &lt;x> 和 &lt;y>) 以取得 x 和 y 的總和        
    ![計算機：加法](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-app-service-on-linux"></a>Linux 上的 App Service

* 若要確認，請在 Azure CLI 中，執行：

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    您會收到下列結果︰
    
    ```
    [
    "calculator"
    ]
    ```
    
    移至 http://&lt;app_name>.azurewebsites.net/api/calculator/ping。 您會看見此訊息： 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    移至 http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (以任何數字取代 &lt;x> 和 &lt;y>) 以取得 x 和 y 的總和
    
## <a name="next-steps"></a>後續步驟

在本教學課程中，您使用 Azure App Service 外掛程式來部署至 Azure。

您已了解如何︰

> [!div class="checklist"]
> * 將 Jenkins 設定為透過 FTP 部署 Azure App Service 
> * 將 Jenkins 設定為透過 Docker 部署至 Linux 上的 Azure App Service 
