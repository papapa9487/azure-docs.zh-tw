---
title: "在 Azure 中建立 Service Fabric 叢集 | Microsoft Docs"
description: "了解如何使用 PowerShell 在 Azure 中建立 Windows 或 Linux Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: cristyg
ms.custom: mvc
ms.openlocfilehash: 29a8d5cf9a59a08dc63df1dbd601aa1f509cad95
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2017
---
# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>使用 PowerShell 在 Azure 上建立安全叢集
這是教學課程一系列步驟中的第一個步驟，示範如何使用 Service Fabric 叢集以及容器，將 .NET 應用程式移至雲端。 在後續步驟中，您會了解如何建立在 Azure 中執行的 Service Fabric 叢集 (Windows 或 Linux)。 完成時，您會有在您可以部署應用程式的雲端中執行安全的叢集。

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前：
- 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 安裝 [Service Fabric SDK](service-fabric-get-started.md)
- 安裝 [Azure Powershell 模組 4.1 版或更新版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (如果需要，[安裝 Azure PowerShell](/powershell/azure/overview) 或[更新至較新的版本](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps))


# <a name="create-a-service-fabric-cluster"></a>建立 Service Fabric 叢集

此指令碼會建立單一節點預覽 Service Fabric 叢集。 此叢集會由隨著叢集一起建立並放置在金鑰保存庫中的自我簽署憑證所保護。 調整單一節點叢集的規模時，不可以超過一部虛擬機器，而且預覽叢集無法升級至較新的版本。

若要計算在 Azure 中執行 Service Fabric 叢集產生的成本，請使用 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)。
如需建立 Service Fabric 叢集的詳細資訊，請參閱[使用 Azure Resource Manager 來建立 Service Fabric 叢集](service-fabric-cluster-creation-via-arm.md)。

## <a name="log-in-to-azure"></a>登入 Azure
開啟 PowerShell 主控台，登入 Azure，並選取想要在其中部署叢集的訂用帳戶：

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>叢集參數

   此指令碼使用下列參數和概念。 自訂參數以符合您的需求。

   | 參數       | 說明 | 建議的值 |
   | --------------- | ----------- | --------------- |
   | 位置 | 要作為叢集部署目的地的 Azure 區域。 | *例如 westeurope、eastasia、eastus* |
   | 名稱     | 您想要建立之叢集的名稱。 名稱必須介於 4-23 個字元之間，而且只能使用小寫字母、數字和連字號。 | *例如 bobs-sfpreviewcluster* |
   | resourceGroupName   | 要在其中建立叢集之資源群組的名稱。 | 例如，myresourcegroup |
   | VmSku  | 要用於節點的虛擬機器 SKU。 | 任何有效的虛擬機器 SKU |
   | 作業系統  | 要用於節點的虛擬機器 OS。 | 任何有效的虛擬機器 OS |
   | KeyVaultName | 與叢集相關聯之新 KeyVault 的名稱。 | 例如，mykeyvault |
   | ClusterSize | 叢集中的虛擬機器數目 (可以是 1 或 3-99)。| 針對預覽叢集僅指定一部虛擬機器 |
   | CertificateSubjectName | 要建立之憑證的主旨名稱。 | 遵循格式 <name>.<location>.cloudapp.azure.com |

## <a name="default-parameter-values"></a>預設參數值
**虛擬機器**設定是選擇性的。 如果您未指定它們，系統管理員使用者名稱預設為 "vmadmin"，且 PowerShell 會在建立叢集之前提示您輸入虛擬機器密碼。

**連接埠**將預設為連接埠 80 和 8081。 您可以依照 [Service Fabric 叢集中的連接埠](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule)的指引，指定其他連接埠。

預設會啟用**診斷**。

預設不會啟用 **DNS 服務**。

預設也不會啟用**反向 Proxy**。

## <a name="create-the-cluster-with-your-parameters"></a>使用您的參數建立叢集

一旦您決定符合您需求的參數，執行下列命令以產生安全的 Service Fabric 叢集以及其憑證。

您可以修改此指令碼，以包含其他參數。 如需用於叢集建立之參數的詳細資訊，請參閱 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md) Cmdlet。

>[!NOTE]
>執行此命令之前，您必須建立要在其中輸出叢集憑證的資料夾。

```powershell

    # Certificate variables. This will create and encrypt a password to be used by Service Fabric.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

    # You must create the folder where you want to output the certificate on your machine before executing this step.
    $certfolder="c:\mycertificates\"

    # Variables for common values. Change the values to fit your needs.
    $clusterloc="WestUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=1

    # Create the Service Fabric cluster and its self-signed certificate. The OS specified here allows you use this cluster with any applications that are also leveraging containers.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

建立程序可能需要幾分鐘的時間。 在設定完成後，它會輸出 Azure 中所建立之叢集的相關資訊。 它也會將叢集憑證複製到您為這個參數所指定之路徑上的 -CertificateOutputFolder 目錄。

記下叢集的 **ManagementEndpoint** URL，此 URL 可能會類似下列 URL：https://mycluster.westeurope.cloudapp.azure.com:19080

## <a name="import-the-certificate"></a>匯入憑證

成功建立叢集時，執行下列命令以確保您可以使用自我簽署憑證。

```powershell

    # Connect to the cluster using by installing the certificate into the Personal (My) store of the current user on your computer.
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
            -Password $certpwd
```

此命令會為機器的目前使用者安裝憑證。  您需要有此憑證才能存取 Service Fabric Explorer 並檢視叢集的健康情況。


## <a name="view-your-cluster-optional"></a>檢視叢集 (選擇性)

一旦您同時具備叢集和匯入的憑證，您可以連線到叢集，並檢視其健康情況。 連線的方式有許多種，透過 Service Fabric Explorer 或 PowerShell。

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
您可以藉由開啟 Service Fabric Explorer 來檢視叢集的健康情況。 若要這樣做，請使用網頁瀏覽器瀏覽至叢集的 **ManagementEndpoint** URL，然後選取您的機器上儲存的憑證。

>[!NOTE]
>由於您使用自我簽署的憑證，因此開啟 Service Fabric Explorer 時會顯示憑證錯誤。 在 Edge 中，您必須依序按一下 [詳細資料] 與 [繼續瀏覽網頁] 連結。 在 Chrome 中，您必須依序按一下 [進階] 與 [繼續] 連結。

### <a name="powershell"></a>PowerShell

**Service Fabric** PowerShell 模組提供許多 Cmdlet 來管理 Service Fabric 叢集、應用程式和服務。  使用 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) Cmdlet 連接到安全的叢集。 在上一個步驟的輸出中找到憑證指紋和連線端點詳細資訊。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

您也可以使用 [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) Cmdlet 檢查已連線，而且叢集狀況良好。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>後續步驟
在本教學課程中，您會了解如何使用 PowerShell 在 Azure 中建立安全的 Service Fabric 叢集

接下來，前進到下列的教學課程，了解如何部署現有的應用程式。
> [!div class="nextstepaction"]
> [透過 Docker Compose 部署現有的 .NET 應用程式](service-fabric-host-app-in-a-container.md)

---
