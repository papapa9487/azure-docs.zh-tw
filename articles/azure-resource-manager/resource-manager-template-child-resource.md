---
title: "在 Azure 範本中定義子資源 | Microsoft Docs"
description: "示範如何在 Azure Resource Manager 範本中設定資源類型和子資源名稱"
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
ms.date: 06/01/2017
ms.author: tomfitz
ms.openlocfilehash: 5b6ce5526f354008eb4a697deec737876f22391f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="set-name-and-type-for-child-resource-in-resource-manager-template"></a>在 Resource Manager 範本中設定子資源的名稱和類型
建立範本時，您經常需要包含與父資源相關的子資源。 例如，您的範本可能包含 SQL Server 和資料庫。 SQL Server 是父系的資源，而資料庫則為子資源。 

子資源類型的格式如下︰`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

子資源名稱的格式如下︰`{parent-resource-name}/{child-resource-name}`

不過，根據子資源是以巢狀方式內嵌於父資源中，還是本身位於最上層而定，您在範本中指定類型和名稱的方式會有所不同。 本主題說明如何處理這兩種方式。

當建構資源的完整參考時，要從類型和名稱合併區段的順序並非只是將兩個串連。  相反地，在命名空間之後，使用從最特定到最不特定的一連串*類型/名稱*組：

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

例如：

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` 為正確 `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` 為不正確

## <a name="nested-child-resource"></a>巢狀子資源
定義子資源的最簡單方式是將其以巢狀方式內嵌於父資源中。 下列範例顯示以巢狀方式內嵌於 SQL Server 中的 SQL Database。

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

子資源的類型已設為 `databases`，但是其完整資源類型為 `Microsoft.Sql/servers/databases`。 您未提供 `Microsoft.Sql/servers/`，因為它被認為是來自父資源類型。 子資源名稱會設為 `exampledatabase`，但是完整名稱包含父系名稱。 您未提供 `exampleserver`，因為它被認為是來自父資源。

## <a name="top-level-child-resource"></a>最上層的子資源
您可以定義最上層的子資源。 如果父資源並未部署在相同範本中，或者想要使用 `copy` 來建立多個子資源，您可以使用此方法。 使用這個方法，您必須提供完整資源類型，並將父資源名稱納入子資源名稱中。

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

資料庫是伺服器的子資源，即使兩者都定義於範本中的相同層級上。

## <a name="next-steps"></a>後續步驟
* 如需如何建立範本的建議，請參閱 [建立 Azure Resource Manager 範本的最佳做法](resource-manager-template-best-practices.md)。
* 如需建立多個子資源的範例，請參閱[在 Azure Resource Manager 範本中部署資源的多個執行個體](resource-group-create-multiple.md)。
