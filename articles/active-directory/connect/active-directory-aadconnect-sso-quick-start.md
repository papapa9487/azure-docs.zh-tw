---
title: "Azure AD Connect：無縫單一登入 - 快速入門 | Microsoft Docs"
description: "本文描述如何開始使用 Azure Active Directory 無縫單一登入"
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
ms.date: 12/05/2017
ms.author: billmath
ms.openlocfilehash: 1bc76062b05938992b71eedaa71b3c7dfedd7ef4
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory 無縫單一登入：快速入門

## <a name="deploy-seamless-single-sign-on"></a>部署無縫單一登入

使用者位於連線到公司網路的公司桌上型電腦時，Azure Active Directory (Azure AD) 無縫單一登入 (無縫 SSO) 會自動登入使用者。 無縫 SSO 可讓使用者輕鬆存取雲端式應用程式，而不需要任何額外的內部部署元件。

若要部署無縫 SSO，請遵循下列步驟。

## <a name="step-1-check-the-prerequisites"></a>步驟 1：檢查必要條件

請確保已具備下列必要條件︰

* **設定 Azure AD Connect 伺服器**：如果您使用[傳遞驗證](active-directory-aadconnect-pass-through-authentication.md)作為登入方法，不需要進行額外的必要條件檢查。 如果您使用[密碼雜湊同步處理](active-directory-aadconnectsync-implement-password-synchronization.md)作為登入方法，而且 Azure AD Connect 與 Azure AD 之間有防火牆，請確定︰
   - 您使用 Azure AD Connect 1.1.644.0 或更新版本。 
   - 如果您的防火牆或 Proxy 允許建立 DNS 白名單，便可將透過連接埠 443 進行的 **\*.msappproxy.net** URL 連線加入白名單。 如果不允許建立，請允許存取每週更新的 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 只有啟用此功能時，此必要條件才適用。 不需要實際的使用者登入。

    >[!NOTE]
    >Azure AD Connect 版本 1.1.557.0、1.1.558.0、1.1.561.0 和 1.1.614.0 具有與密碼雜湊同步處理相關的問題。 如果您_不_想要使用密碼雜湊同步處理搭配傳遞驗證，請閱讀 [Azure AD Connect 版本資訊](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470)，以深入了解。

* **設定網域管理員員認證**：下列情況的每個 Active Directory 樹系，均需擁有網域管理員認證：
    * 透過 Azure AD Connect 同步至 Azure AD。
    * 包含您要啟用無縫 SSO 的使用者。

## <a name="step-2-enable-the-feature"></a>步驟 2︰啟用功能

透過 [Azure AD Connect](active-directory-aadconnect.md) 啟用無縫 SSO。

如果您要執行 Azure AD Connect 的全新安裝，請選擇[自訂安裝路徑](active-directory-aadconnect-get-started-custom.md)。 在 [使用者登入] 頁面中，選取 [啟用單一登入] 選項。

![Azure AD Connect：使用者登入](./media/active-directory-aadconnect-sso/sso8.png)

如果您已經安裝 Azure AD Connect，請在 Azure AD Connect 上選取 [變更使用者登入] 頁面，然後選取 [下一步]。

![Azure AD Connect：變更使用者登入](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

繼續執行精靈，直到抵達 [啟用單一登入] 頁面。 為下列情況的每個 Active Directory 樹系提供網域管理員認證：
    * 透過 Azure AD Connect 同步至 Azure AD。
    * 包含您要啟用無縫 SSO 的使用者。

完成精靈之後，無縫 SSO 就會在您的租用戶上啟用。

>[!NOTE]
> 網域管理員認證不會儲存在 Azure AD Connect 或 Azure AD 中。 這些認證僅用於啟用此功能。

請遵循下列指示來確認您已正確啟用無縫 SSO：

1. 使用租用戶的全域管理員認證來登入 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 在左窗格中，選取 [Azure Active Directory]。
3. 選取 [Azure AD Connect]。
4. 確認 [無縫單一登入] 功能顯示為 [已啟用]。

![Azure 入口網站：Azure AD Connect 窗格](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>步驟 3：推出功能

若要對使用者推出功能，您必須使用 Active Directory 中的群組原則，將下列 Azure AD URL 新增至使用者的內部網路區域設定：

- https://autologon.microsoftazuread-sso.com
- https://aadg.windows.net.nsatc.net

此外，您也需要透過「群組原則」，啟用內部網路區域原則設定，稱為**允許透過指令碼更新狀態列**。 

>[!NOTE]
> 下列指示僅適用於 Windows 上的 Internet Explorer 和 Google Chrome (如果它與 Internet Explorer 共用一組受信任的網站 URL)。 如需如何在 Mac 上設定 Mozilla Firefox 和 Google Chrome 的指示，請閱讀下節。

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>為什麼需要修改使用者的內部網路區域設定？

瀏覽器預設會自動從指定的 URL 計算正確的區域 (網際網路或內部網路)。 例如，"http://contoso/" 會對應到內部網路區域，而 "http://intranet.contoso.com/" 會對應到網際網路區域 (因為 URL 包含句點)。 除非將 URL 明確地新增至瀏覽器的內部網路區域，否則瀏覽器不會將 Kerberos 票證傳送給雲端端點 (例如兩個 Azure AD URL)。

### <a name="detailed-steps"></a>詳細步驟

1. 開啟群組原則管理編輯器工具。
2. 編輯套用至部分或所有使用者的群組原則。 此範例使用**預設網域原則**。
3. 瀏覽至 [使用者設定] > [系統管理範本] > [Windows 元件] > [Internet Explorer] > [網際網路控制台] > [安全性頁面]。 然後選取 [指派網站到區域清單]。
    ![單一登入](./media/active-directory-aadconnect-sso/sso6.png)
4. 啟用原則，然後在對話方塊中輸入下列值：
   - **值名稱**：轉送 Kerberos 票證的 Azure AD URL。
   - **值** (資料)：**1** 表示內部網路區域。

   結果如下所示：

    值︰https://autologon.microsoftazuread-sso.com
  
    資料︰1
        
   值︰https://aadg.windows.net.nsatc.net

    Data 1

   >[!NOTE]
   > 如果您想要禁止部分使用者使用無縫 SSO (例如，如果這些使用者在共用 Kiosk 上登入)，請將先前的值設定為 **4**。 此動作會將 Azure AD URL 新增至限制區域，而且隨時讓無縫 SSO 失敗。
   >

5. 選取 [確定]，然後再次選取 [確定]。

    ![單一登入](./media/active-directory-aadconnect-sso/sso7.png)

6. 瀏覽至 [使用者設定] > [系統管理範本] > [Windows 元件] > [Internet Explorer] > [網際網路控制台] > [安全性頁面] > [內部網路區域]。 然後選取 [允許透過指令碼更新狀態列]。

    ![單一登入](./media/active-directory-aadconnect-sso/sso11.png)

7. 啟用原則設定，然後選取 [確定]。

    ![單一登入](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>瀏覽器考量

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (所有平台)

Mozilla Firefox 不會自動使用 Kerberos 驗證。 每個使用者都必須使用下列步驟，手動將 Azure AD URL 新增至其 Firefox 設定：
1. 執行 Firefox，並在網址列中輸入 `about:config`。 關閉任何您看到的通知。
2. 搜尋 **network.negotiate-auth.trusted-uris** 喜好設定。 此喜好設定列出 Firefox 進行 Kerberos 驗證的受信任網站。
3. 按一下滑鼠右鍵，然後選取 [修改]。
4. 在欄位中，輸入 https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net。
5. 選取 [確定]，然後重新開啟瀏覽器。

#### <a name="safari-mac-os"></a>Safari (Mac OS)

確定執行 Mac OS 的電腦已加入 Azure AD。 如需加入 Azure AD 的相關說明，請參閱[整合 OS X 與 Active Directory 的最佳做法](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf)。

#### <a name="google-chrome-all-platforms"></a>Google Chrome (所有平台)

如果您已覆寫環境中的 [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) \(英文\) 或 [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) \(英文\) 原則設定，請確定也將 Azure AD 的 URL (https://autologon.microsoftazuread-sso.com 和 https://aadg.windows.net.nsatc.net) 新增到這些設定。

#### <a name="google-chrome-mac-os-only"></a>Google Chrome (僅限 Mac OS)

針對 Mac OS 和其他非 Windows 平台上的 Google Chrome，請參閱 [Chromium 專案原則清單](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist)，以了解如何將 Azure AD URL 設為白名單以進行整合式驗證的資訊。

使用協力廠商 Active Directory 群組原則延伸模組，向 Mac 使用者上的 Firefox 和 Google Chrome 推出 Azure AD URL，不在本文的範圍內。

#### <a name="known-browser-limitations"></a>已知的瀏覽器限制

無縫 SSO 無法在 Firefox 和 Edge 瀏覽器的私人瀏覽模式中運作。 如果瀏覽器是在「增強保護」模式中執行，它也無法在 Internet Explorer 上運作。

## <a name="step-4-test-the-feature"></a>步驟 4：測試功能

若要測試特定使用者的功能，請確認已具備下列所有條件：
  - 使用者是在公司裝置上登入。
  - 裝置已加入您的 Active Directory 網域。
  - 裝置能夠直接連線至網域控制站 (DC)，不論是在公司的有線或無線網路上進行，還是透過遠端存取連線 (例如 VPN 連線) 來進行。
  - 您已透過群組原則，向這位使用者[推出功能](##step-3-roll-out-the-feature)。

測試使用者只輸入使用者名稱而非密碼的案例：
   - 在新的私用瀏覽器工作階段中，登入 https://myapps.microsoft.com/。

若要測試使用者不需要輸入使用者名稱或密碼的案例，請使用下列其中一個步驟： 
   - 在新的私用瀏覽器工作階段中，登入 https://myapps.microsoft.com/contoso.onmicrosoft.com。 使用您的租用戶名稱取代 *contoso*。
   - 在新的私用瀏覽器工作階段中，登入 https://myapps.microsoft.com/contoso.com。 在您的租用戶上，以驗證過的網域 (非同盟網域) 取代 *contoso.com*。

## <a name="step-5-roll-over-keys"></a>步驟 5：變換金鑰

在步驟 2 中，Azure AD Connect 會在您已啟用無縫 SSO 的所有 Active Directory 樹系中建立電腦帳戶 (代表 Azure AD)。 若要深入了解，請參閱 [Azure Active Directory 無縫單一登入：技術深入探討](active-directory-aadconnect-sso-how-it-works.md)。 為了提升安全性，建議您定期變換這些電腦帳戶的 Kerberos 解密金鑰。 如需如何變換金鑰的指示，請參閱 [Azure Active Directory 無縫單一登入：常見問題集](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account)。

>[!IMPORTANT]
>您不需要在啟用此功能後「立即」執行此步驟。 至少每隔 30 天變換一次 Kerberos 解密金鑰。

## <a name="next-steps"></a>後續步驟

- [技術深入探討](active-directory-aadconnect-sso-how-it-works.md)：了解無縫單一登入功能的運作方式。
- [常見問題集](active-directory-aadconnect-sso-faq.md)：取得無縫單一登入常見問題集的答案。
- [疑難排解](active-directory-aadconnect-troubleshoot-sso.md)：了解如何解決無縫單一登入功能的常見問題。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 論壇提出新功能要求。
