---
title: "比較 Azure Active Directory 的 B2B 共同作業和 B2C | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業和 Azure AD B2C 有何不同？"
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
ms.openlocfilehash: 44cbbc149787a2d6cf2e0e8750b98d33b52f6136
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>比較 Azure Active Directory 的 B2B 共同作業和 B2C

Azure Active Directory (Azure AD) B2B 共同作業和 Azure AD B2C 皆可讓您在 Azure AD 中使用外部使用者進行操作。 但它們究竟差在哪兒？


B2B 共同作業 |     Azure AD B2C 獨立供應項目
-------- | --------
適用於︰想要能夠驗證來自合作夥伴組織 (無論是否為識別提供者) 之使用者的組織。 | 適用於︰邀請您的行動和 Web 應用程式的客戶 (無論是個人、機構或組織客戶) 加入您的 Azure AD。
支援的身分識別︰本機有工作或學校帳戶的員工、本機有工作或學校帳戶的合作夥伴、或任何電子郵件地址。 即將支援直接聯盟。  | 支援的身分識別︰具有本機應用程式帳戶的取用者使用者 (任何電子郵件地址或使用者名稱) 或任何以直接聯盟支援的社交身分識別。
合作夥伴使用者位於哪個目錄︰外部組織的合作夥伴使用者與員工在相同的管理目錄中，但特別註解。 管理他們的方式可和員工相同，可以加入到相同的群組等等  | 客戶使用者實體位於哪個目錄︰在應用程式目錄中。 和組織的員工及合作夥伴目錄 (若有) 分開管理。
支援單一登入 (SSO) 至所有和 Azure AD 連線的應用程式。 例如，您可以提供 Office 365 或內部部署應用程式的存取權，以及其他 SaaS 應用程式 (例如 Salesforce 或 Workday) 的存取權。  |  支援 SSO 至客戶在 Azure AD B2C 租用戶內擁有的應用程式。 不支援 SSO 至 Office 365 或其他 Microsoft 和非 Microsoft SaaS 應用程式。
合作夥伴生命週期︰由主控/邀請組織管理。  | 客戶生命週期︰自助式管理或由應用程式管理。
安全性原則和合規性︰由主控/邀請組織管理。  | 安全性原則和合規性︰由應用程式管理。
商標︰使用主控/邀請組織的品牌。  |    商標︰由應用程式管理。 通常多半會是產品品牌加上組織在背景淡出的效果。
其他資訊：[部落格文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[文件](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | 其他資訊：[產品頁面](https://azure.microsoft.com/en-us/services/active-directory-b2c/)、[文件](https://docs.microsoft.com/en-us/azure/active-directory-b2c/)


### <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 共同作業使用者屬性](active-directory-b2b-user-properties.md)
* [將 B2B 共同作業使用者新增至角色](active-directory-b2b-add-guest-to-role.md)
* [委派 B2B 共同作業邀請](active-directory-b2b-delegate-invitations.md)
* [動態群組和 B2B 共同作業](active-directory-b2b-dynamic-groups.md)
* [設定適用於 B2B 共同作業的 SaaS 應用程式](active-directory-b2b-configure-saas-apps.md)
* [B2B 共同作業使用者權杖](active-directory-b2b-user-token.md)
* [B2B 共同作業使用者宣告對應](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共用](active-directory-b2b-o365-external-user.md)
* [B2B 共同作業目前的限制](active-directory-b2b-current-limitations.md)
* [取得 B2B 共同作業的支援](active-directory-b2b-support.md)
