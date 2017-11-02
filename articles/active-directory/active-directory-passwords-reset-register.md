---
title: "Azure AD：SSPR 註冊 | Microsoft Docs"
description: "註冊 Azure AD 自助式密碼重設的驗證資料"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: b884f8bc3a20052fa0cb40772deef591b69d7b10
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="register-for-self-service-password-reset"></a>註冊自助式密碼重設

> [!IMPORTANT]
> **您來到此處是因為有登入問題嗎？** 若是如此， [以下是如何變更和重設密碼的說明](active-directory-passwords-update-your-own-password.md)。

身為一般使用者，您可以重設密碼或解除鎖定您的帳戶，而不需使用自助式密碼重設 (SSPR) 與人員對話。 在您可以使用這項功能之前，您必須註冊驗證方法，或確認您系統管理員已填入的預先定義驗證方法。

## <a name="register-or-confirm-authentication-data-with-sspr"></a>使用 SSPR 註冊或確認驗證資料

1. 開啟您裝置的 web 瀏覽器並移至[密碼重設註冊頁面](http://aka.ms/ssprsetup)
2. 輸入您的使用者名稱和您系統管理員所提供的密碼
3. 視您的 IT 人員如何如何設定而定，下列一或多個選項可供您設定及驗證。 如果您的系統管理員有權限使用資訊，他們可能會為您填入部分資訊。
    * 只有您的系統管理員可以設定辦公室電話
    * [驗證電話] 應設定為另一個您可存取的電話號碼，如可以接收簡訊或電話的行動電話。
    * [驗證電子郵件] 應設定為您可以存取而不需要重設密碼的替代電子郵件地址。
    * [安全性問題] 可提供一份您系統管理員已核准的問題清單以供您回答。 您不可以使用相同的問題或回答一次以上。
4. 提供並確認您的系統管理員所需的資訊。 如果可使用多個選項，我們建議您註冊多個方法，以當另一種方法無法使用時提供彈性 (範例︰出差而無法存取您的辦公室電話)

    ![註冊驗證方法，並按一下 [完成]][Register]

5. 當完成步驟 4 時，選擇 [完成]現在您便可以在未來需要時使用自助式密碼重設。

如果您在驗證電話或驗證電子郵件中輸入資料，在全域目錄中會看不見。 您和您的系統管理員是唯一可以看到此資料的人員。 只有您可以查看您安全性問題的答案。

系統管理員在一段時間後可能會要求您確認驗證方法，確定您仍有適當的已註冊方法。

## <a name="common-problems-and-their-solutions"></a>常見問題及其解決方案

 以下提供一些常見的錯誤案例及其解決方案：

| 錯誤案例| 您看到什麼錯誤訊息？| 方案 |
| --- | --- | --- |
| 在輸入我的使用者 ID 後，出現了「請連絡您的系統管理員」頁面 | 請連絡您的系統管理員 <br> <br> 我們偵測到您的使用者帳戶密碼未受到 Microsoft 管理。 因此，我們無法自動重設您的密碼。 <br> <br> 您必須連絡 IT 人員以尋求進一步的協助。 | 您之所以看到此訊息，是因為 IT 人員在內部部署環境中管理您的密碼，而不允許您從 [無法存取您的帳戶] 連結重設您的密碼。 <br> <br> 若要重設密碼，請直接向 IT 人員尋求協助，讓他們了解您想要重設密碼，而為您啟用這項功能。|
| 在輸入我的使用者 ID 後，出現了「您的帳戶未啟用密碼重設」錯誤 | 您的帳戶未啟用密碼重設功能 <br> <br> 很抱歉，IT 人員還沒將您的帳戶設定用於此服務。 <br> <br> 如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。 | 您之所以看到此訊息，是因為 IT 人員尚未針對您的組織啟用從 [無法存取您的帳戶] 連結重設密碼的功能，或尚未授權您使用該功能。 <br> <br> 若要重設密碼，請按一下 [連絡系統管理員] 連結以傳送電子郵件給公司的 IT 人員，讓他們了解您想要重設密碼，而為您啟用這項功能。 |
| 在輸入我的使用者 ID 後，出現了「我們無法驗證您的帳戶」錯誤 | 我們無法驗證您的帳戶 <br> <br> 如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。 | 您之所以看到此訊息，是因為您已啟用密碼重設，但並未註冊使用此服務。 若要註冊密碼重設，請在重新取得帳戶存取權後移至 http://aka.ms/ssprsetup。 <br> <br> 若要重設密碼，請按一下 [連絡系統管理員] 連結，以傳送電子郵件給公司的 IT 人員。 |

## <a name="next-steps"></a>後續步驟

* [如何使用自助式密碼重設變更您的密碼](active-directory-passwords-update-your-own-password.md)
* [密碼重設註冊頁面](http://aka.ms/ssprsetup)
* [密碼重設入口網站](https://passwordreset.microsoftonline.com/)
* [無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "顯示已註冊方法及完成按鈕的密碼重設註冊頁面"
