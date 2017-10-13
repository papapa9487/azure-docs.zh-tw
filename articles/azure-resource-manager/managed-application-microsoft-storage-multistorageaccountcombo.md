---
title: "Azure 受管理的應用程式 MultiStorageAccountCombo UI 元素 | Microsoft Docs"
description: "描述 Azure 受管理應用程式的 Microsoft.Storage.MultiStorageAccountCombo UI 元素"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.openlocfilehash: 20b98b14fbe3b2cec32452cffd57cecc4375b07a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI 元素
用來建立名稱以通用前置詞作為開頭的多個儲存體帳戶之控制項群組。 您可以在[建立 Azure 受管理應用程式](managed-application-publishing.md)時使用此元素。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>結構描述
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>備註
- `defaultValue.prefix` 的值是以一或多個整數所串連，可產生儲存體帳戶名稱的順序。 例如，如果 `defaultValue.prefix` 為 **foobar** 且 `count` 為 **2**，就會產生 **foobar1** 和 **foobar2** 儲存體帳戶名稱。 會自動驗證所產生儲存體帳戶名稱的唯一性。
- 會根據 `count`，依辭典編纂順序產生儲存體帳戶名稱。 例如，如果 `count` 為 10，儲存體帳戶名稱就會以 2 位數的整數 (01、02、03 等等) 作為結尾。
- `defaultValue.prefix` 的預設值為 **null**，`defaultValue.type` 的預設值則為 **Premium_LRS**。
- `constraints.allowedTypes` 中未指定的任何類型都會加以隱藏，`constraints.excludedTypes` 中未指定的任何類型都會加以顯示。
`constraints.allowedTypes` 和 `constraints.excludedTypes` 都是選擇性的，但不能同時使用。
- 除了產生儲存體帳戶名稱之外，`count` 還會用來設定元素的適當乘數。 它支援靜態值 (例如 **2**)，或者另一個元素的動態值 (例如 `[steps('step1').storageAccountCount]`)。 預設值為 **1**。

## <a name="sample-output"></a>範例輸出
```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>後續步驟
* 如需受管理應用程式的簡介，請參閱 [Azure 受管理的應用程式概觀](managed-application-overview.md)。
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。
