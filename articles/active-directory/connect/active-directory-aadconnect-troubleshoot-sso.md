---
title: "Azure AD Connect：針對無縫單一登入進行疑難排解 | Microsoft Docs"
description: "本主題說明如何針對 Azure Active Directory 無縫單一登入 (Azure AD 無縫 SSO) 進行疑難排解。"
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
ms.date: 08/26/2017
ms.author: billmath
ms.openlocfilehash: 7eea3621a52bf13dc44e89c342c503905ff24a0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>針對 Azure Active Directory 無縫單一登入進行疑難排解

這篇文章可協助您尋找有關 Azure AD 無縫單一登入常見問題的疑難排解資訊。

## <a name="known-issues"></a>已知問題

- 在某些情況下，啟用無縫 SSO 最久可能需要 30 分鐘。
- 不支援 Edge 瀏覽器。
- 在 Office 用戶端啟用授權 (尤其是在共用的電腦案例中) 會導致使用者收到額外的登入輸入。
- 在 Firefox 的私用瀏覽模式中，無法使用無縫 SSO。 和 
- Internet Explorer 在開啟「增強保護」模式時也無法使用無縫 SSO。
- iOS 和 Android 上的行動瀏覽器無法使用無縫 SSO。
- 如果您正在同步處理 30 個或更多的 AD 樹系，您無法使用 Azure AD Connect 啟用無縫 SSO。 您可以在租用戶上[手動啟用](#manual-reset-of-azure-ad-seamless-sso)此功能，以解決這個問題。
- 將 Azure AD 服務 URL (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) 新增至「信任的網站」區域而非「近端內部網路」區域，**以阻止使用者登入**。

## <a name="check-status-of-the-feature"></a>檢查功能的狀態

確認您租用戶端的無縫 SSO 功能仍為 [已啟用]。 您可以前往 [Azure Active Directory 管理中心](https://aad.portal.azure.com/)上的 [Azure AD Connect] 刀鋒視窗來檢查狀態。

![Azure Active Directory 管理中心 - Azure AD Connect 刀鋒視窗](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Azure Active Directory 管理中心上的登入失敗原因 (需要 Premium 授權)

如果您的租用戶已有與其相關聯的 Azure AD Premium 授權，您也可以查看 [Azure Active Directory 管理中心](https://aad.portal.azure.com/)上的[登入活動報告](../active-directory-reporting-activity-sign-ins.md)。

![Azure Active Directory 管理中心 - 登入報告](./media/active-directory-aadconnect-sso/sso9.png)

巡覽至位在 [Azure Active Directory 管理中心](https://aad.portal.azure.com/)的 **Azure Active Directory** -> [登入]，按一下特定使用者的登入活動。 尋找 [登入錯誤碼] 欄位。 使用下表，將該欄位的值對應至失敗的原因和解決方式：

|登入錯誤碼|登入失敗原因|解決方案
| --- | --- | ---
| 81001 | 使用者的 Kerberos 票證太大。 | 降低使用者的群組成員資格，並再試一次。
| 81002 | 無法驗證使用者的 Kerberos 票證。 | 請參閱[針對檢查清單進行疑難排解](#troubleshooting-checklist)。
| 81003 | 無法驗證使用者的 Kerberos 票證。 | 請參閱[針對檢查清單進行疑難排解](#troubleshooting-checklist)。
| 81004 | Kerberos 驗證嘗試失敗。 | 請參閱[針對檢查清單進行疑難排解](#troubleshooting-checklist)。
| 81008 | 無法驗證使用者的 Kerberos 票證。 | 請參閱[針對檢查清單進行疑難排解](#troubleshooting-checklist)。
| 81009 | 「無法驗證使用者的 Kerberos 票證。 | 請參閱[針對檢查清單進行疑難排解](#troubleshooting-checklist)。
| 81010 | 無縫式 SSO 失敗，因為使用者的 Kerberos 票證已過期或無效。 | 使用者需要從您公司網路內部已加入網域的裝置登入。
| 81011 | 找不到以使用者的 Kerberos 票證中資訊為基礎的使用者物件。 | 使用 Azure AD Connect 同步處理使用者資訊至 Azure AD。
| 81012 | 嘗試登入 Azure AD 的使用者與登入裝置的使用者不同。 | 從不同的裝置登入。
| 81013 | 找不到以使用者的 Kerberos 票證中資訊為基礎的使用者物件。 |使用 Azure AD Connect 同步處理使用者資訊至 Azure AD。 

## <a name="troubleshooting-checklist"></a>疑難排解檢查清單

使用下列檢查清單來針對無縫 SSO 問題進行疑難排解︰

- 在 Azure AD Connect 上檢查是否已啟用無縫 SSO 功能。 如果您無法啟用此功能 (例如，因為連接埠已封鎖)，請確定您已完成所有[必要條件](active-directory-aadconnect-sso-quick-start.md#step-1-check-prerequisites)。
- 請檢查這兩個 Azure AD URL (https://autologon.microsoftazuread-sso.com 和 https://aadg.windows.net.nsatc.net) 都屬於使用者的內部網路區域設定。
- 確定公司裝置已加入 AD 網域。
- 確定使用者是使用 AD 網域帳戶登入裝置。
- 確定使用者帳戶是來自已設定無縫 SSO 的 AD 樹系。
- 確定裝置已連線到公司網路。
- 確定裝置的時間已經與 Active Directory 和網域控制站的時間同步，且彼此的時間差不到五分鐘。
- 從命令提示字元使用 **klist** 命令，列出裝置上現有的 Kerberos 票證。 檢查是否有核發給 `AZUREADSSOACCT` 電腦帳戶的票證。 使用者的 Kerberos 票證有效期通常為 12 個小時。 您的 Active Directory 可能有不同的設定。
- 使用 **klist purge** 命令從裝置中清除現有的 Kerberos 票證，然後再試一次。
- 若要判斷是否有 JavaScript 相關問題，請檢閱瀏覽器的主控台記錄 (在 [開發人員工具] 底下)。
- 同時檢閱[網域控制站記錄](#domain-controller-logs)。

### <a name="domain-controller-logs"></a>網域控制站記錄

如果網域控制站已啟用成功稽核，則每次使用者使用無縫 SSO 登入時，系統都會在事件記錄中記錄一個安全性項目。 您可以使用下列查詢找到這些安全性事件 (尋找與電腦帳戶 **AzureADSSOAcc$** 建立關聯的事件 **4769**)：

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>手動重設該功能

如果疑難排解無法解決問題，您可以在租用戶上手動重設此功能。 請在執行 Azure AD Connect 的內部部署伺服器上依照下列步驟操作：

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>步驟 1：匯入無縫 SSO PowerShell 模組

1. 首先，下載並安裝 [Microsoft Online Services 登入小幫手](http://go.microsoft.com/fwlink/?LinkID=286152)。
2. 接著下載並安裝 [適用於 Windows PowerShell 的 64 位元 Azure Active Directory 模組](http://go.microsoft.com/fwlink/p/?linkid=236297)。
3. 瀏覽到 `%programfiles%\Microsoft Azure Active Directory Connect` 資料夾。
4. 使用此命令匯入順暢 SSO PowerShell 模組：`Import-Module .\AzureADSSO.psd1`。

### <a name="step-2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>步驟 2：取得已啟用無縫 SSO 的 AD 樹系清單

1. 以系統管理員身分執行 PowerShell。 在 PowerShell 中，呼叫 `New-AzureADSSOAuthenticationContext`。 出現提示時，輸入您租用戶的全域管理員認證。
2. 呼叫 `Get-AzureADSSOStatus`。 此命令會提供已啟用這項功能的 AD 樹系清單 (查看 [網域] 清單)。

### <a name="step-3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>步驟3：針對已設定無縫 SSO 的每個 AD 樹系停用該功能

1. 呼叫 `$creds = Get-Credential`。 出現提示時，輸入預定 Azure AD 樹系的網域系統管理員認證。
2. 呼叫 `Disable-AzureADSSOForest -OnPremCredentials $creds`。 此命令會從此特定 AD 樹系的內部部署網域控制站中移除 `AZUREADSSOACCT` 電腦帳戶。
3. 針對您已設定此功能的每個 AD 樹系，重複上述步驟。

### <a name="step-4-enable-seamless-sso-for-each-ad-forest"></a>步驟 4：為每個 AD 樹系啟用無縫 SSO

1. 呼叫 `Enable-AzureADSSOForest`。 出現提示時，輸入預定 Azure AD 樹系的網域系統管理員認證。
2. 針對您想要設定此功能的每個 AD 樹系，重複上述步驟。

### <a name="step-5-enable-the-feature-on-your-tenant"></a>步驟 5。 啟用租用戶上的功能

呼叫 `Enable-AzureADSSO` 並且在 `Enable: ` 提示字元鍵入 "true"，以在租用戶中開啟該功能。
