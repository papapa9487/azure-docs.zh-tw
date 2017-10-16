---
title: "Azure Active Directory 中的 B2B 共同作業使用者宣告對應 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業的宣告對應參考資料"
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
ms.openlocfilehash: 5f8559450b24effd40a38879aeae3a8dd03944a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Azure Active Directory 中的 B2B 共同作業使用者宣告對應

Azure Active Directory (Azure AD) 支援針對 B2B 共同作業使用者自訂在 SAML 權杖中簽發的宣告。 當使用者對應用程式進行驗證時，Azure AD 會將 SAML 權杖簽發給應用程式，其中包含可唯一識別使用者的使用者相關資訊 (或宣告)。 根據預設值，其中包含使用者的使用者名稱、電子郵件地址、名字和姓氏。 您可以在 [屬性] 索引標籤下檢視或編輯在 SAML 權杖中傳送給應用程式的宣告。

編輯在 SAML 權杖中簽發的宣告有兩個可能原因。

1. 應用程式是設計為要求不同的宣告 URI 組或宣告值

2. 您的應用程式要求 NameIdentifier 宣告必須是 Azure Active Directory 中儲存之使用者主體名稱以外的項目。

  ![檢視 SAML 權杖中的宣告](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

如需有關如何新增及編輯宣告的詳細資訊，請查看此宣告自訂文章：[在 Azure Active Directory 中為預先整合的應用程式自訂在 SAML 權杖中發出的宣告](develop/active-directory-saml-claims-customization.md)。 對於 B2B 共同作業使用者，基於安全性原因，系統會防止對應 NameID 與 UPN 跨租用戶。


## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 共同作業使用者屬性](active-directory-b2b-user-properties.md)
* [將 B2B 共同作業使用者新增至角色](active-directory-b2b-add-guest-to-role.md)
* [委派 B2B 共同作業邀請](active-directory-b2b-delegate-invitations.md)
* [動態群組與 B2B 共同作業](active-directory-b2b-dynamic-groups.md)
* [B2B 共同作業程式碼與 PowerShell 範例](active-directory-b2b-code-samples.md)
* [設定適用於 B2B 共同作業的 SaaS 應用程式](active-directory-b2b-configure-saas-apps.md)
* [Office 365 外部共用](active-directory-b2b-o365-external-user.md)
* [B2B 共同作業使用者權杖](active-directory-b2b-user-token.md)
* [B2B 共同作業目前的限制](active-directory-b2b-current-limitations.md)
