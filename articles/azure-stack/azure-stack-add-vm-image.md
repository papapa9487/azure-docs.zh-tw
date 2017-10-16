---
title: "將 VM 映像新增到 Azure Stack | Microsoft Docs"
description: "新增您組織的自訂 Windows 或 Linux VM 映像以供租用戶使用"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: de8540397b63093457382cf427a65ea0e48b93e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>在 Azure Stack 中提供自訂虛擬機器映像

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

Azure Stack 可讓操作員將自訂虛擬機器映像提供給其使用者使用。 這些映像可供 Azure Resource Manager 範本參考，或藉由建立 Marketplace 項目新增到 Azure Marketplace UI。 

## <a name="add-a-vm-image-to-marketplace-with-powershell"></a>使用 PowerShell 將 VM 映像新增到市集

從[開發套件](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或從 Windows 型外部用戶端 (如果您是[透過 VPN 連線](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn))，執行下列先決條件

* [安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。  

* 下載[與 Azure Stack 搭配運作所需的工具](azure-stack-powershell-download.md)。  

* 準備 VHD 格式 (非 VHDX) 的 Windows 或 Linux 作業系統虛擬硬碟映像。
   
   * 針對 Windows 映像，[將 Windows VM 映像上傳至 Azure 供 Resource Manager 部署使用](../virtual-machines/windows/upload-generalized-managed.md)一文的**準備 VHD 以供上傳**一節中包含了映像準備指示。
   * 針對 Linux 映像，請依照[在 Azure Stack 上部署 Linux 虛擬機器](azure-stack-linux.md)一文所述的步驟，準備映像或使用現有的 Azure Stack Linux 映像。  

現在，執行下列步驟以將映像新增到 Azure Stack 市集：

1. 匯入 Connect 和 ComputeAdmin 模組：
   
   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # import the Connect and ComputeAdmin modules
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ``` 

2. 登入您的 Azure Stack 環境。 根據您的 Azure Stack 環境是使用 AAD 還是 AD FS 部署而定，執行下列指令碼 (務必按照您的環境設定來取代 AAD tenantName、GraphAudience 端點和 ArmEndpoint 值)： 

   a. **Azure Active Directory** - 使用下列 Cmdlet：

   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"

   #Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint $ArmEndpoint 

   Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience

   $TenantID = Get-AzsDirectoryTenantId `
     -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
     -EnvironmentName AzureStackAdmin

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```

   b. **Active Directory 同盟服務** - 使用下列 Cmdlet：
    
   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"

   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint $ArmEndpoint

   Set-AzureRmEnvironment `
     -Name "AzureStackAdmin" `
     -GraphAudience $GraphAudience `
     -EnableAdfsAuthentication:$true

   $TenantID = Get-AzsDirectoryTenantId `
     -ADFS 
     -EnvironmentName AzureStackAdmin 

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```
    
3. 叫用 `Add-AzsVMImage` Cmdlet 來新增 VM 映像。 在 Add-AzsVMImage Cmdlet 中，將 osType 指定為 Windows 或 Linux。 請包含 VM 映像的發行者、供應項目、SKU 及版本。 如需有關所允許參數的資訊，請參閱[參數](#parameters)一節。 Azure Resource Manager 範本會使用這些參數來參考 VM 映像。 以下是此指令碼的範例引動過程：
     
     ```powershell
     Add-AzsVMImage `
       -publisher "Canonical" `
       -offer "UbuntuServer" `
       -sku "14.04.3-LTS" `
       -version "1.0.0" `
       -osType Linux `
       -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
     ```

命令會執行下列動作：

* 向 Azure Stack 環境進行驗證
* 將本機 VHD 上傳到新建立的臨時儲存體帳戶
* 將 VM 映像新增到 VM 映像存放庫並
* 建立 Marketplace 項目

若要確認命令執行成功，請在入口網站中移至 [Marketplace]，然後確認 [虛擬機器] 類別中有該 VM 映像。

![已成功新增 VM 映像](./media/azure-stack-add-vm-image/image5.PNG) 

## <a name="remove-a-vm-image-with-powershell"></a>使用 PowerShell 來移除 VM 映像

當您已不再需要先前上傳的虛擬機器映像時，您可以使用下列 Cmdlet 將它從市集中刪除：

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>參數

| 參數 | 說明 |
| --- | --- |
| **publisher** |部署映像時，使用者所使用 VM 映像的發行者名稱區段。 例如 ‘Microsoft’。 請勿在此欄位中包含空格或其他特殊字元。 |
| **offer** |部署 VM 映像時，使用者所使用 VM 映像的供應項目名稱區段。 例如 ‘WindowsServer’。 請勿在此欄位中包含空格或其他特殊字元。 |
| **sku** |部署 VM 映像時，使用者所使用 VM 映像的 SKU 名稱區段。 例如 ‘Datacenter2016’。 請勿在此欄位中包含空格或其他特殊字元。 |
| **version** |部署 VM 映像時，使用者所使用 VM 映像的版本。 版本的格式為 *\#.\#.\#*。 例如 ‘1.0.0’。 請勿在此欄位中包含空格或其他特殊字元。 |
| **osType** |映像的 osType 必須是 ‘Windows’ 或 ‘Linux’。 |
| **osDiskLocalPath** |您以 VM 映像形式上傳到 Azure Stack 之 OS 磁碟 VHD 的本機路徑。 |
| **dataDiskLocalPaths** |可以與 VM 映像一起上傳之資料磁碟的本機路徑陣列 (選擇性)。 |
| **CreateGalleryItem** |可決定是否要在 Marketplace 中建立項目的布林值旗標。 預設會設定為 true。 |
| **title** |Marketplace 項目的顯示名稱。 預設會設定為 VM 映像的 Publisher-Offer-Sku。 |
| **description** |Marketplace 項目的描述。 |
| **位置** |應作為 VM 映像發行目的地的位置。 此值預設會設定為 local。|
| **osDiskBlobURI** |此指令碼也可視需要接受 osDisk 的 Blob 儲存體 URI。 |
| **dataDiskBlobURIs** |此指令碼也可視需要接受 Blob 儲存體 URI 陣列，以將資料磁碟新增到映像。 |

## <a name="add-a-vm-image-through-the-portal"></a>透過入口網站新增 VM 映像

> [!NOTE]
> 此方法需要個別建立 Marketplace 項目。

對映像的其中一個需求就是必須可藉由 Blob 儲存體 URI 參考它們。 準備 VHD 格式 (非 VHDX) 的 Windows 或 Linux 作業系統映像，然後將該映像上傳到 Azure 或 Azure Stack 中的儲存體帳戶。 如果您的映像已經上傳到 Azure 或 Azure Stack 中的 Blob 儲存體，則您可以略過步驟 1。

1. [將 Windows VM 映像上傳至 Azure 供 Resource Manager 部署使用](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)，或如果是 Linux 映像，則依照[在 Azure Stack 上部署 Linux 虛擬機器](azure-stack-linux.md)一文所述的指示操作。 上傳映像之前，您應該先了解下列考量：

   * 將映像上傳到 Azure Stack Blob 儲存體比上傳到 Azure Blob 儲存體有效率，因為將映像推送到 Azure Stack 映像存放庫所需的時間較短。 
   
   * 上傳 [Windows VM 映像](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)時，請務必以[設定 Azure Stack 操作員的 PowerShell 環境](azure-stack-powershell-configure-admin.md)步驟取代「登入 Azure」步驟。  

   * 記下您上傳映像的 Blob 儲存體 URI，其格式如下：*&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd

   * 若要讓 Blob 可供匿名存取，請移至將 VM 映像 VHD 上傳到 **Blob** 時的儲存體帳戶 Blob 容器，然後選取 [存取原則]。 如果您想要的話，也可以改為產生容器的共用存取簽章，然後將它包含在 Blob URI 中。

   ![瀏覽至儲存體帳戶 Blob](./media/azure-stack-add-vm-image/image1.png)

   ![將 Blob 存取權設定為公用](./media/azure-stack-add-vm-image/image2.png)

2. 以操作員身分登入 Azure Stack，從功能表中按一下 [更多服務] > [資源提供者] > 選取 [計算] > [VM 映像] > [新增]

3. 在 [新增 VM 映像] 刀鋒視窗上，輸入虛擬機器映像的發行者、供應項目、SKU 及版本。 這些名稱區段指的是 Resource Manager 範本中的 VM 映像。 請務必正確選取 **osType**。 針對 [OD 磁碟 Blob URI]，請輸入上傳映像的 Blob URI，然後按一下 [建立] 來開始建立 VM 映像。
   
   ![開始建立映像](./media/azure-stack-add-vm-image/image4.png)

   成功建立映像時，VM 映像狀態會變更為「成功」。

4. 若要更容易在 UI 中將虛擬機器映像提供給使用者取用，最好是[建立 Marketplace 項目](azure-stack-create-and-publish-marketplace-item.md)。

## <a name="next-steps"></a>後續步驟

[佈建虛擬機器](azure-stack-provision-vm.md)