---
title: "委派 Azure Active Directory B2B 共同作業邀請 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業使用者屬性是可設定的"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 78613cc978b585a98d235245194c02371f7f3849
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>委派 Azure Active Directory B2B 共同作業邀請

使用 Azure Active Directory (Azure AD) 企業對企業 (B2B) 共同作業時，您不需要是全域系統管理員即可傳送邀請。 相反地，您可以使用相關原則並將邀請委派給其角色允許他們傳送邀請的使用者。 委派來賓使用者邀請的一個重要新方式是透過「來賓邀請者」角色。

## <a name="guest-inviter-role"></a>來賓邀請者角色
我們可以將使用者指派到「來賓邀請者」角色以傳送邀請。 您不需要是全域系統管理員角色的成員即可傳送邀請。 根據預設，一般使用者也可叫用邀請 API，除非全域系統管理員已停用一般使用者的邀請功能。 使用者也可以使用 Azure 入口網站或 PowerShell 來叫用 API。

以下範例顯示如何使用 PowerShell 將使用者新增到「來賓邀請者」角色：

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>控制誰可以邀請

![控制邀請方式](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

使用 Azure AD B2B 共同作業，租用戶系統管理員可以設定下列邀請原則：

- 關閉邀請
- 只有系統管理員與「來賓邀請者」角色中使用者可以邀請
- 系統管理員、來賓邀請者角色與成員可以邀請
- 所有使用者 (包括來賓) 都可以邀請

根據預設，租用戶會設定為 #4。 (所有使用者 (包括來賓) 都可以邀請 B2B 使用者。)

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 共同作業使用者屬性](active-directory-b2b-user-properties.md)
* [將 B2B 共同作業使用者新增至角色](active-directory-b2b-add-guest-to-role.md)
* [動態群組與 B2B 共同作業](active-directory-b2b-dynamic-groups.md)
* [B2B 共同作業程式碼與 PowerShell 範例](active-directory-b2b-code-samples.md)
* [設定適用於 B2B 共同作業的 SaaS 應用程式](active-directory-b2b-configure-saas-apps.md)
* [B2B 共同作業使用者權杖](active-directory-b2b-user-token.md)
* [B2B 共同作業使用者宣告對應](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共用](active-directory-b2b-o365-external-user.md)
* [B2B 共同作業目前的限制](active-directory-b2b-current-limitations.md)
