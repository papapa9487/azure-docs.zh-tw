---
title: "在 Azure 中建立 Windows Service Fabric 叢集 | Microsoft Docs"
description: "了解如何使用 PowerShell，將 Windows Service Fabric 叢集部署到現有的 Azure 虛擬網路。"
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
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: d1eabaa1a2f1f8ba8102d567fee97c65d04ca5f7
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>將安全的 Service Fabric Windows 叢集部署到 Azure 虛擬網路
本教學課程是一個系列的第一部分。 您將會了解如何使用 PowerShell 將 Windows Service Fabric 叢集部署到現有的 Azure 虛擬網路 (VNET) 和子網路。 完成時，您會有在您可以部署應用程式的雲端中執行的叢集。  若要使用 Azure CLI 建立 Linux 叢集，請參閱[在 Azure 上建立安全的 Linux 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用 PowerShell 在 Azure 中建立 VNET
> * 建立金鑰保存庫並上傳憑證
> * 在 Azure PowerShell 中建立安全的 Service Fabric 叢集
> * 使用 X.509 憑證保護叢集
> * 使用 PowerShell 連線到叢集
> * 刪除叢集

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * 在 Azure 上建立安全叢集
> * [使用 Service Fabric 部署 API 管理](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前：
- 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 安裝 [Service Fabric SDK 和 PowerShell 模組](service-fabric-get-started.md)
- 安裝 [Azure PowerShell 模組 4.1 版或更新版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

下列程序會建立五個節點的 Service Fabric 叢集。 若要計算在 Azure 中執行 Service Fabric 叢集產生的成本，請使用 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)。

## <a name="sign-in-to-azure-and-select-your-subscription"></a>登入 Azure 並選取您的訂用帳戶
本指南使用 Azure PowerShell。 開始新的 PowerShell 工作階段時，請先登入您的 Azure 帳戶並選取您的訂用帳戶，然後再執行 Azure 命令。
 
執行下列指令碼，以登入您的 Azure 帳戶並選取您的訂用帳戶：

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>建立資源群組
針對您的部署建立新的資源群組，並指定名稱和位置。

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc
```

## <a name="deploy-the-network-topology"></a>部署網路拓撲
接下來，設定將作為 API 管理與 Service Fabric 叢集部署位置的網路拓撲。 [Network.json][network-arm] Resource Manager 範本已設定來建立虛擬網路 (VNET)，同時也會建立適用於 Service Fabric 的子網路與網路安全性群組 (NSG) 以及適用於 API 管理的子網路和 NSG。 在[這裡](../virtual-network/virtual-networks-overview.md)深入了解 VNET、子網路與 NSG。

[Network.parameters.json][network-parameters-arm] 參數檔包含要作為 Service Fabric 與 API 管理部署位置之子網路和 NSG 的名稱。  [下一個教學課程](service-fabric-tutorial-deploy-api-management.md)會部署 API 管理。 就這份指南而言，參數值無須變更。 Service Fabric Resource Manager 範本會使用這些值。  如果在此處修改這些值，您也必須在本教學課程和[部署 API 管理教學課程](service-fabric-tutorial-deploy-api-management.md)所使用的其他 Resource Manager 範本中修改這些值。 

下載下列 Resource Manager 範本和參數檔：
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

使用下列 PowerShell 命令來部署用於網路設定的 Resource Manager 範本和參數檔：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>部署 Service Fabric 叢集
部署完網路資源之後，下一個步驟是將 Service Fabric 叢集部署至為 Service Fabric 叢集指定之子網路與 NSG 中的 VNET。 將叢集部署至現有的 VNET 和子網路 (先前已在本文中部署) 需要 Resource Manager 範本。  如需詳細資訊，請參閱[使用 Azure Resource Manager 建立叢集](service-fabric-cluster-creation-via-arm.md)。 本教學課程系列已將範本預先設定為使用您在上一個步驟中設定之 VNET、子網路及 NSG 的名稱。  下載下列 Resource Manager 範本和參數檔：
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

憑證可用來保護您叢集的節點對節點通訊，以及管理使用者對您 Service Fabric 叢集的存取。 「API 管理」也會使用此憑證來存取「Service Fabric 命名服務」以探索服務。 

下列指令碼會使用 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) Cmdlet 以在 Azure 中部署新的叢集。 Cmdlet 也會在 Azure 中建立金鑰保存庫、建立自我簽署憑證和金鑰保存庫，並將憑證檔案下載至本機。   

```powershell
# Certificate variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"

# Variables for VM admin.
$adminuser="vmadmin"
$adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

# Variables for common values
$clustername = "mysfcluster"
$vmsku = "Standard_D2_v2"
$vaultname = "clusterkeyvault"
$vaultgroupname="clusterkeyvaultgroup"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Set the number of cluster nodes. Possible values: 1, 3-99
$clustersize=5 

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
-ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
-CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
-OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname
```

## <a name="connect-to-the-secure-cluster"></a>連線到安全的叢集
使用隨 Service Fabric SDK 一起安裝的 Service Fabric PowerShell 模組連線到叢集。  首先，將憑證安裝到您的電腦上目前使用者的個人 (My) 存放區。  執行下列 PowerShell 命令：

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

您現在即可連線到您安全的叢集。

**Service Fabric** PowerShell 模組提供許多 Cmdlet 來管理 Service Fabric 叢集、應用程式和服務。  使用 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) Cmdlet 連接到安全的叢集。 在上一個步驟的輸出中找到憑證指紋和連線端點詳細資訊。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

使用 [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) Cmdlet 檢查已連線，而且叢集狀況良好。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>清除資源
本教學課程系列的其他文章會使用您剛才建立的叢集。 如果您現在不打算繼續閱讀下一篇文章，您可能要刪除該叢集和金鑰保存庫以避免產生費用。 刪除叢集及其取用之所有資源的最簡單方式，就是刪除資源群組。

使用 [Remove-AzureRMResourceGroup Cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) 刪除資源群組和所有叢集資源。  也會刪除包含金鑰保存庫的資源群組。

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 PowerShell 在 Azure 中建立 VNET
> * 建立金鑰保存庫並上傳憑證
> * 使用 PowerShell 在 Azure 中建立安全的 Service Fabric 叢集
> * 使用 X.509 憑證保護叢集
> * 使用 PowerShell 連線到叢集
> * 刪除叢集

接下來，請前進到下列教學課程，了解如何使用 Service Fabric 部署 API 管理。
> [!div class="nextstepaction"]
> [部署 API 管理](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
