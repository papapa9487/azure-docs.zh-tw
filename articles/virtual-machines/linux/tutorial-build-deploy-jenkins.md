---
title: "透過 Team Services 將 CI/CD 從 Jenkins 部署到 Azure VM | Microsoft Docs"
description: "透過 Visual Studio Team Services (VSTS) 或 Microsoft Team Foundation Server (TFS) 中的 Release Management，使用 Jenkins 將 Node.js 應用程式的持續整合 (CI) 和持續部署 (CD) 安裝至 Azure VM"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/15/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: feaced0d0784b5724fb1e30be5e66cb7c808d77f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-team-services"></a>使用 Jenkins 和 Team Services 將您的應用程式部署至 Linux Vm

持續整合 (CI) 與持續部署 (CD) 是讓您可以建置、發行和部署程式碼的管道。 Team Services 提供了一組完整且功能齊全的 CI/CD 自動化工具以便部署至 Azure。 Jenkins 是廣為使用的第三方 CI/CD 伺服器型工具，也提供 CI/CD 自動化。 您可以同時使用這兩者來自訂雲端應用程式或服務的傳遞方式。

在此教學課程中，您可以使用 Jenkins 建置 **Node.js Web 應用程式**，並使用 Visual Studio Team Services 將其部署至包含 Linux 虛擬機器的[部署群組](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)。

您將：

> [!div class="checklist"]
> * 在 Jenkins 中建置應用程式
> * 設定適用於 Team Services 整合的 Jenkins
> * 建立 Azure 虛擬機器的部署群組
> * 建立發行定義以設定 VM 及部署應用程式

## <a name="before-you-begin"></a>開始之前

* 您需要 Jenkins 帳戶的存取權。 如果您尚未建立 Jenkins 伺服器，請參閱 [Jenkins 文件](https://jenkins.io/doc/)。 

* 登入您的 Team Services 帳戶 (`https://{youraccount}.visualstudio.com`)。 
  您可以取得[免費的 Team Services 帳戶](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)。

  > [!NOTE]
  > 如需詳細資訊，請參閱＜[連線至 Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)＞。

* 如果您的 Team Services 帳戶中沒有個人存取權杖 (PAT)，請建立一個。 Jenkins 需要這項資訊來存取您的 Team Services 帳戶。
  請閱讀＜[如何建立 Team Services 和 TFS 的個人存取權杖](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)＞，以了解如何產生個人存取權杖。

## <a name="get-the-sample-app"></a>取得範例應用程式

您需要可部署的應用程式 (儲存在 Git 存放庫)。
在本教學課程中，我們建議您使用[從 GitHub 中取得的此範例應用程式](https://github.com/azooinmyluggage/fabrikam-node)。

1. 建立此應用程式的分支並記下位置 (URL)，此教學課程的後續步驟中會用到此位置。

1. 將分支設為**公用**，以便之後可輕易連線到 GitHub。

> [!NOTE]
> 如需詳細資訊，請參閱＜[建立存放庫的分支](https://help.github.com/articles/fork-a-repo/)＞和＜[將私密存放庫設為公用](https://help.github.com/articles/making-a-private-repository-public/)＞。

> [!NOTE]
> 應用程式是使用 [Yeoman](http://yeoman.io/learning/index.html) 建置的；可使用 **Express**、**bower** 和 **grunt**；而且具有一些 **npm** 套件作為相依項目。
> 範例應用程式包含一組 [Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)，用於動態地建立虛擬機器，以便在 Azure 上進行部署。 這些範本會由 [Team Services 發行定義](https://www.visualstudio.com/docs/build/actions/work-with-release-definitions)中的工作使用。
> 主要範本會建立網路安全性群組、虛擬機器和虛擬網路。 並指派公用 IP 位址和開啟輸入連接埠 80。 主要範本也會新增由部署群組使用的標記，以選取要接收部署的機器。
>
> 此範例也包含設定 Nginx 和部署應用程式的指令碼。 其執行於每部虛擬機器上。 具體來說，指令碼會安裝節點、Nginx 和 PM2，以及設定 Nginx 和 PM2，然後啟動節點應用程式。

## <a name="configure-jenkins-plugins"></a>設定 Jenkins 外掛程式

首先，您必須設定兩個 Jenkins 外掛程式，用於 **NodeJS**，以及**與 Team Services 整合**。

1. 開啟您的 Jenkins 帳戶，然後選擇 [管理 Jenkins]。

1. 在 [管理 Jenkins] 頁面中，選擇 [管理外掛程式]。

1. 篩選清單以找出 **NodeJS** 外掛程式並安裝，無須重新啟動。

   ![將 NodeJS 外掛程式新增至 Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)

1. 篩選清單以找出 **Team Foundation Server** 外掛程式並安裝。 (此外掛程式適用於 Team Services 和 Team Foundation Server。)不需要重新啟動 Jenkins。

## <a name="configure-jenkins-build-for-nodejs"></a>設定適用於 Node.js 的 Jenkins 組建

在 Jenkins 中建立新的組建專案並加以設定，如下所示：

1. 在 [一般] 索引標籤中，輸入組建專案的名稱。

1. 在 [原始程式碼管理] 索引標籤中，選取**Git**，然後輸入包含應用程式程式碼的存放庫與分支詳細資料。

   ![將存放庫新增至組建](media/tutorial-build-deploy-jenkins/jenkins-git.png)

   > [!NOTE]
   > 如果您的存放庫不是公用的，請選擇 [新增]，並提供認證以便進行連線。

1. 在 [組建觸發程序] 索引標籤上，選取 [Poll SCM]\(輪詢 SCM\)，並輸入排程 `H/03 * * * *`，每隔三分鐘輪詢 Git 存放庫以檢查變更。 

1. 在 [組建環境] 索引標籤上，選取 [Provide Node &amp; npm bin/ folder PATH]\(提供節點與 npm bin/ 資料夾路徑\)，並輸入 `NodeJS` 作為 Node JS 安裝值。 將 **npmrc 檔案**的設定保留為「使用系統預設值」。

1. 在 [組建] 索引標籤上，輸入命令 `npm install`，以確保所有相依項目都會更新。

## <a name="configure-jenkins-for-team-services-integration"></a>設定適用於 Team Services 整合的 Jenkins

1. 在 [建置後動作] 索引標籤上，針對 [要封存的檔案] 輸入 `**/*` 以包含所有檔案。

1. 針對 [觸發 TFS/Team Services 中的發行]，輸入帳戶的完整 URL (例如 `https://your-account-name.visualstudio.com`)、專案名稱、版本定義的名稱 (稍後建立) 和認證，以便與您的帳戶連線。
   需要您的使用者名稱和您稍早建立的 PAT。 

   ![設定 Jenkins 建置後動作](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)

1. 儲存組建專案。

## <a name="create-a-jenkins-service-endpoint"></a>建立 Jenkins 服務端點

服務端點可讓 Team Services 與 Jenkins 連線。

1. 開啟 Team Services 中的 [服務] 頁面，並開啟 [新的服務端點] 清單，然後選擇 [Jenkins]。

   ![新增 Jenkins 端點](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)

1. 輸入您用來表示此連線的名稱。

1. 輸入您的 Jenkins 伺服器 URL，並核取 [接受未受信任的 SSL 憑證] 選項。

1. 輸入您 Jenkins 帳戶的使用者名稱和密碼。

1. 選擇 [驗證連線] 以檢查資訊是否正確。

1. 選擇 [確定] 以建立服務端點。

## <a name="create-a-deployment-group"></a>建立部署群組

您需要[部署群組](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)以包含虛擬機器。

1. 開啟 [組建與發行] 中樞裡的 [版本] 索引標籤，然後開啟 [部署群組] 索引標籤，並選擇 [+ 新增]。

1. 輸入部署群組的名稱和選擇性說明。
   然後選擇 [建立]。

當 Azure 資源群組部署工作使用 Azure Resource Manager 範本執行時，該工作會建立與註冊 VM。
您不需要自行建立與註冊虛擬機器。

## <a name="create-a-release-definition"></a>建立發行定義

發行定義會指定 Team Services 要執行的流程，以部署應用程式。
在 Team Services 中建立發行定義：

1. 開啟 [組建與發行] 中樞裡的 [發行] 索引標籤，並開啟發行定義中的 **+** 下拉式清單，然後選取 [建立發行定義]。 

1. 選取 [空白] 範本，並選擇 [下一步]。

1. 在 [成品] 區段中，按一下 [連結成品]，並選擇 [Jenkins]。 選取您的 Jenkins 服務端點連線。 然後選取 Jenkins 來源作業，並選擇 [建立]。 

1. 在新的發行定義中，選擇 [+ 加入工作]，並將 **Azure 資源群組部署** 工作新增至預設環境。

1. 選擇 [+ 加入工作] 連結旁的下拉箭頭，然後將部署群組階段新增至定義。

   ![新增部署群組階段](media/tutorial-build-deploy-jenkins/deployment-group-phase-in-release-definition.png) 

1. 在工作目錄中，開啟 [公用程式] 區段，然後新增**殼層指令碼**工作的執行個體。

1. Azure 資源群組部署工作中使用的參數範本會設定系統管理員密碼，以用來與 VM 連線。
   您會使用變數 **$(adminpassword)** 來提供此密碼：
   
   - 開啟 [變數] 索引標籤，然後在 [變數] 區段中輸入名稱 `adminpassword`。

   - 輸入系統管理員密碼。

   - 選擇值文字方塊旁的「掛鎖」圖示來保護密碼。 

## <a name="configure-the-azure-resource-group-deployment-task"></a>設定 Azure 資源群組部署工作

**Azure 資源群組部署**工作可用來建立部署群組。 進行下列設定：

* **Azure 訂用帳戶：**從 [可用的 Azure 服務連線] 下方的清單選取連線。 
  如果沒有顯示任何連線，請選擇 [管理]，並選取 [新的服務端點]與 [Azure Resource Manager]，然後依循提示操作。
  返回您的發行定義，重新整理 [AzureRM 訂用帳戶] 清單，並選取您建立的連線。

* **資源群組**：輸入您稍早建立的資源群組名稱。

* **位置**：選取部署地區。

  ![建立新的資源群組](media/tutorial-build-deploy-jenkins/provision-web-server.png)

* **範本位置**：`URL of the file`

* **範本連結**：`{your-git-repo}/ARM-Templates/UbuntuWeb1.json`

* **範本參數連結**：`{your-git-repo}/ARM-Templates/UbuntuWeb1.parameters.json`

* **覆寫範本參數**：覆寫值的清單，例如：`-location {location} -virtualMachineName {machine] -virtualMachineSize Standard_DS1_v2 -adminUsername {username} -virtualNetworkName fabrikam-node-rg-vnet -networkInterfaceName fabrikam-node-websvr1 -networkSecurityGroupName fabrikam-node-websvr1-nsg -adminPassword $(adminpassword) -diagnosticsStorageAccountName fabrikamnodewebsvr1 -diagnosticsStorageAccountId Microsoft.Storage/storageAccounts/fabrikamnodewebsvr1 -diagnosticsStorageAccountType Standard_LRS -addressPrefix 172.16.8.0/24 -subnetName default -subnetPrefix 172.16.8.0/24 -publicIpAddressName fabrikam-node-websvr1-ip -publicIpAddressType Dynamic`。<br />在 {預留位置} 插入您自己的特定值。 

* **啟用必要條件**：`Configure with Deployment Group agent`

* **TFS/VSTS 端點**：選擇 [新增]，然後在 [新增 Team Foundation Server/Team Services 連線] 對話方塊中，選取 [權杖型驗證]。 輸入連線的名稱和您的 Team 專案 URL。 接著產生並輸入[個人存取權杖 (PAT)]( https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)，以驗證 Team 專案上的連線。

  ![建立個人存取權杖](media/tutorial-build-deploy-jenkins/create-a-pat.png)

* **Team 專案**：選取您目前的專案。

* **部署群組**：輸入部署群組名稱，需與**資源群組**參數所用的名稱相同。

Azure 資源群組部署工作的預設設定是要建立或更新資源，並漸進式地執行這項操作。 此工作會在第一次執行時建立 VM，而之後只進行更新。

## <a name="configure-the-shell-script-task"></a>設定殼層指令碼工作

**殼層指令碼**工作用來提供每個伺服器上要執行的指令碼組態，以安裝 Node.js 並啟動應用程式。 進行下列設定：

* **指令碼路徑**：`$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`

* **指定工作目錄**：`Checked`

* **工作目錄**：`$(System.DefaultWorkingDirectory)/Fabrikam-Node`
   
## <a name="rename-and-save-the-release-definition"></a>重新命名和儲存發行定義

1. 將發行定義的名稱編輯為 [建置後動作] 索引標籤中 (位於 Jenkins 組建) 指定的名稱。 當來源成品更新時，Jenkins 需要此名稱才可觸發新的發行。

1. (選擇性) 按一下環境名稱可變更名稱。 

1. 選擇 [儲存]，然後選擇 [確定]。

## <a name="start-a-manual-deployment"></a>啟動手動部署

1. 選擇 [+ 發行]，然後選取 [建立發行]。

1. 在反白顯示的下拉式清單中選取您完成的組建，並選擇 [建立]。

1. 在快顯視窗訊息中選擇發行連結。 例如：「發行「**發行-1**」已建立。」

1. 開啟 [記錄] 索引標籤以查看發行主控台輸出。

1. 在瀏覽器中，開啟您在部署群組中新增的其中一個伺服器 URL。 例如，輸入 `http://{your-server-ip-address}`

## <a name="start-a-cicd-deployment"></a>啟動 CI/CD 部署

1. 在發行定義中，從 Azure 資源群組部署工作設定上的 [控制選項] 區段中，取消核取 [啟用] 核取方塊。
   未來若要在現有部署群組上進行部署時，您將不需要重新執行這項工作。

1. 移至來源 Git 存放庫，並修改 [app/views/index.jade](https://github.com/azooinmyluggage/fabrikam-node/blob/master/app/views/index.jade) 檔案中的 **h1** 標題。

1. 認可變更。

1. 稍待幾分鐘後，您會在 Team Services 或 TFS 的 [發行] 頁面上看到新的發行。 開啟發行以查看正在進行的部署。 恭喜！

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已會使用 Jenkins 組建和 Team Services (用於發行) 將應用程式自動部署到 Azure。 您已了解如何︰

> [!div class="checklist"]
> * 在 Jenkins 中建置應用程式
> * 設定適用於 Team Services 整合的 Jenkins
> * 建立 Azure 虛擬機器的部署群組
> * 建立發行定義以設定 VM 及部署應用程式

前進到下一個教學課程，以了解如何部署 LAMP (Linux、Apache、MySQL 和 PHP) 堆疊。

> [!div class="nextstepaction"]
> [部署 LAMP 堆疊](tutorial-lamp-stack.md)