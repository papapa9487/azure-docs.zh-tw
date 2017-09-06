---
title: "標籤的 Azure 資源原則 | Microsoft Docs"
description: "提供在資源上管理標籤之資源原則的範例"
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
ms.date: 08/24/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 469bd8d637337e5900ea84c6bfaf88064695fb7e
ms.contentlocale: zh-tw
ms.lasthandoff: 08/25/2017

---
# <a name="apply-resource-policies-for-tags"></a>套用標籤的資源原則

本主題提供可套用以確保一致地在資源上使用標籤的通用原則規則。

使用現有的資源將標籤原則套用到資源群組或訂用帳戶不會追溯性地將原則套用到這些資源。 若要在這些資源上強制執行原則，請觸發現有資源的更新。 本文包含觸發更新的 PowerShell 範例。

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>請確定資源群組中的所有資源都有標籤/值

常見的需求是資源群組中的所有資源都有特定的標籤和值。 通常需要這項需求來依部門追蹤成本。 必須符合下列條件：

* 必要的標籤和值會附加至沒有任何標籤的新增和更新的資源。
* 無法從任何現有的資源移除必要的標籤和值。

將下列兩個內建原則套用至資源群組來完成這項需求。

| ID | 說明 |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | 使用者未指定時，套用必要的標籤和其預設值。 |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | 強制必要的標籤和其值。 |

### <a name="powershell"></a>PowerShell

下列 PowerShell 指令碼會將兩個內建的原則定義指派給資源群組。 執行指令碼之前，請將所有必要的標籤指派給資源群組。 資源群組中每個標籤對群組中的資源而言都是必要的。 若要指派給您的訂用帳戶中的所有資源群組，取得資源群組時請勿提供 `-Name` 參數。

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

在指派原則之後，您可以對所有現有的資源觸發更新，以強制您加入的標籤原則。 下列指令碼會保留資源中現存的任何其他標籤︰

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="require-tags-for-a-resource-type"></a>需要資源類型的標籤
下列範例說明如何建立巢狀邏輯運算子，只為指定的資源類型 (在此案例中為儲存體帳戶) 要求應用程式標籤。

```json
{
    "if": {
        "allOf": [
          {
            "not": {
              "field": "tags",
              "containsKey": "application"
            }
          },
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          }
        ]
    },
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>需要標籤
下列原則會拒絕沒有包含 "costCenter" 索引鍵標籤的要求 (可套用任何值)：

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="next-steps"></a>後續步驟
* 定義原則規則 (如上述範例所示) 之後，您必須建立原則定義，並將它指派到某個範圍。 範圍可以是訂用帳戶、資源群組或資源。 若要透過入口網站來指派原則，請參閱[使用 Azure 入口網站來指派和管理資源原則](resource-manager-policy-portal.md)。 若要透過 REST API、PowerShell 或 Azure CLI 來指派原則，請參閱[透過指令碼來指派和管理原則](resource-manager-policy-create-assign.md)。
* 如需資源原則的簡介，請參閱[資源原則概觀](resource-manager-policy.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。


