---
title: "安裝 Azure Stack 的 PowerShell | Microsoft Docs"
description: "瞭解如何安裝 Azure Stack 的 PowerShell。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 5555ee022e74b5d4791c5826ef80b166c4990a61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="install-powershell-for-azure-stack"></a>安裝適用於 Azure Stack 的 PowerShell  

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

您需要與 Azure Stack 相容的 Azure PowerShell 模組，才能搭配 Azure Stack 使用。 在本指南中，我們引導您安裝 Azure Stack 的 PowerShell 所需要的步驟。 您可以從 Azure Stack 開發套件，或從以 Windows 為基礎的外部用戶端 (如果您透過 VPN 連線) 來使用這篇文章中所述的步驟。

這篇文章有安裝 Azure Stack 的 PowerShell 的詳細指示。 不過，如果您想要快速安裝和設定 PowerShell，您可以使用[透過 PowerShell 執行](azure-stack-powershell-configure-quickstart.md)主題所提供的指令碼。 

> [!NOTE]
> 下列步驟需要 PowerShell 5.0。 若要檢查您的版本，請執行 $PSVersionTable.PSVersion 並比較「主要」版本。

Azure Stack 的 PowerShell 命令是透過 PowerShell 程式庫進行安裝。 若要註冊 PSGallery 存放庫，請從開發套件，或從以 Windows 為基礎的外部用戶端 (如果是透過 VPN 連線) 開啟提升權限的 PowerShell 工作階段，然後執行下列命令：

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>解除安裝現有的 PowerShell 版本

在安裝必要版本之前，請確定解除安裝任何現有的 Azure PowerShell 模組。 您可以使用下列兩種方法之一來解除安裝它們：

* 若要解除安裝現有的 PowerShell 模組，請登入至開發套件，或以 Windows 為基礎的外部用戶端 (如果您打算建立 VPN 連線)。 關閉所有作用中的 PowerShell 工作階段，然後執行下列命令： 

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* 登入至開發套件，或以 Windows 為基礎的外部用戶端 (如果您打算建立 VPN 連線)。 從 `C:\Program Files (x86)\WindowsPowerShell\Modules` 和 `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` 資料夾刪除以「Azure」開頭的所有資料夾。 刪除這些資料夾會從「AzureStackAdmin」和「global」使用者領域中移除任何現有的 PowerShell 模組。 

下列各節說明安裝 Azure Stack 的 PowerShell 的必要步驟。 可在連線、部分連線或中斷連線案例中，於 Azure Stack 上安裝 PowerShell。 

## <a name="install-powershell-in-a-connected-scenario"></a>在連線案例中安裝 PowerShell 

透過 API 版本設定檔安裝 Azure Stack 相容的 AzureRM 模組。 Azure Stack 需要 **2017-03-09-profile** API 版本設定檔 (可藉由安裝 AzureRM.Bootstrapper 模組取得)。 若要深入了解 API 版本設定檔和其所提供的 cmdlet，請參閱[管理 API 版本設定檔](azure-stack-version-profiles.md)。 除了 AzureRM 模組外，您也應該安裝 Azure Stack 特有的 Azure PowerShell 模組。 執行下列 PowerShell 指令碼，在開發工作站上安裝這些模組：

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.10
  ```

若要確認安裝，請執行下列命令：

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```
  如果安裝成功，輸出中會顯示 AzureRM 和 AzureStack 模組。

## <a name="install-powershell-in-a-disconnected-or-in-a-partially-connected-scenario"></a>在已中斷連線或部分連線案例中安裝 PowerShell

在中斷連線的案例中，您必須先將 PowerShell 模組下載至具有網際網路連線的電腦，然後將其傳送至 Azure Stack 開發套件進行安裝。

1. 登入具有網際網路連線的電腦並使用下列指令碼，將 AzureRM 及 AzureStack 套件下載到您的本機電腦：

   ```powershell
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.10

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.10 
   ```

2. 將下載的套件複製到 USB 裝置。

3. 登入開發套件，並將套件從 USB 裝置複製到開發套件上的位置。 

4. 現在您必須註冊此位置作為預設存放庫，並從此存放庫安裝 AzureRM 與 AzureStack 模組：

   ```powershell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   ```powershell
   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="next-steps"></a>後續步驟

* [從 GitHub 下載 Azure Stack 工具](azure-stack-powershell-download.md)
* [設定 Azure Stack 使用者的 PowerShell 環境](user/azure-stack-powershell-configure-user.md)  
* [設定 Azure Stack 操作員的 PowerShell 環境](azure-stack-powershell-configure-admin.md) 
* [在 Azure Stack 中管理 API 版本設定檔](azure-stack-version-profiles.md)  
