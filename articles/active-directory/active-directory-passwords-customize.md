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
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 1b6e368df4914e58eb3f8d6481132f25d27312b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>自訂 Azure AD 的自助式密碼重設功能

IT 專業人員期待部署自助式密碼重設可以自訂體驗以符合他們的使用者需求。

## <a name="customize-the-contact-your-administrator-link"></a>自訂「連絡您的系統管理員」連結

即使未啟用 SSPR，使用者在密碼重設入口網站上仍會看到「連絡您的系統管理員」連結。  按一下此連結會寄電子郵件給系統管理員，請他協助關變更使用者的密碼。 這封電子郵件會依下列順序傳送給下列收件者︰

1. 如果已指派**密碼系統管理員**角色，具有此角色的系統管理員會收到通知
2. 如果未指派密碼系統管理員，則具有**使用者系統管理員**角色的系統管理員會收到通知
3. 如果上述角色都未指派，則**全域管理員**會收到通知

在所有情況下，最多 100 位收件者會收到通知。

若要進一步了解不同的系統管理員角色，以及如何指派他們，請參閱文件[在 Azure Active Directory 中指派系統管理員角色](active-directory-assign-admin-roles.md)

### <a name="disable-contact-your-administrator-emails"></a>停用「連絡您的系統管理員」電子郵件

如果您的組織不想讓系統管理員收到有關密碼重設要求的通知，則可以啟用下列設定

* 對所有使用者啟用自助式密碼重設。 此選項位於 [密碼重設] > [屬性] 下。
    * 如果您不想讓使用者重設其自己的密碼，您可以將存取範圍設為空的群組，**但我們不建議使用此選項**。
* 自訂技術服務人員連結，以提供可讓使用者取得協助的 Web URL 或 mailto︰位址。 此選項位於 [密碼重設] > [自訂] > [自訂的技術服務人員電子郵件或 URL] 下。

## <a name="customize-adfs-sign-in-page-for-sspr"></a>為 SSPR 自訂 ADFS 登入頁面

ADFS 系統管理員可以使用在[新增登入分頁描述](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description)一文中找到的指引，將連結新增至他們的登入分頁。

使用 ADFS 伺服器上接續的命令，將連結新增至 ADFS，讓使用者直接輸入自助式密碼重設工作流程。

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>自訂登入和存取面板的外觀與風格

當您的使用者存取登入頁面時，您可以自訂隨著登入頁面影像一起出現的標誌，以符合您的公司商標。

下列情況會顯示這些圖形︰

* 使用者輸入其使用者名稱之後
* 使用者存取自訂的 url
    * 將 "whr" 參數傳遞至密碼重設頁面，例如 "https://login.microsoftonline.com/?whr=contoso.com"
    * 將 "username" 參數傳遞至密碼重設頁面，例如 "https://login.microsoftonline.com/?username=admin@contoso.com"

### <a name="graphics-details"></a>圖形的詳細資料

下列設定可讓您變更登入頁面的視覺特性，您可以在 [Azure Active Directory]、[公司商標]、[編輯公司商標] 下找到這些設定

* 登入頁面影像應該是 PNG 或 JPG 檔案 1420x1200 像素，不能大於 500KB。 建議大約 200 KB 即可，效果最佳。
* 登入分頁背景色彩在高延遲連線時使用，必須是 RGB 十六進位格式。
* 橫幅影像應該是 PNG 或 JPG 檔案 60x280 像素，不能大於 10 KB。
* 正方形標誌 (一般和深色佈景主題) PNG 或 JPG 240x240 (可調整大小)，不能大於 10 KB。

### <a name="sign-in-text-options"></a>登入文字選項

下列設定可讓您將組織的相關文字新增至登入頁面。 您可以在 [Azure Active Directory]、[公司商標]、[編輯公司商標] 下找到這些設定

* [使用者名稱提示] 會以更適合使用者的文字取代範例文字 someone@example.com，當支援內部和外部使用者時，建議保留預設值
* [登入頁面文字] 的長度最多 256 個字元。 此文字會出現在使用者登入線上的任何地方，也出現在 Windows 10 的 Azure AD Join 體驗中。 使用此文字作為給使用者的使用條款、指示和提示。 **任何人都可以看到您的登入頁面，請勿在此處提供任何機密資訊。**

### <a name="keep-me-signed-in-disabled"></a>已停用 [讓我保持登入]

[已停用讓我保持登入] 選項可讓使用者在關閉又重新開啟瀏覽器視窗時，保持登入狀態。 這個選項不會影響工作階段存留期。 您可以在 [Azure Active Directory] > [公司商標] > [編輯公司商標] 下找到此設定。

使用者必須能夠勾選此方塊，才能使用 SharePoint Online 和 Office 2010 的某些功能。 如果您隱藏此選項時，使用者可能會收到其他非預期的登入提示。

### <a name="directory-name"></a>目錄名稱

您可以在 [Azure Active Directory] > [屬性] 下變更名稱屬性，讓入口網站和自動通訊中顯示好記的組織名稱。 這個選項最常以如下的自動化電子郵件形式出現

* 「Microsoft 代表 CONTOSO 示範」電子郵件中的易記名稱
* 「CONTOSO 示範帳戶電子郵件驗證碼」電子郵件的主旨列

## <a name="next-steps"></a>後續步驟

下列連結提供有關使用 Azure AD 重設密碼的其他資訊

* [**快速入門**](active-directory-passwords-getting-started.md) - 開始執行 Azure AD 自助式密碼管理 
* [**授權**](active-directory-passwords-licensing.md) - 設定 Azure AD 授權
* [**資料**](active-directory-passwords-data.md) -了解所需的資料以及如何將它使用於密碼管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此處提供的指引來規劃 SSPR 並部署給使用者
* [**原則**](active-directory-passwords-policy.md) - 了解並設定 Azure AD 密碼原則
* [**密碼回寫**](active-directory-passwords-writeback.md) - 密碼回寫如何使用您的內部部署目錄
* [**報告**](active-directory-passwords-reporting.md) - 探索您的使用者是否、何時、何地存取 SSPR 功能
* [**技術性深入探討**](active-directory-passwords-how-it-works.md) - 深入探索以了解其運作方式
* [**常見問題集**](active-directory-passwords-faq.md) - 如何？ 原因為何？ 何事？ 何地？ 何人？ 何時？ - -您一直想要詢問之問題的答案
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何解決我們看到的 SSPR 常見問題

