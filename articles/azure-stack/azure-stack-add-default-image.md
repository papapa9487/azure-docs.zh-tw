---
title: "將預設 VM 映像新增到 Azure Stack Marketplace | Microsoft Docs"
description: "將 Windows Server 2016 VM 預設映像新增到 Azure Stack Marketplace。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2849E53F-3D58-48A5-8007-3238FC39F630
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: mabrigg
ms.openlocfilehash: ed62f2f8441220eb37aea7f4c848702e9821698b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>將 Windows Server 2016 VM 映像新增到 Azure Stack Marketplace

根據預設，Azure Stack Marketplace 中沒有提供任何虛擬機器映像。 Azure Stack 操作員必須先將映像新增到 Marketplace，以便使用者存取。 您可以使用下列其中一種方法，將 Windows Server 2016 映像新增到 Azure Stack Marketplace：

* [從 Azure Marketplace 下載映像](#add-the-image-by-downloading-it-from-the-azure-marketplace)。 如果您是在已連線的情況下進行操作，並且已向 Azure 註冊 Azure Stack 執行個體，請使用此選項。

* [使用 PowerShell 來新增映像](#add-the-image-by-using-powershell)。 如果您是在中斷連線或連線能力有限的情況下部署 Azure Stack，請使用此選項。

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>從 Azure Marketplace 下載映像來新增映像

1. 部署 Azure Stack，然後登入您的 Azure Stack 開發套件。

2. 按一下 [更多服務] > [Marketplace 管理] > [從 Azure 新增]。 

3. 尋找或搜尋 [Windows Server 2016 Datacenter – 評估版] 映像，然後選取 [下載]。

   ![從 Azure 下載映像](media/azure-stack-add-default-image/download-image.png)

下載完成時，即可在 [Marketplace 管理] 之下使用映像。 此映像也可以在 [虛擬機器] 之下使用。

## <a name="add-the-image-by-using-powershell"></a>使用 PowerShell 來新增映像

### <a name="prerequisites"></a>必要條件 

從[開發套件](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或從 Windows 型外部用戶端 (如果您是[透過 VPN 連線](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn))，執行下列先決條件：

1. 安裝 [Azure Stack 相容的 Azure PowerShell 模組](azure-stack-powershell-install.md)。  

2. 下載[處理 Azure Stack 所需的工具](azure-stack-powershell-download.md)。  

3. 在 Windows Server 評估版頁面上，[下載 Windows Server 2016 評估版](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016)。 出現提示時，選取下載項的 ISO 版本。 記錄下載位置的路徑，該路徑稍後會使用於本文描述的這些步驟中。 此步驟需要網際網路連線能力。  

### <a name="add-the-image-to-the-azure-stack-marketplace"></a>將映像新增至 Azure Stack Marketplace
   
1. 使用下列命令來匯入 Azure Stack 的 Connect 和 ComputeAdmin 模組：

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
     -EnvironmentName "AzureStackAdmin" 

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```
   
3. 將 Windows Server 2016 映像新增到 Azure Stack Marketplace。 (以您下載之 Windows Server 2016 ISO 的路徑取代 *fully_qualified_path_to_ISO*。)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

若要確保 Windows Server 2016 VM 映像具有最新的累積更新，請在執行 `New-AzsServer2016VMImage` Cmdlet 時包含 `IncludeLatestCU` 參數。 如需有關 `New-AzsServer2016VMImage` Cmdlet 所允許參數的資訊，請參閱[參數](#parameters)。 將映像發佈到 Azure Stack Marketplace 需要大約一小時的時間。 

## <a name="parameters"></a>參數

|New-AzsServer2016VMImage 參數|必要？|說明|
|-----|-----|------|
|ISOPath|是|所下載 Windows Server 2016 ISO 的完整路徑。|
|Net35|否|在 Windows Server 2016 映像上安裝 .NET 3.5 執行階段。 此值預設會設定為 **true**。|
|版本|否|指定 [核心]、[完整]，或 [兩者] 的 Windows Server 2016 映像。 此值預設會設定為 **Full**。|
|VHDSizeInMB|否|設定要新增到您 Azure Stack 環境之 VHD 映像的大小 (單位為 MB)。 此值預設會設定為 40,960 MB。|
|CreateGalleryItem|否|指定是否應該為 Windows Server 2016 映像建立 Marketplace 項目。 此值預設會設定為 **true**。|
|location |否 |指定應作為 Windows Server 2016 映像發行目的地的位置。|
|IncludeLatestCU|否|將最新的 Windows Server 2016 累積更新套用到新的 VHD。|
|CUUri |否 |設定從特定的 URI 執行 Windows Server 2016 累積更新。 |
|CUPath |否 |設定從本機路徑執行 Windows Server 2016 累積更新。 如果您是在已中斷連線的環境中部署 Azure Stack 執行個體，此選項會相當有用。|

## <a name="next-steps"></a>後續步驟

[佈建虛擬機器](azure-stack-provision-vm.md)
