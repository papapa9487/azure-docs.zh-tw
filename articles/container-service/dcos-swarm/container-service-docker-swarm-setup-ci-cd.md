---
title: "搭配 Azure Container Service 和 Swarm 使用 CI/CD"
description: "搭配 Docker Swarm、Azure Container Registry 及 Visual Studio Team Services 使用 Azure Container Service ，來持續傳遞多容器 .NET Core 應用程式"
services: container-service
author: jcorioland
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 38877afb63e993eeaab723a6ea5f4c40d3c956a5
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>使用 Visual Studio Team Services 的完整 CI/CD 管線，搭配 Docker Swarm 在 Azure Container Service 上部署多容器應用程式

為雲端開發現代化應用程式的其中一個最大挑戰是要能持續傳遞這些應用程式。 在本文中，您會了解如何實作完整的持續整合與部署 (CI/CD)，以及搭配 Docker Swarm、Azure Container Registry 及 Visual Studio Team Services 使用 Azure Container Service 來建置與發行管理。

本文是以一個使用 ASP.NET Core 開發的簡單應用程式 (可在 [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs) 上取得) 為依據。 該應用程式由四個不同的服務組成：三個 Web API 和一個 Web 前端：

![MyShop 範例應用程式](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

目標是使用 Visual Studio Team Services 在 Docker Swarm 叢集中持續傳遞此應用程式。 下圖詳述此持續傳遞管線：

![MyShop 範例應用程式](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

以下是個步驟的簡短說明：

1. 程式碼變更認可至原始程式碼存放庫 (在此為 GitHub) 
2. GitHub 觸發 Visual Studio Team Services 中的組建 
3. Visual Studio Team Services 取得最新版本的來源，並建置構成應用程式的所有映像 
4. Visual Studio Team Services 將每個映像推送到使用 Azure Container Registry 服務建立的 Docker 登錄 
5. Visual Studio Team Services 觸發新版本 
6. 該版本在 Azure Container Service 叢集主要節點上使用 SSH 執行一些命令 
7. 叢集上的 Docker Swarm 提取最新版本的映像 
8. 使用 Docker Compose 部署應用程式的最新版本 

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前，您需要完成下列工作：

- [在 Azure 容器服務中建立 Swarm 叢集](container-service-deployment.md)
- [連接到 Azure 容器服務中的 Swarm 叢集](../container-service-connect.md)
- [建立 Azure 容器登錄](../../container-registry/container-registry-get-started-portal.md)
- [建立 Visual Studio Team Services 帳戶以及 Team 專案 (英文)](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [在您的 GitHub 帳戶建立該 GitHub 存放庫的分叉 (英文)](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

您也需要已經安裝 Docker 的 Ubuntu (14.04 or 16.04) 機器。 Visual Studio Team Services 會在建置與發行程序期間使用此機器。 建立此機器的其中一個方法是使用可在 [Azure Marketplace (英文)](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/) 中取得的映像。 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>步驟 1：設定 Visual Studio Team Services 帳戶 

在本節中，您會設定您的 Visual Studio Team Services 帳戶。

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>設定 Visual Studio Team Services Linux 組件代理程式

為了建立 Docker 映像並將映像從 Visual Studio Team Services 組建推送到 Azure 容器登錄，您需要註冊 Linux 代理程式。 安裝選項如下：

* [在 Linux 上部署代理程式 (英文)](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [使用 Docker 執行 VSTS 代理程式 (英文)](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>安裝 Docker Integration VSTS 擴充

Microsoft 提供 VSTS 擴充以在建置與發行程序中搭配 Docker 使用。 此擴充功能可在 [VSTS Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker) \(英文\) 中取得。 按一下 [安裝] 以將此擴充新增到您的 VSTS 帳戶：

![安裝 Docker 整合](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

系統會要求使用您的認證連線到您的 VSTS 帳戶。 

### <a name="connect-visual-studio-team-services-and-github"></a>將 Visual Studio Team Services 與 GitHub 連線

設定您的 VSTS 專案與 GitHub 帳戶之間的連線。

1. 在您的 Visual Studio Team Services 專案中，按一下工具列中的 [設定] 圖示，然後選取 [服務]。

    ![Visual Studio Team Services - 外部連線](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. 在左側，按一下 [新增服務端點] > [GitHub]。

    ![Visual Studio Team Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. 若要授權 VSTS 搭配您的 GitHub 帳戶使用，請按一下 [授權] 並依照所開啟視窗中的程序執行作業。

    ![Visual Studio Team Services - 授權 GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>將 VSTS 連線到您的 Azure 容器登錄和 Azure Container Service 叢集

進入 CI/CD 管線的最後一步是設定外部連線，來連線到您的容器登錄和您在 Azure 中的 Docker Swarm 叢集。 

1. 在您的 Visual Studio Team Services 專案的 [服務] 設定中，新增 [Docker 登錄] 類型的服務端點。 

2. 在開啟的快顯視窗中，輸入您的 Azure 容器登錄的 URL 和認證。

    ![Visual Studio Team Services - Docker 登錄](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. 針對 Docker Swarm 叢集新增 [SSH] 類型的端點。 然後輸入您的 Swarm 叢集的 SSH 連線資訊。

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

已經完成所有設定。 在下一步中，您會建立建置應用程式並部署到 Docker Swarm 叢集的 CI/CD 管線。 

## <a name="step-2-create-the-build-definition"></a>步驟 2︰建立組建定義

在此步驟中，您會設定 VSTS 專案的組建定義，並定義容器映像的建置工作流程

### <a name="initial-definition-setup"></a>初始定義設定

1. 若要建立組建定義，請連線到您的 Visual Studio Team Services 專案，然後按一下 [組建與版本]。 

2. 在 [組件定義] 區段中，按一下 [+ 新增]。 選取 [空白] 範本。

    ![Visual Studio Team Services - 新增組建定義](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. 使用 GitHub 存放庫來源設定新組建，選取 [持續整合]，然後選取註冊 Linux 代理程式所在的代理程式佇列。 按一下 [建立] 來建立組建定義。

    ![Visual Studio Team Services - 建立組建定義](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. 在 [組建定義] 頁面中，先開啟 [儲存機制] 索引標籤，並使用您在必要條件中建立的 MyShop 專案分叉來設定組建。 請確定您是選取 *acs-docs* 做為 [預設分支]。

    ![Visual Studio Team Services - 組建存放庫組態](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. 在 [觸發程序] 索引標籤上，將組建設為在每次認可之後觸發。 選取 [持續整合] 和 [批次變更]。

    ![Visual Studio Team Services - 組建觸發組態](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>定義組建工作流程
接下來的步驟會定義組建工作流程。 要為 *MyShop* 應用程式建置五個容器映像。 每個映像都是使用專案資料夾中的 Dockerfile 來建置：

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

您需要為每個映像新增兩個 [Docker] 步驟，一個是建置映像，另一個是將映像推送至 Azure 容器登錄。 

1. 若要在組建工作流程中新增步驟，按一下 [+ 加入建置步驟] 然後選取 [Docker]。

    ![Visual Studio Team Services - 新增建置步驟](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. 針對每個映像，設定一個使用 `docker build` 命令的步驟。

    ![Visual Studio Team Services - Docker 建置](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    針對建置作業，選取您的 Azure 容器登錄，選取 [Build an image] \(建置映像) 動作，以及定義每個映像的 Dockerfile。 將 [Build context] \(組件內容) 設為 Dockerfile 根目錄，並定義 [Image Name] \(映像名稱)。 
    
    如上一個畫面顯示，使用您 Azure 容器登錄的 URI 做為映像名稱的開頭。 (您也可以使用組建變數將映像的標籤參數化，就像此範例中的組建識別碼一樣。)

3. 針對每個映像，設定使用 `docker push` 命令的第二個步驟。

    ![Visual Studio Team Services - Docker 推送](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    針對推送作業，選取您的 Azure 容器登錄，選取 **[Push an image]**  \(推送映像) 動作，並輸入在上一個步驟中建置的 **[Image Name]**  \(映像名稱)。

4. 針對這五個映像個別設定好建置和推送步驟之後，請在建置工作流程中再新增兩個步驟。

    a. 命令列工作會使用 bash 指令碼以目前的組建識別碼取代 docker-compose.yml 檔案中的 *BuildNumber*。請參閱以下畫面了解詳細資料。

    ![Visual Studio Team Services - 更新 Compose 檔案](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. 一個工作，會將更新的 Compose 檔案卸除成組建構件，讓它能夠在版本中使用。 請參閱以下畫面了解詳細資料。

    ![Visual Studio Team Services - 發佈 Compose 檔案](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. 按一下 [儲存] 並命名您的組建定義。

## <a name="step-3-create-the-release-definition"></a>步驟 3︰建立發行定義

Visual Studio Team Services 可讓您[跨環境管理發行 (英文)](https://www.visualstudio.com/team-services/release-management/)。 您可以啟用持續部署以確保應用程式會順利部署到不同環境 (例如開發、測試、進入生產階段前和生產)。 您可以建立代表 Azure Container Service Docker Swarm 叢集的新環境。

![Visual Studio Team Services - 發行至 ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>初始發行設定

1. 若要建立發行定義，請按一下 [發行] > [+ 發行]

2. 若要設定構件來源，請按一下 [構件] > [連結構件來源]。 在這裡，將這個新的發行定義連結到您在上一個步驟中定義的組建。 如此一來，就可以在發行程序中取得 docker-compose.yml 檔案。

    ![Visual Studio Team Services - 發行構件](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. 若要設定發行觸發程序，請按一下 [觸發程序]，然後選取 [持續部署]。 在相同的構件來源上設定觸發程序。 此設定可確保一旦順利完成建置，就會立即開始新的發行。

    ![Visual Studio Team Services - 發行觸發程序](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>定義發行工作流程

發行工作流程由您新增的兩個工作組成。

1. 設定工作並使用您先前設定的 SSH 連線來安全地將 Compose 檔案複製到 Docker Swarm 主要節點上的 [deploy] 資料夾。 請參閱以下畫面了解詳細資料。

    ![Visual Studio Team Services - 發行 SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. 設定第二個工作以在主要節點上執行 Bash 命令以執行 `docker` 和 `docker-compose` 命令。 請參閱以下畫面了解詳細資料。

    ![Visual Studio Team Services - 發行 Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    在主要節點上執行的命令會使用 Docker CLI 和 Docker-Compose CLI 執行下列工作：

    - 登入 Azure 容器登錄 (它使用 [變數] 索引標籤中定義的三個組建變數)
    - 定義 **DOCKER_HOST** 變數來與 Swarm 端點 (:2375) 搭配使用
    - 瀏覽到先前安全複製工作建立的 [deploy] 資料夾，其中包含 docker-compose.yml 檔案 
    - 執行提取新映像的 `docker-compose` 命令，停止服務並移除服務，然後建立容器。

    >[!IMPORTANT]
    > 如先前畫面所示，讓 [在 STDERR 上失敗] 核取方塊保持未選取。 這個設定很種要，因為 `docker-compose` 會在標準錯誤輸出上印出數個診斷訊息 (例如容器已停止或已刪除)。 如果您選取該核取方塊，即使一切正常運作，Visual Studio Team Services 也會報告發行期間出現錯誤。
    >
3. 儲存這個新的發行定義。


>[!NOTE]
>這個部署包含幾段停機時間，因為我們會停止舊服務並執行新服務。 執行藍綠部署可以避免此情況。
>

## <a name="step-4-test-the-cicd-pipeline"></a>步驟 4. 測試 CI/CD 管線

您已經完成設定，現在可以開始測試這個新的 CI/CD 管線。 最簡單的測試方法是更新原始程式碼，然後將變更認可到您的 GitHub 存放庫。 推送程式碼幾秒後，您會看到新的組建在 Visual Studio Team Services 執行。 一旦順利完成，就會觸發新的發行，並且在 Azure Container Service 叢集中部署新版的應用程式。

## <a name="next-steps"></a>後續步驟

* 如需 CI/CD 與 Visual Studio Team Services 的相關詳細資訊，請參閱 [VSTS 建置概觀](https://www.visualstudio.com/docs/build/overview)。
