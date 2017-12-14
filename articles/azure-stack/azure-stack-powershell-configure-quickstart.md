---
title: "安裝並設定 Azure Stack 的 PowerShell 快速入門 | Microsoft Docs"
description: "深入瞭解 Azure Stack 的 PowerShell 的安裝和設定。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: mabrigg
ms.openlocfilehash: 0a85fc78d31c58ee83721d7dccbcdf46f98b3ddd
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>在 Azure Stack 使用 PowerShell 啟動和執行

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

本快速入門可協助您使用 PowerShell 來安裝和設定 Azure Stack 環境。 本文中提供的指令碼僅適用於 **Azure Stack 操作員**。

本文是[安裝 PowerShell]( azure-stack-powershell-install.md)、[下載工具]( azure-stack-powershell-download.md) 和 [設定 Azure Stack 操作員的 PowerShell 環境]( azure-stack-powershell-configure-admin.md)文章中描述之步驟的精簡版本。 藉由使用本主題中的指令碼，您可以設定與 Azure Active Directory 或 Active Directory 同盟服務 (AD FS) 一起部署的 Azure Stack 的 PowerShell 環境。  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>設定 PowerShell 進行以 Azure Active Directory 為基礎的部署

如果您透過 VPN 連線，登入到 Azure Stack 開發套件或以 Windows 為基礎的外部用戶端。 開啟提升權限的 PowerShell ISE 工作階段，然後執行下列指令碼。 確定視需要更新環境設定的 **TenantName**、**ArmEndpoint** 和 **GraphAudience** 變數：

> [!IMPORTANT]
> AzureRM 1.2.11 PowerShell 模組的發行版本隨附重大變更清單。 若要從 1.2.10 版升級，請參閱[移轉指南](https://aka.ms/azspowershellmigration)。

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"


# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>設定 PowerShell 進行以 AD FS 為基礎的部署

如果您在連線至網際網路時執行 Azure Stack，您可以使用下列指令碼。 如果您在未連線至網際網路時執行 Azure Stack，請使用[離線安裝 PowerShell 的方法](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity)，設定 PowerShell 的 Cmdlet 一樣如同此指令碼中所示。 如果您透過 VPN 連線，登入到 Azure Stack 開發套件或以 Windows 為基礎的外部用戶端。 開啟提升權限的 PowerShell ISE 工作階段，然後執行下列指令碼。 確定視需要更新環境設定的 **ArmEndpoint** 和 **GraphAudience** 變數：

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>測試連線

既然您已設定 PowerShell，您可以建立資源群組來測試設定：

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

建立資源群組後，會將 [佈建狀態] 屬性設為 [成功]。

## <a name="next-steps"></a>後續步驟

* [安裝及設定 CLI](azure-stack-connect-cli.md)

* [開發範本](user/azure-stack-develop-templates.md)







