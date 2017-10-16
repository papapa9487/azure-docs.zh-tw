---
title: "如何授與對 Privileged Identity Management 的存取權 - Azure | Microsoft Docs"
description: "了解如何使用 Azure Active Directory Privileged Identity Management 擴充功能為使用者加入角色，以便他們可以管理 PIM。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: d4c53b53-2b37-41e6-813c-96ec08a1c897
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: aeaefb484b29da6e89c2c3c650a79a881b3fa5b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>授與存取權以管理 Azure AD Privileged Identity Management
為組織啟用 Azure AD Privileged Identity Management (PIM) 的全域系統管理員會自動取得角色指派及 PIM 的存取權。 不過，預設不會有任何其他人取得寫入存取權，包括其他全域系統管理員。 其他全域系統管理員、安全性管理員及安全性讀取者具有 Azure AD PIM 的唯讀存取權。 若要授與對 PIM 的存取權，第一位使用者可以將其他使用者指派給「特殊權限角色管理員」  角色。 這項指派必須在 PIM 內完成，而且無法透過 PowerShell 或其他入口網站變更。

> [!NOTE]
> 管理 Azure AD PIM 需要 Azure MFA。 因為 Microsoft 帳戶無法註冊 Azure MFA，所以使用 Microsoft 帳戶登入的使用者無法存取 Azure AD PIM。
> 
> 

請確保特殊權限角色管理員角色中永遠至少有兩位使用者，以防一位使用者遭到鎖定或他們的帳戶遭刪除。

## <a name="give-another-user-access-to-manage-pim"></a>授與另一位使用者存取權以管理 PIM
1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後在儀表板上選取 **Azure AD Privileged Identity Management** 應用程式。
2. 選取 [管理特殊權限角色]  >  [特殊權限角色管理員]  >  [新增]。
   
    ![新增特殊權限角色管理員 - 螢幕擷取畫面][1]
3. 在 [新增受管理的使用者] 刀鋒視窗上，步驟 1 已經完成。 請選取步驟 2 [選取使用者]  ，然後搜尋您想要新增的使用者。
   
    ![選取使用者 - 螢幕擷取畫面][2]
4. 從搜尋結果中選取使用者，然後按一下 [完成] 。
5. 按一下 [確定]  以儲存您的選取項目。 您選取的使用者將會出現在特殊權限角色管理員清單中。
   
   * 每當您指派新角色給某位使用者時，系統都會自動將他們設定成符合啟用該角色的資格。 如果您想要將使用者在該角色中設為永久，請按一下清單中的該使用者。 在使用者資訊功能表中選取 [設為永久]  。
6. 將 [開始使用 Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)連結傳送給使用者。

## <a name="remove-another-users-access-rights-for-managing-pim"></a>移除其他使用者管理 PIM 的存取權限
將某人自特殊權限角色管理員角色移除之前，請確保該角色仍有兩位指派的使用者。

1. 在 PIM 儀表板中，按一下 [特殊權限角色管理員] 角色。  該角色的目前使用者清單隨即出現。
2. 按一下使用者清單中的使用者。
3. 按一下 [移除] 。  您將會看到確認訊息。
4. 按一下 [是]  以將使用者從角色中移除。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
