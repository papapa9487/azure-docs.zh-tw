---
title: "將 VM 映像新增到 Azure Stack | Microsoft Docs"
description: "新增您組織的自訂 Windows 或 Linux VM 映像以供租用戶使用。"
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
ms.openlocfilehash: b54bb67e1188037385d0b373850b6b923eb8d7ac
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>在 Azure Stack 中提供自訂虛擬機器映像

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

在 Azure Stack 中，操作員可以自訂虛擬機器映像提供給其使用者使用。 這些映像可供 Azure Resource Manager 範本參考，或者您可以將其新增到 Azure Marketplace UI 作為 Marketplace 項目。 

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>使用 PowerShell 將 VM 映像新增到 Marketplace

從[開發套件](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或從 Windows 型外部用戶端 (如果您是[透過 VPN 連線](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn))，執行下列先決條件：

1. [安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。  

2. 下載[處理 Azure Stack 所需的工具](azure-stack-powershell-download.md)。  

3. 準備 VHD 格式 (不使用 VHDX 格式) 的 Windows 或 Linux 作業系統虛擬硬碟映像。
   
   * 對於 Windows 映像，如需準備映像的指示，請參閱[將 Windows VM 映像上傳至 Azure 供 Resource Manager 部署使用](../virtual-machines/windows/upload-generalized-managed.md)。
   * 對於 Linux 映像，請參閱[在 Azure Stack 上部署 Linux 虛擬機器](azure-stack-linux.md)。 完成本文所述之準備映像或使用現有 Azure Stack Linux 映像的步驟。  

若要將映像新增到 Azure Stack Marketplace，請完成下列步驟：

1. 匯入 Connect 和 ComputeAdmin 模組：
   
   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ``` 

2. 登入您的 Azure Stack 環境。 執行下列其中一個指令碼，取決於您是使用 Azure Active Directory (Azure AD) 或 Active Directory 同盟服務 (AD FS) 來部署您的 Azure Stack 環境。 (取代 Azure AD `tenantName`、`GraphAudience` 端點，以及 `ArmEndpoint` 值，以反映您的環境設定。)

    * **Azure Active Directory**。 使用下列 Cmdlet：

      ```PowerShell
      # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "<Resource Manager endpoint for your environment>"

      # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "<GraphAuidence endpoint for your environment>"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
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

   * **Active Directory 同盟服務**。 使用下列 Cmdlet：
    
        ```PowerShell
        # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
        $ArmEndpoint = "<Resource Manager endpoint for your environment>"

        # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
        $GraphAudience = "<GraphAuidence endpoint for your environment>"

        # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
        Add-AzureRMEnvironment `
          -Name "AzureStackAdmin" `
          -ArmEndpoint $ArmEndpoint

        Set-AzureRmEnvironment `
          -Name "AzureStackAdmin" `
          -GraphAudience $GraphAudience `
          -EnableAdfsAuthentication:$true

        $TenantID = Get-AzsDirectoryTenantId `
          -ADFS `
          -EnvironmentName AzureStackAdmin 

        Login-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID 
        ```
    
3. 叫用 `Add-AzsVMImage` Cmdlet 來新增 VM 映像。 在 `Add-AzsVMImage` Cmdlet 中，將 `osType` 指定為 Windows 或 Linux。 請包含 VM 映像的發行者、供應項目、SKU 及版本。 如需有關所允許參數的詳細資訊，請參閱[參數](#parameters)。 Azure Resource Manager 範本會使用參數來參考 VM 映像。 下列範例會叫用指令碼：
     
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

* 向 Azure Stack 環境進行驗證。
* 將本機 VHD 上傳到新建立的臨時儲存體帳戶。
* 將 VM 映像新增到 VM 映像存放庫。
* 建立 Marketplace 項目。

若要確認命令執行成功，在入口網站中移至 Marketplace。 請確認 VM 映像在 [虛擬機器] 類別目錄中可供使用。

![已成功新增 VM 映像](./media/azure-stack-add-vm-image/image5.PNG) 

## <a name="remove-a-vm-image-by-using-powershell"></a>使用 PowerShell 來移除 VM 映像

當您已不再需要上傳的虛擬機器映像時，您可以使用下列 Cmdlet 將它從 Marketplace 中刪除：

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
| **publisher** |部署映像時，使用者所使用 VM 映像的發行者名稱區段。 例如 **Microsoft**。 請勿在此欄位中包含空格或其他特殊字元。 |
| **offer** |部署 VM 映像時，使用者所使用 VM 映像的供應項目名稱區段。 例如 **WindowsServer**。 請勿在此欄位中包含空格或其他特殊字元。 |
| **sku** |部署 VM 映像時，使用者所使用 VM 映像的 SKU 名稱區段。 例如 **Datacenter2016**。 請勿在此欄位中包含空格或其他特殊字元。 |
| **version** |部署 VM 映像時，使用者所使用 VM 映像的版本。 版本的格式為 *\#.\#.\#*。 例如 **1.0.0**。 請勿在此欄位中包含空格或其他特殊字元。 |
| **osType** |映像的 osType 必須是 **Windows** 或 **Linux**。 |
| **osDiskLocalPath** |您以 VM 映像形式上傳到 Azure Stack 之 OS 磁碟 VHD 的本機路徑。 |
| **dataDiskLocalPaths** |可以與 VM 映像一起上傳之資料磁碟的本機路徑陣列 (選擇性)。 |
| **CreateGalleryItem** |可決定是否要在 Marketplace 中建立項目的布林值旗標。 預設會設定為 **true**。 |
| **title** |Marketplace 項目的顯示名稱。 預設會設定為 VM 映像的 `Publisher-Offer-Sku` 值。 |
| **description** |Marketplace 項目的描述。 |
| **位置** |應作為 VM 映像發佈目的地的位置。 此值預設會設定為 **local**。|
| **osDiskBlobURI** |(選擇性) 此指令碼也可接受 `osDisk` 的 Blob 儲存體 URI。 |
| **dataDiskBlobURIs** |(選擇性) 此指令碼也可接受 Blob 儲存體 URI 陣列，以將資料磁碟新增到映像。 |

## <a name="add-a-vm-image-through-the-portal"></a>透過入口網站新增 VM 映像

> [!NOTE]
> 使用此方法時，您必須個別建立 Marketplace 項目。

映像必須能夠由 Blob 儲存體 URI 參考。 準備 VHD 格式 (非 VHDX) 的 Windows 或 Linux 作業系統映像，然後將該映像上傳到 Azure 或 Azure Stack 中的儲存體帳戶。 如果您的映像已經上傳到 Azure 或 Azure Stack 中的 Blob 儲存體，則您可以略過步驟 1。

1. [將 Windows VM 映像上傳至 Azure 供 Resource Manager 部署使用](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)，或如果是 Linux 映像，則依照[在 Azure Stack 上部署 Linux 虛擬機器](azure-stack-linux.md)所述的指示操作。 您上傳映像之前，務必考慮下列因素：

   * 將映像上傳到 Azure Stack Blob 儲存體比上傳到 Azure Blob 儲存體有效率，因為將映像推送到 Azure Stack 映像存放庫所需的時間較短。 
   
   * 上傳 [Windows VM 映像](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)時，請務必以[設定 Azure Stack 操作員的 PowerShell 環境](azure-stack-powershell-configure-admin.md)步驟取代「登入 Azure」步驟。  

   * 記下您上傳映像的 Blob 儲存體 URI。 Blob 儲存體 URI 的格式如下：*&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd。

   * 若要讓 Blob 可供匿名存取，請移至將 VM 映像 VHD 上傳時的儲存體帳戶 Blob 容器。 選取 [Blob]，然後選取 [存取原則]。 您可以選擇性地改為產生容器的共用存取簽章，然後將它包含在 Blob URI 中。

   ![移至儲存體帳戶 Blob](./media/azure-stack-add-vm-image/image1.png)

   ![將 Blob 存取權設定為公用](./media/azure-stack-add-vm-image/image2.png)

2. 以操作員身分登入 Azure Stack。 在功能表中，選取 [更多服務] > [資源提供者]。 然後，選取 [計算] > [VM 映像] > [新增]。

3. 在 [新增 VM 映像] 底下，輸入虛擬機器映像的發行者、供應項目、SKU 及版本。 這些名稱區段指的是 Resource Manager 範本中的 VM 映像。 請務必正確選取 **osType** 值。 針對 [OS 磁碟 Blob URI]，輸入上傳映像所在的 Blob URI。 然後，選取 [建立] 以開始建立 VM 映像。
   
   ![開始建立映像](./media/azure-stack-add-vm-image/image4.png)

   成功建立映像時，VM 映像狀態會變更為「成功」。

4. 若要更容易在 UI 中將虛擬機器映像提供給使用者取用，最好是[建立 Marketplace 項目](azure-stack-create-and-publish-marketplace-item.md)。

## <a name="next-steps"></a>後續步驟

[佈建虛擬機器](azure-stack-provision-vm.md)
