---
title: "如何新增或移除使用者角色 | Microsoft Docs"
description: "了解如何使用 Azure Active Directory Privileged Identity Management 應用程式來將角色新增到特殊權限身分識別。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 6a47ced8-cf34-4ce8-bea2-e4fc548cfe22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim;oldportal;it-pro;
ms.openlocfilehash: 3ac07bb7b070f44595c099a454b3d0dbc66126c9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management：如何新增或移除使用者角色
使用 Azure Active Directory (AD)，全域系統管理員 (或公司系統管理員) 可以更新哪些使用者獲 **永久** 指派 Azure AD 的角色。 做法是使用 PowerShell Cmdlet，如 `Add-MsolRoleMember` 和 `Remove-MsolRoleMember`。 或者可以依 [在 Azure Active Directory (Azure AD) 中指派系統管理員角色](active-directory-assign-admin-roles.md)中所述，使用 Azure 傳統入口網站。

Azure AD Privileged Identity Management 應用程式也允許特殊權限角色管理員指派永久的角色。 此外，特殊權限角色系統管理員可以讓使用者 **有資格**扮演系統管理員角色。 合格系統管理員可在需要時啟用角色，而在完成工作之後，其權限就隨即失效。

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>在 Azure 入口網站管理使用 PIM 的角色
在您的組織中，您可以將使用者指派給 Azure AD、Office 365 及其他 Microsoft 服務和應用程式中不同的系統管理角色。  如需可用角色的詳細資訊，請參閱 [Azure AD PIM 中的角色](active-directory-privileged-identity-management-roles.md)。

若要新增或移除使用者使用 Privileged Identity Management 的角色，請啟動 PIM 儀表板。 然後按一下 [系統管理員角色的使用者]  按鈕，或從角色資料表中選取特定角色 (例如全域系統管理員)。

> [!NOTE]
> 如果您還沒有在 Azure 入口網站中啟用 PIM，請移至 [開始使用 Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) 以取得詳細資訊。

如果您想要讓其他使用者存取 PIM 本身，請參閱 [How to give access to manage Azure AD Privileged Identity Management (如何提供管理  Azure AD Privileged Identity Management 的存取權)](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)，進一步了解 PIM 需要使用者具有哪些角色。

## <a name="add-a-user-to-a-role"></a>將使用者新增至角色
1. 在 [Azure 入口網站](https://portal.azure.com/)中，於儀表板上選取 [Azure AD Privileged Identity Management] 圖格。
2. 選取 [管理特殊權限角色] 。
3. 在 [角色摘要]  表格中，選取您想要管理的角色。
4. 在 [角色] 刀鋒視窗中，選取 [加入] 。
5. 按一下 [選取使用者]，然後在 [選取使用者] 刀鋒視窗上搜尋使用者。  
6. 從搜尋結果清單中選取使用者，然後按一下 [完成] 。
7. 按一下 [確定]  以儲存您的選取項目。 您已選取的使用者將會在清單中顯示為該角色的合格使用者。

> [!NOTE]
> 角色中的新使用者預設僅是該角色的合格使用者。 如果想要讓角色變成永久，請按一下清單中的使用者。 該使用者的資訊即會出現在新的刀鋒視窗中。 在使用者資訊功能表中，選取 [設為永久]  。  
> 如果使用者無法註冊的 Azure Multi-factor Authentication (MFA)，或使用 Microsoft 帳戶 (通常@outlook.com)，您需要自行永久的所有角色。 系統會要求合格系統管理員在啟用啟用期間註冊 MFA。

既然使用者有資格扮演某個角色，請讓他們知道他們可以根據[如何啟用或停用角色](active-directory-privileged-identity-management-how-to-activate-role.md)中的指示來啟用角色。

## <a name="remove-a-user-from-a-role"></a>從角色移除使用者
您可以將使用者從合格角色指派中移除，但請務必一律至少保留一個永久全域系統管理員使用者。

請遵循下列步驟移除角色中的特定使用者︰

1. 在 Azure AD PIM 儀表板中選取一個角色，或按一下 [系統管理員角色的使用者]  按鈕，來瀏覽至角色清單中的角色。
2. 按一下使用者清單中的使用者。
3. 按一下 [移除] 。 會出現訊息要求您確認。
4. 按一下 [是]  ，即可從使用者中移除角色。

如果您不確定哪些使用者仍然需要其角色指派，您可以 [開始角色的存取權檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)。

## <a name="next-steps"></a>後續步驟
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

