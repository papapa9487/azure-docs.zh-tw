---
title: "Azure AD Connect：傳遞驗證 - 運作方式 | Microsoft Docs"
description: "本文描述 Azure Active Directory 傳遞驗證運作方式"
services: active-directory
keywords: "Azure AD Connect 傳遞驗證, 安裝 Active Directory, Azure AD 的必要元件, SSO, 單一登入"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 00420e07957e7d8fe68bb8f1a1742110429d16b8
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory 傳遞驗證：技術深入探討
本文概述 Azure Active Directory (Azure AD)傳遞驗證的運作方式。 如需深層技術和安全性資訊，請參閱[安全性深入探討](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)一文。

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Azure Active Directory 傳遞驗證運作方式

當使用者嘗試登入 Azure AD 所保護的應用程式，並且在租用戶上啟用傳遞驗證，則會執行下列步驟：

1. 使用者嘗試存取應用程式，例如 [Outlook Web App](https://outlook.office365.com/owa/)。
2. 如果使用者尚未登入，則會將使用者重新導向 Azure AD [使用者登入] 頁面。
3. 使用者將其使用者名稱和密碼輸入 Azure AD 登入頁面中，然後選取 [登入] 按鈕。
4. 接收登入要求時，Azure AD 會將使用者名稱和密碼 (使用公開金鑰加密) 置於佇列。
5. 內部部署驗證代理程式會從佇列中擷取使用者名稱和加密的密碼。
6. 代理程式會使用其私密金鑰將密碼解密。
7. 代理程式會使用標準 Windows API 向 Active Directory 驗證使用者名稱和密碼，類似於 Active Directory 同盟服務 (AD FS) 所使用的機制。 使用者名稱可以是內部部署的預設使用者名稱 (通常是 `userPrincipalName`)，或可在 Azure AD Connect 中設定的另一個屬性 (又稱為 `Alternate ID`)。
8. 內部部署 Active Directory 網域控制站 (DC) 會評估要求，並將適當的回應 (成功、失敗、密碼過期或鎖定使用者) 傳回給代理程式。
9. 驗證代理程式再將此回應傳回給 Azure AD。
10. Azure AD 會評估回應，並視情況回應使用者。 舉例而言，Azure AD 會立即將使用者登入，或要求 Azure Multi-Factor Authentication。
11. 如果使用者登入成功，使用者即可存取應用程式。

下圖說明所有元件和相關步驟：

![傳遞驗證](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>後續步驟
- [目前的限制](active-directory-aadconnect-pass-through-authentication-current-limitations.md)：了解支援的情節和不支援的情節。
- [快速入門](active-directory-aadconnect-pass-through-authentication-quick-start.md)：開始使用 Azure AD 傳遞驗證。
- [智慧鎖定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)：在租用戶中設定智慧鎖定功能以保護使用者帳戶。
- [常見問題集](active-directory-aadconnect-pass-through-authentication-faq.md)：常見問題集的答案。
- [疑難排解](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)：了解如何解決傳遞驗證功能的常見問題。
- [安全性深入探討](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)：取得傳遞驗證功能上的深入技術資訊。
- [Azure AD 無縫 SSO](active-directory-aadconnect-sso.md)：深入了解此互補功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 論壇提出新功能要求。

