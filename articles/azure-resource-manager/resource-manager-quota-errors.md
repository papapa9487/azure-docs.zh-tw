---
title: "Azure 配額錯誤 |Microsoft Docs"
description: "描述如何解決資源配額錯誤。"
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
ms.openlocfilehash: 6a8024a12c4a79e92e37df0a56b6e6bd0cb6a8d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-resource-quotas"></a>解決資源配額的錯誤

本文描述當部署資源時，可能會遇到的配額錯誤。

## <a name="symptom"></a>徵狀

如果您部署的範本會建立超過 Azure 配額的資源，您會看到類似以下的部署錯誤：

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

或者，您可能會看到：

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>原因

配額在每個資源群組、訂用帳戶、帳戶和其他範圍套用。 例如，您的訂用帳戶可能設定為要限制區域的核心數目。 如果您嘗試部署超過允許核心數目的虛擬機器，您會收到錯誤訊息指出已超過配額。
如需完整的配額資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../azure-subscription-service-limits.md)。

## <a name="solution"></a>方案

### <a name="solution-1"></a>解決方案 1

針對 Azure CLI，使用 `az vm list-usage` 命令以找出虛擬機器配額。

```azurecli
az vm list-usage --location "South Central US"
```

它會傳回：

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="solution-2"></a>解決方案 2

針對 PowerShell，使用 **Get-AzureRmVMUsage** 命令以找出虛擬機器配額。

```powershell
Get-AzureRmVMUsage -Location "South Central US"
```

它會傳回：

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

### <a name="solution-3"></a>解決方案 3

如果您需要增加配額限制，請移至入口網站，並提出支援問題，以針對您想要部署的區域提高配額。

> [!NOTE]
> 請記住，對於資源群組，配額適用於每個個別區域，而不是整個訂用帳戶。 如果您需要在美國西部部署 30 個核心，就必須要求在美國西部擁有 30 個資源管理員核心。 如果您需要在任何具有存取權限的區域中部署 30 個核心，就應該要求在所有區域中擁有 30 個 Resource Manager 核心。
>
>