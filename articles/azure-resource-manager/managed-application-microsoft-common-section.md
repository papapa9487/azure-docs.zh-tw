---
title: "Azure 受管理的應用程式 Section UI 元素 | Microsoft Docs"
description: "描述 Azure 受管理應用程式的 Microsoft.Common.Section UI 元素"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 34c0f2f88e6af5a0f822ec116e7e2334e4e29e8d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017

---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI 元素
將標題下方一個或多個元素進行群組的控制項。 您可以在[建立 Azure 受管理應用程式](managed-application-publishing.md)時使用此元素。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>結構描述
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>備註
- `elements` 必須包含至少一個元素，且可以包含 `Microsoft.Common.Section` 以外的所有元素類型。
- 此元素不支援 `toolTip` 屬性。

## <a name="sample-output"></a>範例輸出
若要存取 `elements` 中的元素輸出值，可使用 [basics()](managed-application-createuidefinition-functions.md#basics) 或 [steps()](managed-application-createuidefinition-functions.md#steps) 函式和點標記法︰

```json
basics('section1').element1
```

`Microsoft.Common.Section` 類型的元素本身沒有任何輸出值。

## <a name="next-steps"></a>後續步驟
* 如需受管理應用程式的簡介，請參閱 [Azure 受管理的應用程式概觀](managed-application-overview.md)。
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

