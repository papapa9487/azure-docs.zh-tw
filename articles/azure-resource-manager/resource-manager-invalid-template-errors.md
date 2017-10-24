---
title: "Azure 無效範本錯誤 | Microsoft Docs"
description: "描述如何解決無效範本錯誤。"
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
ms.openlocfilehash: e58c10cfb4cdd4ba49945e6c19845cbc957d6326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-invalid-template"></a>解決無效範本的錯誤

本文描述如何解決無效範本錯誤。

## <a name="symptom"></a>徵狀

在部署範本時您收到錯誤，指出：

```
Code=InvalidTemplate
Message=<varies>
```

錯誤訊息取決於錯誤類型。

## <a name="cause"></a>原因

此錯誤可能起因於數個不同類型的錯誤。 通常涉及範本中的語法或結構錯誤。

## <a name="solution"></a>方案

### <a name="solution-1---syntax-error"></a>解決方案 1 - 語法錯誤

如果您收到錯誤訊息指出無法驗證範本，則可能是範本中發生語法問題。

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

此錯誤很容易發生，因為範本運算式可能很複雜。 例如，儲存體帳戶的下列名稱指派包含一組方括號、三個函式、三組圓括號、一組單引號和一個屬性︰

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

如果您未提供相符的語法，範本會產生與您所要的值截然不同的值。

當您收到此類錯誤時，請仔細檢閱運算式語法。 請考慮使用 [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 或 [Visual Studio Code](resource-manager-vs-code.md) 等 JSON 編輯器，它們可以警告您有語法錯誤。

### <a name="solution-2---incorrect-segment-lengths"></a>解決方案 2 - 不正確的區段長度

資源名稱的格式不正確時，就會發生另一種無效範本錯誤。

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

根層級資源的名稱必須比資源類型少一個區段。 每個區段是以斜線區分。 在下列範例中，類型具有 2 個區段，而名稱則有 1 個區段，因此是**有效名稱**。

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

但下一個範例則 **不是有效名稱** ，因為它的區段數目和類型相同。

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

對於子資源來說，類型和名稱具有相同的區段數目。 此區段數目很合理，因為子資源的完整名稱和類型包含父資源的名稱和類型。 因此，完整名稱較完整類型仍少一個區段。

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

對於跨資源提供者套用的 Resource Manager 類型而言，要讓區段保持正確可能很棘手。 例如，將資源鎖定套用至網站需要具有四個區段的類型。 因此，名稱會是三個區段：

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

### <a name="solution-3---parameter-is-not-valid"></a>解決方案 3 - 參數無效

如果範本會指定參數允許的值，而您提供的值不是其中一個值，則會收到類似下列錯誤的訊息：

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

在範本中重複檢查允許的值，並在部署期間提供一個值。

### <a name="solution-4---circular-dependency-detected"></a>解決方案 4 - 偵測到循環相依性

當資源以防止部署啟動的方式互相相依，您會收到這個錯誤。 只要有相互相依性的組合，就會讓兩個或多個資源等候其他也正在等候的資源。 例如，resource1 相依於 resource3、resource2 相依於 resource1，而 resource3 相依於 resource2。 您通常可以移除不必要的相依性來解決此問題。
