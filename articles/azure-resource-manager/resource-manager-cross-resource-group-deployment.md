---
title: "將 Azure 資源部署至多個訂用帳戶和資源群組 | Microsoft Docs"
description: "示範如何在部署期間將目標放在多個 Azure 訂用帳戶和資源群組。"
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
ms.date: 12/01/2017
ms.author: tomfitz
ms.openlocfilehash: 763f46b9b5be7edf06ee0604bfc51a2482405b60
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>將 Azure 資源部署至多個訂用帳戶和資源群組

一般而言，您要將範本中的所有資源部署至單一資源群組。 不過，在某些情況下，您要將一組資源部署在一起，但將它們放在不同的資源群組或訂用帳戶中。 例如，建議您將 Azure Site Recovery 的備份虛擬機器部署至不同的資源群組和位置。 Resource Manager 可讓您使用巢狀的範本，將目標放在與父範本所使用之訂用帳戶和資源群組不同的訂用帳戶和資源群組。

資源群組是應用程式及其資源集合的生命週期容器。 您要建立樣板之外的資源群組，並指定要在部署期間作為目標的資源群組。 如需資源群組的簡介，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。

## <a name="specify-a-subscription-and-resource-group"></a>指定訂用帳戶和資源群組

若要將目標放在不同的資源，您必須在部署期間使用巢狀或連結的範本。 `Microsoft.Resources/deployments` 資源類型提供 `subscriptionId` 和 `resourceGroup` 的參數。 這些屬性可讓您指定不同的訂用帳戶和資源群組來進行巢狀部署。 執行部署之前，所有資源群組都必須存在。 如果您未指定訂用帳戶識別碼或資源群組，則會使用父範本中的訂用帳戶及資源群組。

下列範例會部署兩個儲存體帳戶 - 一個是在部署期間指定的資源群組中，另一個是在 `secondResourceGroup` 參數內指定的資源群組中：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
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
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

如果您將 `resourceGroup` 設定為不存在的資源群組名稱，部署就會失敗。

## <a name="deploy-the-template"></a>部署範本

若要部署範例範本，請使用 2017年 5 月或更新版本的 Azure PowerShell 或 Azure CLI 版本。 針對這些範例，請使用 GitHub 中的[跨訂用帳戶範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json)。

### <a name="two-resource-groups-in-the-same-subscription"></a>相同訂用帳戶中的兩個資源群組

在 PowerShell 中，若要將兩個儲存體帳戶部署到相同訂用帳戶中的兩個資源群組，請使用：

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

在 Azure CLI 中，若要將兩個儲存體帳戶部署到相同訂用帳戶中的兩個資源群組，請使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

部署完成後，您會看到兩個資源群組。 每個資源群組都包含儲存體帳戶。

### <a name="two-resource-groups-in-different-subscriptions"></a>不同訂用帳戶中的兩個資源群組

在 PowerShell 中，若要將兩個儲存體帳戶部署到兩個訂用帳戶，請使用：

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

在 Azure CLI 中，若要將兩個儲存體帳戶部署到兩個訂用帳戶，請使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

## <a name="use-the-resourcegroup-function"></a>使用 resourceGroup() 函式

對於跨資源群組部署，[resouceGroup() 函式](resource-group-template-functions-resource.md#resourcegroup)會根據您指定巢狀範本的方式，以不同的方式進行解析。 

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

如果您連結至個別範本，連結範本中的 resourceGroup() 會解析至巢狀資源群組。 連結範本會使用下列格式：

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

若要測試不同的 `resourceGroup()` 解析方式，請部署[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)，以傳回父範本、內嵌範本和連結範本的資源群組物件。 父範本和內嵌範本都會解析至相同資源群組。 連結範本會解析至連結的資源群組。

對於 PowerShell，請使用：

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

對於 Azure CLI，請使用：

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

## <a name="next-steps"></a>後續步驟

* 若要了解如何在您的範本中定義參數，請參閱[了解 Azure Resource Manager 範本的結構和語法](resource-group-authoring-templates.md)。
* 如需解決常見部署錯誤的秘訣，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。
* 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-powershell-sas-token.md)。
