---
title: "要求使用者指派 - Azure AD | Microsoft Docs'"
description: "如何要求 Azure 應用程式的使用者指派。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 30b78cba-1e0f-472f-8314-f2250a9b91c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: kgremban
robots: noindex
ms.openlocfilehash: 079b806c041a4a21e9350342867aee581c57bf45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-and-applications-require-user-assignment"></a>Azure AD 和應用程式：要求使用者指派
## <a name="requiring-user-assignment"></a>要求使用者指派
1. 使用系統管理員帳戶登入 Azure 入口網站。
2. 在主功能表中按一下 [ **所有項目** ] 項目。
3. 選擇您要為應用程式使用的目錄。
4. 按一下 [ **應用程式** ] 索引標籤。
5. 從與此目錄相關聯的應用程式清單中選取應用程式。
6. 按一下 [設定]  索引標籤。
7. 將 [ **需要使用者指派才能存取應用程式** ] 切換成 [是]。
8. 按一下畫面底部的 [ **儲存** ] 按鈕。

現在您需要將使用者及/或群組指派給應用程式。 請參閱[將使用者指派給應用程式](active-directory-applications-guiding-developers-assigning-users.md)和[將群組指派給應用程式](active-directory-applications-guiding-developers-assigning-groups.md)。

## <a name="next-steps"></a>後續步驟
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
