---
title: "什麼是 Azure Active Directory B2B 共同作業？ | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業讓企業合作夥伴選擇性地存取您的公司應用程式，以支援公司間的關係。"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: curtand
ms.custom: aaddev
ms.reviewer: sasubram
ms.openlocfilehash: fbc12a52555b190d43b5e953fd4d19923a25b0ed
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>什麼是 Azure AD B2B 共同作業？

<iframe width="560" height="315" src="https://www.youtube.com/embed/AhwrweCBdsc" frameborder="0" allowfullscreen></iframe>

Azure AD 企業對企業 (B2B) 共同作業功能可讓任何使用 Azure AD 的組織 (不論規模大小)，安全地與來自任何其他組織的使用者共同作業。 這些組織可以有或沒有 Azure AD，或甚至可以有或沒有 IT 組織。 

使用 Azure AD 的組織可以將文件、資源及應用程式的存取權提供給它們的合作夥伴，同時又保有其本身公司資料的完整控制權。 開發人員可以使用 Azure AD 企業對企業 API 來撰寫應用程式，以更安全的方式將兩個組織結合在一起。 還可讓使用者相當輕鬆地瀏覽。

我們的客戶中有 97% 告訴我們，Azure AD B2B 共同作業對他們非常重要。

![圓形圖](media/active-directory-b2b-what-is-azure-ad-b2b/97-percent-support.png)

截至 2017 年 4 月初為止，我們有大約三百萬個使用者已經使用 Azure AD B2B 共同作業功能。 而有超過 23% 有 10 個使用者以上的 Azure AD 組織已經從這些功能獲益。

## <a name="the-key-benefits-of-azure-ad-b2b-collaboration-to-your-organization"></a>Azure AD B2B 共同作業可為您組織提供的主要優點

### <a name="work-with-any-user-from-any-partner"></a>可與來自任何合作夥伴的任何使用者共同作業

* 合作夥伴可使用他們自己的認證

* 合作夥伴不一定要使用 Azure AD

* 不需要外部目錄或複雜的設定

### <a name="simple-and-secure-collaboration"></a>簡單且安全的共同作業

* 可提供對任何公司應用程式或資料的存取權，同時又套用精密、Azure AD 提供技術的授權原則

* 方便使用者

* 企業級的應用程式與資料安全性

### <a name="no-management-overhead"></a>沒有任何額外的管理負荷

* 不需要進行任何外部帳戶或密碼管理

* 不需要進行任何同步處理或手動帳戶生命週期管理

* 沒有任何額外的外部系統管理負荷

## <a name="you-can-easily-add-b2b-collaboration-users-to-your-organization"></a>您可以將 B2B 共同作業使用者輕鬆地新增到您的組織

系統管理員可以在 [Azure 入口網站](https://portal.azure.com)中新增 B2B 共同作業 (來賓) 使用者。

![新增來賓使用者](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>讓您的共同作業者使用自己的身分識別

B2B 共同作業者可以使用自己選擇的身分識別來登入。 如果使用者沒有 Microsoft 帳戶或 Azure AD 帳戶 – 系統就會在他們兌換提供項目時為他們建立一個帳戶。

![登入身分識別選項](media/active-directory-b2b-what-is-azure-ad-b2b/sign-in-identity-choice.png)

### <a name="delegate-to-application-and-group-owners"></a>委派給應用程式和群組擁有者 
應用程式和群組擁有者可以將 B2B 使用者直接新增到您在意的任何應用程式，不論它是否為 Microsoft 應用程式。 系統管理員可以將新增 B2B 使用者的權限委派給非系統管理員。 非系統管理員可以使用 [Azure AD 應用程式存取面板](https://myapps.microsoft.com)，將 B2B 共同作業使用者新增到應用程式或群組。

![存取面板](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![新增成員](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>授權原則可保護您的公司內容

可以強制執行條件式存取原則，例如多重要素驗證：
- 在租用戶層級
- 在應用程式層級
- 針對特定使用者來保護公司應用程式和資料

![新增成員](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="use-our-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>使用我們的 API 和範例程式碼來輕鬆建置要上線的應用程式
讓您的外部合作夥伴以針對您組織需求量身打造的方式上線使用

您可以使用 [B2B 共同作業邀請 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) 來自訂入門訓練體驗，包括建立自助式註冊入口網站。 我們[在 Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) 提供自助式入口網站的範例程式碼。

![註冊入口網站](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

使用 Azure AD B2B 共同作業，您便可以運用 Azure AD 的完整強大功能，以使用者覺得簡單且直覺的方式來保護您的合作夥伴關係。 因此，請不要猶豫，加入成為已使用 Azure AD B2B 進行其外部共同作業之數千個組織的一員吧！

## <a name="next-steps"></a>後續步驟

* 系統管理員體驗是在 [Azure 入口網站](https://portal.azure.com)中進行。

* 資訊工作者體驗可在[存取面板](https://myapps.microsoft.com)中進行。

* 一如往常，如有任何意見反應、討論及建議，請透過我們的 [Microsoft 技術社群 (英文)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)，與產品小組聯繫。

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](active-directory-b2b-admin-add-users.md)
* [資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
* [B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)
* [B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 共同作業授權](active-directory-b2b-licensing.md)
* [針對 Azure Active Directory B2B 共同作業問題進行疑難排解](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 共同作業常見問題 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 共同作業 API 和自訂](active-directory-b2b-api.md)
* [適用於 B2B 共同作業使用者的多重要素驗證](active-directory-b2b-mfa-instructions.md)
* [在沒有邀請的情況下新增 B2B 共同作業使用者](active-directory-b2b-add-user-without-invite.md)
* [B2B 共同作業使用者稽核和報告](active-directory-b2b-auditing-and-reporting.md)
* [Azure Active Directory 中應用程式管理的文章索引](active-directory-apps-index.md)
