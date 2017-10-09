---
title: "安裝並設定 Azure Stack 的 PowerShell 快速入門 | Microsoft Docs"
description: "深入瞭解 Azure Stack 的 PowerShell 的安裝和設定。"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 6e5c420ed80127213e38849ac1999bba199e36c2
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>在 Azure Stack 使用 PowerShell 啟動和執行

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

本文說明如何使用 PowerShell 以快速開始安裝和設定 Azure Stack 環境。 本文中提供的此指令碼僅適用於 **Azure Stack 操作員**。

本文是[安裝 PowerShell]( azure-stack-powershell-install.md)、[下載工具]( azure-stack-powershell-download.md)、[設定 Azure Stack 操作員的 PowerShell 環境]( azure-stack-powershell-configure-admin.md)文件中說明之步驟的精簡版本。 藉由使用本主題中的指令碼，您可以設定與 Azure Active Directory 或 Active Directory 同盟服務一起部署的 Azure Stack 的 PowerShell 環境。  


## <a name="set-up-powershell-for-aad-based-deployments"></a>針對以 AAD 為基礎的部署設定 PowerShell

如果您透過 VPN 連線，登入到 Azure Stack 開發套件或以 Windows 為基礎的外部用戶端。 開啟已提高權限的 PowerShell ISE 工作階段，然後執行下列指令碼 (請務必依據您的環境組態來更新 TenantName、ArmEndpoint、GraphAudience 變數)：

```powershell
# Specify Azure Active Directory tenant name
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.10 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module `
  .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<GraphAuidence endpoint for your environment>"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackAdmin" `
  -ArmEndpoint $ArmEndpoint

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience $GraphAudience

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $TenantName `
  -EnvironmentName AzureStackAdmin

# Sign-in to the operator's portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackAdmin" `
  -TenantId $TenantID 

```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>針對以 AD FS 為基礎的部署設定 PowerShell 

如果您透過 VPN 連線，登入到 Azure Stack 開發套件或以 Windows 為基礎的外部用戶端。 開啟已提高權限的 PowerShell ISE 工作階段，然後執行下列指令碼 (請務必依據您的環境組態來更新 ArmEndpoint 和 GraphAudience 變數)：

```powershell

# Set the module repository and the execution policy
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.10 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module `
  .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<GraphAuidence endpoint for your environment>"

# Configure the cloud administrator’s PowerShell environment.
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

# Sign-in to the operator's portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackAdmin" `
  -TenantId $TenantID 

```

## <a name="test-the-connectivity"></a>測試連線

既然您已設定 PowerShell，您可以建立資源群組來測試設定：

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

建立資源群組後，Cmdlet 輸出會將佈建狀態屬性設定為「成功」。

## <a name="next-steps"></a>後續步驟

* [安裝及設定 CLI](azure-stack-connect-cli.md)

* [開發範本](user/azure-stack-develop-templates.md)








