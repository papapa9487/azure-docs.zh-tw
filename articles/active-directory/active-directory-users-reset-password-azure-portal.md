---
title: "Azure Active Directory 中的密碼重設 | Microsoft Docs"
description: "系統管理員在 Azure Active Directory 中為使用者起始的密碼重設"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: bea082081e3f3f52ba78188903a9536fe9de9392
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>在 Azure Active Directory 中重設使用者的密碼

當使用者發生忘記密碼、被鎖定等情況時，系統管理員可能必須重設該使用者的密碼。 接下來的步驟將逐步引導您完成重設使用者密碼的程序。

## <a name="how-to-reset-the-password-for-a-user"></a>如何重設使用者的密碼

1. 使用具備可重設使用者密碼之目錄權限的帳戶來登入 [Azure AD 系統管理中心](https://aad.portal.azure.com)。
2. 選取 [Azure Active Directory] > [使用者和群組] > [所有使用者]。
3. 選取您想要重設密碼的使用者。
2. 針對選取的使用者，選取 [重設密碼]。

    ![從 Azure AD 中的使用者設定檔重設使用者密碼](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. 在 [重設密碼] 上，選取 [重設密碼]。
7. 系統會顯示一個暫時密碼，您可以接著將此密碼提供給使用者。 系統會要求使用者在下次登入時變更其密碼。 

   > [!NOTE]
   > 這個暫時密碼並沒有到期時間，因此這個密碼會一直有效，直到使用者登入後，系統就會強制使用者變更密碼。 

## <a name="next-steps"></a>後續步驟
* [新增使用者](active-directory-users-create-azure-portal.md)
* [將系統管理員角色指派給使用者](active-directory-users-assign-role-azure-portal.md)
* [管理使用者設定檔](active-directory-users-profile-azure-portal.md)
* [刪除 Azure AD 中的使用者](active-directory-users-delete-user-azure-portal.md)
