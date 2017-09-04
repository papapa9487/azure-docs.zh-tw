---
title: "Android 上的 Azure Active Directory 憑證式驗證 | Microsoft Docs"
description: "了解在有 Android 裝置的解決方案中，設定憑證式驗證的支援案例和需求"
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: c6ad7640-8172-4541-9255-770f39ecce0e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: nigu
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 239d2e1724f2759a52cccdac11fc781d57393125
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Android 上的 Azure Active Directory 憑證式驗證


在將您的 Exchange Online 帳戶連線到下列各項時，憑證式驗證 (CBA) 可讓您由 Azure Active Directory 透過用戶端憑證在 Windows、Android 或 iOS 裝置上驗證您的身分︰ 

* Office 行動應用程式，例如 Microsoft Outlook 與 Microsoft Word   
* Exchange ActiveSync (EAS) 用戶端 

設定這項功能之後，就不需要在行動裝置上的特定郵件和 Microsoft Office 應用程式中，輸入使用者名稱和密碼的組合。 

本主題針對 Office 365 企業版、商務版、教育版、美國政府機關、中國和德國方案的租用戶使用者，提供在 iOS (Android) 裝置上設定 CBA 的需求和支援案例。



在 Office 365 US Government Defense 和 Federal 方案中，這項功能處於預覽版。


## <a name="office-mobile-applications-support"></a>Office 行動應用程式支援
| 應用程式 | 支援 |
| --- | --- |
| Azure 資訊保護應用程式 |![勾選][1] |
| Microsoft Teams |![勾選][1] |
| OneNote |![勾選][1] |
| OneDrive |![勾選][1] |
| Outlook |![勾選][1] |
| Power BI |![勾選][1] |
| 商務用 Skype |![勾選][1] |
| Word / Excel / PowerPoint |![勾選][1] |
| Yammer |![勾選][1] |


### <a name="implementation-requirements"></a>實作需求

裝置作業系統版本必須是 Android 5.0 (Lollipop) 和更新版本。 

必須設定同盟伺服器。  

ADFS 權杖必須要有下列宣告，Azure Active Directory 才能撤銷用戶端憑證︰  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (用戶端憑證序號) 
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (用戶端憑證簽發者字串) 

如果 ADFS 權杖 (或任何其他 SAML 權杖) 中有上述宣告，Azure Active Directory 就會將這些宣告新增至重新整理權杖。 當需要驗證重新整理權杖時，這項資訊會用於檢查撤銷。 

最佳做法是依照如何取得使用者憑證的指示來更新 ADFS 錯誤頁面。  
如需詳細資訊，請參閱 [自訂 AD FS 登入頁面](https://technet.microsoft.com/library/dn280950.aspx)。  

某些 Office 應用程式 (已啟用新式驗證) 會在其要求中將 ‘*prompt=login*’ 傳送至 Azure AD。 根據預設，Azure AD 會將給 ADFS 的要求中的這項轉譯成 ‘*wauth=usernamepassworduri*’ (請求 ADFS 進行 U/P 驗證) 和 ‘*wfresh=0*’ (請求 ADFS 忽略 SSO 狀態並進行全新驗證)。 如果您想要啟用這些應用程式的憑證型驗證，您必須修改預設的 Azure AD 行為。 只要將您的同盟網域設定中的 'PromptLoginBehavior' 設定為 ‘Disabled‘。 您可以使用 [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) Cmdlet 來執行這項工作︰

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`



## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync 用戶端支援
支援 Android 5.0 (Lollipop) 或更新版本上的某些 Exchange ActiveSync 應用程式。 若要判斷電子郵件應用程式是否支援這項功能，請連絡您的應用程式開發人員。 


## <a name="next-steps"></a>後續步驟

如果您想要在環境中設定憑證式驗證，相關指示請參閱[在 Android 上開始使用憑證式驗證](active-directory-certificate-based-authentication-get-started.md)。

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png

