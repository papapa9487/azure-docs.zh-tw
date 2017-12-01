---
title: "自訂︰Azure AD SSPR | Microsoft Docs"
description: "Azure AD 自助式密碼重設的自訂選項"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: e8cf0ce8ed154a7e42b885e605dcdf37827a6447
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>自訂 Azure AD 的自助式密碼重設功能

想要在 Azure Active Directory (Azure AD) 中部署自助式密碼重設 (SSPR) 的 IT 專業人員可以自訂體驗以符合其使用者需求。

## <a name="customize-the-contact-your-administrator-link"></a>自訂 [請連絡您的系統管理員] 連結

即使未啟用 SSPR，使用者在密碼重設入口網站上仍然會有 [請連絡您的系統管理員] 連結。 如果使用者選取此連結，它將會執行下列其中一項操作：
   * 傳送電子郵件給系統管理員，請他們協助關變更使用者密碼。 
   * 將使用者傳送到您所指定的 URL 以取得協助。 

建議您將此連絡人設定為使用者已針對支援問題使用的電子郵件地址或網站等項目。

![連絡人][Contact]

這封連絡人電子郵件會依下列順序傳送給下列收件者︰

1. 如果已指派**密碼管理員**角色，就會通知具備此角色的系統管理員。
2. 如果未指派任何密碼管理員，就會通知具備**使用者管理員**角色的系統管理員。
3. 如果上述角色都未指派，則會通知**全域管理員**。

在所有情況下，最多 100 位收件者會收到通知。

若要深入了解不同的系統管理員角色，以及如何指派這些角色，請參閱[在 Azure Active Directory 中指派系統管理員角色](active-directory-assign-admin-roles-azure-portal.md)。

### <a name="disable-contact-your-administrator-emails"></a>停用 [請連絡您的系統管理員] 電子郵件

如果您的組織不想傳送密碼重設要求通知給系統管理員，則您可以啟用下列設定：

* 對所有使用者啟用自助式密碼重設。 此選項位於 [密碼重設] > [屬性] 底下。
  
  如果您不想讓使用者重設其自己的密碼，可以將存取範圍設定為空群組。 不建議使用此選項。
* 自訂技術服務人員連結，以提供可讓使用者取得協助的 Web URL 或 mailto︰位址。 此選項位於 [密碼重設] > [自訂] > [自訂的技術服務人員電子郵件或 URL] 底下。

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>自訂 SSPR 的 AD FS 登入頁面

「Active Directory 同盟服務」(AD FS) 系統管理員可以使用[新增登入頁面描述](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) \(機器翻譯\) 一文中所提供的指引，新增其登入頁面的連結。

若要新增 AD FS 登入頁面的連結，請在您的 AD FS 伺服器上使用下列命令。 使用者可以使用此頁面來進入 SSPR 工作流程。

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>自訂登入頁面和存取面板的外觀與風格

您可以自訂登入頁面。 您可以新增與符合您公司商標的影像一起顯示的標誌。

您選擇的圖形會在下列情況下顯示：

* 在使用者輸入其使用者名稱之後
* 如果使用者存取自訂的 URL，則會透過下列方式：
    * 將 *whr* 參數傳遞給密碼重設頁面，例如 "https://login.microsoftonline.com/?whr=contoso.com"
    * 將 *username* 參數傳遞給密碼重設頁面，例如 "https://login.microsoftonline.com/?username=admin@contoso.com"

### <a name="graphics-details"></a>圖形的詳細資料

請使用下列設定來變更登入頁面的視覺特性。 移至 [Azure Active Directory] > [公司商標] > [編輯公司商標]：

* 登入頁面影像應該是 .png 或 .jpg 檔案、1420 x 1200 像素，且不能大於 500 KB。 為了獲得最佳效果，建議您將它保持在 200 KB 左右。
* 登入頁面背景色彩會在高延遲連線時使用，且必須是 RGB 十六進位格式。
* 橫幅影像應該是 .png 或 .jpg 檔案、60 x 280 像素，且不能大於 10 KB。
* 方形標誌 (一般和深色佈景主題) 應該是 .png 或 .jpg 檔案、240 x 240 (可調整大小) 像素，且不能大於 10 KB。

### <a name="sign-in-text-options"></a>登入文字選項

請使用下列設定將與您組織相關的文字新增至登入頁面。 移至 [Azure Active Directory] > [公司商標] > [編輯公司商標]：

* **使用者名稱提示**：以更適合您使用者的文字取代範例文字 *someone@example.com*。 當您支援內部和外部使用者時，建議保留預設提示。
* **登入頁面文字**：長度上限為 256 個字元。 此文字會出現在使用者於線上及 Windows 10 之 Azure AD Workplace Join 體驗中登入的任何地方。 請使用此文字作為給使用者的使用條款、指示及提示。 

   >[!IMPORTANT]
   >所有人都可以看到您的登入頁面，因此請勿在此處提供任何機密資訊。
   >

### <a name="the-keep-me-signed-in-disabled-setting"></a>[讓我保持登入] 設定

使用 [讓我保持登入] 選項時，使用者可以在關閉又重新開啟瀏覽器視窗時，保持登入狀態。 這個選項不會影響工作階段存留期。 移至 [Azure Active Directory] > [公司商標] > [編輯公司商標]。

使用者必須能夠選取此方塊，才能使用 SharePoint Online 和 Office 2010 的某些功能。 如果您隱藏此選項，使用者可能會收到額外且非預期的登入提示。

### <a name="directory-name"></a>目錄名稱

您可以在 [Azure Active Directory] > [屬性] 底下變更目錄名稱屬性。 您可以顯示一個會在入口網站及自動化通訊中顯示的易記組織名稱。 這個選項最常以下列形式出現在自動化電子郵件中：

* 電子郵件中的易記名稱，例如「Microsoft 代表 CONTOSO 示範」
* 電子郵件中的主旨列，例如「CONTOSO 示範帳戶電子郵件驗證碼」

## <a name="next-steps"></a>後續步驟

* [如何完成 SSPR 成功首度發行？](active-directory-passwords-best-practices.md)
* [重設或變更您的密碼](active-directory-passwords-update-your-own-password.md)
* [註冊自助式密碼重設](active-directory-passwords-reset-register.md)
* [您有授權問題嗎？](active-directory-passwords-licensing.md)
* [SSPR 使用哪些資料，以及您應該為使用者填入哪些資料？](active-directory-passwords-data.md)
* [哪些驗證方法可供使用者使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [使用 SSPR 的原則選項有哪些？](active-directory-passwords-policy.md)
* [什麼是密碼回寫，且為什麼我需要了解它？](active-directory-passwords-writeback.md)
* [如何回報 SSPR 中的活動？](active-directory-passwords-reporting.md)
* [SSPR 中的所有選項有哪些，以及它們有何意義？](active-directory-passwords-how-it-works.md)
* [我認為有中斷。如何針對 SSPR 進行疑難排解？](active-directory-passwords-troubleshoot.md)
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "連絡您的系統管理員以協助重設您的密碼電子郵件範例"
