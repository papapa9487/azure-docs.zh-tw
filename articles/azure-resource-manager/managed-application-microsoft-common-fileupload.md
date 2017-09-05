---
title: "Azure 受管理的應用程式 FileUpload UI 元素 | Microsoft Docs"
description: "描述 Azure 受管理應用程式的 Microsoft.Common.FileUpload UI 元素"
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
ms.openlocfilehash: 217e9e63eb7cd198f70cee42b418867df9f1f993
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017

---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload UI 元素
控制項可讓使用者指定要上傳的一個或多個檔案。 您可以在[建立 Azure 受管理應用程式](managed-application-publishing.md)時使用此元素。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>結構描述
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>備註
- `constraints.accept` 會指定在瀏覽器的 [檔案] 對話方塊中顯示的檔案類型。 請參閱 [HTML5 規格](http://www.w3.org/TR/html5/forms.html#attr-input-accept) 以取得允許的值。 預設值為 **null**。
- 如果將 `options.multiple` 設為 **true**，使用者就允許在瀏覽器的 [檔案] 對話方塊中選取一個以上的檔案。 預設值為 **false**。
- 這個元素會根據 `options.uploadMode` 的值，支援兩種檔案上傳模式。 如果是指定 **file**，輸出就會包含檔案內容作為 blob。 如果是指定 **url**，檔案就會上傳至暫存位置，而輸出會包含 blob 的 URL。 24 小時之後，就會清除暫存 blob。 預設值為 **file**。
- `options.openMode` 的值會決定讀取檔案的方式。 如果預期是純文字檔案，則指定為 **text**；否則，指定為 **binary**。 預設值為 **text**。
- 如果將 `options.uploadMode` 設為 **file**，且將 `options.openMode` 設為 **binary**，輸出就會是 base64 編碼。
- `options.encoding` 會指定讀取檔案時要使用的編碼方式。 預設值為 **UTF-8**，且僅在 `options.openMode` 設為 **text** 時才會使用。

## <a name="sample-output"></a>範例輸出
如果 options.multiple 為 false 且 options.uploadMode 為 file，輸出就會包含檔案內容作為 JSON 字串：

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

如果 options.multiple 為 true 且 options.uploadMode 為 file，輸出就會包含檔案內容作為 JSON 陣列：

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

如果 options.multiple 為 false 且 options.uploadMode 為 URL，輸出就會包含 URL 作為 JSON 字串：

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

如果 options.multiple 為 true 且 options.uploadMode 為 URL，輸出就會包含 URL 清單作為 JSON 陣列：
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

測試 CreateUiDefinition 時，某些瀏覽器 (例如 Google Chrome) 會將瀏覽器主控台的 Microsoft.Common.FileUpload 元素所產生的 URL 截斷。 您可能需要以滑鼠右鍵按一下個別連結來複製完整的 URL。


## <a name="next-steps"></a>後續步驟
* 如需受管理應用程式的簡介，請參閱 [Azure 受管理的應用程式概觀](managed-application-overview.md)。
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

