---
title: "如何移除使用者的應用程式存取 | Microsoft Docs"
description: "了解如何移除使用者的應用程式存取"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 497429e7bf62f7e1d67ea429d6b858725f843688
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>如何移除使用者的應用程式存取

本文協助您了解如何移除使用者的應用程式存取。

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>我想要移除特定使用者或群組的應用程式指派

若要移除使用者或群組的應用程式指派，請依照[在 Azure Active Directory 中從企業應用程式移除使用者或群組指派](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)文件列出的步驟執行。

.## 我想要讓每個使用者都無法存取應用程式

若要讓所有使用者都無法登入應用程式，請依照[在 Azure Active Directory 中停用使用者登入企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)文件列出的步驟執行。

## <a name="i-want-to-delete-an-application-entirely"></a>我想要完全刪除應用程式

若要**刪除應用程式**，請遵循下列指示：

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**或 **共同管理員**身分登入

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式] 以檢視所有應用程式的清單。

   * 若在這裡沒看到您要的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  選取您要刪除的應用程式。

7.  應用程式載入後，從頂端應用程式的 [概觀] 刀鋒視窗按一下 [刪除] 圖示。

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>我想要停用任何應用程式的所有未來使用者同意作業

停用整個目錄的使用者同意會阻止使用者同意任何應用程式。 系統管理員仍然可以代表使用者行使同意。 若要進一步了解應用程式同意，以及為什麼您想要或不不想這樣做，請參閱[了解使用者和系統管理員同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)。

若要**停用整個目錄中的所有未來使用者同意作業**，請遵循下列指示：

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。

2.  按一下左邊主瀏覽功能表底部的 [更多服務]，以開啟 [Azure Active Directory 延伸模組]。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4.  按一下瀏覽功能表中的 [使用者和群組]。

5.  按一下 [使用者設定]。

6.  將 [使用者可以允許應用程式存取其資料] 切換開關設為 [否]，並按一下 [儲存] 按鈕，以停用所有未來的使用者同意作業。


# <a name="next-steps"></a>後續步驟
[管理應用程式的存取](active-directory-managing-access-to-apps.md)
