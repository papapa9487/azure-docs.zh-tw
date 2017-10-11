---
title: "命名慣例的 Azure 資源原則 | Microsoft Docs"
description: "描述資源命名慣例的 Azure Resource Manager 原則。"
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
ms.date: 06/27/2017
ms.author: tomfitz
ms.openlocfilehash: 51b3519bbba8cb4c768bfdd7dadf92fced434f22
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="apply-resource-policies-for-names-and-text"></a>為名稱和文字套用資源原則
本主題將示範數個您可以套用的[資源原則](resource-manager-policy.md)，以建立名稱和文字慣例。 這些原則可確保資源名稱和標籤值的一致性。 

## <a name="set-naming-convention-with-wildcard"></a>使用萬用字元設定命名慣例
下列範例示範如何使用 **like** 條件所支援的萬用字元。 條件指出如果名稱符合所述模式 (namePrefix\*nameSuffix)，則拒絕要求：

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-naming-convention-with-pattern"></a>使用模式設定命名慣例

若要指定資源名稱符合模式，請使用符合條件。 下列範例需要以 `contoso` 為開頭，且包含六個額外字母的名稱︰

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-date-pattern-for-tag-value"></a>設定標籤值的日期模式

如需兩個數字、破折號、三個字母、破折號和四個數字的日期模式，請使用︰

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>後續步驟
* 定義原則規則 (如上述範例所示) 之後，您必須建立原則定義，並將它指派到某個範圍。 範圍可以是訂用帳戶、資源群組或資源。 若要透過入口網站來指派原則，請參閱[使用 Azure 入口網站來指派和管理資源原則](resource-manager-policy-portal.md)。 若要透過 REST API、PowerShell 或 Azure CLI 來指派原則，請參閱[透過指令碼來指派和管理原則](resource-manager-policy-create-assign.md)。 
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。

