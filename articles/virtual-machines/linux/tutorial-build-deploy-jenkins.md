---
title: "透過 VSTS 將 CI/CD 從 Jenkins 部署到 Azure VM | Microsoft Docs"
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
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: d5c15d6ab36a8454d1c69bac498be89b990c7e33
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-vsts"></a>使用 Jenkins 和 VSTS 將您的應用程式部署至 Linux VM

持續整合 (CI) 與持續部署 (CD) 是讓您可以建置、發行和部署程式碼的管道。 Visual Studio Team Services (VSTS) 提供了一組完整且功能齊全的 CI/CD 自動化工具以供部署至 Azure。 Jenkins 是廣為使用的第三方 CI/CD 伺服器型工具，也提供 CI/CD 自動化。 您可以同時使用這兩者來自訂雲端應用程式或服務的傳遞方式。

在此教學課程中，您可以使用 Jenkins 建置 **Node.js Web 應用程式**，並使用 VSTS 或 Team Foundation Server (TFS) 將其部署至包含 Linux 虛擬機器的[部署群組](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)。

您將：

> [!div class="checklist"]
> * 取得範例應用程式
> * 設定 Jenkins 外掛程式
> * 設定適用於 Node.js 的 Jenkins Freestyle 專案
> * 設定適用於 VSTS 整合的 Jenkins
> * 建立 Jenkins 服務端點
> * 建立 Azure 虛擬機器的部署群組
> * 建立 VSTS 發行定義
> * 執行手動和 CI 觸發部署

## <a name="before-you-begin"></a>開始之前

* 您需要 Jenkins 伺服器的存取權。 如果您尚未建立 Jenkins 伺服器，請參閱[在 Azure 虛擬機器上建立 Jenkins 主要伺服器](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template)。 

* 登入您的 VSTS 帳戶 (`https://{youraccount}.visualstudio.com`)。 
  您可以取得[免費 VSTS 帳戶](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)。

  > [!NOTE]
  > 如需詳細資訊，請參閱[連線至 VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)。

*  若是部署目標，您需要 Linux 虛擬機器。  如需詳細資訊，請參閱[使用 Azure CLI 來建立和管理 Linux VM](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm)。

*  開啟虛擬機器的輸入連接埠 80。  如需詳細資訊，請參閱[使用 Azure 入口網站建立網路安全性群組](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)。

## <a name="get-the-sample-app"></a>取得範例應用程式

您需要可部署的應用程式 (儲存在 Git 存放庫)。
在本教學課程中，我們建議您使用[從 GitHub 中取得的此範例應用程式](https://github.com/azooinmyluggage/fabrikam-node)。  本教學課程包含用於安裝 Node.js 和應用程式的範例指令碼。  如果您需要使用自己的存放庫，就應該設定類似的範例。

1. 建立此應用程式的分支並記下位置 (URL)，此教學課程的後續步驟中會用到此位置。  如需詳細資訊，請參閱[分支存放庫](https://help.github.com/articles/fork-a-repo/)    

> [!NOTE]
> 應用程式是使用 [Yeoman](http://yeoman.io/learning/index.html) 建置的；可使用 **Express**、**bower** 和 **grunt**；而且具有一些 **npm** 套件作為相依項目。
> 此範例也包含設定 Nginx 和部署應用程式的指令碼。 其執行於虛擬機器上。 具體來說，指令碼會安裝節點、Nginx 和 PM2，以及設定 Nginx 和 PM2，然後啟動節點應用程式。

## <a name="configure-jenkins-plugins"></a>設定 Jenkins 外掛程式

首先，您必須設定兩個 Jenkins 外掛程式，用於 **NodeJS** 和 **VS Team Services 持續部署**。

1. 開啟您的 Jenkins 帳戶，然後選擇 [管理 Jenkins]。
2. 在 [管理 Jenkins] 頁面中，選擇 [管理外掛程式]。
3. 篩選清單以找出 **NodeJS** 外掛程式，然後選擇 [安裝但不要重新啟動] 選項。
    ![將 NodeJS 外掛程式新增至 Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. 篩選清單以尋找 **VS Team Services 持續部署**外掛程式，然後選擇 [安裝但不要重新啟動] 選項。
5. 瀏覽回到 Jenkins 儀表板，然後選擇 [管理 Jenkins]。
6. 選擇 [全域工具組態]。  尋找 **NodeJS** 然後按一下 [NodeJS 安裝]。
7. 啟用 [自動安裝] 選項，然後再輸入 [名稱] 值。
8. 按一下 [儲存] 。

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>設定適用於 Node.js 的 Jenkins Freestyle 專案

1. 按一下 [新增項目]。  輸入**項目名稱**。
2. 選擇 [自由樣式專案]。  按一下 [確定] 。
3. 在 [原始程式碼管理] 索引標籤中，選取**Git**，然後輸入包含應用程式程式碼的存放庫與分支詳細資料。    
    ![將存放庫新增至組建](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. 在 [組建觸發程序] 索引標籤上，選取 [Poll SCM]\(輪詢 SCM\)，並輸入排程 `H/03 * * * *`，每隔三分鐘輪詢 Git 存放庫以檢查變更。 
5. 在 [組建環境] 索引標籤上，選取 [提供節點 &amp; npm bin/ 資料夾路徑]，並選取 [NodeJS 安裝] 值。 將 **npmrc 檔案**的設定保留為「使用系統預設值」。
6. 在 [組建] 索引標籤上，選擇 [執行殼層] 並輸入 `npm install` 命令，以確保所有相依項目都會更新。


## <a name="configure-jenkins-for-vsts-integration"></a>設定適用於 VSTS 整合的 Jenkins

  > [!NOTE]
  請確定您用於下列步驟的個人存取權杖 (PAT) 包含 **VSTS 中的發行 (讀取、寫入、執行和管理) 權限**。
 
1.  在 VSTS 帳戶中建立 PAT (如果您還沒有帳戶的話)。 Jenkins 需要這項資訊來存取您的 VSTS 帳戶。  請確定您**儲存**本節中後續步驟的權杖資訊。
  請閱讀[如何建立 VSTS 和 TFS 的個人存取權杖](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)，以了解如何產生個人存取權杖。
2. 在 [建置後動作] 索引標籤上，按一下 [新增建置後動作]。 選擇 [封存成品]。
3. 針對 [要封存的檔案] 輸入 `**/*` 以包含所有檔案。
4. 若要建立另一個動作，請按一下 [新增建置後動作]。
5. 選擇 [觸發 TFS/Team Services 中的發行]，輸入 VSTS 帳戶的 URI，例如：`https://{your-account-name}.visualstudio.com`。
6. 輸入 **Team 專案**名稱。
7. 選擇 [發行定義] 的名稱 (您稍後會在 VSTS 中建立這個發行定義)。
8. 選擇要連線到 VSTS 或 TFS 環境的認證。  如果您是使用 VSTS，請將 **Username** 保留空白。
   如果您是使用 TFS 的內部部署版本，請輸入**使用者名稱和密碼**。    
    ![設定 Jenkins 建置後動作](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. **儲存** Jenkins 專案。

## <a name="create-a-jenkins-service-endpoint"></a>建立 Jenkins 服務端點

服務端點可讓 VSTS 與 Jenkins 連線。

1. 開啟 VSTS 中的 [服務] 頁面，並開啟 [新的服務端點] 清單，然後選擇 [Jenkins]。
    ![新增 Jenkins 端點](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. 輸入連線的名稱。
3. 輸入您的 Jenkins 伺服器 URL，並核取 [接受未受信任的 SSL 憑證] 選項。  範例 URL 為：`http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com`
4. 輸入您 Jenkins 帳戶的**使用者名稱和密碼**。
5. 選擇 [驗證連線] 以檢查資訊是否正確。
6. 選擇 [確定] 以建立服務端點。

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>建立 Azure 虛擬機器的部署群組

您需要[部署群組](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)以註冊 VSTS 代理程式，使發行定義可以部署至您的虛擬機器。  部署群組可讓您輕鬆地定義部署的目標機器邏輯群組，並在每部機器上安裝必要的代理程式。

   > [!NOTE]
   > 在下列步驟中，請確認您安裝必要條件且**不使用 sudo 權限執行指令碼。**

1. 開啟 [建置 &amp;發行] 中樞裡的 [發行] 索引標籤，然後開啟 [部署群組] 索引標籤，並選擇 [+ 新增]。
2. 輸入部署群組的名稱和選擇性說明。
   然後選擇 [建立]。
3. 選擇部署目標虛擬機器的作業系統。  例如，選擇 [Ubuntu 16.04 +]。
4. 核取 [使用指令碼中的個人存取權杖進行驗證]。
5. 選取 [系統必要條件] 連結。  安裝適用於您作業系統的必要條件。
6. 選取 [將指令碼複製到剪貼簿] 以複製指令碼。
7. [登入] 到您的部署目標虛擬機器並 [執行] 指令碼。  **請勿**使用 sudo 權限執行該指令碼。
8. 安裝之後，系統會提示您提供部署群組標記。  接受預設值。
9. 在 VSTS 中，檢查您在 [部署群組] 下的 [目標] 註冊的虛擬機器。

## <a name="create-a-vsts-release-definition"></a>建立 VSTS 發行定義

發行定義會指定 VSTS 要執行的流程，以部署應用程式。  在此範例中，您要執行殼層指令碼。

在 VSTS 中建立發行定義：

1. 開啟 [建置 &amp; 發行] 中樞裡的 [發行]，然後選擇 [建立發行定義]。 
2. 選擇開頭為**空白流程**，以選取**空白**範本。
3. 在 [成品] 區段中，按一下 [+ 新增成品]，並選擇 [Jenkins]用於 [來源類型]。 選取您的 Jenkins 服務端點連線。 然後選取 Jenkins 來源作業，並選擇 [新增]。
4.  按一下 [環境 1] 旁的省略符號。  按一下 [新增部署群組階段]。
5.  選擇 [部署群組]。
5. 按一下 [+] 將工作新增至 [部署群組階段]。
6. 選擇 [殼層指令碼] 工作，並按一下 [新增]。    
    **殼層指令碼**工作用來提供每個伺服器上要執行的指令碼組態，以安裝 Node.js 並啟動應用程式。 進行下列設定：
8. **指令碼路徑**：     
    `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`
9.  按一下 [進階]，然後再啟用 [指定工作目錄]。
10.  **工作目錄**：`$(System.DefaultWorkingDirectory)/Fabrikam-Node`
11. 將發行定義的名稱編輯為 [建置後動作] 索引標籤中 (位於 Jenkins 組建) 指定的名稱。 當來源成品更新時，Jenkins 需要此名稱才可觸發新的發行。
12. 依序按一下 [儲存] 和 [確定] 可儲存發行定義。

## <a name="execute-manual-and-ci-triggered-deployments"></a>執行手動和 CI 觸發部署

1. 選擇 [+ 發行]，然後選取 [建立發行]。
2. 在反白顯示的下拉式清單中選取您完成的組建，並選擇 [佇列]。
3. 在快顯視窗訊息中選擇發行連結。 例如：「發行「**發行-1**」已建立。」
4. 開啟 [記錄] 索引標籤以查看發行主控台輸出。
5. 在瀏覽器中，開啟您在部署群組中新增的其中一個伺服器 URL。 例如，輸入 `http://{your-server-ip-address}`
6. 移至來源 Git 存放庫，並使用某些變更的文字修改 app/views/index.jade 檔案中的 **h1** 標題。
7. **認可**變更。
8. 稍待幾分鐘後，您會在 VSTS 或 TFS 的 [發行] 頁面上看到新的發行。 開啟發行以查看正在進行的部署。 恭喜！

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已會使用 Jenkins 組建和 VSTS (用於發行) 將應用程式自動部署到 Azure。 您已了解如何︰

> [!div class="checklist"]
> * 在 Jenkins 中建置應用程式
> * 設定適用於 VSTS 整合的 Jenkins
> * 建立 Azure 虛擬機器的部署群組
> * 建立發行定義以設定 VM 及部署應用程式

前進到下一個教學課程，以了解如何部署 LAMP (Linux、Apache、MySQL 和 PHP) 堆疊。

> [!div class="nextstepaction"]
> [部署 LAMP 堆疊](tutorial-lamp-stack.md)