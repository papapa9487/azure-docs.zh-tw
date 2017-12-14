---
title: "管理 Azure Stack 中的 API 版本設定檔 | Microsoft Docs"
description: "了解 Azure Stack 中的 API 版本設定檔"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6B749785-DCF5-4AD8-B808-982E7C6BBA0E
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: mabrigg
ms.openlocfilehash: d86a54ea9e165269131eb961df7f74703f0ec6ff
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>管理 Azure Stack 中的 API 版本設定檔

Azure App Service 版本設定檔的 API 功能提供一種管理 Azure 與 Azure Stack 之間版本差異的方式。 API 版本設定檔是一組具有特定 API 版本的 AzureRM PowerShell 模組。 每個雲端平台都有一組支援的 API 版本設定檔。 例如，Azure Stack 支援特定日期的設定檔版本 (例如 **2017-03-09-profile**)，而 Azure 則支援*最新的* API 版本設定檔。 當您安裝設定檔時，會安裝與指定的設定檔對應的 AzureRM PowerShell 模組。

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>安裝使用 API 版本設定檔所需的 PowerShell 模組

可透過「PowerShell 資源庫」取得的 **AzureRM.Bootstrapper** 模組會提供使用 API 版本設定檔所需的 PowerShell Cmdlet。 請使用下列 Cmdlet 來安裝 **AzureRM.Bootstrapper** 模組：

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
**AzureRM.Bootstrapper** 模組目前為預覽版，所以詳細資料與功能可能會有所變更。 若要從「PowerShell 資源庫」下載並安裝此模組的最新版本，請執行下列 Cmdlet：

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>安裝設定檔

使用 **Install-AzureRmProfile** Cmdlet 搭配 **2017-03-09-profile** API 版本設定檔，以安裝 Azure Stack 所需的 AzureRM 模組。 

>[!NOTE]
>Azure Stack 雲端管理員模組並不會隨著這個 API 版本設定檔一起安裝。 您應該如[安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md) 一文的步驟 3 中所指定，個別安裝管理員模組。

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>安裝並匯入設定檔中的模組

使用 **Use-AzureRmProfile** Cmdlet 來安裝並匯入與 API 版本設定檔關聯的模組。 在一個 PowerShell 工作階段中只能匯入一個 API 版本設定檔。 若要匯入不同的 API 版本設定檔，您必須開啟新的 PowerShell 工作階段。 **Use-AzureRMProfile** Cmdlet 會執行下列工作：  
1. 查看目前範圍中是否已安裝與所指定 API 版本設定檔關聯的 PowerShell 模組。  
2. 下載並安裝這些模組 (如果尚未安裝)。   
3. 將模組匯入到目前的 PowerShell 工作階段中。 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

若要從某個 API 版本設定檔安裝並匯入選取的 AzureRM 模組，請搭配 *Module* 參數執行 **Use-AzureRMProfile** Cmdlet：

```PowerShell
# Installs and imports the Compute, Storage, and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>取得已安裝的設定檔

使用 **Get-AzureRmProfile** Cmdlet 來取得可用的 API 版本設定檔清單： 

```PowerShell
# Lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# Lists the API version profiles that are installed on your machine.
Get-AzureRmProfile
```
## <a name="update-profiles"></a>更新設定檔

使用 **Update-AzureRmProfile** Cmdlet，以將 API 版本設定檔中的模組更新成 PowerShell 資源庫中可用的最新版模組。 建議您在匯入模組時，在新的 PowerShell 工作階段中執行 **Update-AzureRmProfile** Cmdlet，以避免發生衝突。 **Update-AzureRmProfile** Cmdlet 會執行下列工作：

1. 查看目前範圍的指定 API 版本設定檔中是否已安裝最新版模組。  
2. 提示您安裝這些模組 (如果尚未安裝)。  
3. 將已更新的模組安裝並匯入到目前的 PowerShell 工作階段中。  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

若要在更新成最新可用版本之前，先移除先前安裝的模組版本，請搭配 -RemovePreviousVersions 參數使用 **Update-AzureRmProfile** Cmdlet：

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

此 Cmdlet 會執行下列工作：  

1. 查看目前範圍的指定 API 版本設定檔中是否已安裝最新版模組。  
2. 從目前的 API 版本設定檔並在目前 PowerShell 工作階段中，移除舊版模組。  
3. 提示您安裝模組的最新版本。  
4. 將已更新的模組安裝並匯入到目前的 PowerShell 工作階段中。  
 
## <a name="uninstall-profiles"></a>將設定檔解除安裝

使用 **Uninstall-AzureRmProfile** Cmdlet 將指定的 API 版本設定檔解除安裝：

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>後續步驟
* [安裝 Azure Stack 適用的 PowerShell](azure-stack-powershell-install.md)
* [設定 Azure Stack 使用者的 PowerShell 環境](azure-stack-powershell-configure-user.md)  
