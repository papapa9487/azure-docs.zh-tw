---
title: "Azure 父代資源錯誤 | Microsoft Docs"
description: "描述當使用父代資源時如何解決錯誤。"
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
ms.openlocfilehash: e59147c4ac18f730f27b9d4aa9c008f219881065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-parent-resources"></a>解決父代資源的錯誤

這篇文章描述當部署相依於父代資源的資源時，可能會遇到的錯誤。

## <a name="symptom"></a>徵狀

部署是另一個資源子系的資源時，您可能會遇到下列錯誤：

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>原因

當某資源為另一個資源的子系資源時，父代資源必須在建立子系資源之前就存在。 子資源的名稱包含父系名稱。 例如，SQL Database 可能定義如下︰

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

不過，如果您未在伺服器上指定相依性，可能可以在部署伺服器之前啟動資料庫部署。

## <a name="solution"></a>方案

若要解決這個錯誤，請包含相依性。

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

如需詳細資訊，請參閱[定義 Azure Resource Manager 範本中部署資源的順序](resource-group-define-dependencies.md)。