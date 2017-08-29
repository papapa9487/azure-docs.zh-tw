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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/13/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 80c5a2a43302e1cc8ec3b4298eb393a1861252d3
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---

# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>使用 PowerShell 在 Azure 上建立安全叢集
本教學課程會示範如何建立在 Azure 中執行的 Service Fabric 叢集 (Windows 或 Linux)。 完成時，您會有在您可以部署應用程式的雲端中執行的叢集。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用 PowerShell 在 Azure 中建立安全的 Service Fabric 叢集
> * 使用 X.509 憑證保護叢集
> * 使用 PowerShell 連線到叢集
> * 刪除叢集

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前：
- 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 安裝 [Service Fabric SDK 和 PowerShell 模組](service-fabric-get-started.md)
- 安裝 [Azure PowerShell 模組 4.1 版或更新版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

下列程序會建立單一節點 (單一虛擬機器) 的預覽 Service Fabric 叢集。 此叢集會由隨著叢集一起建立並放置在金鑰保存庫中的自我簽署憑證所保護。 調整單一節點叢集的規模時，不可以超過一部虛擬機器，而且預覽叢集無法升級至較新的版本。

若要計算在 Azure 中執行 Service Fabric 叢集產生的成本，請使用 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)。
如需建立 Service Fabric 叢集的詳細資訊，請參閱[使用 Azure Resource Manager 來建立 Service Fabric 叢集](service-fabric-cluster-creation-via-arm.md)。

## <a name="create-the-cluster-using-azure-powershell"></a>使用 Azure PowerShell 建立叢集
1. 從 [適用於 Service Fabric 的 Azure Resource Manager 範本](https://aka.ms/securepreviewonelineclustertemplate) GitHub 存放庫，下載 Azure Resource Manager 範本和參數檔案的本機複本。  *azuredeploy.json* 是可定義 Service Fabric 叢集的 Azure Resource Manager 範本。 *azuredeploy.parameters.json* 是可供您自訂叢集部署的參數檔。

2. 自訂 *azuredeploy.parameters.json* 參數檔中的下列參數：

   | 參數       | 說明 | 建議的值 |
   | --------------- | ----------- | --------------- |
   | clusterLocation | 要作為叢集部署目的地的 Azure 區域。 | *例如 westeurope、eastasia、eastus* |
   | clusterName     | 您想要建立之叢集的名稱。 | *例如 bobs-sfpreviewcluster* |
   | adminUserName   | 叢集虛擬機器上的本機管理帳戶。 | *任何有效的 Windows Server 使用者名稱* |
   | adminPassword   | 叢集虛擬機器上本機管理帳戶的密碼。 | *任何有效的 Windows Server 密碼* |
   | clusterCodeVersion | 要執行的 Service Fabric 版本。 (255.255.X.255 是預覽版本)。 | **255.255.5718.255** |
   | vmInstanceCount | 叢集中的虛擬機器數目 (可以是 1 或 3-99)。 | **1** | *對於預覽叢集，請僅指定一部虛擬機器* |

3. 開啟 PowerShell 主控台，登入 Azure，並選取想要在叢集中部署的訂用帳戶：

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```
4. 針對要供 Service Fabric 使用的憑證建立密碼並予以加密。

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```
5. 執行下列命令來建立叢集和其憑證：

   ```powershell
      New-AzureRmServiceFabricCluster `
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >`-CertificateSubjectName` 參數應該配合參數檔中指定的 clusterName 參數，以及繫結至您所選擇 Azure 區域的網域，例如：`clustername.eastus.cloudapp.azure.com`。

在設定完成後，它會輸出 Azure 中所建立之叢集的相關資訊。 它也會將叢集憑證複製到您為這個參數所指定之路徑上的 -CertificateOutputFolder 目錄。 您需要有此憑證才能存取 Service Fabric Explorer 並檢視叢集的健康情況。

記下叢集的 URL，此 URL 可能會類似下列 URL：*https://mycluster.westeurope.cloudapp.azure.com:19080*

## <a name="modify-the-certificate--access-service-fabric-explorer"></a>修改憑證和存取 Service Fabric Explorer ##

1. 按兩下憑證，即可開啟 [憑證匯入精靈]。

2. 請使用預設設定，但務必勾選 [將此金鑰標記為可匯出] 核取方塊 (在「私密金鑰保護」步驟中)。 設定 Azure Container Registry 時，Visual Studio 必須匯出憑證，以進行本教學課程稍後的 Service Fabric 叢集驗證。

3. 現在，您可以在瀏覽器中開啟 Service Fabric Explorer。 若要這樣做，請使用網頁瀏覽器導覽至叢集的 **ManagementEndpoint** URL，然後選取您的機器上儲存的憑證。

>[!NOTE]
>由於您使用自我簽署的憑證，因此開啟 Service Fabric Explorer 時會顯示憑證錯誤。 在 Edge 中，您必須依序按一下 [詳細資料] 與 [繼續瀏覽網頁] 連結。 在 Chrome 中，您必須依序按一下 [進階] 與 [繼續] 連結。

>[!NOTE]
>如果叢集建立失敗，您隨時可以重新執行命令，藉此更新已部署的資源。 如果憑證是與失敗的部署一起建立，則會產生一個新憑證。 若要對叢集建立問題進行疑難排解，請參閱[使用 Azure Resource Manager 來建立 Service Fabric 叢集](service-fabric-cluster-creation-via-arm.md)。

## <a name="connect-to-the-secure-cluster"></a>連線到安全的叢集
使用隨 Service Fabric SDK 一起安裝的 Service Fabric PowerShell 模組連線到叢集。  首先，將憑證安裝到您的電腦上目前使用者的個人 (My) 存放區。  執行下列 PowerShell 命令：

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
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

叢集是由叢集資源本身和其他 Azure 資源所構成。 刪除叢集及其取用之所有資源的最簡單方式，就是刪除資源群組。

登入 Azure 並選取您要移除叢集的訂用帳戶識別碼。  您可以登入[Azure 入口網站](http://portal.azure.com)找到您的訂用帳戶識別碼。 使用 [Remove-AzureRMResourceGroup Cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) 刪除資源群組和所有叢集資源。

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 Service Fabric 叢集
> * 使用 X.509 憑證保護叢集
> * 使用 PowerShell 連線到叢集
> * 刪除叢集

接下來，前進到下列的教學課程，了解如何部署現有的應用程式。
> [!div class="nextstepaction"]
> [透過 Docker Compose 部署現有的 .NET 應用程式](service-fabric-host-app-in-a-container.md)

