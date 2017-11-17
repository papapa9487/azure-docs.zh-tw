---
title: "Azure AD Connect：傳遞驗證 - 目前的限制 | Microsoft Docs"
description: "本文說明 Azure Active Directory (Azure AD) 傳遞驗證目前的限制。"
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
ms.openlocfilehash: 4a33df43ca218545d6c684103a64f2cd1460913b
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory 傳遞驗證：目前的限制

>[!IMPORTANT]
>Azure AD 傳遞驗證是免費功能，您不需要擁有任何 Azure AD 付費版本即可使用此功能。 傳遞驗證僅適用於 Azure AD 的全球執行個體，不適用於 [Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) 和 [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/)。

## <a name="supported-scenarios"></a>支援的案例

下列案例會有完整支援︰

- 使用者登入所有網頁瀏覽器應用程式。
- 使用者登入支援[新式驗證](https://aka.ms/modernauthga)的 Office 365 用戶端應用程式 - _具有_新式驗證的 Office 2016 和 Office 2013。
- 適用於 Windows 10 裝置的 Azure AD Join。
- Exchange ActiveSync 支援。

## <a name="unsupported-scenarios"></a>不支援的情節

下列案例不受支援︰

- 使用者登入舊版 Office 用戶端應用程式 - _沒有_新式驗證的 Office 2010 和 Office 2013。 組織應該盡可能切換到新式驗證。 新式驗證提供傳遞驗證支援，但也可使用 Multi-Factor Authentication (MFA) 等[條件式存取](../active-directory-conditional-access-azure-portal.md)功能，協助您保護使用者帳戶。
- 使用者登入商務用 Skype 用戶端應用程式，包括商務用 Skype 2016。
- 使用者登入 PowerShell 1.0 版。 建議您改為使用 PowerShell 2.0 版。
- Azure AD Domain Services。
- MFA 的應用程式密碼。
- 偵測[認證外洩](../active-directory-reporting-risk-events.md#leaked-credentials)的使用者。

>[!IMPORTANT]
>_僅_針對不支援的情節，請在 Azure AD Connect 精靈的 [選用功能](active-directory-aadconnect-get-started-custom.md#optional-features) 頁面上，啟用密碼雜湊同步處理作為因應措施。 啟用密碼雜湊同步處理也可讓您選擇在內部部署基礎結構完全中斷時，將驗證容錯移轉。 這個從傳遞驗證到密碼雜湊同步處理的容錯移轉不會自動進行，但必須藉助 Microsoft 支援服務才能完成。

## <a name="next-steps"></a>後續步驟
- [**快速入門**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - 開始使用 Azure AD 傳遞驗證。
- [**智慧鎖定**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) - 在租用戶中設定智慧鎖定功能來保護使用者帳戶。
- [**技術性深入探討**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - 了解這項功能的運作方式。
- [**常見問題集**](active-directory-aadconnect-pass-through-authentication-faq.md) - 常見問題集的答案。
- [**疑難排解**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 了解如何解決此功能的常見問題。
- [**安全性深入探討**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) - 關於此功能的其他深入技術資訊。
- [**Azure AD 無縫 SSO**](active-directory-aadconnect-sso.md) - 深入了解此互補功能。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用於提出新的功能要求。
