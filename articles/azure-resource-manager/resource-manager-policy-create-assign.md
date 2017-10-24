---
title: "指派和管理 Azure 資源原則 | Microsoft Docs"
description: "描述如何將 Azure 資源原則套用至訂用帳戶和資源群組，以及如何檢視資源原則。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: tomfitz
ms.openlocfilehash: 64bdd6ed41e98079c8d4112e895aaeddcd629282
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="assign-and-manage-resource-policies"></a>指派和管理資源原則

若要實作原則，您必須執行這些步驟︰

1. 檢查原則定義 (包含 Azure 提供的內建原則)，以查看您的訂用帳戶中是否已存在符合您需求的定義。
2. 如果存在，請記下它的名稱。
3. 如果不存在，請使用 JSON 定義原則規則，並將它新增為訂用帳戶中的原則定義。 這個步驟會使原則可供指派，但不會將規則套用至您的訂用帳戶。
4. 不論存在與否，請將原則指派給範圍 (例如訂用帳戶或資源群組)。 現在會強制執行原則的規則。

本文著重於建立原則定義，然後透過 REST API、PowerShell 或 Azure CLI 將該定義指派至範圍的步驟。 如果您想要使用入口網站來指派原則，請參閱[使用 Azure 入口網站來指派和管理資源原則](resource-manager-policy-portal.md)。 本文並不會著重於建立原則定義的語法。 如需原則語法的詳細資訊，請參閱[資源原則概觀](resource-manager-policy.md)。

## <a name="exclusion-scopes"></a>排除範圍

指派原則時，您可以排除範圍。 這項功能可以簡化原則指派，因為您可以在訂用帳戶層級指派原則，但是指定不套用原則的位置。 例如，在您的訂用帳戶中，您會有適用於網路基礎結構的資源群組。 應用程式小組會將其資源部署到其他資源群組。 您不想要讓這些小組建立可能會導致安全性問題的網路資源。 不過，在網路資源群組中，您想要允許網路資源。 您在訂用帳戶層級指派原則，但是排除網路資源群組。 您可以指定多個子範圍。

```json
{
    "properties":{
        "policyDefinitionId":"<ID for policy definition>",
        "notScopes":[
            "/subscriptions/<subid>/resourceGroups/networkresourceGroup1"
        ]
    }
}
```

如果您在指派中指定排除範圍，請使用 **2017-06-01-preview** API 版本。

## <a name="rest-api"></a>REST API

### <a name="create-policy-definition"></a>建立原則定義

您可以使用 [適用於原則定義的 REST API](/rest/api/resources/policydefinitions)來建立原則。 REST API 可讓您建立和刪除原則定義，以及取得現有定義的相關資訊。

若要建立原則定義，請執行︰

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

納入如下範例的要求內文：

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>指派原則

您可以透過 [適用於原則指派的 REST API](/rest/api/resources/policyassignments)，在所需範圍內套用原則定義。 REST API 可讓您建立和刪除原則指派，以及取得現有指派的相關資訊。

若要建立原則指派，請執行：

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

{policy-assignment} 是原則指派的名稱。

納入如下範例的要求內文：

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>檢視原則
若要取得原則，請使用[取得原則定義](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get)作業。

### <a name="get-aliases"></a>取得別名
您可以透過 REST API 擷取別名︰

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

下列範例顯示別名的定義。 如您所見，別名在不同 API 版本中均會定義路徑，無論屬性名稱是否變更。 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

繼續 PowerShell 範例之前，請確定您已[安裝最新版](/powershell/azure/install-azurerm-ps)的 Azure PowerShell。 原則參數是於 3.6.0 版中加入。 如果您使用舊版本，範例會傳回找不到參數的錯誤。

### <a name="view-policy-definitions"></a>檢視原則定義
若要查看訂用帳戶中的所有原則定義，請使用下列命令：

```powershell
Get-AzureRmPolicyDefinition
```

它會傳回所有可用的原則定義，包括內建原則。 每個原則都以下列格式傳回：

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

在繼續建立原則定義之前，請先查看內建原則。 如果找到可套用所需限制的內建原則，則您可以略過建立原則定義。 反之，將內建原則指派給所需的範圍。

### <a name="create-policy-definition"></a>建立原則定義
您可以使用 `New-AzureRmPolicyDefinition` cmdlet 建立原則定義。

若要從檔案建立原則定義，請將路徑傳遞至檔案。 針對外部檔案，使用：

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

針對本機檔案，使用：

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

若要建立具有內嵌規則的原則定義，請使用：

```powershell
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

輸出會儲存在於原則指派期間使用的 `$definition` 物件中。 

下列範例建立了包含參數的原則定義：

```powershell
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}' 

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters 
```

### <a name="assign-policy"></a>指派原則

使用 `New-AzureRmPolicyAssignment` Cmdlet 將原則套用至所需範圍。 以下範例將原則指派到資源群組。

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

若要指派需要參數的原則，可建立包含那些值的物件。 以下範例會擷取內建原則並將參數值傳入：

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $definition -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>檢視原則指派

若要取得特定的原則指派，請使用：

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

若要檢視原則定義的原則規則，請使用：

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>移除原則指派 

若要移除原則指派，請使用：

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>Azure CLI

### <a name="view-policy-definitions"></a>檢視原則定義
若要查看訂用帳戶中的所有原則定義，請使用下列命令：

```azurecli
az policy definition list
```

它會傳回所有可用的原則定義，包括內建原則。 每個原則都以下列格式傳回：

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

在繼續建立原則定義之前，請先查看內建原則。 如果找到可套用所需限制的內建原則，則您可以略過建立原則定義。 反之，將內建原則指派給所需的範圍。

### <a name="create-policy-definition"></a>建立原則定義

您可以使用 Azure CLI 搭配原則定義命令來建立原則定義。

若要建立具有內嵌規則的原則定義，請使用：

```azurecli
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>指派原則

您可以使用原則指派命令將原則套用至所需的範圍。 以下範例將原則指派到資源群組。

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>檢視原則指派

若要檢視原則指派，請提供原則指派名稱和範圍：

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>移除原則指派 

若要移除原則指派，請使用：

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>後續步驟
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。

