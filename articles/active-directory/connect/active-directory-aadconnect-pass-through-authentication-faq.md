---
title: "Azure AD Connect：傳遞驗證 - 常見問題集 | Microsoft Docs"
description: "Azure Active Directory 傳遞驗證常見問題的解答"
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
ms.date: 12/05/2017
ms.author: billmath
ms.openlocfilehash: 8722d7827aad10bcae3e8ec06b7014ebc64179d5
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory 傳遞驗證：常見問題集

本文將會解決 Azure Active Directory (Azure AD) 傳遞驗證的常見問題。 請隨時回來查看更新的內容。

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>我應該選擇哪一種 Azure AD 登入方法，傳遞驗證、密碼雜湊同步處理，還是 Active Directory 同盟服務 (AD FS)？

這取決於您的內部部署環境和組織需求。 請檢閱 [Azure AD Connect 使用者登入選項](active-directory-aadconnect-user-signin.md)文章，比較各種 Azure AD 登入方法。

## <a name="is-pass-through-authentication-a-free-feature"></a>傳遞驗證是否為免費功能？

傳遞驗證是免費功能。 不需要任何付費的 Azure AD 版本即可使用。

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>[Microsoft Azure 德國雲端](http://www.microsoft.de/cloud-deutschland)和 [Microsoft Azure Government 雲端](https://azure.microsoft.com/features/gov/)是否有提供傳遞驗證功能？

否。 只有全球版的 Azure AD 執行個體會提供傳遞驗證功能。

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>[條件式存取](../active-directory-conditional-access-azure-portal.md)是否能與傳遞驗證搭配運作？

是。 所有條件式存取功能 (包括 Azure Multi-Factor Authentication) 都能與傳遞驗證搭配運作。

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>傳遞驗證支援以「替代識別碼」而非「userPrincipalName」來作為使用者名稱嗎？

是。 當您在 Azure AD Connect 中設定了傳遞驗證時，傳遞驗證支援以 `Alternate ID` 作為使用者名稱。 如需詳細資訊，請參閱[自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)。 並非所有 Office 365 應用程式都支援 `Alternate ID`。 請參閱支援陳述式的特定應用程式文件。

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>密碼雜湊同步處理是否會作為傳遞驗證的遞補？

否。 傳遞驗證_不會_自動容錯移轉至密碼雜湊同步處理。 它只會作為[目前不支援傳遞驗證的情況下](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios)的遞補。 若要避免使用者登入失敗，您應該為傳遞驗證設定[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)。

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>能否在傳遞驗證代理程式所在的同一部伺服器上安裝 [Azure AD 應用程式 Proxy](../active-directory-application-proxy-get-started.md) 連接器？

是。 傳遞驗證代理程式的改版版本 (1.5.193.0 版或更新版本) 支援此組態。

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>需要哪些版本的 Azure AD Connect 和傳遞驗證代理程式？

若要讓這項功能運作，您需要 Azure AD Connect 1.1.486.0 版或更新版本以及傳遞驗證代理程式 1.5.58.0 或更新版本。 請將所有軟體安裝在 Windows Server 2012 R2 或更新版本的伺服器上。

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>如果使用者的密碼過期又嘗試使用傳遞驗證來登入，會發生什麼事？

如果您已經為特定使用者設定[密碼回寫](../active-directory-passwords-update-your-own-password.md)，而使用者又使用傳遞驗證來登入，則他們將能夠變更或重設其密碼。 密碼將會如預期般回寫至內部部署 Active Directory。

如果您尚未為特定使用者設定密碼回寫，或使用者未獲指派有效的 Azure AD 授權，則使用者將無法在雲端中更新其密碼。 即使他們的密碼過期，他們也無法加以更新。 使用者會看到這則訊息：「您的組織不允許您在此網站更新您的密碼。 請依據您組織所建議的方法更新密碼，或洽詢您的管理員尋求協助。」 使用者或系統管理員必須在內部部署 Active Directory 中重設其密碼。

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>傳遞驗證如何防範暴力密碼破解攻擊？

如需詳細資訊，請參閱 [Azure Active Directory 傳遞驗證：智慧鎖定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)。

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>傳遞驗證代理程式會透過連接埠 80 和 443 進行哪些方面的通訊？

- 驗證代理程式會透過連接埠 443 對所有功能作業進行 HTTPS 要求。
- 驗證代理程式會透過連接埠 80 提出 HTTP 要求，以下載 SSL 憑證撤銷清單 (CRL)。

     >[!NOTE]
     >最近的更新減少了此功能所需的連接埠數目。 如果您有舊版的 Azure AD Connect 或驗證代理程式，請將下列連接埠保持開放：5671、8080、9090、9091、9350、9352、10100-10120。

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>傳遞驗證代理程式是否能透過輸出 Web Proxy 伺服器進行通訊？

是。 如果您的內部部署環境啟用了 Web Proxy 自動探索 (WPAD)，則驗證代理程式會自動嘗試在網路上找出並使用 Web Proxy 伺服器。

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>是否可以在相同的伺服器上安裝兩個以上的傳遞驗證代理程式？

不可以，您只能在單一伺服器上安裝一個傳遞驗證代理程式。 如果您想要為傳遞驗證設定高可用性，請遵循 [Azure Active Directory 傳遞驗證：快速入門](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)中的指示。

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>我已使用 AD FS 登入 Azure AD。 要如何改為使用傳遞驗證？

如果您已經透過 Azure AD Connect 精靈將 AD FS 設定為您的登入方法，請將使用者的登入方法變更為傳遞驗證。 這項變更會讓租用戶啟用傳遞驗證，並將您_所有_同盟網域轉換為受控網域。 傳遞驗證會處理登入租用戶的所有後續要求。 Azure AD Connect 內目前沒有任何支援的方法可跨不同網域使用 AD FS 和傳遞驗證的組合。

如果 Azure AD Connect 精靈的_外部_登入方法設為 AD FS，請將使用者登入方法變更為傳遞驗證。 可以從 [請勿設定] 選項進行變更。 這項變更會讓租用戶啟用傳遞驗證，但您所有的同盟網域會繼續使用 AD FS 登入。 請使用 PowerShell 以手動方式將其中的部分或所有同盟網域轉換為受控網域。 變更後，*只會由*傳遞驗證處理所有登入受控網域的要求。

>[!IMPORTANT]
>傳遞驗證不會為僅限雲端的 Azure AD 使用者處理登入要求。

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>是否可以在多樹系 Active Directory 環境中使用傳遞驗證？

是。 如果 Active Directory 樹系之間有樹系信任且名稱尾碼路由已正確設定，就支援多樹系環境。

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>我需要安裝幾個傳遞驗證代理程式？

安裝多個傳遞驗證代理程式可確保[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)。 但是，它不會在驗證代理程式之間提供確定性的負載平衡。

請考慮您預期在租用戶上看到的登入要求的尖峰與平均負載。 在標準的 4 核心 CPU、16-GB RAM 伺服器上，單一驗證代理程式每秒可處理 300 到 400 次驗證，乃是效能評定的基準。

若要估計網路流量，請使用下列調整大小指導方針：
- 每個要求都具有 (0.5K + 1K * num_of_agents) 個位元組的承載大小；也就是從 Azure AD 到驗證代理程式的資料。 在這裡，"num_of_agents" 表示已在您的租用戶上註冊的驗證代理程式數目。
- 每個回應都具有 1K 個位元組的承載大小；也就是從驗證代理程式到 Azure AD 的資料。

對大多數客戶來說，總計中兩個或三個驗證代理程式已足以因應高可用性和容量。 應在靠近網域控制站的地方安裝驗證代理程式，以改善登入的延遲情形。

>[!NOTE]
>系統限制每個租用戶只能有 12 個驗證代理程式。

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>是否可以在不同於 Azure AD Connect 執行所在伺服器的某個伺服器上安裝第一個傳遞驗證代理程式？

不行，「不」支援這種情況。

## <a name="how-can-i-disable-pass-through-authentication"></a>如何停用傳遞驗證？

重新執行 Azure AD Connect 精靈，並將使用者的登入方法從傳遞驗證變更為其他方法。 這項變更會讓租用戶停用傳遞驗證，並從該伺服器解除安裝驗證代理程式。 至於其他伺服器的驗證代理程式，則必須手動解除安裝。

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>解除安裝傳遞驗證代理程式會發生什麼事？

如果從伺服器解除安裝傳遞驗證代理程式，會讓該伺服器停止接受登入要求。 為了避免中斷租用戶上的使用者登入功能，務必要有另一個驗證代理程式正在執行，才能解除安裝傳遞驗證代理程式。

## <a name="next-steps"></a>後續步驟
- [目前的限制](active-directory-aadconnect-pass-through-authentication-current-limitations.md)：了解支援的情節和不支援的情節。
- [快速入門](active-directory-aadconnect-pass-through-authentication-quick-start.md)：開始使用 Azure AD 傳遞驗證。
- [智慧鎖定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)：了解如何在租用戶中設定智慧鎖定功能以保護使用者帳戶。
- [技術深入探討](active-directory-aadconnect-pass-through-authentication-how-it-works.md)：了解傳遞驗證功能的運作方式。
- [疑難排解](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)：了解如何解決傳遞驗證功能的常見問題。
- [安全性深入探討](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)：取得傳遞驗證功能上的深入技術資訊。
- [Azure AD 無縫 SSO](active-directory-aadconnect-sso.md)：深入了解此互補功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 論壇提出新功能要求。

