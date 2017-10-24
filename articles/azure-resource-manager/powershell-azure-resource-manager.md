---
title: "使用 PowerShell 管理 Azure 解決方案 | Microsoft Docs"
description: "使用 Azure PowerShell 和 Resource Manager 管理資源。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: ae5ccb83a0088cb7c9668f18620b74f9f3f1e9b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-resources-with-azure-powershell-and-resource-manager"></a>使用 Azure PowerShell 和 Resource Manager 管理資源

在本文中，您會了解如何使用 Azure PowerShell 和 Azure Resource Manager 管理您的解決方案。 如果您不熟悉如何使用 Resource Manager，請參閱 [Resource Manager 概觀](resource-group-overview.md)。 本文著重於管理工作。 您將：

1. 建立資源群組
2. 將資源加入資源群組
3. 將標籤加入資源
4. 根據名稱或標籤值查詢資源
5. 套用和移除資源的鎖定
6. 刪除資源群組

本文不會說明如何將 Resource Manager 範本部署到您的訂用帳戶。 如需該資訊，請參閱[使用 Resource Manager 範本與 Azure PowerShell 來部署資源](resource-group-template-deploy.md)。

## <a name="get-started-with-azure-powershell"></a>開始使用 Azure PowerShell

如果您未安裝 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

如果您之前已安裝 Azure PowerShell 但近期未更新，請考慮安裝最新版本。 您可以透過先前用來安裝的方法來更新版本。 例如，如果您使用 Web Platform Installer，請再次啟動它並尋找更新。

若要檢查您 Azure 資源模組的版本，請使用下列 Cmdlet：

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

本文已針對版本 3.3.0 更新。 如果您的版本較舊，您的經驗可能與本文中所顯示的步驟不符。 如需此版本中 Cmdlet 的相關文件，請參閱 [AzureRM.Resources 模組](/powershell/module/azurerm.resources)。

## <a name="log-in-to-your-azure-account"></a>登入您的 Azure 帳戶
在使用您的解決方案之前，您必須登入您的帳戶。

若要登入您的 Azure 帳戶，請使用 **Login-AzureRmAccount** Cmdlet。

```powershell
Login-AzureRmAccount
```

cmdlet 會提示您 Azure 帳戶的登入認證。 登入之後，它會下載您的帳戶設定以供 Azure PowerShell 使用。

Cmdlet 會傳回您的帳戶和訂用帳戶的相關資訊供作業使用。

```powershell
Environment           : AzureCloud
Account               : example@contoso.com
TenantId              : {guid}
SubscriptionId        : {guid}
SubscriptionName      : Example Subscription One
CurrentStorageAccount :

```

如果您有多個訂用帳戶，可以切換成其他訂用帳戶。 首先，我們來看看您帳戶的所有訂用帳戶。

```powershell
Get-AzureRmSubscription
```

它會傳回啟用和停用的訂用帳戶。

```powershell
SubscriptionName : Example Subscription One
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Two
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Three
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Disabled
```

若要切換成其他訂用帳戶，請使用 **Set-AzureRmContext** Cmdlet 提供訂用帳戶名稱。

```powershell
Set-AzureRmContext -SubscriptionName "Example Subscription Two"
```

## <a name="create-a-resource-group"></a>建立資源群組

將任何資源部署至訂用帳戶之前，您必須建立將包含該資源的資源群組。

若要建立資源群組，請使用 **New-AzureRmResourceGroup** Cmdlet。 此命令會使用 **Name** 參數來指定資源群組的名稱，並使用 **Location** 參數來指定其位置。

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location "South Central US"
```

輸出的格式如下：

```powershell
ResourceGroupName : TestRG1
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1
```

如果您稍後需要擷取資源群組，請使用下列 Cmdlet：

```powershell
Get-AzureRmResourceGroup -ResourceGroupName TestRG1
```

若要取得訂用帳戶中所有資源群組，請不要指定名稱：

```powershell
Get-AzureRmResourceGroup
```

## <a name="add-resources-to-a-resource-group"></a>將資源加入資源群組

若要將資源加入資源群組，您可以使用 **New-AzureRmResource** Cmdlet 或是與您建立之資源類型相關的 Cmdlet (像是**New-AzureRmStorageAccount**)。 您可能會發現使用與資源類型相關的 Cmdlet 會比較容易，因為它包含新資源所需的屬性參數。 若要使用 **New-AzureRmResource**，您必須知道要設定的所有屬性而不需經過提示。

不過，透過 Cmdlet 新增資源可能會造成未來的混淆，因為新的資源不在 Resource Manager 範本中。 Microsoft 建議在 Resource Manager 範本中定義 Azure 解決方案的基礎結構。 範本可讓您可靠且重複地部署您的解決方案。 在本文中，您會使用 PowerShell Cmdlet 建立儲存體帳戶，但之後您會從資源群組中產生範本。

下列 Cmdlet 會建立儲存體帳戶。 請不要使用範例所顯示的名稱，而是為儲存體帳戶提供唯一的名稱。 名稱的長度必須介於 3 到 24 個字元，而且只能使用數字和小寫字母。 如果使用範例所顯示的名稱，您會收到錯誤，因為該名稱已在使用中。

```powershell
New-AzureRmStorageAccount -ResourceGroupName TestRG1 -AccountName mystoragename -Type "Standard_LRS" -Location "South Central US"
```

如果您稍後需要擷取此資源，請使用下列 Cmdlet：

```powershell
Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1
```

## <a name="add-a-tag"></a>新增標記

標籤可讓您根據不同的屬性組織您的資源。 例如，您在不同的資源群組中可能有數個資源，屬於相同的部門。 您可以對這些資源套用部門標籤和值，將它們標示為屬於相同的類別目錄。 或者，您可以標記資源是在生產或測試環境中使用。 在本文中，您只會對一個資源套用標籤，但在您的環境中，很有可能會對所有資源套用標籤。

下列 Cmdlet 對您的儲存體帳戶套用兩個標籤︰

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
 ```

標籤會以一個物件的方式更新。 若要對已經包含標籤的資源新增標籤，請先擷取現有的標籤。 將新的標籤加入包含現有標籤的物件，然後對資源重新套用所有標籤。

```powershell
$tags = (Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
```

## <a name="search-for-resources"></a>搜尋資源

使用 **Find-AzureRmResource** Cmdlet 擷取不同搜尋條件的資源。

* 若要依名稱取得資源，請提供 **ResourceNameContains** 參數︰

  ```powershell
  Find-AzureRmResource -ResourceNameContains mystoragename
  ```

* 若要取得資源群組中的所有資源，請提供 **ResourceGroupNameContains** 參數︰

  ```powershell
  Find-AzureRmResource -ResourceGroupNameContains TestRG1
  ```

* 若要取得有某個標籤名稱和值的所有資源，請提供 **TagName** 和 **TagValue** 參數︰

  ```powershell
  Find-AzureRmResource -TagName Dept -TagValue IT
  ```

* 若要取得有特定資源類型的所有資源，請提供 **ResourceType** 參數︰

  ```powershell
  Find-AzureRmResource -ResourceType Microsoft.Storage/storageAccounts
  ```

## <a name="get-resource-id"></a>取得資源識別碼

許多命令會採用資源識別碼作為參數。 若要取得資源識別碼並且儲存在變數中，請使用：

```powershell
$webappID = (Get-AzureRmResource -ResourceGroupName exampleGroup -ResourceName exampleSite).ResourceId
```

## <a name="lock-a-resource"></a>鎖定資源

當您必須確保重要資源不會意外被刪除或修改時，就可以對資源進行鎖定。 您可以指定 **CanNotDelete** 或 **ReadOnly**。

若要建立或刪除管理鎖定，您必須擁有 `Microsoft.Authorization/*` 或 `Microsoft.Authorization/locks/*` 動作的存取權。 在內建角色中，只有「擁有者」和「使用者存取管理員」被授與這些動作的存取權。

若要套用鎖定，請使用下列 Cmdlet：

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

在上述範例中鎖定的資源要到鎖定移除之後才能刪除。 若要移除鎖定，請使用︰

```powershell
Remove-AzureRmResourceLock -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

如需設定鎖定的詳細資訊，請參閱[使用 Azure Resource Manager 鎖定資源](resource-group-lock-resources.md)。

## <a name="remove-resources-or-resource-group"></a>移除資源或資源群組
您可以移除資源或資源群組。 當您移除資源群組時，也會移除該資源群組內的所有資源。

* 若要從資源群組中刪除資源，請使用 **Remove-AzureRmResource** Cmdlet。 此 Cmdlet 會刪除資源，但不會刪除資源群組。

  ```powershell
  Remove-AzureRmResource -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
  ```

* 若要刪除資源群組及其所有資源，請使用 **Remove-AzureRmResourceGroup** Cmdlet。

  ```powershell
  Remove-AzureRmResourceGroup -Name TestRG1
  ```

這兩個 Cmdlet 都會要求您確認您想要移除資源或資源群組。 如果作業成功刪除資源或資源群組，則會傳回 **True**。

## <a name="run-resource-manager-scripts-with-azure-automation"></a>使用 Azure 自動化執行 Resource Manager 指令碼

本文說明如何使用 Azure PowerShell 對資源執行基本作業。 對於更進階的管理案例，您通常要建立指令碼，並視需要或根據排程重複使用該指令碼。 [Azure 自動化](../automation/automation-intro.md)提供您一種方式自動化經常使用的指令碼，管理您的 Azure 解決方案。

下列主題說明如何使用 Azure 自動化、Resource Manager 和 PowerShell 有效地執行管理工作︰

- 如需建立 Runbook 的相關資訊，請參閱[我的第一個 PowerShell Runbook](../automation/automation-first-runbook-textual-powershell.md)。
- 如需使用指令碼資源庫的相關資訊，請參閱[Azure 自動化的 Runbook 和模組資源庫](../automation/automation-runbook-gallery.md)。
- 如需啟動和停止虛擬機器的 Runbook，請參閱 [Azure 自動化案例：使用 JSON 格式化標籤來建立 Azure VM 啟動和關閉的排程](../automation/automation-scenario-start-stop-vm-wjson-tags.md)。
- 如需於下班時間啟動和停止虛擬機器的 Runbook，請參閱[於下班時間自動化啟動/停止 VM 的解決方案](../automation/automation-solution-vm-management.md)。

## <a name="next-steps"></a>後續步驟
* 若要了解如何建立 Resource Manager 範本，請參閱[編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 若要了解部署範本的相關資訊，請參閱[使用 Azure Resource Manager 範本部署應用程式](resource-group-template-deploy.md)。
* 您可以將現有的資源移動到新的資源群組。 如需範例，請參閱 [將資源移至新的資源群組或訂用帳戶](resource-group-move-resources.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。

