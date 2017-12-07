---
title: "Azure AD 傳遞驗證 - 快速入門 | Microsoft Docs"
description: "本文說明如何開始使用 Azure Active Directory (Azure AD) 傳遞驗證。"
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
ms.openlocfilehash: 8adca38cc5a783abfe29725dbb0a201de4183dad
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory 傳遞驗證：快速入門

## <a name="deploy-azure-ad-pass-through-authentication"></a>部署 Azure AD 傳遞驗證

Azure Active Directory (Azure AD) 傳遞驗證可讓您的使用者以相同密碼登入內部部署和雲端式應用程式。 傳遞驗證會直接向內部部署 Active Directory 驗證使用者的密碼，以決定是否讓使用者登入。

>[!IMPORTANT]
>如果您透過預覽版本使用這項功能，請務必使用 [Azure Active Directory 傳遞驗證：將預覽驗證代理程式升級](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md)提供的指示，升級預覽版本驗證代理程式。

請依照下列指示部署傳遞驗證：

## <a name="step-1-check-the-prerequisites"></a>步驟 1：檢查必要條件

請確保已具備下列必要條件。

### <a name="in-the-azure-active-directory-admin-center"></a>於 Azure Active Directory 管理中心

1. 在 Azure AD 租用戶上建立僅限雲端的全域管理員帳戶。 如此一來，如果您的內部部署服務失敗或無法使用，您便可以管理租用戶組態。 了解如何[新增僅限雲端管理員帳戶](../active-directory-users-create-azure-portal.md)。 這是確保您不會遭租用戶封鎖的關鍵步驟。
2. 將一或多個[自訂網域名稱](../active-directory-domains-add-azure-portal.md)新增至 Azure AD 租用戶。 您的使用者可以使用其中一個網域名稱登入。

### <a name="in-your-on-premises-environment"></a>在內部部署環境中

1. 識別一部執行 Windows Server 2012 R2 或更新版本的伺服器來執行 Azure AD Connect。 根據需要驗證密碼之使用者所在的 Active Directory 樹系，將伺服器新增至同一個樹系。
2. 在上一個步驟中識別的伺服器上，安裝[最新版本的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。 如果您已執行 Azure AD Connect，請確定版本是 1.1.644.0 或更新版本。

    >[!NOTE]
    >Azure AD Connect 版本 1.1.557.0、1.1.558.0、1.1.561.0 和 1.1.614.0 具有與密碼雜湊同步處理相關的問題。 如果您「不」想要使用密碼雜湊同步處理搭配傳遞驗證，請閱讀 [Azure AD Connect 版本資訊](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470)。

3. 識別額外的伺服器 (執行 Windows Server 2012 R2 或更新版本) 來執行獨立驗證代理程式。 驗證代理程式版本必須是 1.5.193.0 或更新版本。 這部額外的伺服器可用來確保高可用性，滿足登入要求的需求。 根據需要驗證密碼之使用者所在的 Active Directory 樹系，將伺服器新增至同一個樹系。
4. 如果您的伺服器和 Azure AD 之間有防火牆，請設定下列項目：
   - 確定驗證代理程式會透過以下連接埠對 Azure AD 提出*輸出*要求：
   
    | 連接埠號碼 | 使用方式 |
    | --- | --- |
    | **80** | 驗證 SSL 憑證時下載憑證撤銷清單 (CRL) |
    | **443** | 處理所有與服務之間的輸出通訊 |
   
    如果您的防火牆會根據原始使用者強制執行規則，請開啟這些連接埠，讓來自以網路服務形式執行之 Windows 服務的流量得以通行。
   - 如果您的防火牆或 Proxy 允許建立 DNS 白名單，便可將對 **\*.msappproxy.net** 與 **\*.servicebus.windows.net** 的連線加入白名單。 如果不允許建立，請允許存取每週更新的 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。
   - 您的驗證代理程式必須存取 **login.windows.net** 與 **login.microsoftonline.com**才能進行初始註冊， 因此也請針對這些 URL 開啟您的防火牆。
   - 為了驗證憑證，請解除封鎖以下 URL：**mscrl.microsoft.com:80**、**crl.microsoft.com:80**、**ocsp.msocsp.com:80** 和 **www.microsoft.com:80**。 這些 URL 會用於其他 Microsoft 產品的憑證驗證， 因此您可能已將這些 URL 解除封鎖。

## <a name="step-2-enable-exchange-activesync-support-optional"></a>步驟 2：啟用 Exchange ActiveSync 支援 (選擇性)

遵循以下指示啟用 Exchange ActiveSync 支援：

1. 使用 [Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) 執行下列命令：
```
Get-OrganizationConfig | fl per*
```

2. 檢查 `PerTenantSwitchToESTSEnabled` 設定的值。 如果值為 **true**，代表租用戶的設定正確。 大多數的客戶都會是這種情況。 如果值為**false**，請執行下列命令：
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. 驗證 `PerTenantSwitchToESTSEnabled` 設定的值現在設為 **true**。 等候一小時，再開始下一個步驟。

如果您在此步驟中遭遇任何問題，請參閱[疑難排解指南](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues)。

## <a name="step-3-enable-the-feature"></a>步驟 3︰啟用功能

透過 [Azure AD Connect](active-directory-aadconnect.md) 啟用傳遞驗證。

>[!IMPORTANT]
>您可以在 Azure AD Connect 主要或暫存伺服器上啟用傳遞驗證。 您應該從主要伺服器啟用。

如果您是第一次安裝 Azure AD Connect，請選擇[自訂安裝路徑](active-directory-aadconnect-get-started-custom.md)。 在 [使用者登入] 頁面上，選擇 [傳遞驗證] 作為 [登入方法]。 成功完成時，傳遞驗證代理程式會安裝在 Azure AD Connect 所在的同一部伺服器上。 此外，您的租用戶上也會啟用傳遞驗證功能。

![Azure AD Connect：使用者登入](./media/active-directory-aadconnect-sso/sso3.png)

如果您已安裝 Azure AD Connect (使用[快速安裝](active-directory-aadconnect-get-started-express.md)或[自訂安裝](active-directory-aadconnect-get-started-custom.md)路徑)，請在 Azure AD Connect 上選取 [變更使用者登入] 工作，並選取 [下一步]。 然後選取 [傳遞驗證] 作為登入方法。 成功完成時，傳遞驗證代理程式會安裝在 Azure AD Connect 所在的同一部伺服器上，且您的租用戶上會啟用此功能。

![Azure AD Connect：變更使用者登入](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>傳遞驗證是租用戶層級的功能。 開啟此功能會影響租用戶中「所有」受控網域的使用者登入。 如果您從 Active Directory Federation Services (AD FS) 改為使用傳遞驗證，應等候至少 12 個小時再關閉 AD FS 基礎結構。 這裡的等候時間可確保使用者在轉換期間依然可以繼續登入 Exchange ActiveSync。

## <a name="step-4-test-the-feature"></a>步驟 4：測試功能

請遵循下列指示來確認您已正確啟用傳遞驗證：

1. 使用租用戶的全域管理員認證來登入 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 在左窗格中，選取 [Azure Active Directory]。
3. 選取 [Azure AD Connect]。
4. 確認 [傳遞驗證] 功能顯示為 [已啟用]。
5. 選取 [傳遞驗證]。 [傳遞驗證] 窗格會列出安裝驗證代理程式的伺服器。

![Azure Active Directory 管理中心：Azure AD Connect 窗格](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory 管理中心：傳遞驗證窗格](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

在此階段，租用戶中所有受控網域的使用者都可以使用傳遞驗證來登入。 不過，同盟網域的使用者會繼續使用 ADFS 或您先前設定的其他同盟提供者來登入。 如果您將同盟網域轉換成受控網域，該網域中的所有使用者都會自動開始使用傳遞驗證來登入。 傳遞驗證功能不會影響僅限雲端的使用者。

## <a name="step-5-ensure-high-availability"></a>步驟 5：確保高可用性

如果您打算在生產環境中部署傳遞驗證，您應該安裝獨立驗證代理程式。 在執行 Azure AD Connect 和第一個驗證代理程式「以外」的伺服器上，安裝這第二個驗證代理程式。 此設定可提供高可用性來滿足登入要求。 請依照下列指示來部署獨立驗證代理程式：

1. 下載最新版本的驗證代理程式 (1.5.193.0 或更新版本)。 使用租用戶的全域管理員認證來登入 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 在左窗格中，選取 [Azure Active Directory]。
3. 依序選取 [Azure AD Connect]、[傳遞驗證] 及 [下載代理程式]。
4. 選取 [接受條款並下載] 按鈕。
5. 執行在上個步驟中下載的可執行檔，安裝最新版本的驗證代理程式。 出現提示時，提供租用戶的全域管理員認證。

![Azure Active Directory 管理中心：下載驗證代理程式按鈕](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Azure Active Directory 管理中心：下載代理程式窗格](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>您也可以下載 [Azure Active Directory 驗證代理程式](https://aka.ms/getauthagent)。 請確定在安裝_之前_，檢閱並接受驗證代理程式的[服務條款](https://aka.ms/authagenteula)。

## <a name="next-steps"></a>後續步驟
- [智慧鎖定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)：了解如何在租用戶中設定智慧鎖定功能以保護使用者帳戶。
- [目前的限制](active-directory-aadconnect-pass-through-authentication-current-limitations.md)：了解傳遞驗證支援的情節和不支援的情節。
- [技術深入探討](active-directory-aadconnect-pass-through-authentication-how-it-works.md)：了解傳遞驗證功能的運作方式。
- [常見問題集](active-directory-aadconnect-pass-through-authentication-faq.md)：常見問題集的答案。
- [疑難排解](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)：了解如何解決傳遞驗證功能的常見問題。
- [安全性深入探討](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)：取得傳遞驗證功能的技術資訊。
- [Azure AD 無縫 SSO](active-directory-aadconnect-sso.md)：深入了解此互補功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 論壇提出新功能要求。

