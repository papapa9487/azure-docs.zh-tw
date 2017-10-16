---
title: "Azure Active Directory B2B 共同作業的自助式註冊入口網站 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業讓企業合作夥伴選擇性地存取您的公司應用程式，以支援公司間的關係"
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
ms.date: 05/24/2017
ms.author: sasubram
ms.openlocfilehash: 307373c75bbb87cec683f7a3097f8f159c9d5e61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Azure AD B2B 共同作業註冊的自助入口網站

客戶可以使用內建功能進行許多工作，這些功能是透過我們的 IT 系統管理員 [Azure 入口網站](https://portal.azure.com)所公開，至於終端使用者，則是透過我們的[應用程式存取面板](https://myapps.microsoft.com)來公開。 但是我們也知道，企業需要自訂適用於 B2B 使用者的登入工作流程，以符合其組織的需求。 透過[我們的 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)，企業就可以達成這個目的。

在與客戶討論過後，我們發現，企業有一項明顯的常見需求。 提出邀請的組織可能事先不知道需要存取其資源的外部共同作業者分別有誰。 他們想要讓合作夥伴公司的使用者有方法可以自行註冊，並以提出邀請的組織所能控制的一組原則來加以規範。 透過我們的 API 就能實現此案例，因此我們在 GitHub 上發佈了專門針對此需求的專案：[GitHub 專案範例](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web)。

我們的 GitHub 專案會示範組織可以如何使用我們的 API，並為他們信任的合作夥伴提供以原則為基礎的自助式註冊功能，而且這些功能會有規則可以判斷合作夥伴能存取的應用程式。 合作夥伴使用者可以在需要時安全地存取資源，而不需要提出邀請的組織手動讓使用者上線。 您可以輕鬆地將專案部署到您選擇的 Azure 訂用帳戶中。

## <a name="as-is-code"></a>現狀程式碼

請記住，此程式碼只提供作為範例使用，目的是要示範如何使用 Azure Active Directory B2B 邀請 API。 您應該讓開發小組或合作夥伴對此程式碼進行自訂，於檢閱過後再部署到生產案例。

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：
* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](active-directory-b2b-admin-add-users.md)
* [資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
* [B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)
* [B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 共同作業授權](active-directory-b2b-licensing.md)
* [針對 Azure Active Directory B2B 共同作業問題進行疑難排解](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 共同作業常見問題 (FAQ)](active-directory-b2b-faq.md)
* [適用於 B2B 共同作業使用者的多重要素驗證](active-directory-b2b-mfa-instructions.md)
* [在沒有邀請的情況下新增 B2B 共同作業使用者](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory 中應用程式管理的文章索引](active-directory-apps-index.md)