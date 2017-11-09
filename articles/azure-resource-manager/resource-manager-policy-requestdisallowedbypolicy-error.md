---
title: "Azure 資源原則產生的 RequestDisallowedByPolicy 錯誤 | Microsoft Docs"
description: "描述 RequestDisallowedByPolicy 錯誤的原因。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 2e821c0369c6f01a7f09361c1093259429a79fa6
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure 資源原則產生的 RequestDisallowedByPolicy 錯誤

本文說明 RequestDisallowedByPolicy 錯誤的原因，其中也會提供此錯誤的解決方案。

## <a name="symptom"></a>徵狀

當您嘗試在部署期間採取動作時，可能會收到 **RequestDisallowedByPolicy** 錯誤，阻止完成該動作。 下列範例顯示錯誤：

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>疑難排解

若要擷取有關封鎖了您部署之原則的詳細資訊，請使用下列其中一種方法：

### <a name="method-1"></a>方法 1

在 PowerShell 中，提供該原則識別碼作為 `Id` 參數，以擷取有關封鎖了部署之原則的詳細資料。

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>方法 2 

在 Azure CLI 2.0 中，提供原則定義的名稱： 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>方案

基於安全性或合規性等因素，您的 IT 部門可能會強制執行資源原則，以禁止建立公用 IP 位址、網路安全性群組、使用者定義的路由或路由表。 [徵兆] 區段中的錯誤訊息會顯示名為 **regionPolicyDefinition** 的原則。 您的原則可能有不同的名稱。
若要解決這個問題，請與您的 IT 部門合作來檢閱資源原則，並決定如何依照這些原則來執行所要求的動作。

如需詳細資訊，請參閱下列文章。

- [資源原則概觀](resource-manager-policy.md)
- [透過入口網站檢視原則指派](resource-manager-policy-portal.md)
