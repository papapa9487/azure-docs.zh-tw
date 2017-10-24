---
title: "找不到 Azure 資源錯誤 | Microsoft Docs"
description: "描述如何在找不到資源時解決錯誤。"
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
ms.openlocfilehash: c76c965c43ca8217faa9488c01975ce09a21daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>解決找不到 Azure 資源的錯誤

本文描述在部署期間找不到資源時可能會遇到的錯誤。 

## <a name="symptom"></a>徵狀

當您的範本包含的資源名稱無法解析時，您會收到類似以下的錯誤：

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

如果您嘗試對無法解析的資源使用 [reference](resource-group-template-functions-resource.md#reference) 或 [listKeys](resource-group-template-functions-resource.md#listkeys) 函式，您會收到下列錯誤：

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>原因

資源管理員需要擷取資源的屬性，但是無法識別您的訂用帳戶中的資源。

## <a name="solution"></a>方案

### <a name="solution-1"></a>解決方案 1

如果您嘗試在範本中部署遺漏的資源，請檢查是否需要新增相依性。 如果可能，Resource Manager 會以平行方式建立資源，將部署最佳化。 如果一個資源必須在另一個資源之後部署，您就必須在範本中使用 **dependsOn** 元素來建立與該另一個資源的相依性。 例如，在部署 Web 應用程式時，App Service 方案必須存在。 如果您沒有指定該 Web 應用程式相依於 App Service 方案，Resource Manager 會同時建立這兩個資源。 您收到錯誤訊息，指出找不到 App Service 方案的資源，因為嘗試在 Web 應用程式上設定屬性時它尚未存在。 您會設定 Web 應用程式的相依性，以避免此錯誤。

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

如需針對相依性錯誤進行疑難排解的建議，請參閱[檢查部署順序](resource-manager-troubleshoot-tips.md#check-deployment-sequence)。

### <a name="solution-2"></a>解決方案 2

當資源存在於與部署目標不同的資源群組時，使用 [resourceId 函式](resource-group-template-functions-resource.md#resourceid)以取得資源的完整名稱。

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

### <a name="solution-3"></a>解決方案 3

尋找包含 [reference](resource-group-template-functions-resource.md#reference) 函式的運算式。 您提供的值會根據資源是否位於相同範本、資源群組及訂用帳戶而有所不同。 再次確認您為案例提供必要的參數值。 如果資源位於不同資源群組中，請提供完整資源識別碼。 例如，若要參考另一個資源群組的儲存體帳戶，請使用：

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```