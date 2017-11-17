---
title: "在 Azure Active Directory 中將使用者指派給系統管理員角色 | Microsoft Docs"
description: "說明如何在 Azure Active Directory 中變更使用者系統管理資訊"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a1ca1a53-50d8-4bf0-ae8f-73fa1253e2d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 290f2e837cae9bdb95cf8acb486938632927ca8c
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中將使用者指派給系統管理員角色
本文說明如何在 Azure Active Directory (Azure AD) 中將系統管理角色指派給使用者。 如需有關在您組織中新增新使用者的資訊，請參閱[將新的使用者新增到 Azure Active Directory](active-directory-users-create-azure-portal.md)。 新增的使用者預設不會有系統管理員權限，但是您可以隨時指派角色給他們。

## <a name="assign-a-role-to-a-user"></a>將角色指派給使用者
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 選取 [使用者和群組]。

   ![開啟使用者管理](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. 選取 [所有使用者]。

   ![開啟所有使用者群組](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
1. 從清單中選取使用者。
2. 針對選取的使用者，選取 [目錄角色]，然後將使用者指派給 [目錄角色] 清單中的角色。 如需有關使用者和系統管理員角色的詳細資訊，請參閱 [在 Azure AD 中指派系統管理員角色](active-directory-assign-admin-roles-azure-portal.md)。

      ![將使用者指派給角色](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. 選取 [ **儲存**]。

## <a name="next-steps"></a>後續步驟
* [新增使用者](active-directory-users-create-azure-portal.md)
* [在新 Azure 入口網站中重設使用者的密碼](active-directory-users-reset-password-azure-portal.md)
* [變更使用者的工作資訊](active-directory-users-work-info-azure-portal.md)
* [管理使用者設定檔](active-directory-users-profile-azure-portal.md)
* [在 Azure AD 中刪除使用者](active-directory-users-delete-user-azure-portal.md)
