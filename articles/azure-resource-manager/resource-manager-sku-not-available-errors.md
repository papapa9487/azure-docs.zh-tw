---
title: "Azure SKU 無法使用錯誤 | Microsoft Docs"
description: "描述如何針對部署期間 SKU 無法使用錯誤進行疑難排解。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: 25cea4ae23471d182105ca3f720aaf74f81bf8c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-sku-not-available"></a>解決 SKU 無法使用的錯誤

本文說明如何解決 **SkuNotAvailable** 錯誤。

## <a name="symptom"></a>徵狀

在部署資源 (通常是虛擬機器) 時，您可能會收到下列錯誤碼和錯誤訊息︰

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>原因

當您選取的資源 SKU (例如 VM 大小) 不適用於您選取的位置時，您會收到這個錯誤。

## <a name="solution"></a>方案

若要解決此問題，您需要判斷區域中有哪些 SKU。 您可以使用 PowerShell、入口網站或 REST 作業來尋找可用的 SKU。

### <a name="solution-1"></a>解決方案 1

在 PowerShell 中使用 [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) 命令。 依據位置篩選結果。 您必須擁有最新版 PowerShell 才能執行此命令。

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("southcentralus")}
```

結果包括該位置的 SKU 清單，以及該 SKU 的任何限制。

```powershell
ResourceType                Name      Locations Restriction                      Capability Value
------------                ----      --------- -----------                      ---------- -----
availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
virtualMachines      Standard_A0 southcentralus
virtualMachines      Standard_A1 southcentralus
virtualMachines      Standard_A2 southcentralus
```

### <a name="solution-2"></a>解決方案 2

在 Azure CLI 中，使用 `az vm list-skus` 命令。 接著可以使用 `grep` 或類似的公用程式來篩選輸出。

```bash
$ az vm list-skus --output table
ResourceType      Locations           Name                    Capabilities                       Tier      Size           Restrictions
----------------  ------------------  ----------------------  ---------------------------------  --------  -------------  ---------------------------
availabilitySets  eastus              Classic                 MaximumPlatformFaultDomainCount=3
avilabilitySets   eastus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Aligned                 MaximumPlatformFaultDomainCount=3
```

### <a name="solution-3"></a>解決方案 3

使用[入口網站](https://portal.azure.com)。 登入入口網站，並透過介面新增資源。 當您設定值時，您會看到該資源可用的 SKU。 您不需要完成部署。

![可用的 SKU](./media/resource-manager-sku-not-available-errors/view-sku.png)

### <a name="solution-4"></a>解決方案 4

針對虛擬機器使用 REST API。 傳送下列要求：

```HTTP 
GET
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
```

它會以下列格式傳回可用的 SKU 和區域︰

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

如果您在該區域或符合您業務需求的替代區域中找不到適當的 SKU，請向 Azure 支援服務提交 [SKU 要求](https://aka.ms/skurestriction)。