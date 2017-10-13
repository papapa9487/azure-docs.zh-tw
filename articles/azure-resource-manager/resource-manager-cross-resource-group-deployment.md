---
title: "將 Azure 資源部署至多個資源群組 | Microsoft Docs"
description: "示範如何在部署期間將目標放在多個 Azure 資源群組。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: tomfitz
ms.openlocfilehash: d8b041213b269775175a810e585103d3c538557f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-azure-resources-to-more-than-one-resource-group"></a>將 Azure 資源部署至多個資源群組

一般而言，您要將範本中的所有資源部署至單一資源群組。 不過，在某些情況下，您要將一組資源部署在一起，但將它們放在不同的資源群組中。 例如，建議您將 Azure Site Recovery 的備份虛擬機器部署至不同的資源群組和位置。 Resource Manager 可讓您使用巢狀的範本，將目標放在與父系範本所使用之資源群組不同的資源群組。

資源群組是應用程式及其資源集合的生命週期容器。 您要建立樣板之外的資源群組，並指定要在部署期間作為目標的資源群組。 如需資源群組的簡介，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。

## <a name="example-template"></a>範本範例

若要將目標放在不同的資源，您必須在部署期間使用巢狀或連結的範本。 `Microsoft.Resources/deployments` 資源類型會提供`resourceGroup` 參數，可讓您為巢狀部署指定不同的資源群組。 執行部署之前，所有資源群組都必須存在。 下列範例會部署兩個儲存體帳戶 - 一個是在部署期間指定的資源群組中，另一個是在名為 `crossResourceGroupDeployment` 的資源群組中：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "StorageAccountName1": {
            "type": "string"
        },
        "StorageAccountName2": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "crossResourceGroupDeployment",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[parameters('StorageAccountName2')]",
                            "apiVersion": "2015-06-15",
                            "location": "West US",
                            "properties": {
                                "accountType": "Standard_LRS"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('StorageAccountName1')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
                "accountType": "Standard_LRS"
            }
        }
    ]
}
```

如果您將 `resourceGroup` 設定為不存在的資源群組名稱，部署就會失敗。 如果您未提供 `resourceGroup` 的值，Resource Manager 就會使用父系資源群組。  

## <a name="deploy-the-template"></a>部署範本

若要部署範例範本，您可以使用入口網站、Azure PowerShell 或 Azure CLI。 若是使用 Azure PowerShell 或 Azure CLI，您必須使用 2017年 5 月或更新版本。 這些範例會假設您已在本機將範本儲存為名為 **crossrgdeployment.json** 的檔案。

對於 PowerShell：

```powershell
Login-AzureRmAccount

New-AzureRmResourceGroup -Name mainResourceGroup -Location "South Central US"
New-AzureRmResourceGroup -Name crossResourceGroupDeployment -Location "Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName mainResourceGroup `
  -TemplateFile c:\MyTemplates\crossrgdeployment.json
```

對於 Azure CLI：

```azurecli
az login

az group create --name mainResourceGroup --location "South Central US"
az group create --name crossResourceGroupDeployment --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group mainResourceGroup \
    --template-file crossrgdeployment.json
```

部署完成後，您會看到兩個資源群組。 每個資源群組都包含儲存體帳戶。

## <a name="use-resourcegroup-function"></a>使用 resourceGroup() 函數

對於跨資源群組部署，[resouceGroup() 函數](resource-group-template-functions-resource.md#resourcegroup)會根據您指定巢狀範本的方式，以不同的方式進行解析。 

如果您將某個範本內嵌於另一個範本內，巢狀範本中的 resouceGroup() 會解析至父資源群組。 內嵌範本會使用下列格式：

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

如果您連結至個別範本，連結範本中的 resouceGroup() 會解析至巢狀資源群組。 連結範本會使用下列格式：

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="next-steps"></a>後續步驟

* 若要了解如何在您的範本中定義參數，請參閱[了解 Azure Resource Manager 範本的結構和語法](resource-group-authoring-templates.md)。
* 如需解決常見部署錯誤的秘訣，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。
* 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-powershell-sas-token.md)。
