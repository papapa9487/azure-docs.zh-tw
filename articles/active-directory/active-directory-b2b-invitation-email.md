---
title: "Azure Active Directory B2B 共同作業邀請電子郵件的元素 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業邀請電子郵件範本"
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
ms.openlocfilehash: 458a2cab13b7e83f120e0926a95d454070181dfb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email"></a>B2B 共同作業邀請電子郵件的元素

邀請電子郵件是一個可讓合作夥伴在 Azure AD 中以 B2B 共同作業使用者身分上線的重要元件。 您可以用它們提高收件者的信任度。 您可以為電子郵件增添合法性和社交證明，以確保收件者在選取 [開始使用] 按鈕來接受邀請時沒有疑慮。 此信任是減少共用摩擦的重要手段。 而且您也希望電子郵件看起來很讚！

![Azure AD B2b 邀請電子郵件](media/active-directory-b2b-invitation-email/invitation-email.png)

## <a name="explaining-the-email"></a>說明電子郵件
讓我們看看一些電子郵件項目，以便了解如何充分利用這些功能。

### <a name="subject"></a>主旨
電子郵件的主旨依循以下模式：您收到加入 &lt;tenantname&gt; 組織的邀請

### <a name="from-address"></a>寄件者地址
針對「寄件者地址」，我們使用類似 LinkedIn 的模式。  您應該清楚邀請者是誰及來自哪個公司，並且表明電子郵件是來自 Microsoft 電子郵件帳戶。 其格式為：來自 &lt;tenantname&gt; 的 &lt;邀請者的顯示名稱&gt; (透過 Microsoft) <invites@microsoft.com&gt;

### <a name="reply-to"></a>回覆地址
回覆電子郵件會設定為邀請者的電子郵件 (如果可用)，以便在回覆電子郵件時會將電子郵件傳回給邀請者。

### <a name="branding"></a>商標
來自您租用戶的邀請電子郵件會使用您可能已為租用戶設定的公司商標。 如果您想要利用此功能，[這裡](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)提供有關如何設定的詳細資料。 電子郵件中會顯示橫幅標誌。 依照[這裡](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)的影像大小和品質指示，以獲得最佳效果。 此外，公司名稱也會顯示在對動作的呼叫中。

### <a name="call-to-action"></a>對動作的呼叫
對動作的呼叫是由兩個部分組成：說明收件者收到郵件的原因，以及要求收件者採取的動作。
- 「原因」區段可以使用以下模式來處理：您收到存取 &lt;tenantname&gt; 組織中應用程式的邀請

- 而「要求您採取的動作」區段是以 [開始使用] 按鈕的存在來表示。 在已新增使用者而不需要邀請的情況下，就不會顯示此按鈕。

### <a name="inviters-information"></a>邀請者的資訊
邀請者的顯示名稱會包含在電子郵件中。 此外，如果您已為 Azure AD 帳戶設定個人檔案圖片，則邀請電子郵件也會包含該圖片。 這兩項措施都是為了提升收件者對電子郵件的信賴感。

如果您尚未設定自己的個人檔案圖片，圖片位置就會顯示邀請者的姓名縮寫圖示：

  ![顯示邀請者的姓名縮寫](media/active-directory-b2b-invitation-email/inviters-initials.png)

### <a name="body"></a>body
本文內容包含邀請者撰寫或透過邀請 API 傳遞的訊息。 因為它是文字區域，所以不會基於安全考量處理 HTML 標記。

### <a name="footer-section"></a>頁尾區段
頁尾包含 Microsoft 公司品牌，並讓收件者知道電子郵件是否是由未受監視的別名所傳送。 特殊案例：

- 邀請者在邀請方租用中沒有電子郵件地址

  ![在邀請方租用中沒有電子郵件地址的邀請者圖片](media/active-directory-b2b-invitation-email/inviter-no-email.png)


- 收件者不需要兌換邀請函

  ![當收件者不需要兌換邀請函時](media/active-directory-b2b-invitation-email/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [什麼是 Azure AD B2B 共同作業](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](active-directory-b2b-admin-add-users.md)
* [資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
* [B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 共同作業授權](active-directory-b2b-licensing.md)
* [針對 Azure Active Directory B2B 共同作業問題進行疑難排解](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 共同作業常見問題 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 共同作業 API 和自訂](active-directory-b2b-api.md)
* [適用於 B2B 共同作業使用者的多重要素驗證](active-directory-b2b-mfa-instructions.md)
* [在沒有邀請的情況下新增 B2B 共同作業使用者](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory 中應用程式管理的文章索引](active-directory-apps-index.md)
