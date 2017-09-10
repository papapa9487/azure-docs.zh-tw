---
title: "儲存體帳戶的 Azure 資源原則 | Microsoft Docs"
description: "描述 Azure Resource Manager 管理儲存體帳戶部署的的原則。"
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
ms.date: 07/05/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6612ee61f5c50e743241b92030660cea7ae7094d
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="apply-resource-policies-to-storage-accounts"></a>將資源原則套用至儲存體帳戶
本主題將示範數個您可以套用到 Azure 儲存體帳戶的[資源原則](resource-manager-policy.md)。 這些原則可確保您組織中所部署之儲存體帳戶的一致性。 

## <a name="define-permitted-storage-account-types"></a>定義允許的儲存體帳戶類型

下列原則會限制可部署的[儲存體帳戶類型](../storage/common/storage-redundancy.md)︰

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

包含接受允許 SKU 參數的類似原則規則可做為內建的原則定義。 內建的原則具有 `/providers/Microsoft.Authorization/policyDefinitions/7433c107-6db4-4ad1-b57a-a76dce0154a1` 的資源識別碼。 

## <a name="define-permitted-access-tier"></a>定義允許的存取層

下列原則指定可針對儲存體帳戶指定的[存取層](../storage/blobs/storage-blob-storage-tiers.md)類型︰

```json
{
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
}
```

## <a name="ensure-encryption-is-enabled"></a>請確定已啟用加密

下列原則要求所有儲存體帳戶啟用[儲存體服務加密](../storage/common/storage-service-encryption.md)：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

此原則規則也可做為具有資源識別碼 `/providers/Microsoft.Authorization/policyDefinitions/7c5a74bf-ae94-4a74-8fcf-644d1e0e6e6f` 的內建原則定義。

## <a name="next-steps"></a>後續步驟
* 定義原則規則 (如上述範例所示) 之後，您必須建立原則定義，並將它指派到某個範圍。 範圍可以是訂用帳戶、資源群組或資源。 若要透過入口網站來指派原則，請參閱[使用 Azure 入口網站來指派和管理資源原則](resource-manager-policy-portal.md)。 若要透過 REST API、PowerShell 或 Azure CLI 來指派原則，請參閱[透過指令碼來指派和管理原則](resource-manager-policy-create-assign.md)。 
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。


