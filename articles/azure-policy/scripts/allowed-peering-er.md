---
title: "Azure 原則 JSON 範例 - 允許的 Express Route 對等互連位置 | Microsoft Docs"
description: "此原則會要求 Express Route 使用指定的對等互連位置。"
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 3b7a9cccf00cfcf5530cfbd83d0c911f1a26cf76
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="allowed-peering-location-for-express-route"></a>允許的 Express Route 對等互連位置

此原則會要求 Express Route 使用指定的對等互連位置。 您需指定允許的對等互連位置陣列。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>範例範本

[!code-json[main](../../../policy-templates/samples/Network/express-route-peeringLocation/azurepolicy.json "Allowed Peering Location for Express Route")]


您可以使用 [Azure 入口網站](#deploy-with-the-portal)[PowerShell](#deploy-with-powershell) 或 [Azure CLI](#deploy-with-azure-cli) 來部署此範本。

## <a name="deploy-with-the-portal"></a>使用入口網站部署

[![部署至 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade)

## <a name="deploy-with-powershell"></a>使用 PowerShell 部署

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

````powershell
$definition = New-AzureRmPolicyDefinition -Name "express-route-peeringLocation" -DisplayName "Allowed Peering Location for Express Route" -description "This policy enables you to specify a set of allowed peering location for express route" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition
$assignment
````

### <a name="clean-up-powershell-deployment"></a>清除 PowerShell 部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

````cli

az policy definition create --name 'express-route-peeringLocation' --display-name 'Allowed Peering Location for Express Route' --description 'This policy enables you to specify a set of allowed peering location for express route' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "express-route-peeringLocation"

````

### <a name="clean-up-azure-cli-deployment"></a>清除 Azure CLI 部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

- 其他「Azure 原則」範本範例都位於[Azure 原則的範本](../json-samples.md)。
