---
title: "Azure AD：密碼重設 |Microsoft Docs"
description: "使用自助式密碼重設來重新取得您工作或學校使用者帳戶的存取權"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: gahug
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 9595b318b8bd974f778dc12b38cccd949a4e7a1a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="reset-your-work-or-school-password"></a>重設您的工作密碼或學校密碼

如果您忘記密碼、未曾收到公司支援團隊給予的密碼、系統鎖定了您帳戶的密碼、或是您想要變更密碼，我們都可以提供協助。 如果您知道密碼，而且只是需要加以變更，請逕行前往下面的[變更我的密碼](#change-my-password)一節。

   > [!NOTE]
   > 如果您想要回到個人帳戶 (例如，Xbox、hotmail.com 或 outlook.com)，請嘗試[這篇文章所提供的建議](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>重設公司或學校帳戶的密碼或將密碼解除鎖定

如果您因為下列其中一個原因而無法存取帳戶：

* 密碼沒有作用，所以您想要予以重設。
* 您知道密碼，但您的帳戶已遭鎖定，因此想要將帳戶解除鎖定。

請遵循下列步驟來存取 Azure AD 自助式密碼重設 (我們喜歡將它簡稱為 SSPR)，以取回您的帳戶。

1. 從公司或學校的任何登入頁面按一下 [無法存取您的帳戶嗎?] 連結，然後按一下 [公司或學校帳戶]，或直接移至[密碼重設頁面](https://passwordreset.microsoftonline.com/)。

    ![無法存取您的帳戶嗎？][Login]

2. 輸入公司或學校的 [使用者識別碼]，輸入您在畫面上所看到的字元以證明您不是機器人，然後按 [下一步]。

   > [!NOTE]
   > 如果您的 IT 人員尚未啟用這項功能，畫面上便會出現「連絡系統管理員」連結，以供 IT 人員透過其本身的電子郵件或 Web 入口網站來提供協助。
   >
   > 如果您需要將帳戶解除鎖定，請在此時選擇 [我知道密碼，但卻無法登入] 選項。
   >

3. 根據 IT 人員所使用的 SSPR 設定方式而定，您會看到下列一或多個驗證方法。 您或您的 IT 人員之前已使用[註冊自助式密碼重設](active-directory-passwords-reset-register.md)一文來填入下列某幾項資訊。

   * **寄電子郵件到我的備用電子郵件地址**
   * **傳簡訊到我的行動電話**
   * **撥打我的行動電話**
   * **撥打我的辦公室電話**
   * **回答我的安全性問題**

   選擇某個選項，提供正確的回應，然後按 [下一步]。

   ![確認驗證資料][Verification]

4. 您的 IT 人員可能需要更多的驗證，因此您可能必須使用不同的選項來重複執行步驟 3。
5. 在 [選擇新密碼] 頁面上輸入新的密碼，確認您的密碼，然後按一下 [完成]。 您的工作或學校密碼可能有特定要求；我們建議的密碼是 8-16 個字元，並具有大小寫字母、數字和特殊字元。
6. 當您看到**您的密碼已經重設**時，可以使用您的新密碼登入。

    ![您的密碼已經重設][Complete]

您現在應該可以存取帳戶了，如果情況不是如此，請連絡貴組織的 IT 人員以獲得進一步協助。

您可能會收到確認電子郵件，其來源是「Microsoft 代表 \<貴組織>」之類的帳戶。 如果您收到這樣的電子郵件，但您不是使用自助式密碼重設來重新取得帳戶的存取權，請連絡貴組織的 IT 人員。

## <a name="change-my-password"></a>變更我的密碼

如果您已經知道密碼並想要加以變更，請使用下列步驟來變更密碼。

### <a name="change-your-password-from-the-office-365-portal"></a>從 Office 365 入口網站變更您的密碼

如果您通常是使用 Office 入口網站來存取應用程式，請使用這個方法

1. 使用現有的密碼登入 [Office 365 帳戶](https://www.office.com)
2. 按一下您右上方的設定檔，然後按一下 [檢視帳戶]
3. 按一下 [安全性與隱私權] > [密碼]
4. 輸入舊密碼，設定並確認新密碼，然後按一下 [提交]

### <a name="change-your-password-from-the-azure-access-panel"></a>從 Azure 存取面板變更您的密碼

如果您通常是從 Azure 存取入口網站來存取應用程式，請使用這個方法

1. 使用您的現有密碼，登入 [Azure 存取入口網站](https://myapps.microsoft.com/)
2. 按一下您右上方的設定檔，然後按一下 [設定檔]
3. 按一下 [變更密碼]
4. 輸入舊密碼，設定並確認新密碼，然後按一下 [提交]

## <a name="reset-password-at-login"></a>登入時重設密碼

如果您的管理員已啟用該功能，則現在可以在 Windows 10 Fall Creators Update 登入畫面中看到**重設密碼**的連結。

![LoginScreen][LoginScreen]

按一下 [重設密碼] 連結，即會在登入畫面開啟 SSPR 體驗，如此即可以重設密碼，而無需登入使用一般的 Web 式體驗。

1. 確認您的使用者識別碼，然後按一下 [下一步]
2. 選擇並確認驗證的連絡方法。 您的 IT 人員可能需要更多的驗證，因此您可能必須使用不同的選項來重複此步驟。

   ![ContactMethod][ContactMethod]

3. 在 [建立新密碼] 頁面上輸入新的密碼，確認您的密碼，然後按一下 [下一步]。 我們建議的密碼是 8-16 個字元，並具有大小寫字母、數字和特殊字元。

   ![ResetPassword][ResetPassword]

4. 當您看到 [您的密碼已重設] 時，按一下 [完成]

您現在應該可以存取帳戶了，如果情況不是如此，請連絡貴組織的 IT 人員以獲得進一步協助。

## <a name="common-problems-and-their-solutions"></a>常見問題及其解決方案

 以下提供一些常見的錯誤案例及其解決方案：

| 錯誤案例| 您看到什麼錯誤訊息？| 方案 |
| --- | --- | --- |
| 在輸入我的使用者 ID 後，出現了「請連絡您的系統管理員」頁面 | 請連絡您的系統管理員 <br> <br> 我們偵測到您的使用者帳戶密碼未受到 Microsoft 管理。 因此，我們無法自動重設您的密碼。 <br> <br> 您必須連絡 IT 人員以尋求進一步的協助。 | 您之所以看到此訊息，是因為 IT 人員在內部部署環境中管理您的密碼，而不允許您從 [無法存取您的帳戶] 連結重設您的密碼。 <br> <br> 若要重設密碼，請直接向 IT 人員尋求協助，讓他們了解您想要重設密碼，而為您啟用這項功能。|
| 在輸入我的使用者 ID 後，出現了「您的帳戶未啟用密碼重設」錯誤 | 您的帳戶未啟用密碼重設功能 <br> <br> 很抱歉，IT 人員還沒將您的帳戶設定用於此服務。 <br> <br> 如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。 | 您之所以看到此訊息，是因為 IT 人員尚未針對您的組織啟用從 [無法存取您的帳戶] 連結重設密碼的功能，或尚未授權您使用該功能。 <br> <br> 若要重設密碼，請按一下 [連絡系統管理員] 連結以傳送電子郵件給公司的 IT 人員，讓他們了解您想要重設密碼，而為您啟用這項功能。 |
| 在輸入我的使用者 ID 後，出現了「我們無法驗證您的帳戶」錯誤 | 我們無法驗證您的帳戶 <br> <br> 如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。 | 您之所以看到此訊息，是因為您已啟用密碼重設，但並未註冊使用此服務。 若要註冊密碼重設，請在重新取得帳戶存取權後移至 http://aka.ms/ssprsetup。 <br> <br> 若要重設密碼，請按一下 [連絡系統管理員] 連結，以傳送電子郵件給公司的 IT 人員。 |

## <a name="next-steps"></a>後續步驟

* [如何註冊使用自助式密碼重設](active-directory-passwords-reset-register.md)
* [密碼重設註冊頁面](http://aka.ms/ssprsetup)
* [密碼重設入口網站](https://passwordreset.microsoftonline.com/)
* [無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "登入頁面 [無法存取您的帳戶嗎?]"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "確認驗證資料"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "變更密碼"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "密碼已經重設"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Windows 10 Fall Creators Update 登入畫面重設密碼連結"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "確認驗證資料"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "變更密碼"
