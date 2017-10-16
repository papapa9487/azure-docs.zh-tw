---
title: "Azure Active Directory B2B 共同作業 API 和自訂 | Microsoft Docs"
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
ms.date: 04/11/2017
ms.author: sasubram
ms.openlocfilehash: c85e05b38b4a9525e13ec510a17b7ef4841198d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B 共同作業 API 和自訂

有許多客戶告訴我們他們想要以最適合其組織的方式自訂邀請程序。 使用我們的 API，您可以進行自訂。 [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>邀請 API 的功能
API 提供下列功能：

1. 使用*任何*電子郵件地址來邀請外部使用者。

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. 自訂使用者接受其邀請之後將會看到的內容。

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. 選擇透過我們的服務 傳送標準邀請郵件

    ```
    "sendInvitationMessage": true
    ```

  使用您可以自訂的訊息傳送給收件者

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. 此外，您也可以選擇在邀請共同作業者時將相關人員新增到副本收件者。

5. 或者，透過選擇不以 Azure AD 傳送通知，以完全自訂您的邀請與上線工作流程。

    ```
    "sendInvitationMessage": false
    ```

  在此案例中，您會從 API 取得兌換 URL，您可以將它內嵌在電子郵件範本、IM 或其他發佈方法。

6. 最後，若您是系統管理員，您可以選擇邀請使用者做為成員。

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>授權模型
API 可以在下列授權模型中執行：

### <a name="app--user-mode"></a>應用程式 + 使用者模式
在此模式中，使用 API 的任何使用者必須有建立 B2B 邀請的權限。

### <a name="app-only-mode"></a>僅應用程式模式
在僅應用程式模式中，應用程式需要 User.ReadWrite.All 或 Directory.ReadWrite.All 範圍，邀請才能成功。

如需詳細資訊，請參閱︰ https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
您現在可以輕鬆地使用 PowerShell 來新增並邀請外部使用者加入組織。 使用 Cmdlet 建立邀請：

```
New-AzureADMSInvitation
```

您可以使用下列選項：

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

您也可以參考 [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) 中的邀請 API 參考資料

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
