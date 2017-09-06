---
title: "設定 Azure Service Fabric 叢集 | Microsoft Docs"
description: "快速入門 - 在 Azure 上建立 Windows 或 Linux Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: ec59450052b377412a28f7eaf55d1f1512b55195
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>在 Azure 上建立您的第一個 Service Fabric 叢集
[Service Fabric 叢集](service-fabric-deploy-anywhere.md)是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 此快速入門可協助您建立包含五個節點的叢集，在短短幾分鐘內透過 [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) 或 [Azure 入口網站](http://portal.azure.com)在 Windows 或 Linux 上執行該叢集。  

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。


## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

登入 Azure 入口網站，網址是 [http://portal.azure.com](http://portal.azure.com)。

### <a name="create-the-cluster"></a>建立叢集

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。
2. 選取 [新增] 刀鋒視窗中的 [計算]，然後選取 [計算] 刀鋒視窗中的 [Service Fabric 叢集]。
3. 填妥 Service Fabric 的 [基本資料] 表單。 針對 [作業系統]，選取您希望叢集節點執行的 Windows 或 Linux 版本。 在此輸入的使用者名稱和密碼用於登入虛擬機器。 對於 [資源群組]，建立新的資源群組。 資源群組是在其中建立和共同管理 Azure 資源的邏輯容器。 完成時，按一下 [確定]。

    ![叢集設定輸出][cluster-setup-basics]

4. 填妥 [叢集組態] 表單。  對於 [節點類型計數]，請輸入"1"。

5. 選取 [節點類型 1 (主要)] 並填妥 [節點類型組態] 表單。  輸入節點類型名稱並將[持久性層](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)設定為「銅級」。  選取 VM 大小。

    節點類型可定義 VM 大小、VM 數目、自訂端點，以及該類型 VM 的其他設定。 所定義的每個節點類型都會設定為不同的虛擬機器擴展集，以便用集合形式部署和管理虛擬機器。 每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並可以有不同的容量計量。  第一個 (或主要) 節點類型就是 Service Fabric 系統服務所在的節點類型，且必須具有 5 部或更多部 VM。

    對於任何生產部署而言，[容量規劃](service-fabric-cluster-capacity.md)都是一個很重要的步驟。  不過，在此快速入門中，您不會執行應用程式，因此選取 DS1_v2 Standard VM 大小。  針對[可靠性層](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)選取「銀級」，並將初始虛擬機器擴展集容量設定為 5。  

    自訂端點會在 Azure Load Balancer 中開啟連接埠，以便與在叢集上執行的應用程式連線。  輸入 "80, 8172" 以開啟連接埠 80 和 8172。

    請勿核取 [進行進階設定] 方塊中，其用於自訂 TCP/HTTP 管理端點、應用程式連接埠範圍、[放置條件約束](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints)和[容量屬性](service-fabric-cluster-resource-manager-metrics.md)。    

    選取 [確定] 。

6. 在 [叢集組態] 表單中，將 [診斷] 設定為 [開啟]。  在此快速入門中，您不需要輸入任何 [Fabric 設定](service-fabric-cluster-fabric-settings.md)屬性。  在 [Fabric 版本] 中，選取 [自動] 升級模式，以便 Microsoft 自動更新執行叢集的 Fabric 程式碼版本。  如果您想要[選擇支援的版本](service-fabric-cluster-upgrade.md) (升級目標)，請將模式設定為 [手動] 。 

    ![節點類型組態][node-type-config]

    選取 [確定] 。

7. 填妥 [安全性] 表單。  在此快速入門中選取 [不安全]。  強烈建議您針對生產工作負載建立一個安全的叢集，因為任何人都可以用匿名方式連線到不安全的叢集並執行管理作業。  

    憑證是在 Service Fabric 中用來提供驗證與加密，以保護叢集和其應用程式的各個層面。 如需如何在 Service Fabric 中使用憑證的詳細資訊，請參閱 [Service Fabric 叢集安全性案例](service-fabric-cluster-security.md)。  若要使用 Azure Active Directory 進行使用者驗證，或為了應用程式安全性設定憑證，請參閱[從 Resource Manager 範本建立叢集](service-fabric-cluster-creation-via-arm.md)。

    選取 [確定] 。

8. 檢閱摘要。  如果您想要下載從您輸入的設定建立的 Resource Manager 範本，請選取 [下載範本和參數]。  選取 [建立] 以建立叢集。

    您可以在通知功能中看到叢集的建立進度。 (請按一下畫面右上角狀態列附近的鈴噹圖示。)如果您在建立叢集時按了 [釘選到「開始面板」]，您會看到 [部署 Service Fabric 叢集] 已釘選到 [開始] 面板。

### <a name="view-cluster-status"></a>檢視叢集狀態
建立叢集之後，您就可以在入口網站的 [概觀] 刀鋒視窗中檢查您的叢集。 現在儀表板會顯示叢集的詳細資料，包括叢集的公用端點和 Service Fabric Explorer 的連結。

![叢集狀態][cluster-status]

### <a name="visualize-the-cluster-using-service-fabric-explorer"></a>使用 Service Fabric Explorer 將叢集視覺化
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 是一個理想的工具，可將叢集視覺化及管理應用程式。  Service Fabric Explorer 是在叢集中執行的一項服務。  按一下入口網站中叢集 [概觀]頁面 的 [Service Fabric Explorer] 連結，即可使用網頁瀏覽器進行存取。  您也可以直接在瀏覽器中輸入位址︰[http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer](http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer)

叢集儀表板會提供您叢集的概觀，包括應用程式和節點健康情況的摘要。 節點檢視會顯示叢集的實體配置。 對於指定的節點，您可以檢查已經在該節點上部署程式碼的應用程式。

![Service Fabric Explorer][service-fabric-explorer]

### <a name="connect-to-the-cluster-using-powershell"></a>使用 PowerShell 連線到叢集
使用 PowerShell 進行連線，以確認叢集正在執行。  ServiceFabric PowerShell 模組會隨著 [Service Fabric SDK](service-fabric-get-started.md) 一起安裝。  [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) Cmdlet 會建立叢集連線。   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
如需連線到叢集的其他範例，請參閱[連線到安全的叢集](service-fabric-connect-to-secure-cluster.md)。 連線到叢集之後，使用 [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) Cmdlet 來顯示叢集中的節點清單以及每個節點的狀態資訊。 每個節點的 **HealthState** 應該為「正常」。

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>移除叢集
Service Fabric 叢集是由叢集資源本身和其他 Azure 資源所構成。 因此，若要完全刪除 Service Fabric 叢集，您也必須刪除組成叢集的所有資源。 刪除叢集及其取用之所有資源的最簡單方式，就是刪除資源群組。 如需刪除叢集或刪除資源群組中一些 (但並非全部) 資源的其他方式，請參閱[刪除叢集](service-fabric-cluster-delete.md)

在 Azure 入口網站中刪除資源群組：
1. 瀏覽至您想要刪除的 Service Fabric 叢集。
2. 按一下叢集基本資訊頁面上的 [資源群組] 名稱。
3. 在 [資源群組基本資訊] 頁面中，按一下 [刪除資源群組] 並遵循該頁面上的指示，以完成資源群組的刪除。
    ![刪除資源群組][cluster-delete]


## <a name="use-azure-powershell-to-deploy-a-secure-cluster"></a>使用 Azure Powershell 來部署安全的叢集
1. 在您的電腦下載 [Azure Powershell 模組 4.0 版或更新版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。

2. 開啟 Windows PowerShell 視窗並執行下列命令。 
    
    ```powershell

    Get-Command -Module AzureRM.ServiceFabric 
    ```

    您應該會看到如下所示的輸出。

    ![ps-list][ps-list]

3. 登入 Azure 並選取您要建立叢集的訂用帳戶。

    ```powershell

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId "Subcription ID" 
    ```

4. 執行下列命令，立即建立安全的叢集。 別忘了自訂參數。 

    ```powershell
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $RDPpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 
    $RDPuser="vmadmin"
    $RGname="mycluster" # this is also the name of your cluster
    $clusterloc="SouthCentralUS"
    $subname="$RGname.$clusterloc.cloudapp.azure.com"
    $certfolder="c:\mycertificates\"
    $clustersize=1 # can take values 1, 3-99

    New-AzureRmServiceFabricCluster -ResourceGroupName $RGname -Location $clusterloc -ClusterSize $clustersize -VmUserName $RDPuser -VmPassword $RDPpwd -CertificateSubjectName $subname -CertificatePassword $certpwd -CertificateOutputFolder $certfolder
    ```

    此命令可能需要 10 到 30 分鐘的時間才能完成，而在結束時，您應該會取得如下所示的輸出。 此輸出包含憑證相關資訊、憑證上傳至的 Key Vault，以及複製憑證的本機資料夾。 

    ![ps-out][ps-out]

5. 複製整個輸出並儲存到文字檔，這是我們需要參考的資料。 記下輸出中的下列資訊。 

    - **CertificateSavedLocalPath** : c:\mycertificates\mycluster20170504141137.pfx
    - **CertificateThumbprint** : C4C1E541AD512B8065280292A8BA6079C3F26F10
    - **ManagementEndpoint** : https://mycluster.southcentralus.cloudapp.azure.com:19080
    - **ClientConnectionEndpointPort** : 19000

### <a name="install-the-certificate-on-your-local-machine"></a>在本機電腦上安裝憑證
  
若要連線到叢集，您需要將憑證安裝到目前使用者的個人 (My) 存放區。 

執行下列 PowerShell

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\the name of the cert.pfx `
        -Password (ConvertTo-SecureString -String certpwd -AsPlainText -Force)
```

您現在即可連線到您安全的叢集。

### <a name="connect-to-a-secure-cluster"></a>連線到安全的叢集 

執行下列 PowerShell 命令來連線到安全的叢集。 憑證詳細資料必須與用來設定叢集的憑證相符。 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```


下列範例顯示已完成的參數： 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

執行下列命令來檢查您連線的叢集是否狀況良好。

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="publish-your-apps-to-your-cluster-from-visual-studio"></a>從 Visual Studio 將您的應用程式發佈至叢集

您現在已設定好 Azure 叢集，即可依照[發佈至叢集](service-fabric-publish-app-remote-cluster.md)文件中所述，從 Visual Studio 將您的應用程式發佈至該叢集。 

### <a name="remove-the-cluster"></a>移除叢集
叢集是由叢集資源本身和其他 Azure 資源所構成。 刪除叢集及其取用之所有資源的最簡單方式，就是刪除資源群組。 

```powershell

Remove-AzureRmResourceGroup -Name $RGname -Force

```

## <a name="next-steps"></a>後續步驟
您已設定好開發叢集，請嘗試下列作業︰
* [在入口網站中建立安全的叢集](service-fabric-cluster-creation-via-portal.md)
* [從範本建立叢集](service-fabric-cluster-creation-via-arm.md) 
* [使用 PowerShell 部署應用程式](service-fabric-deploy-remove-applications.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
[ps-list]: ./media/service-fabric-get-started-azure-cluster/pslist.PNG
[ps-out]: ./media/service-fabric-get-started-azure-cluster/psout.PNG

