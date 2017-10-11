---
title: "無邀請將 B2B 共同作業使用者新增至 Azure Active Directory | Microsoft Docs"
description: "您可以讓來賓使用者將其他來賓使用者加入您的 Azure AD，而不需在 Azure Active Directory B2B 共同作業中兌換邀請。"
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
ms.date: 03/15/2017
ms.author: sasubram
ms.openlocfilehash: 91b9477cdb679851e7d8d2942c06999a05f64e46
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>在沒有邀請的情況下新增 B2B 共同作業來賓使用者

您可以允許使用者 (例如合作夥伴代表) 將來自合作夥伴的使用者加入您的組織，而不需要兌換邀請。 您必須做的是將合作夥伴組織在目錄中的列舉權限授與該使用者。 

授與這些權限的時機︰

1. 主組織 (例如，WoodGrove) 中的使用者邀請合作夥伴組織 (例如，Sam@litware.com) 的使用者做為「來賓」。
2. 主組織中的系統管理員設定原則以允許 Sam 識別及新增來自合作夥伴組織 (Litware) 的使用者。
3. 現在 Sam 可以將來自 Litware 的其他使用者新增到 WoodGrove 目錄、群組或應用程式，而不需要兌換邀請。 若 Sam 具有 Litware 中的適當列舉權限，這會自動發生。

### <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](active-directory-b2b-admin-add-users.md)
* [資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
* [B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)
* [B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 共同作業授權](active-directory-b2b-licensing.md)
* [針對 Azure Active Directory B2B 共同作業問題進行疑難排解](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 共同作業常見問題 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 共同作業 API 和自訂](active-directory-b2b-api.md)
* [適用於 B2B 共同作業使用者的多重要素驗證](active-directory-b2b-mfa-instructions.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)