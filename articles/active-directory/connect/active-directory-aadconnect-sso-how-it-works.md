---
title: "Azure AD Connect：無縫單一登入 - 運作方式 | Microsoft Docs"
description: "本文描述 Azure Active Directory 無縫單一登入功能的運作方式。"
services: active-directory
keywords: "何謂 Azure AD Connect、安裝 Active Directory、Azure AD、SSO、單一登入的必要元件"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 38b107513e72635fd034bb86d0d866bcb0fcb8e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory 無縫單一登入：技術性深入探討

本文提供 Azure Active Directory 無縫單一登入 (無縫 SSO) 功能運作方式的技術詳細資料。

## <a name="how-does-seamless-sso-work"></a>順暢 SSO 如何運作？

本節有兩個部分：
1. 無縫 SSO 功能的設定。
2. 單一使用者登入交易如何與無縫 SSO 搭配運作。

### <a name="how-does-set-up-work"></a>設定如何運作？

無縫 SSO 是使用 Azure AD Connect 所啟用，如[這裏](active-directory-aadconnect-sso-quick-start.md)所示。 啟用此功能時，會執行下列步驟：
- 名為 `AZUREADSSOACC` 的電腦帳戶 (代表 Azure AD) 是在您的內部部署 Active Directory (AD) 中建立。
- 電腦帳戶的 Kerberos 解密金鑰可安全地與 Azure AD 共用。
- 此外，系統會建立兩個 Kerberos 服務主體名稱 (SPN)，以代表 Azure AD 登入期間所使用的兩個 URL。

>[!NOTE]
> 在您同步處理至 Azure AD (使用 Azure AD Connect) 的每個 AD 樹系中，以及您想要為其使用者啟用無縫 SSO 者，建立電腦帳戶和 Kerberos SPN。 將 `AZUREADSSOACC` 電腦帳戶移至儲存其他電腦帳戶的組織單位 (OU)，確保它是透過相同方式進行管理，而且不會予以刪除。

>[!IMPORTANT]
>強烈建議您至少每隔 30 天變換一次 `AZUREADSSOACC` 電腦帳戶的 [Kerberos 解密金鑰](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account)。

### <a name="how-does-sign-in-with-seamless-sso-work"></a>使用無縫 SSO 登入的運作方式？

設定完成之後，無縫 SSO 登入的運作方式與其他任何使用整合式 Windows 驗證 (IWA) 的登入相同。 流程如下︰

1. 使用者嘗試從公司網路內加入網域的公司裝置存取應用程式 (例如 Outlook Web App - https://outlook.office365.com/owa/)。
2. 如果使用者尚未登入，則會將使用者重新導向至 Azure AD 登入頁面。

  >[!NOTE]
  >如果 Azure AD 登入要求包含 `domain_hint` (識別您的租用戶；例如 contoso.onmicrosoft.com) 或 `login_hint` (識別使用者；例如 user@contoso.onmicrosoft.com 或 user@contoso.com) 參數，則會跳過步驟 2。

3. 使用者將他們的使用者名稱鍵入 Azure AD 登入頁面中。
4. 在背景中使用 JavaScript 時，Azure AD 會透過 401 未授權回應來挑戰瀏覽器，以提供 Kerberos 票證。
5. 瀏覽器接著會從 Active Directory 要求 `AZUREADSSOACC` 電腦帳戶 (代表 Azure AD) 的票證。
6. Active Directory 會找出該電腦帳戶，並將 Kerberos 票證傳回給瀏覽器 (使用電腦帳戶的祕密加密)。
7. 瀏覽器會將它需要的 Kerberos 票證從 Active Directory 轉送到 Azure AD (在其中一個[先前新增至瀏覽器內部網路區域設定的 Azure AD URL](active-directory-aadconnect-sso-quick-start.md#step-3-roll-out-the-feature) 上)。
8. Azure AD 會解密 Kerberos 票證，其中包含使用先前共用的金鑰登入公司裝置之使用者的身分識別。
9. 評估之後，Azure AD 會將權杖傳回給應用程式，或要求使用者執行其他證明，例如 Multi-Factor Authentication。
10. 如果使用者登入成功，則使用者可以存取應用程式。

下圖說明所有元件和相關步驟。

![順暢單一登入](./media/active-directory-aadconnect-sso/sso2.png)

無縫 SSO 是靈活變換的功能，這表示如果失敗，登入體驗會改回其一般行為； 也就是，使用者必須輸入密碼才能登入。

## <a name="next-steps"></a>後續步驟

- [**快速入門**](active-directory-aadconnect-sso-quick-start.md) - 開始使用 Azure AD 無縫 SSO。
- [**常見問題集**](active-directory-aadconnect-sso-faq.md) - 常見問題集的答案。
- [**疑難排解**](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解決此功能的常見問題。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用於提出新的功能要求。
