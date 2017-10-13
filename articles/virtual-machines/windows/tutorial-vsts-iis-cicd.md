---
title: "在 Azure 中使用 Team Services 建立 CI/CD 管線 | Microsoft Docs"
description: "了解如何建立用於持續整合和持續傳遞的 Visual Studio Team Services 管線，將 Web 應用程式部署至 Windows VM 上的 IIS"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a587f58fad2ec74c7633823c4d34f900e7c01f7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>使用 Visual Studio Team Services 和 IIS 建立持續整合管線
若要將應用程式開發的組建、測試、部署階段自動化，可以使用持續整合和部署 (CI/CD) 管線。 在本教學課程中，您可以使用 Visual Studio Team Services 以及 Azure 中執行 IIS 的 Windows 虛擬機器 (VM) 建立 CI/CD 管線。 您會了解如何：

> [!div class="checklist"]
> * 將 ASP.NET Web 應用程式發佈至 Team Services 專案
> * 建立以程式碼認可所觸發的組建定義
> * 在 Azure 的虛擬機器上安裝和設定 IIS
> * 將 IIS 執行個體新增至 Team Services 中的部署群組
> * 建立發行定義將新的 Web 部署套件發佈至 IIS
> * 測試 CI/CD 管線

本教學課程需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。


## <a name="create-project-in-team-services"></a>在 Team Services 中建立專案
Visual Studio Team Services 可讓您進行簡單的共同作業與開發，不需要維護內部部署的程式碼管理解決方案。 Team Services 提供雲端的程式碼測試、組建和 Application Insights。 您可以選擇版本控制存放庫和最符合您的程式碼開發的整合式開發環境 (IDE)。 在本教學課程中，您可以使用免費帳戶來建立基本 ASP.NET Web 應用程式和 CI/CD 管線。 如果您還沒有 Team Services 帳戶，請[建立帳戶](http://go.microsoft.com/fwlink/?LinkId=307137)。

若要管理的程式碼認可程序、組建定義、發行定義，請在 Team Services 中建立專案，如下所示︰

1. 在 Web 瀏覽器中開啟 Team Services 儀表板，選擇 [新增專案]。
2. 在 [專案名稱] 輸入 myWebApp。 保留所有其他使用 Git 版本控制和 Agile 工作項目程序的預設值。
3. 選擇與小組成員 [共用] 的選項，然後選取 [建立]。
5. 建立您的專案後，選擇 [以 README 或 gitignore 初始化] 選項，然後 [初始化]。
6. 在您的新專案中，選擇上端的 [儀表板]，然後選取 [在 Visual Studio 中開啟]。


## <a name="create-aspnet-web-application"></a>建立 ASP.NET Web 應用程式
在前面的步驟，您已在 Team Services 中建立專案。 最後一個步驟是在 Visual Studio 中開啟您的新專案。 在 [Team Explorer] 視窗中管理您的程式碼認可。 建立新專案的本機副本，然後從範本建立 ASP.NET web 應用程式，如下所示︰

1. 選取 [複製] 建立 Team Services 專案的本機 Git 存放庫。
    
    ![從 Team Services 專案複製存放庫](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. 在 [解決方案] 下，選取 [新增]。

    ![建立 Web 應用程式解決方案](media/tutorial-vsts-iis-cicd/new_solution.png)

3. 選取 [Web] 範本，然後選擇 [ASP.NET Web 應用程式] 範本。
    1. 為您的應用程式輸入名稱，例如 myWebApp，然後取消選取 [為解決方案建立目錄] 核取方塊。
    2. 取消選取 [將 Application Insights 新增至專案] \(如果有此選項)。 Application Insights 需要您以 Azure Application Insights 授權您的 Web 應用程式。 為了簡化本教學課程中，請略過此程序。
    3. 選取 [確定] 。
4. 從範本清單中選擇 [MVC]。
    1. 選擇 [變更驗證]，選擇 [不需要驗證]，然後選取 [確定]。
    2. 選取 [確定]  建立您的解決方案。
5. 在 [Team Explorer] 視窗中，選擇 [變更]。

    ![認可對 Team Services Git 存放庫的本機變更](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. 在認可文字方塊中輸入訊息，例如「初始認可」。 從下拉式功能表選擇 [全部認可並同步處理]。


## <a name="create-build-definition"></a>建立組建定義
在 Team Services 中，您使用組建定義來概述應如何組建您的應用程式。 在本教學課程中，我們建立的基本定義會採用我們的來源程式碼、組建解決方案，然後建立可以用來執行 IIS 伺服器上 Web 應用程式的 Web 部署套件。

1. 在您的 Team Services 專案中，選擇上端的 組建及發行，然後選取組建。
3. 選取 [+新增定義]。
4. 選擇 [ASP.NET (PREVIEW)] 範本，然後選取 [套用]。
5. 保留所有預設的工作值。 在 [取得來源] 下，確定已選取 myWebApp 存放庫和 master 分支。

    ![在 Visual Studio Team Services 中建立組建定義](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. 在 [觸發程序] 索引標籤上，將 [啟用此觸發程序] 滑桿移動至 [啟用]。
7. 選取 [儲存並排入佇列] 以儲存組建定義並將新組建排入佇列，然後再選取 [儲存並排入佇列] 一次。 保留預設值，然後選取 [佇列]。

監看組建排入裝載代理程式的排程，然後開始組建。 輸出類似於下列範例：

![Team Services 專案的成功組建](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Create virtual machine
若要提供執行 ASP.NET Web 應用程式的平台，您需要執行 IIS 的 Windows 虛擬機器。 當您認可程式碼且組建觸發後，Team Services 會使用代理程式來與 IIS 執行個體互動。

使用[此指令碼範例](../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)建立 Windows Server 2016 VM。 指令碼需要花幾分鐘的時間來執行及建立 VM。 建立 VM 後，使用 [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.resources/new-azurermresourcegroup) 開啟連接埠 80 用於 Web 流量，如下所示︰

```powershell
Get-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $resourceGroup `
  -Name "myNetworkSecurityGroup" | `
Add-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWeb" `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority "1001" `
  -SourceAddressPrefix "*" `
  -SourcePortRange "*" `
  -DestinationAddressPrefix "*" `
  -DestinationPortRange "80" `
  -Access "Allow" | `
Set-AzureRmNetworkSecurityGroup
```

若要連線至您的 VM，使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 取得公用 IP 位址，如下所示：

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup | Select IpAddress
```

建立與 VM 的遠端桌面工作階段：

```cmd
mstsc /v:<publicIpAddress>
```

在 VM 上，開啟 **系統管理員 PowerShell** 命令提示字元。 安裝 IIS 和所需的 .NET 功能，如下所示︰

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>建立部署群組
若要將 Web 部署封裝推送到 IIS 伺服器，請在 Team Services 中定義部署群組。 此群組可讓您在 Team Services 認可程式碼且組建完成時，指定哪些伺服器是新組建的目標。

1. 在 Team Services 中，選擇 [組建及發行]，然後選取 [部署群組]。
2. 選擇 [新增部署群組]。
3. 輸入資源群組的名稱，例如 myIIS，然後選取 [建立]。
4. 在 [註冊機器] 區段中，確定已選取 [Windows]，然後選取 [在指令碼中使用個人存取權杖進行驗證] 核取方塊。
5. 選取 [將指令碼複製到剪貼簿]。


### <a name="add-iis-vm-to-the-deployment-group"></a>將 IIS VM 新增至部署群組
建立部署群組後，將每個 IIS 執行個體新增至群組。 Team Services 產生的指令碼，會下載並設定 VM 上的代理程式 (此 VM 會收到新 Web 部署套件)，然後將它套用至 IIS。

1. 回到您的 VM 上的 **系統管理員 PowerShell** 工作階段，貼上從 Team Services 複製的指令碼並執行。
2. 當系統提示您設定代理程式的標記，選擇 [Y] 並輸入 Web。　
3. 當系統提示輸入使用者帳戶，按Return 接受預設值。
4. 等待指令碼完成並顯示「已成功啟動 vstsagent.account.computername 服務」訊息。
5. 在 [組建及發行] 功能表的 [部署群組] 頁面中，開啟 myIIS 部署群組。 在 [機器] 索引標籤中，確認已列出您的 VM。

    ![成功將 VM 新增至 Team Services 部署群組](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>建立發行定義
若要發佈您的組建，請在 Team Services 中建立發行定義。 您的應用程式的成功組建會自動觸發此定義。 您選擇您的 Web 部署套件要推送至哪個部署群組，並定義適當的 IIS 設定。

1. 選擇 [組建及發行]，然後選取 [組建]。 選擇在上一個步驟中建立的組建定義。
2. 在 [最近完成] 之下，選擇最新的組建，然後選取 [發行]。
3. 選擇 [是] 建立發行定義。
4. 選擇 [空白] 範本，然後選取 [下一步]。
5. 確認專案和來源組建定義已填入您的專案。
6. 選取 [持續部署] 核取方塊，然後選取 [建立]。
7. 選取 [新增工作] 旁的下拉式清單，然後選擇 [新增部署群組階段]。
    
    ![在 Team Services 中將工作新增至發行定義](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. 選擇 [IIS Web 應用程式部署 (預覽)] 旁的 [新增]，然後選取[關閉]。
9. 選取 [在部署群組上執行] 父工作。
    1. 在 [部署群組] 選取您稍早建立的部署群組，例如 myIIS。
    2. 在 [機器標記] 方塊中，選取 [新增]，然後選擇 [Web] 標記。
    
    ![用於 IIS 的發行定義部署群組工作](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. 選取 [部署︰IIS Web 應用程式部署] 工作來設定您的 IIS 執行個體設定，如下所示︰
    1. 在 [網站名稱] 輸入 Default Web Site。
    2. 保留所有其他預設設定。
12. 選擇 [儲存]，然後選取 [確定] 兩次。


## <a name="create-a-release-and-publish"></a>建立發行並發佈
您現在可以將您的 Web 部署套件當做新發行加以推送。 這個步驟會與屬於部署群組的每個執行個體上的代理程式通訊，推送 Web 部署套件，然後將 IIS 設定為執行更新的 Web 應用程式。

1. 在您的發行定義中，選取 [+ 發行]，然後選擇 [建立發行]。
2. 在下拉式清單中，確認已選取最新組建以及 [自動部署︰建立發行之後]。 選取 [ **建立**]。
3. 在您的發行定義上方會顯示一個小橫幅，例如「已建立 'Release-1' 發行」。 選取發行連結。
4. 開啟 [記錄] 索引標籤以查看發行進度。
    
    ![成功的 Team Services 發行和 Web 部署封裝推送](media/tutorial-vsts-iis-cicd/successful_release.png)

5. 完成發行後，開啟網頁瀏覽器，並輸入您的 VM 的公用 IP 位址。 您的 ASP.NET Web 應用程式正在執行。

    ![IIS VM 上執行的 ASP.NET Web 應用程式](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>測試整個 CI/CD 管線
您的 Web 應用程式正在 IIS 上執行，現在，請嘗試整個 CI/CD 管線。 在 Visual Studio 中進行變更並認可您的程式碼後，就會觸發組建，組建再觸發您更新過之 Web 部署套件並要推送至 IIS 的發行：

1. 在 Visual Studio 中開啟 [解決方案總管] 視窗。
2. 瀏覽至 [myWebApp | 檢視 | 首頁 | Index.cshtml] 並開啟。
3. 將第 6 行編輯為︰

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. 儲存檔案。
5. 開啟 [Team Explorer] 視窗，選取 myWebApp 專案，然後選擇 [變更]。
6. 輸入認可訊息，例如「測試 CI/CD 管線」，然後選擇下拉式選單中的 [全部認可並同步處理]。
7. 在 Team Services 工作區中，新組建是從程式碼認可觸發。 
    - 選擇 [組建及發行]，然後選取 [組建]。 
    - 選擇您的組建定義，然後選取 [已排入佇列和執行中] 組建查看組建進度。
9. 組建成功之後，就會觸發新的發行。
    - 選擇 [組建及發行]，然後選擇 [發行] 查看推送至 IIS VM 的 Web 部署套件。 
    - 選取**重新整理**圖示以更新狀態。 當 [環境] 欄顯示綠色核取記號，表示發行已成功部署到 IIS。
11. 若要查看您套用的變更，在瀏覽器中重新整理您的 IIS 網站。

    ![從 CI/CD 管線在 IIS VM 上執行的 ASP.NET Web 應用程式](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 Team Services 中建立 ASP.NET Web 應用程式，以及設定組建和發行定義，用以為每個程式碼認可將新的 Web 部署套件部署至 IIS。 您已了解如何︰

> [!div class="checklist"]
> * 將 ASP.NET Web 應用程式發佈至 Team Services 專案
> * 建立以程式碼認可所觸發的組建定義
> * 在 Azure 的虛擬機器上安裝和設定 IIS
> * 將 IIS 執行個體新增至 Team Services 中的部署群組
> * 建立發行定義將新的 Web 部署套件發佈至 IIS
> * 測試 CI/CD 管線

前進到下一個教學課程，以了解如何使用 SSL 憑證保護 Web 伺服器。

> [!div class="nextstepaction"]
> [使用 SSL 保護 Web 伺服器](tutorial-secure-web-server.md)