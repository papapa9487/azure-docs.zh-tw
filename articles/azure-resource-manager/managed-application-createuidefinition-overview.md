---
title: "了解如何建立 Azure 受管理應用程式的 UI 定義 | Microsoft Docs"
description: "描述如何建立 Azure 受管理應用程式的 UI 定義"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2017
ms.author: tomfitz
ms.openlocfilehash: 077012a2fe9b4b6e6e042fdeb9be2b1d37685bc9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-createuidefinition"></a>開始使用 CreateUiDefinition
本文介紹 CreateUiDefinition 的核心概念，Azure 入口網站運用這個概念來產生使用者介面，從而建立受管理的應用程式。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition 一律會包含三個屬性︰ 

* 處理常式
* 版本
* 參數

針對受管理的應用程式，處理常式應該一律為 `Microsoft.Compute.MultiVm`，而最新的支援版本是 `0.1.2-preview`。

parameters 屬性的結構描述取決於指定的處理常式和版本之組合。 針對受管理的應用程式，支援的屬性為 `basics`、`steps` 和 `outputs`。 basics 和 steps屬性包含要在 Azure 入口網站中顯示的_元素_ - 如同文字方塊和下拉式清單。 outputs 屬性可用來將指定元素的輸出值對應至 Azure Resource Manager 部署範本的參數。

建議包含 `$schema`，但是為選用。 如果指定，`version` 的值必須符合 `$schema` URI 內的版本。

## <a name="basics"></a>基本概念
當 Azure 入口網站剖析 CreateUiDefinition 時，所產生之精靈的第一個步驟一律為 Basics 步驟。 除了顯示 `basics` 中指定的元素之外，入口網站會插入元素，以供使用者選擇部署的訂用帳戶、資源群組和位置。 一般而言，查詢整個部署參數的元素 (例如叢集的名稱或管理員認證) 都應在此步驟中。

如果元素的行為取決於使用者的訂用帳戶、資源群組或位置，就不能在 basics 中使用該元素。 例如，**Microsoft.Compute.SizeSelector** 取決於使用者的訂用帳戶和位置，來判斷可用大小的清單。 因此，**Microsoft.Compute.SizeSelector** 只能用於步驟中。 一般而言，basics 中只能使用 **Microsoft.Common** 命名空間中的元素。 儘管其他命名空間中的某些元素 (例如 **Microsoft.Compute.Credentials**) 並非取決於使用者的內容，但仍可允許使用。

## <a name="steps"></a>步驟
steps 屬性可以包含零個或多個要在 basics 之後顯示的額外步驟，其中都各包含一個或多個元素。 請考慮針對每個角色或要進行部署的應用程式層新增步驟。 例如，針對主要節點的輸入新增一個步驟，以及針對叢集中的背景工作節點新增一個步驟。

## <a name="outputs"></a>輸出
Azure 入口網站會使用 `outputs` 屬性，將 `basics` 和 `steps` 的屬性對應至 Azure Resource Manager 部署範本的參數。 這個字典的金鑰是範本參數的名稱，而值則是所參照元素的輸出物件之屬性。

## <a name="functions"></a>Functions
類似於 Azure Resource Manager 中的[範本函式](resource-group-template-functions.md) (包括語法和功能)，CreateUiDefinition 提供的函式可用於元素的輸入與輸出，以及條件等功能。

## <a name="next-steps"></a>後續步驟
CreateUiDefinition 本身有簡單的結構描述。 它實際的深度來自所有支援的元素和函式，下列文件將會加以詳細說明︰

- [元素](managed-application-createuidefinition-elements.md)
- [函式](managed-application-createuidefinition-functions.md)

以下可取得 CreateUiDefinition 目前的 JSON 結構描述︰https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json。 

可在相同位置取得更新的版本。 將 URL 的 `0.1.2-preview` 部分和 `version` 值取代為您想要使用的版本識別碼。 目前支援的版本識別碼為 `0.0.1-preview`、`0.1.0-preview`、`0.1.1-preview` 和 `0.1.2-preview`。