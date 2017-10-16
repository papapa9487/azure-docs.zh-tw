---
title: "將您的應用程式部署至 Azure 和 Azure Stack | Microsoft Docs"
description: "了解如何使用混合式 CI/CD 管線將應用程式部署至 Azure 和 Azure Stack。"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: helaw
ms.custom: mvc
ms.openlocfilehash: 83bb401d5d65cd2c34015a1a14673363aeee81d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>將應用程式部署至 Azure 和 Azure Stack
適用於：Azure Stack 整合系統和 Azure Stack 開發套件

混合式[持續整合](https://www.visualstudio.com/learn/what-is-continuous-integration/)/[持續傳遞](https://www.visualstudio.com/learn/what-is-continuous-delivery/) (CI/CD) 管線可讓您建置、測試以及將應用程式部署至多個雲端。  在本教學課程中，您會建立一個範例環境以了解混合式 CI/CD 管線可以協助您的方式：
 
> [!div class="checklist"]
> * 根據 Visual Studio Team Services (VSTS) 存放庫的程式碼認可起始新的組建。
> * 自動將您新建置的程式碼部署至 Azure 以進行使用者驗收測試。
> * 一旦您的程式碼已通過測試，便會自動部署至 Azure Stack。 


## <a name="prerequisites"></a>必要條件
建置混合式 CI/CD 管線所需的幾個元件，且可能需要一些時間來準備。  如果您已擁有其中部分元件，請在開始之前確定它們符合需求。

本主題也假設您擁有 Azure 和 Azure Stack 的部分知識。 如果您想要深入了解之後再繼續，請務必從這些主題開始：

- [Azure 簡介](https://docs.microsoft.com/azure/fundamentals-introduction-to-azure)
- [Azure Stack 重要概念](../azure-stack-key-features.md)

### <a name="azure"></a>Azure
 - 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。
 - 建立 [Web 應用程式](../../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)，並將其設定為用於 [FTP 發行](../../app-service/app-service-deploy-ftp.md)。  記下新的 Web 應用程式 URL，因為稍後將會用到。


### <a name="azure-stack"></a>Azure Stack
 - [部署 Azure Stack](../azure-stack-run-powershell-script.md)。  安裝程序通常需要幾個小時才能完成，因此請據以規劃時段。
 - 將 [App Service](../azure-stack-app-service-deploy.md) PaaS 服務部署至 Azure Stack。
 - 建立 Web 應用程式，並將其設定為用於 [FTP 發行](../azure-stack-app-service-enable-ftp.md)。  記下新的 Web 應用程式 URL，因為稍後將會用到。  

### <a name="developer-tools"></a>開發人員工具
 - 建立 [VSTS 工作區](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)。  註冊程序會建立名為「MyFirstProject」的專案。  
 - [安裝 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 並[登入 VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#connect-and-share-code-from-visual-studio)
 - 連線至專案並[在本機複製](https://www.visualstudio.com/docs/git/gitquickstart)。
 - 在 VSTS 中建立[代理程式集區](https://www.visualstudio.com/docs/build/concepts/agents/pools-queues#creating-agent-pools-and-queues)。
 - 安裝 Visual Studio 並將 [VSTS 組建代理程式](https://www.visualstudio.com/docs/build/actions/agents/v2-windows)部署至 Azure Stack 上的虛擬機器。 
 

## <a name="create-app--push-to-vsts"></a>建立應用程式並推送至 VSTS

### <a name="create-application"></a>建立應用程式
在本節中，您會建立簡單的 ASP.NET 應用程式，並將其推送至 VSTS。  這些步驟代表一般開發人員的工作流程，且能夠針對開發人員工具和語言進行調整。 

1.  開啟 Visual Studio。
2.  從 Team Explorer 空間和 [解決方案] 區域中，按一下 [新增]。
3.  選取 [Visual C#]  >  [Web]  >  [ASP.NET Web 應用程式 (.NET Framework)]。
4.  提供應用程式的名稱，然後按一下 [確定]。
5.  在下一個畫面上，保留預設值 (Web Form)，然後按一下 [確定]。

### <a name="commit-and-push-changes-to-vsts"></a>認可並推送變更至 VSTS
1.  使用 Visual Studio 中的 Team Explorer，接著選取下拉式功能表，然後按一下 [變更]。
2.  提供認可訊息，然後選取 [全部認可]。 系統可能會提示您儲存解決方案檔案，請按一下 [是] 以全部儲存。
3.  經過認可之後，Visual Studio 會要求將變更同步至您的專案。 選取 [同步]。

    ![完成認可之後顯示認可畫面的影像](./media/azure-stack-solution-pipeline/image1.png)

4.  在 [同步處理] 索引標籤中的 [傳出] 下，您會看到新的認可。  選取 [推送] 以將變更同步至 VSTS。

    ![顯示同步步驟的影像](./media/azure-stack-solution-pipeline/image2.png)

### <a name="review-code-in-vsts"></a>檢閱 VSTS 中的程式碼
認可變更並推送至 VSTS 之後，請從 VSTS 入口網站檢查您的程式碼。  選取 [程式碼]，然後從下拉式功能表中選取 [檔案]。  您可以看到已建立的解決方案。

## <a name="create-build-definition"></a>建立組建定義
建置程序會定義應用程式的建置和封裝方式，以便針對每個程式碼變更的認可進行部署。 在此範例中，儘管可能會根據您的應用程式調整此組態，我們仍會使用包含的範本來設定 ASP.NET 應用程式的建置程序。

1.  從網頁瀏覽器登入您的 VSTS 工作區。
2.  從橫幅中從選取 [建置與發行]，然後選取 [建置]。
3.  選取 [+新增定義]。
4.  從範本的清單中，選取 [ASP.NET (預覽)]，然後選取 [套用]。
5.  將 [建置方案] 步驟中的 [MSBuild 引數] 欄位修改為：

    `/p:DeployOnBuild=True /p:WebPublishMethod=FileSystem /p:DeployDefaultTarget=WebPublish /p:publishUrl="$(build.artifactstagingdirectory)\\"`

6.  選取 [選項] 索引標籤，然後選取您部署至 Azure Stack 上虛擬機器之組建代理程式的代理程式佇列。 
7.  選取 [觸發程序] 索引標籤，然後啟用 [持續整合]。
7.  按一下 [儲存與佇列]，然後從下拉式功能表選取 [儲存]。 

## <a name="create-release-definition"></a>建立發行定義
發行程序會定義組建如何從上一個步驟部署至環境。  在本教學課程中，我們將使用 FTP 將 ASP.NET 應用程式發行至 Azure Web 應用程式。 若要設定 Azure 的發行，請使用下列步驟：

1.  從 VSTS 橫幅中從選取 [建置與發行]，然後選取 [建置]。
2.  按一下綠色的 [+ 新定義]。
3.  選取 [空白] ，然後按一下 [下一步]。
4.  勾選 持續部署 的核取方塊，然後按一下建立。

現在，您已建立空白的發行定義並將其連結至組建，而我們會加入 Azure 環境的步驟：

1.  按一下綠色的  **+**  來加入工作。
2.  選取 [所有]，接著從清單中新增 [FTP 上傳]，然後選取 [關閉]。
3.  選取您剛才新增的 [FTP 上傳] 工作，並設定下列參數：
    
    | 參數 | 值 |
    | ----- | ----- |
    |驗證方法| 輸入認證|
    |伺服器 URL | 從 Azure 入口網站取出 Web 應用程式 FTP URL |
    |使用者名稱 | 建立 Web 應用程式的 FTP 認證時所設定的使用者名稱 |
    |密碼 | 建立 Web 應用程式的 FTP 認證時所建立的密碼|
    |來源目錄 | $(System.DefaultWorkingDirectory)\**\ |
    |遠端目錄 | /site/wwwroot/ |
    |保留檔案路徑 | 已啟用 (已勾選)|

4.  按一下 [儲存] 

最後，您可以設定要使用代理程式集區的發行定義，而其中包含使用下列步驟部署的代理程式：
1.  選取發行定義，然後按一下 [編輯]。
2.  從中間的資料行中選取 [在代理程式上執行]。  在右邊的資料行中，選取包含在 Azure Stack 上執行之組建代理程式的代理程式佇列。  
    ![顯示發行定義組態以使用特定佇列的影像](./media/azure-stack-solution-pipeline/image3.png)


## <a name="deploy-your-app-to-azure"></a>將應用程式部署至 Azure
此步驟會使用新建立的 CI/CD 管線將 ASP.NET 應用程式部署至 Azure 上的 Web 應用程式。 

1.  從 VSTS 中的橫幅選取 [建置與發行]，然後選取 [建置]。
2.  在先前已建立的組建定義上按一下 [...]，並選取 [將新組建排入佇列]。
3.  接受預設值，然後按一下 [確定]。  組建隨即開始，並顯示進度。
4.  當組建完成之後，您可以藉由選取 [建置與發行]，然後選取 [發行] 來追蹤狀態。
5.  組建完成之後，請使用建立 Web 應用程式時所記下的 URL 來造訪網站。    


## <a name="add-azure-stack-to-pipeline"></a>將 Azure Stack 新增至管線
現在您已透過部署至 Azure 測試過 CI/CD 管線，便可以開始將 Azure Stack 新增至管線。  在下列步驟中，您會建立新的環境並新增 FTP 上傳的工作，以便將您的應用程式部署至 Azure Stack。  您也可以新增發行核准者作為模擬對發行至 Azure Stack 程式碼進行「簽署」的方式。  

1.  在發行定義中，選取 [+ 新增環境] 和 [建立新環境]。
2.  選取 [空白] 並按一下 [下一步]。
3.  選取 [特定使用者] 並指定您的帳戶。  選取 [ **建立**]。
4.  透過選取現有的名稱，然後輸入 Azure Stack 以重新命名環境。
5.  現在，選取 Azure Stack 環境，然後選取 [新增工作]。
6.  選取 [FTP 上傳] 工作，接著選取 [新增]，然後選取 [關閉]。


### <a name="configure-ftp-task"></a>設定 FTP 工作
現在您已建立版本，您將需要設定發行至 Azure Stack 上 Web 應用程式所需的步驟。  如同您為 Azure 設定 FTP 上傳工作，您可以設定 Azure Stack 的工作：

1.  選取您剛才新增的 [FTP 上傳] 工作，並設定下列參數：
    
    | 參數 | 值 |
    | -----     | ----- |
    |驗證方法| 輸入認證|
    |伺服器 URL | 從 Azure Stack 入口網站取出 Web 應用程式 FTP URL |
    |使用者名稱 | 建立 Web 應用程式的 FTP 認證時所設定的使用者名稱 |
    |密碼 | 建立 Web 應用程式的 FTP 認證時所建立的密碼|
    |來源目錄 | $(System.DefaultWorkingDirectory)\**\ |
    |遠端目錄 | /site/wwwroot/|
    |保留檔案路徑 | 已啟用 (已勾選)|

2.  按一下 [儲存] 

最後，設定要使用代理程式集區的發行定義，而其中包含使用下列步驟部署的代理程式：
1.  選取發行定義，然後按一下 [編輯]
2.  從中間的資料行中選取 [在代理程式上執行]。 在右邊的資料行中，選取包含在 Azure Stack 上執行之組建代理程式的代理程式佇列。  
    ![顯示發行定義組態以使用特定佇列的影像](./media/azure-stack-solution-pipeline/image3.png)

## <a name="deploy-new-code"></a>部署新程式碼
您現在可以測試混合式 CI/CD 管線，以及發行至 Azure Stack 的最後一個步驟。  在本節中，您會修改網站的頁尾，並透過管線開始部署。  完成後，您將會看到變更部署至 Azure 以供檢閱，然後在核准發行之後，即會發行至 Azure Stack。

1. 在 Visual Studio 中，開啟 site.master 檔案，並變更這一行：
    
    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
    `

    變更為以下程式碼：

    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application delivered by VSTS, Azure, and Azure Stack</p>
    `
3.  認可變更並同步至 VSTS。  
4.  從 VSTS 工作區中，透過選取 [建置與發行]  > [建置] 來檢查組建狀態
5.  您將會看到進行中的組建。  按兩下 [狀態]，即可觀看組建進度。  當您在主控台中看見 [已完成的組建] 時，請繼續從 [建置與發行]  > [發行] 檢查發行。  按兩下 [發行]。
6.  您將會收到一則發行需要檢閱的通知。 請檢查 Web 應用程式 URL 並確認新的變更會出現。  核准 VSTS 中的發行。
    ![顯示發行定義組態以使用特定佇列的影像](./media/azure-stack-solution-pipeline/image4.png)
    
7.  透過建立 Web 應用程式時所記下的 URL 來造訪網站，以確認發行至 Azure Stack 的作業已完成。
    ![顯示 ASP.NET 應用程式以及已變更頁尾的影像](./media/azure-stack-solution-pipeline/image5.png)


您現在可以使用新的混合式 CI/CD 管線作為其他混合式雲端模式的建置組塊。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已學到如何建置一個混合式 CI/CD 管線：

> [!div class="checklist"]
> * 根據 Visual Studio Team Services (VSTS) 存放庫的程式碼認可起始新的建置。
> * 自動將您新建置的程式碼部署至 Azure 以進行使用者驗收測試。
> * 一旦您的程式碼已通過測試，便會自動部署至 Azure Stack。 

現在，您擁有一個混合式 CI/CD 管線，請繼續了解如何開發 Azure Stack 的應用程式。

> [!div class="nextstepaction"]
> [開發 Azure Stack](azure-stack-developer.md)


