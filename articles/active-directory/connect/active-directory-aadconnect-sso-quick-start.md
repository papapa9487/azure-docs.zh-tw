---
title: "Azure AD Connect：無縫單一登入 - 快速入門 | Microsoft Docs"
description: "本文描述如何開始使用 Azure Active Directory 無縫單一登入。"
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
ms.date: 08/04/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 977108687734a5eb7f7a30419de2a6bdef184d0e
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory 無縫單一登入：快速入門

## <a name="how-to-deploy-seamless-sso"></a>如何部署無縫 SSO

使用者位於連線到公司網路的公司桌上型電腦時，Azure Active Directory 無縫單一登入 (Azure AD 無縫 SSO) 就會自動將他們登入。 它可讓使用者輕鬆存取雲端式應用程式，而不需要任何額外的內部部署元件。

>[!IMPORTANT]
>無縫 SSO 功能目前為預覽狀態。

若要部署無縫 SSO，您需要遵循下列步驟：

## <a name="step-1-check-prerequisites"></a>步驟 1：檢查必要條件

請確保已具備下列必要條件︰

1. 設定 Azure AD Connect 伺服器：如果您使用[傳遞驗證](active-directory-aadconnect-pass-through-authentication.md)作為登入方法，不需要採取任何動作。 如果您使用[密碼雜湊同步處理](active-directory-aadconnectsync-implement-password-synchronization.md)作為登入方法，而且 Azure AD Connect 與 Azure AD 之間有防火牆，請確定︰
- 您使用的是 1.1.484.0 版或更新版本的 Azure AD Connect。
- Azure AD Connect 可以與 `*.msappproxy.net` URL 通訊，而且是透過連接埠 443。 只有當您啟用此功能，而不是針對實際使用者登入時，此必要條件才適用。
- Azure AD Connect 可以對 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)直接建立 IP 連線。 同樣地，只有啟用此功能時，此必要條件才適用。
2. 對於您同步處理至 Azure AD (使用 Azure AD Connect) 的每個 AD 樹系，以及您想要為其使用者啟用無縫 SSO 者，您需要有網域系統管理員認證。

## <a name="step-2-enable-the-feature"></a>步驟 2︰啟用功能

您可以使用 [Azure AD Connect](active-directory-aadconnect.md) 啟用無縫 SSO。

如果您要執行 Azure AD Connect 的全新安裝，請選擇[自訂安裝路徑](active-directory-aadconnect-get-started-custom.md)。 在 [使用者登入] 頁面上，勾選 [啟用單一登入] 選項。

![Azure AD Connect - 使用者登入](./media/active-directory-aadconnect-sso/sso8.png)

如果您已經安裝 Azure AD Connect，請在 Azure AD Connect 上選擇 [變更使用者登入] 頁面，然後按一下 [下一步]。 然後勾選 [啟用單一登入] 選項。

![Azure AD Connect - 變更使用者登入](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

繼續執行精靈，直到抵達 [啟用單一登入] 頁面。 對於您同步處理至 Azure AD (使用 Azure AD Connect) 的每個 AD 樹系，以及您想要為其使用者啟用無縫 SSO 者，請提供網域系統管理員認證。 

完成精靈之後，無縫 SSO 就會在您的租用戶上啟用。

>[!NOTE]
> 網域系統管理員認證不會儲存在 Azure AD Connect 或 Azure AD 中，但只用來啟用此功能。

請遵循下列指示來確認您已正確啟用無縫 SSO：

1. 使用租用戶的全域管理員認證來登入 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 按一下左側導覽上的 [Azure Active Directory]。
3. 選取 [Azure AD Connect]。
4. 確認 [無縫單一登入] 功能顯示為 [已啟用]。

![Azure 入口網站 - Azure AD Connect 刀鋒視窗](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>步驟 3：推出功能

若要對使用者推出此功能，您需要透過 Active Directory 中的群組原則，將兩個 Azure AD URL (https://autologon.microsoftazuread-sso.com 和 https://aadg.windows.net.nsatc.net) 新增至使用者的內部網路區域設定。

>[!NOTE]
> 下列指示僅適用於 Windows 上的 Internet Explorer 和 Google Chrome (如果它與 Internet Explorer 共用一組受信任的網站 URL)。 如需在 Mac 上設定 Mozilla Firefox 和 Google Chrome 的指示，請閱讀下節。

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>為什麼需要修改使用者的內部網路區域設定？

瀏覽器預設會自動從 URL 計算正確的區域 (網際網路或內部網路)。 例如，http://contoso/ 會對應到內部網路區域，而 http://intranet.contoso.com/ 會對應到網際網路區域 (因為 URL 包含句點)。 除非將 URL 明確地新增至瀏覽器的內部網路區域，否則瀏覽器不會將 Kerberos 票證傳送給雲端端點 (例如兩個 Azure AD URL)。

### <a name="detailed-steps"></a>詳細步驟

1. 開啟群組原則管理工具。
2. 編輯套用至某些或所有使用者的群組原則。 在此範例中，我們使用**預設網域原則**。
3. 瀏覽至 **User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security 頁面**，並選取 [指派網站到區域清單]。
![單一登入](./media/active-directory-aadconnect-sso/sso6.png)  
4. 啟用原則，並在對話方塊中輸入下列值 (轉送 Kerberos 票證的 Azure AD URL) 和資料 (*1* 指出內部網路區域)。

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
>[!NOTE]
> 如果您想要禁止部分使用者使用無縫 SSO (例如，如果這些使用者正在登入共用 Kiosk)，請將先前的值設定為 *4*。 此動作會將 Azure AD URL 新增至限制區域，而且隨時讓無縫 SSO 失敗。

5. 按一下 [確定]，然後再按一下 [確定]。

![單一登入](./media/active-directory-aadconnect-sso/sso7.png)

### <a name="browser-considerations"></a>瀏覽器考量

#### <a name="mozilla-firefox"></a>Mozilla Firefox

Mozilla Firefox 不會自動執行 Kerberos 驗證。 每個使用者都必須使用下列步驟，手動將 Azure AD URL 新增至其 Firefox 設定：
1. 執行 Firefox，並在網址列中輸入 `about:config`。 關閉任何您看到的通知。
2. 搜尋 **network.negotiate-auth.trusted-uris** 喜好設定。 此喜好設定列出 Firefox 進行 Kerberos 驗證的受信任網站。
3. 按一下滑鼠右鍵，然後選取 [修改]。
4. 在欄位中，輸入 "https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net"。
5. 按一下 [確定]，然後重新開啟瀏覽器。

#### <a name="safari-on-mac-os"></a>Mac OS 上的 Safari

確定執行 Mac OS 的電腦已加入至 AD。 請參閱[這裡](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf)的操作指示。

#### <a name="google-chrome-on-mac-os"></a>Mac OS 上的 Google Chrome

針對 Mac OS 和其他非 Windows 平台上的 Google Chrome，請參閱[本文](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist)以了解如何將 Azure AD URL 設為白名單進行整合式驗證的資訊。

使用協力廠商 Active Directory 群組原則延伸模組向 Mac 使用者上的 Firefox 和 Google Chrome 推出 Azure AD URL，不在本文的範圍內。

#### <a name="known-limitations"></a>已知限制

無縫 SSO 無法在 Firefox 和 Edge 瀏覽器的私人瀏覽模式中運作。 如果瀏覽器是在「增強保護」模式中執行，它也無法在 Internet Explorer 上運作。

>[!IMPORTANT]
>我們最近已復原對 Edge 的支援，以調查客戶回報的問題。

## <a name="step-4-test-the-feature"></a>步驟 4：測試功能

若要測試特定使用者的功能，請確認已具備下列「所有」條件：
  - 使用者是在公司裝置上登入。
  - 裝置先前已加入您的 Active Directory (AD) 網域。
  - 裝置能夠直接連線至網域控制站 (DC)，不論是在公司的有線或無線網路上進行，還是透過遠端存取連線 (例如 VPN 連線) 來進行。
  - 您已向使用群組原則的這位使用者[推出功能](##step-3-roll-out-the-feature)。

測試使用者只輸入使用者名稱而非密碼的案例：
- 在新的私用瀏覽器工作階段中，登入 *https://myapps.microsoft.com/*。

測試使用者不需要輸入使用者名稱或密碼的案例： 
- 在新的私用瀏覽器工作階段中，登入 *https://myapps.microsoft.com/contoso.onmicrosoft.com*。 將 "*contoso*" 取代為您租用戶的名稱。
- 或者，在新的私用瀏覽器工作階段中，登入 *https://myapps.microsoft.com/contoso.com*。 將 "*contoso.com*" 取代為租用戶中的已驗證網域 (非同盟網域)。

## <a name="step-5-roll-over-keys"></a>步驟 5：變換金鑰

在步驟 2 中，Azure AD Connect 會在您已啟用無縫 SSO 的所有 AD 樹系中建立電腦帳戶 (代表 Azure AD)。 在[這裡](active-directory-aadconnect-sso-how-it-works.md)詳細了解。 為了提升安全性，建議您經常變換這些電腦帳戶的 Kerberos 解密金鑰。

>[!IMPORTANT]
>您不需要在啟用此功能後「立即」執行此步驟。 至少每隔 30 天變換一次 Kerberos 解密金鑰。

## <a name="next-steps"></a>後續步驟

- [**技術性深入探討**](active-directory-aadconnect-sso-how-it-works.md) - 了解這項功能的運作方式。
- [**常見問題集**](active-directory-aadconnect-sso-faq.md) - 常見問題集的答案。
- [**疑難排解**](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解決此功能的常見問題。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用於提出新的功能要求。

