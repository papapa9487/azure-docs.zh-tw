---
title: "Azure 範本中的資源位置 | Microsoft Docs"
description: "示範如何設定 Azure Resource Manager 範本中資源的位置"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: tomfitz
ms.openlocfilehash: 73e50a593c41e841dcaf184abb895406ff5001e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="set-resource-location-in-azure-resource-manager-templates"></a>設定 Azure Resource Manager 範本中的資源位置
在部署範本時，您必須為每個資源提供一個位置。 本主題示範如何決定可供訂用帳戶使用的每個資源類型位置。

## <a name="determine-supported-locations"></a>決定支援的位置

如需支援每個資源類型位置的完整清單，請參閱[依區域提供的產品](https://azure.microsoft.com/regions/services/)。 不過，您的訂用帳戶可能無法存取該清單中所有位置。 若要檢視供您的訂用帳戶使用的自訂位置清單，請使用 Azure PowerShell 或 Azure CLI。 

下列範例使用 PowerShell 來取得 `Microsoft.Web\sites` 資源類型的位置︰

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

下列範例使用 Azure CLI 2.0 來取得 `Microsoft.Web\sites` 資源類型的位置：

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="set-location-in-template"></a>在範本中設定位置

決定支援資源的位置之後，您需要在範本中設定該位置。 設定此值最簡單的方式是在支援資源類型的位置建立一個資源群組，而且將每個位置設定為 `[resourceGroup().location]`。 您可以將範本重新部署到不同位置的資源群組，而且無需變更範本中的任何值或參數。 

下列範例顯示部署到與資源群組相同位置的儲存體帳戶：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

如果您需要在範本中採硬式編碼來指定位置，請提供其中一個支援區域的名稱。 下列範例顯示一律會部署到美國中北部的儲存體帳戶：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="next-steps"></a>後續步驟
* 如需如何建立範本的建議，請參閱 [建立 Azure Resource Manager 範本的最佳做法](resource-manager-template-best-practices.md)。

