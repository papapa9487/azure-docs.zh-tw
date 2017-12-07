---
title: "使用 Jenkins 連續建置和整合 Azure Service Fabric Linux 應用程式 | Microsoft Docs"
description: "使用 Jenkins 連續建置和整合 Service Fabric Linux 應用程式"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: saysa
ms.openlocfilehash: e9422745de1f46098f1a1b0605c2560f44c02f3c
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>使用 Jenkins 建置和部署您的 Linux 應用程式
Jenkins 是連續整合和部署應用程式的熱門工具。 以下是使用 Jenkins 建置和部署 Azure Service Fabric 應用程式的方式。

## <a name="general-prerequisites"></a>一般先決條件
- 在本機安裝 Git。 您可以根據您的作業系統，從 [Git 下載頁面](https://git-scm.com/downloads)安裝適當的 Git 版本。 如果您不熟悉 Git，請從 [Git 文件](https://git-scm.com/docs)深入了解。
- 備妥 Service Fabric Jenkins 外掛程式。 您可以從 [Service Fabric 下載](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi)進行下載。

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>在 Service Fabric 叢集內設定 Jenkins

您可以在 Service Fabric 叢集內部或外部設定 Jenkins。 下列小節說明如何在叢集內設定它，同時又使用 Azure 儲存體帳戶來儲存容器執行個體的狀態。

### <a name="prerequisites"></a>必要條件
1. 讓 Service Fabric Linux 叢集準備就緒。 從 Azure 入口網站建立的 Service Fabric 叢集已安裝 Docker。 如果您在本機執行叢集，請使用命令 ``docker info``來檢查是否已安裝 Docker。 如果未安裝，使用下列命令據以安裝︰

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ``` 

   > [!NOTE]
   > 請確定您已將 8081 連接埠指定為叢集上的自訂端點。
   >

2. 使用下列步驟複製應用程式：
  ```sh
  git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
  cd service-fabric-java-getting-started/Services/JenkinsDocker/
  ```

3. 在檔案共用中保存 Jenkins 容器的狀態：
  * 以 ``sfjenkinsstorage1`` 之類的名稱，在和叢集**相同的區域**中建立 Azure 儲存體帳戶。
  * 以 ``sfjenkins`` 之類的名稱，在儲存體帳戶下建立**檔案共用**。
  * 針對該檔案共用按一下 [連接]，並記下 [正在從 Linux 連線] 底下顯示的值，該值看起來應該如下所示：
  ```sh
  sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
  ```

  > [!NOTE]
  > 若要掛接 cifs 共用，您需要在叢集節點中安裝 cifs Utils 套件。       
  >

4. 將 ```setupentrypoint.sh``` 指令碼中的預留位置值更新為從步驟 3 取得的 Azure 儲存體詳細資料。
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
  * 以來自上述步驟 3 中連線輸出的 ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins`` 值取代 ``[REMOTE_FILE_SHARE_LOCATION]``。
  * 以來自上述步驟 3 的 ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`` 值取代 ``[FILE_SHARE_CONNECT_OPTIONS_STRING]``。

5. **僅限安全叢集：**為了從 Jenkins 設定應用程式在安全叢集上的部署，憑證必須可在 Jenkins 容器內進行存取。 在 Linux 叢集上，只會從容器上 X509StoreName 指定的存放區複製憑證 (PEM)。 在 ContainerHostPolicies 下的 ApplicationManifest 中，新增此憑證參考並更新指紋值。 指紋值必須是位於節點上的憑證指紋值。
  ```xml
  <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
  ```
  > [!NOTE]
  > 指紋值必須與用來連線到安全叢集的憑證相同。 
  >

6. 連線至叢集並安裝容器應用程式。

  **安全叢集**
  ```sh
  sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
  bash Scripts/install.sh
  ```

  **不安全叢集**
  ```sh
  sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
  bash Scripts/install.sh
  ```

  這會在叢集上安裝 Jenkins 容器，您可以使用 Service Fabric Explorer 加以監視。

    > [!NOTE]
    > Jenkins 映像可能需要幾分鐘的時間才能下載到叢集上。
    >

### <a name="steps"></a>步驟
1. 從瀏覽器中，前往 ``http://PublicIPorFQDN:8081``。 它會提供登入所需之初始管理密碼的路徑。 
2. 查看 Service Fabric Explorer，以確定 Jenkins 容器是在哪個節點上執行。 透過安全殼層 (SSH) 登入此節點。
```sh
ssh user@PublicIPorFQDN -p [port]
``` 
3. 使用 ``docker ps -a`` 取得容器執行個體識別碼。
4. 以 Secure Shell (SSH) 登入容器，並貼上您在 Jenkins 入口網站中看到的路徑。 例如，如果在入口網站中它顯示路徑 `PATH_TO_INITIAL_ADMIN_PASSWORD`，執行下列命令︰

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  ```
  ```sh
  cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the pasword value
  ```
5. 在 [Jenkins 入門] 頁面上，選擇 [選取要安裝的外掛程式] 選項，選取 [無] 核取方塊，然後按一下 [安裝]。
6. 建立使用者，或選擇繼續使用系統管理員身分。

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>在 Service Fabric 叢集外設定 Jenkins

您可以在 Service Fabric 叢集內部或外部設定 Jenkins。 下列各節顯示如何在叢集外設定。

### <a name="prerequisites"></a>必要條件
您必須安裝 Docker。 使用下列命令可從終端機安裝 Docker︰

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

現在，當您在終端機執行 ``docker info``，您應該會在輸出中看到 Docker 服務正在執行。

### <a name="steps"></a>步驟
  1. 提取 Service Fabric Jenkins 容器映像︰``docker pull sayantancs/jenkins:v9``
  2. 執行容器映像︰``docker run -itd -p 8080:8080 sayantancs/jenkins:v9``
  3. 取得容器映像執行個體的識別碼。 您可以使用命令 ``docker ps –a`` 列出所有 Docker 容器
  4. 使用下列步驟登入 Jenkins 入口網站︰

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    如果容器識別碼是 2d24a73b5964，請使用 2d24。
    * 從入口網站 (``http://<HOST-IP>:8080``) 登入 Jenkins 儀表板需要此密碼
    * 第一次登入之後，您可以建立您自己的使用者帳戶，並針對未來目的使用該帳戶，或者您可以繼續使用系統管理員帳戶。 您建立使用者之後，需要繼續使用。
  5. 設定 GitHub 以 Jenkins，方法為使用[產生新的 SSH 金鑰，並將它新增至 SSH 代理程式](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)中提到的步驟。
        * 使用 GitHub 所提供的指示產生 SSH 金鑰，並將 SSH 金鑰新增至裝載存放庫的 GitHub 帳戶。
        * 在 Jenkins Docker 殼層 (而非在主機上) 執行上述連結內所提到的命令。
      * 若要從主機登入 Jenkins 殼層，請使用下列命令：

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

請確定 Jenkins 容器映像所裝載的叢集或電腦具有公開 IP。 這可讓 Jenkins 執行個體接收來自 GitHub 的通知。

## <a name="install-the-service-fabric-jenkins-plug-in-from-the-portal"></a>從入口網站安裝 Service Fabric Jenkins 外掛程式

1. 移至 ``http://PublicIPorFQDN:8081``。
2. 從 Jenkins 儀表板中，選取 [管理 Jenkins] > [管理外掛程式] > [進階]。
在這裡，您可以上傳外掛程式。 選取 [選擇檔案]，然後選取 **serviceFabric.hpi** 檔案，您已在「必要條件」下載該檔案，或者，您也可以[在此](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi)下載。 當您選取 [上傳] 時，Jenkins 會自動安裝外掛程式。 在系統要求時允許重新啟動。

## <a name="create-and-configure-a-jenkins-job"></a>建立及設定 Jenkins 作業

1. 從儀表板建立**新項目**。
2. 輸入項目名稱 (例如，**MyJob**)。 選取 [自由樣式專案]，然後按一下 [確定]。
3. 前往 [作業] 頁面，並按一下 [設定]。

   a. 在 [一般] 區段中，選取 [GitHub 專案] 的核取方塊，然後指定您的 GitHub 專案 URL。 此 URL 會裝載您想要與 Jenkins 連續整合、連續部署 (CI/CD) 流程整合的 Service Fabric Java 應用程式 (例如，``https://github.com/sayantancs/SFJenkins``)。

   b. 在 [原始程式碼管理] 區段底下，選取 [Git]。 指定存放庫 URL，它會裝載您想要與 Jenkins CI/CD 流程整合的 Service Fabric Java 應用程式 (例如，``https://github.com/sayantancs/SFJenkins.git``)。 您也可以在這裡指定要建置哪些分支 (例如，**/master**)。
4. 設定您的 *GitHub* (裝載存放庫者)，讓它能夠與 Jenkins 溝通。 請使用下列步驟：

   a. 移至您的 GitHub 儲存機制頁面。 移至 [設定] > [整合和服務]。

   b. 選取 [新增服務]、輸入 **Jenkins**，然後選取 [Jenkins-Github 外掛程式]。

   c. 輸入您的 Jenkins webhook URL (根據預設，它應該是 ``http://<PublicIPorFQDN>:8081/github-webhook/``)。 按一下 [新增/更新服務]。

   d. 隨即會將測試事件傳送至您的 Jenkins 執行個體。 您應該會在 GitHub 中看到 webhook 所做的綠色勾號，而且您的專案將會建置。

   e. 在 [組建觸發程序] 區段下，選取您想要的建置選項。 針對此範例，您要每當發生某些推送至存放庫時觸發組建。 因此，您選取 [GITScm 輪詢的 GitHub 攔截觸發程序]。 (在先前，此選項稱為**當變更推送至 GitHub 時建置**。)

   f. **Java 應用程式的建置區段：**在 [建置區段] 下，從 [加入建置步驟] 下拉式清單選取 [叫用 Gradle 指令碼] 選項。 在出現的小工具中開啟進階功能表，針對您的應用程式指定 [根建置指令碼] 的路徑。 它會從指定的路徑挑選 build.gradle，並據以運作。 如果您建立名為 ``MyActor`` 的專案 (使用 Eclipse 外掛程式或 Yeoman 產生器)，則根建置指令碼應該包含 ``${WORKSPACE}/MyActor``。 請參閱下列螢幕擷取畫面，查看像這樣的範例︰

    ![Service Fabric Jenkins 建置動作][build-step]

   g. **.Net Core 應用程式的建置區段：**在 [建置區段] 下，從 [加入建置步驟] 下拉式清單選取 [執行殼層] 選項。 在出現的命令方塊中，首先需要將目錄變更為 build.sh 檔案所在的路徑。 一旦目錄變更，就可以執行 build.sh 指令碼，並建置應用程式。

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

    以下是使用 Jenkins 作業名稱 CounterServiceApplication 建置 [Counter Service](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) 範例的命令範例。

    ![Service Fabric Jenkins 建置動作][build-step-dotnet]
  
   h. 從 [建置後動作] 下拉式清單，選取 [部署 Service Fabric 專案]。 這裡您必須提供叢集詳細資料，當中會部署 Jenkins 編譯 Service Fabric 應用程式。 您可在容器內回應 echo Certificates_JenkinsOnSF_Code_MyCert_PEM 環境變數的值，找到憑證的路徑。 這個路徑可以用於用戶端識別碼和用戶端憑證欄位。

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
    您也可以提供用來部署應用程式的其他應用程式詳細資料。 請參閱下列螢幕擷取畫面，查看像這樣的範例︰

    ![Service Fabric Jenkins 建置動作][post-build-step]

      > [!NOTE]
      > 如果您使用 Service Fabric 來部署 Jenkins 容器映像，這裡的叢集可能與裝載 Jenkins 容器應用程式的叢集相同。
      >

## <a name="next-steps"></a>後續步驟
現在已設定 GitHub 和 Jenkins。 請考慮在存放庫範例 https://github.com/sayantancs/SFJenkins 的 ``MyActor`` 專案中進行一些範例變更。 將您的變更推送至遠端 ``master`` 分支 (或任何您已設定要使用的分支)。 這會觸發您設定的 Jenkins 作業 ``MyJob``。 它會從 GitHub 擷取變更、建置它們並且將應用程式部署至您在建置後動作中指定的叢集端點。  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-step.png

