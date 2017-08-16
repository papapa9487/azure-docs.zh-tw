---
title: "使用持續整合 (Team Services) 部署 Azure Service Fabric 應用程式 |Microsoft Docs"
description: "了解如何使用 Visual Studio Team Services 設定 Service Fabric 應用程式的持續整合和部署。  將應用程式部署到 Azure 中的 Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: dda5ea77b35fa8491128135ea7709016781f2aea
ms.contentlocale: zh-tw
ms.lasthandoff: 08/10/2017

---

# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>將搭配 CI/CD 的應用程式部署到 Service Fabric 叢集
本教學課程是系列中的第三部分，說明如何使用 Visual Studio Team Services (VSTS) 設定 Azure Service Fabric 應用程式的持續整合和部署。  需要現有的 Service Fabric 應用程式，在[建置 .NET 應用程式](service-fabric-tutorial-create-dotnet-app.md)中建立的應用程式將做為範例。

在系列的第三部分中，您將了解如何：

> [!div class="checklist"]
> * 將原始檔控制新增至專案
> * 在 Team Services 中建立組建定義
> * 在 Team Services 中建立發行定義
> * 自動部署和升級應用程式

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * [建置 .NET Service Fabric 應用程式](service-fabric-tutorial-create-dotnet-app.md)
> * [將應用程式部署到遠端叢集](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * 使用 Visual Studio Team Services 設定 CI/CD

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前：
- 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [安裝 Visual Studio 2017](https://www.visualstudio.com/) 並安裝 **Azure 開發**以及 **ASP.NET 和 Web 開發**工作負載。
- [安裝 Service Fabric SDK](service-fabric-get-started.md)
- 建立 Service Fabric 應用程式，例如[遵循本教學課程](service-fabric-tutorial-create-dotnet-app.md)。 
- 在 Azure 上建立 Windows Service Fabric 叢集，例如[遵循本教學課程](service-fabric-tutorial-create-cluster-azure-ps.md)
- 建立 [Team Services 帳戶](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)。

## <a name="prepare-a-publish-profile"></a>下載發行設定檔
您現在已經[建立應用程式](service-fabric-tutorial-create-dotnet-app.md)，而且已經[將應用程式部署到 Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)，可以準備設定持續整合。  首先，在應用程式中準備部署程序使用的發行設定檔 (於 Team Services 內執行)。  發行設定檔應設定為以先前建立的叢集為目標。  啟動 Visual Studio，並開啟現有的 Service Fabric 應用程式專案。  在 [方案總管] 中，以滑鼠右鍵按一下應用程式並選取 [發佈...]。

在您的應用程式專案內選擇要使用於持續整合工作流程 (例如雲端) 的目標設定檔。  指定叢集連線端點。  勾選**升級應用程式**核取方塊，讓您的應用程式對於 Team Services 中的每個部署升級。  按一下 [儲存] 超連結，將設定儲存至發行設定檔，然後按一下 [取消] 關閉對話方塊。  

![推送設定檔][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>向新的 Team Services Git 儲存機制共用 Visual Studio 解決方案
向 Team Services 中的小組專案共用應用程式原始檔，以便產生組建。  

Visual Studio 右上角的狀態列上，選取 [新增至原始檔控制] -> [Git]，建立專案的新本機 Git 儲存機制。 

在 [Team Explorer] 的 [推送] 檢視中，選取 [推送至 Visual Studio Team Services] 下的 [發佈 Git 儲存機制] 按鈕。

![推送 Git 儲存機制][push-git-repo]

確認您的電子郵件，並在 [Team Services 網域] 下拉式清單選取您的帳戶。 輸入您的儲存機制名稱，並選取 [發佈儲存機制]。

![推送 Git 儲存機制][publish-code]

發佈儲存機制將在您的帳戶中建立與本機儲存機制名稱相同的新 Team 專案。 若要在現有的 Team 專案中建立儲存機制，請按一下 [儲存機制] 名稱旁邊的 [進階]，並選取 Team 專案。 您可以選取**在網路上檢視**，在網路上檢視您的程式碼。

## <a name="configure-continuous-delivery-with-vsts"></a>設定 VSTS 的持續傳遞
使用 Visual Studio 的內建精靈設定 VSTS 的持續傳遞。

1. 以滑鼠右鍵按一下 [方案總管] 中的 **MyApplication** 應用程式專案，然後選取 [新增] -> [設定 VSTS 的持續傳遞]。 對話方塊隨即彈出，以供您輸入所需的組態。

    ![VSTS 的持續傳遞][continuous-delivery-with-VSTS]

    [帳戶]、[專案] 和 [Git 儲存機制] 值會自動輸入。

2. 選擇 [裝載 VS2017] 做為代理程式佇列。

3. 選擇 [叢集連線] 下拉式清單中的 [建立連線]，這會開啟在 VSTS 中設定服務端點的網站。 

4. 將焦點設定在瀏覽器視窗，並選擇 [新增服務端點] -> [Service Fabric]。

    ![新增服務端點][new-service-endpoint]

    對話方塊隨即彈出，以供新增 Service Fabric 連線。
    
5. 選取**憑證式**並完成對話方塊：

    ![新增服務端點對話方塊][new-service-endpoint-dialog]

    1. 輸入**連線名稱**。
    2. 在 [叢集端點] 欄位中輸入叢集的管理端點 (例如，「tcp://mycluster.eastus.cloudapp.azure.com:19000」)。 指定「tcp://」做為通訊協定。  預設管理端點連接埠為 19000。
    3. 輸入**伺服器憑證指紋**。  對於叢集開啟 Service Fabric Explorer (https://mycluster.eastus.cloudapp.azure.com:19080)，即可取得指紋。
        - 選擇樹狀檢視中的 [叢集] 節點和右側窗格中的 [資訊清單] 索引標籤。
        - 尋找 XML 檔案中的 **<ServerCertificate>** 元素，並複製 **X509FindValue** 屬性的內容。
    4. 在 [用戶端憑證] 欄位中，輸入 Base64 編碼字串格式的用戶端憑證，才能取得組建代理程式上安裝的憑證：
        - 請確定您有 PFX 檔案格式的可用憑證。
        - 執行下列 PowerShell 命令，將 Base64 編碼字串格式的 PFX 檔案輸出到剪貼簿：`[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(C:\path\to\certificate.pfx)) | clip`
        - 在對話方塊的欄位中貼上 (ctrl + v) 剪貼簿中的值。
    5. 在 [密碼] 欄位中輸入**憑證密碼**，然後按一下 [確定]。

6. 在 Visual Studio 中，選擇 [VSTS 的持續傳遞] 對話方塊的 [叢集連線] 欄位之中的 **<Refresh>**。 您剛才建立的叢集端點應該會出現在下拉式清單中。

7. 選擇預設組建和發行定義名稱，或變更對話方塊中設定的名稱。 完成後，按一下 [確定]。

經過一段時間之後，Visual Studio 將彈出對話方塊，詢問您是否要在瀏覽器中開啟組建和發行定義。 您可以選擇開啟，以便檢查設定方式，但是完成本教學課程不需要這麼做。

- Team Services 組建定義描述由一組循序執行的組建步驟所組成的工作流程。 建立產生 Service Fabric 應用程式封裝的組建定義，以及其他構件，以便部署到 Service Fabric 叢集。 深入了解 [Team Services 組建定義](https://www.visualstudio.com/docs/build/define/create)。
- Team Services 發行定義描述將應用程式封裝部署到叢集的工作流程。 一起使用時，組建定義和發行定義可以執行整個工作流程，從來源檔案開始，並以叢集中的執行中應用程式結束。 深入了解 Team Services [發行定義](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)。

## <a name="commit-and-push-changes-trigger-a-release"></a>認可並推送變更，觸發發行程序
簽入某些程式碼變更至 Team Services，確認持續整合管線正常運作。    

您撰寫程式碼時，Visual Studio 會自動追蹤您的變更。 認可本機 Git 儲存機制的變更，方法是從右下方的狀態列選取暫止變更圖示 (![Pending][pending])。

在 Team Explorer 的**變更**檢視中，加入描述更新的訊息，並認可變更。

![全部認可][changes]

選取 [未發行的變更] 狀態列圖示 (![未發行的變更][unpublished-changes]) 或 Team Explorer 中的 [同步] 檢視。 選取 [推送] 更新 Team Services/TFS 中的程式碼。

![推送變更][push]

自動將變更推送至 Team Services 觸發組建。  組建定義成功完成時，發行就會自動建立，並開始升級叢集上的應用程式。

若要檢查組建進度，請切換到 **Team Explorer** Visual Studio 中的 [組建] 索引標籤。  您確認組建執行成功，請定義將應用程式部署至叢集的發行定義。

確認部署成功，而且應用程式在叢集中執行。  開啟網頁瀏覽器，並瀏覽至 [http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/)。  請注意應用程式版本，在此範例中是「1.0.0.20170616.3」。

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>更新應用程式
在應用程式中進行程式碼變更。  在先前的步驟儲存並認可變更。

應用程式的升級開始後，即可注意 Service Fabric Explorer 中的升級進度：

![Service Fabric Explorer][sfx2]

應用程式升級可能需要幾分鐘的時間。 升級完成時，應用程式將執行下一個版本。  在此範例中是「1.0.0.20170616.4」。

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 將原始檔控制新增至專案
> * 建立組建定義
> * 建立發行定義
> * 自動部署和升級應用程式

部署應用程式並設定持續整合後，請嘗試下列項目：
- [升級應用程式](service-fabric-application-upgrade.md)
- [測試應用程式](service-fabric-testability-overview.md) 
- [監視與診斷](service-fabric-diagnostics-overview.md)


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[add-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddTask.png
[new-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewTask.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
