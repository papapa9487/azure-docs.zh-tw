---
title: "Azure 受管理的應用程式建立 UI 定義函式 | Microsoft Docs"
description: "描述建構 Azure 受管理應用程式的 UI 定義時要使用的函式"
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
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 635e44a7ec6f9244f5fe75eb5ad947cdd8ae59a4
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017

---
# <a name="createuidefinition-elements"></a>CreateUiDefinition 元素
本文描述 CreateUiDefinition 所有支援元素的結構描述和屬性。 您可以在[建立 Azure 受管理應用程式](managed-application-publishing.md)時使用這些元素。 大部分元素的結構描述如下所示︰

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Keep calm and visit the [Azure Portal](portal.azure.com).",
  "constraints": {},
  "options": {},
  "visible": true
}
```
| 屬性 | 必要 | 說明 |
| -------- | -------- | ----------- |
| 名稱 | 是 | 要參考元素特定執行個體的內部識別碼。 元素名稱的最常見用法是在 `outputs`，其中指定元素的輸出值會對應到範本的參數。 您也可以使用它，將元素的輸出值繫結至另一個元素的 `defaultValue`。 |
| 類型 | 是 | 要呈現元素的 UI 控制項。 如需支援類型的清單，請參閱[元素](#elements)。 |
| 標籤 | 是 | 元素的顯示文字。 某些元素類型會包含多個標籤，因此值可能是包含多個字串的物件。 |
| defaultValue | 否 | 元素的預設值。 某些元素類型支援複雜的預設值，因此值可能是物件。 |
| 工具提示 | 否 | 要顯示在元素之工具提示的文字。 類似於 `label`，某些元素可支援多個工具提示字串。 您可以使用 Markdown 語法將內嵌連結進行內嵌。
| 條件約束 | 否 | 用於自訂元素驗證行為的一個或多個屬性。 支援的條件約束屬性會依元素類型而有所不同。 某些元素類型不支援自訂驗證行為，因此沒有任何 constraints 屬性。 |
| options | 否 | 自訂元素行為的其他屬性。 類似於 `constraints`，支援的屬性會依元素類型而有所不同。 |
| 可見 | 否 | 指出是否要顯示元素。 如果為 `true`，就會顯示元素和適用的子元素。 預設值為 `true`。 使用[邏輯函式](managed-application-createuidefinition-functions.md#logical-functions)以動態方式控制這個屬性的值。

## <a name="elements"></a>元素

每個元素的文件中包含元素行為的 UI 範例、結構描述、註解 (通常是關於驗證和支援的自訂) 以及範例輸出。

- [Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)
- [Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](managed-application-microsoft-common-section.md)
- [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>後續步驟
* 如需受管理應用程式的簡介，請參閱 [Azure 受管理的應用程式概觀](managed-application-overview.md)。
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。

