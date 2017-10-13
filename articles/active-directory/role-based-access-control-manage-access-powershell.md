---
title: "使用 Azure PowerShell 管理角色型存取控制 (RBAC) | Microsoft Docs"
description: "如何使用 Azure PowerShell 管理 RBAC，包括列出角色、指派角色，以及刪除角色指派。"
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: d7b11df21650b5cb27f9c3dd8306f8d12664185e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>使用 Azure PowerShell 管理角色型存取控制
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)

您可以使用 Azure 入口網站和「Azure 資源管理」API 中的「角色型存取控制」(RBAC) 來精確地管理對訂用帳戶的存取。 透過這項功能，您可以為 Active Directory 使用者、群組或是服務主體指派特定範圍的一些角色，藉此賦予其存取權限。

使用 PowerShell 來管理 RBAC 之前，您需要具備下列必要條件：

* Azure PowerShell 0.8.8 或更新版本。 若要安裝最新版本，並將它與您的 Azure 訂用帳戶建立關聯，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。
* Azure Resource Manager Cmdlet。 在 PowerShell 中，安裝 [Azure Resource Manager Cmdlet](/powershell/azure/overview) 。

## <a name="list-roles"></a>列出角色
### <a name="list-all-available-roles"></a>列出所有可用的角色
若要列出可供指派的 RBAC 角色，以及若要檢查它們授與存取權的作業，請使用 `Get-AzureRmRoleDefinition`。

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>列出角色的動作
若要列出特定角色的動作，請使用 `Get-AzureRmRoleDefinition <role name>`。

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell - 特定角色的 Get-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>查看誰具有存取權
若要列出 RBAC 存取權指派，請使用 `Get-AzureRmRoleAssignment`。

### <a name="list-role-assignments-at-a-specific-scope"></a>列出特定範圍的角色指派
您可以查看指定訂用帳戶、資源群組或資源的所有存取權指派。 例如，若要查看某個資源群組的所有使用中指派，請使用 `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`。

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - 資源群組的 Get-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>列出指派給使用者的角色
若要列出指派給指定使用者的所有角色，以及指派給該使用者所屬群組的角色，請使用 `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`。

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - 使用者的 Get-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>列出傳統服務管理員和共同管理員角色指派
若要列出傳統訂用帳戶管理員和共同管理員的存取權指派，請使用：

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>授與存取權
### <a name="search-for-object-ids"></a>搜尋物件識別碼
若要指派角色，您必須識別物件 (使用者、群組或應用程式) 和範圍。

如果您不知道訂用帳戶 ID，可以在 Azure 入口網站的 **[訂用帳戶]** 刀鋒視窗中找到。 若要了解如何查詢訂用帳戶 ID，請參閱 MSDN 上的 [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) 。

若要取得 Azure AD 群組的物件識別碼，請使用：

    Get-AzureRmADGroup -SearchString <group name in quotes>

若要取得 Azure AD 服務主體或應用程式的物件 ID，請使用：

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>將角色指派給訂用帳戶範圍中的應用程式
若要將存取權授與訂用帳戶範圍中的應用程式，請使用：

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>將角色指派給資源群組範圍中的使用者
若要將存取權授與資源群組範圍中的使用者，請使用：

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>將角色指派給資源範圍中的群組
若要將存取權授與資源範圍中的群組，請使用：

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>移除存取
若要移除使用者、群組和應用程式的存取權，請使用：

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>建立自訂角色
若要建立自訂角色，請使用 ```New-AzureRmRoleDefinition``` 命令。 建構角色有兩種方法：使用 PSRoleDefinitionObject 或 JSON 範本。 

## <a name="get-actions-for-a-resource-provider"></a>取得資源提供者的動作
當您從頭建立自訂角色時，務必知道所有可能來自資源提供者的作業。
使用 ```Get-AzureRMProviderOperation``` 命令來取得此資訊。
例如，如果您想要檢查虛擬機器的所有可用作業，請使用此命令：

```
Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize
```

### <a name="create-role-with-psroledefinitionobject"></a>使用 PSRoleDefinitionObject 建立角色
使用 PowerShell 建立自訂角色時，您可以從頭開始，或使用其中一個[內建角色](role-based-access-built-in-roles.md)當作起點。 本節中的範例是以內建角色當作起點，然後使用較高權限來自訂它。 請編輯屬性來新增您想要的 *Actions*、*notActions* 或 *scopes*，然後將變更儲存為新角色。

下列範例會從「虛擬機器參與者」角色來開始，並使用它來建立稱為「虛擬機器操作者」的自訂角色。 新角色會授與對 *Microsoft.Compute*、*Microsoft.Storage* 和 *Microsoft.Network* 資源提供者之所有讀取作業的存取權，以及授與對啟動、重新啟動和監視虛擬機器的存取權。 自訂角色可用於兩個訂用帳戶中。

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>使用 JSON 範本建立角色
JSON 範本可作為自訂角色的來源定義。 下列範例會建立自訂角色來允許讀取儲存體和計算資源及存取支援，然後將該角色新增至兩個訂用帳戶。 建立含有下列範例的新檔案 `C:\CustomRoles\customrole1.json`。 最初建立角色時，應該將 Id 設為 `null`，因為會自動產生新的識別碼。 

```
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
若要將角色新增至訂用帳戶，請執行下列 PowerShell 命令︰
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>修改自訂角色
類似於建立自訂角色，您可以使用 PSRoleDefinitionObject 或 JSON 範本修改現有的自訂角色。

### <a name="modify-role-with-psroledefinitionobject"></a>使用 PSRoleDefinitionObject 修改角色
若要修改自訂角色，請先使用 `Get-AzureRmRoleDefinition` 命令來擷取角色定義。 接著，對角色定義進行想要的變更。 最後，使用 `Set-AzureRmRoleDefinition` 命令來儲存已修改的角色定義。

下列範例會將 `Microsoft.Insights/diagnosticSettings/*` 作業加入到 *Virtual Machine Operator* 自訂角色。

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

下列範例會將 Azure 訂用帳戶新增到 *Virtual Machine Operator* 自訂角色的可指派範圍。

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>使用 JSON 範本修改角色
您可以利用先前的 JSON 範本，輕鬆修改現有的自訂角色來新增或移除 Actions。 更新 JSON 範本，並新增網路的讀取動作，如下範例所示。 範本中所列的定義不會累積套用至現有的定義，這表示角色會完全依照您在範本中指定的樣子出現。 您也需要以角色的識別碼來更新 Id 欄位。 如果不確定此值，您可以使用 `Get-AzureRmRoleDefinition` Cmdlet 來取得這項資訊。

```
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

若要更新現有的角色，請執行下列 PowerShell 命令︰
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>刪除自訂角色
若要刪除自訂角色，請使用 `Remove-AzureRmRoleDefinition` 命令。

下列範例會移除 *Virtual Machine Operator* 自訂角色

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell - Remove-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>列出自訂角色
若要列出範圍中可供指派的角色，請使用 `Get-AzureRmRoleDefinition` 命令。

下列範例會列出選取的訂用帳戶中可供指派的所有角色。

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

在下列範例中，*Virtual Machine Operator* 自訂角色無法在 *Production4* 訂用帳戶中使用，因為該訂用帳戶並沒有在角色的 **AssignableScopes** 中。

![RBAC PowerShell - Get-AzureRmRoleDefinition - 螢幕擷取畫面](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>另請參閱
* [搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

