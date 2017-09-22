---
title: "將預設 VM 映像新增到 Azure Stack 市集 | Microsoft Docs"
description: "將 Windows Server 2016 VM 預設映像新增到 Azure Stack 市集。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: sngun
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 2cbdca8c795346864b6e39e42858f3dc46def199
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>將 Windows Server 2016 VM 映像新增到 Azure Stack 市集

Azure Stack 市集中預設沒有提供任何虛擬機器映像。 Azure Stack 操作員必須先將映像新增到市集，使用者才能夠使用映像。 您可以使用下列兩種方法其中之一，將 Windows Server 2016 映像新增到 Azure Stack 市集：

* [從 Azure Marketplace 下載映像來新增映像](#add-the-image-by-downloading-it-from-the-Azure-marketplace)：如果您是在已連線的情況下進行操作，並且已向 Azure 註冊 Azure Stack 執行個體，請使用此選項。

* [使用 PowerShell 來新增映像](#add-the-image-by-using-powershell)：如果您是在中斷連線或連線能力有限的情況下部署 Azure Stack，請使用此選項。

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>從 Azure Marketplace 下載映像來新增映像

1. 部署 Azure Stack 之後，登入您的「Azure Stack 開發套件」。

2. 按一下 [更多服務] > [市集管理] > [從 Azure 新增] 

3. 尋找或搜尋 [Windows Server 2016 Datacenter – 評估版] 映像 > 按一下 [下載]

   ![從 Azure 下載映像](media/azure-stack-add-default-image/download-image.png)

下載完成之後，映像會被新增到 [市集管理] 刀鋒視窗，而且從 [虛擬機器] 刀鋒視窗也可以使用此映像。

## <a name="add-the-image-by-using-powershell"></a>使用 PowerShell 來新增映像

### <a name="prerequisites"></a>先決條件 

從[開發套件](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或從 Windows 型外部用戶端 (如果您是[透過 VPN 連線](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn))，執行下列先決條件：

* 安裝 [Azure Stack 相容的 Azure PowerShell 模組](azure-stack-powershell-install.md)。  

* 下載[與 Azure Stack 搭配運作所需的工具](azure-stack-powershell-download.md)。  

* 移至 https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016 並下載 Windows Server 2016 評估版。 出現提示時，選取下載項的 **ISO** 版本。 記錄下載位置的路徑，稍後在這些步驟中將會用到。 此步驟需要網際網路連線能力。  

現在，執行下列步驟以將映像新增到 Azure Stack 市集：
   
1. 使用下列命令來匯入 Azure Stack 的 Connect 和 ComputeAdmin 模組：

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # import the Connect and ComputeAdmin modules   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. 登入您的 Azure Stack 環境。 根據部署您 Azure Stack 環境時使用的是 AAD 還是 AD FS (請務必取代 AAD 租用戶名稱) 而定，執行下列指令碼：  

   a. **Azure Active Directory** - 使用下列 Cmdlet：

   ```PowerShell
   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint "https://adminmanagement.local.azurestack.external" 

   Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience "https://graph.windows.net/"

   $TenantID = Get-AzsDirectoryTenantId `
     -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
     -EnvironmentName AzureStackAdmin

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```

   b. **Active Directory 同盟服務** - 使用下列 Cmdlet：
    
   ```PowerShell
   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint "https://adminmanagement.local.azurestack.external"

   Set-AzureRmEnvironment `
     -Name "AzureStackAdmin" `
     -GraphAudience "https://graph.local.azurestack.external/" `
     -EnableAdfsAuthentication:$true

   $TenantID = Get-AzsDirectoryTenantId `
     -ADFS 
     -EnvironmentName AzureStackAdmin 

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```
   
3. 將 Windows Server 2016 映像新增到 Azure Stack 市集 (請務必以您所下載 WS2016 ISO 的路徑取代 *Path_to_ISO*)：

   ```PowerShell
   $ISOPath = "<Fully_Qualified_Path_to_ISO>"

   # Add a Windows Server 2016 Evaluation VM Image.
   New-AzsServer2016VMImage `
     -ISOPath $ISOPath

   ```

若要確保 Windows Server 2016 VM 映像具有最新的累積更新，請在執行 `New-AzsServer2016VMImage` Cmdlet 時包含 `IncludeLatestCU` 參數。 如需有關 `New-AzsServer2016VMImage` Cmdlet 所允許參數的資訊，請參閱[參數](#parameters)一節。 將映像發行到 Azure Stack 市集需要大約一小時的時間。 

## <a name="parameters"></a>參數

|New-AzsServer2016VMImage 參數|必要？|描述|
|-----|-----|------|
|ISOPath|是|所下載 Windows Server 2016 ISO 的完整路徑。|
|Net35|否|此參數可讓您在 Windows Server 2016 映像上安裝 .NET 3.5 執行階段。 此值預設會設定為 true。 映像必須包含 .NET 3.5 執行階段，才能安裝 SQL 和 MYSQL 資源提供者。 |
|Version|否|此參數可讓您選擇是要新增 **Core** (核心)、**Full** (完整) Windows Server 2016 映像，還是 **Both** (兩者都新增)。 此值預設會設定為 "Full"。|
|VHDSizeInMB|否|設定要新增到您 Azure Stack 環境之 VHD 映像的大小 (單位為 MB)。 此值預設會設定為 40960 MB。|
|CreateGalleryItem|否|指定是否應該為 Windows Server 2016 映像建立 Marketplace 項目。 此值預設會設定為 true。|
|location |否 |指定應作為 Windows Server 2016 映像發行目的地的位置。|
|IncludeLatestCU|否|若要將最新的 Windows Server 2016 累積更新套用到新 VHD，請設定此參數。|
|CUUri |否 |若要從特定的 URI 選擇 Windows Server 2016 累積更新，請設定此值。 |
|CUPath |否 |若要從本機路徑選擇 Windows Server 2016 累積更新，請設定此值。 如果您是在已中斷連線的環境中部署 Azure Stack 執行個體，此選項會相當有用。|

## <a name="next-steps"></a>後續步驟

[佈建虛擬機器](azure-stack-provision-vm.md)

