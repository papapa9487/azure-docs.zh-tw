---
title: "Azure AD Connect：傳遞驗證 - 運作方式 | Microsoft Docs"
description: "本文描述 Azure Active Directory 傳遞驗證運作方式。"
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
ms.date: 09/27/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 9507a718ba982eb8b3381a6ef70156f075729161
ms.contentlocale: zh-tw
ms.lasthandoff: 09/28/2017

---

# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory 傳遞驗證：技術深入探討
下列文章概述了 Azure AD 傳遞驗證的運作方式。  如需深層技術和安全性資訊，請參閱 [ **Azure AD 傳遞驗證安全性深入探討**](active-directory-aadconnect-pta-security-deep-dive.md)

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Azure Active Directory 傳遞驗證運作方式

如果使用者嘗試登入 Azure Active Directory (Azure AD) 所保護的應用程式，並且在租用戶上啟用傳遞驗證，則會執行下列步驟：

1. 使用者嘗試存取應用程式 (例如，Outlook Web App - https://outlook.office365.com/owa/)。
2. 如果使用者尚未登入，則會將使用者重新導向至 Azure AD 登入頁面。
3. 使用者將其使用者名稱和密碼輸入 Azure AD 登入頁面，然後按一下 [登入] 按鈕。
4. 接收登入要求時的 Azure AD 會將使用者名稱和密碼 (使用公開金鑰加密) 置於佇列。
5. 內部部署傳遞驗證代理程式會對佇列進行輸出呼叫，並擷取使用者名稱和加密密碼。
6. 代理程式會使用其私密金鑰將密碼解密。
7. 代理程式接著會使用標準 Windows API (和 Active Directory 同盟服務所使用的類似機制)，向 Active Directory 驗證使用者名稱和密碼。 使用者名稱可以是內部部署的預設使用者名稱 (通常是 `userPrincipalName`)，或可在 Azure AD Connect 中設定的另一個屬性 (又稱為 `Alternate ID`)。
8. 內部部署 Active Directory 網域控制站 (DC) 接著會評估要求，並將適當的回應 (成功、失敗、密碼過期或鎖定使用者) 傳回給代理程式。
9. 代理程式再將此回應傳回給 Azure AD。
10. Azure AD 會評估回應，並適當地回應使用者；例如，它會立即將使用者登入，或要求 Multi-Factor Authentication (MFA)。
11. 如果使用者登入成功，則使用者可以存取應用程式。

下圖說明所有元件和相關步驟。

![傳遞驗證](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>後續步驟
- [**目前的限制**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - 了解支援的情節和不支援的情節。
- [**快速入門**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - 開始使用 Azure AD 傳遞驗證。
- [**常見問題集**](active-directory-aadconnect-pass-through-authentication-faq.md) - 常見問題集的答案。
- [**疑難排解**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 了解如何解決此功能的常見問題。
- [**Azure AD 無縫 SSO**](active-directory-aadconnect-sso.md) - 深入了解此互補功能。
- [**安全性深入探討**](active-directory-aadconnect-pta-security-deep-dive.md) - Azure AD 傳遞驗證上的其他深入技術資訊。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用於提出新的功能要求。

