---
title: "推出︰Azure AD SSPR | Microsoft Docs"
description: "成功推出 Azure AD 自助式密碼重設的祕訣"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 0be1616f5df915e566dc73c15dbea2e53177aa1c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="how-to-successfully-rollout-self-service-password-reset"></a>如何成功推出自助式密碼重設

大部分的客戶都會遵循下列步驟，以確保順利推出 SSPR 功能。

1. [在您的目錄中啟用密碼重設](active-directory-passwords-getting-started.md)。
2. [設定密碼回寫的內部部署 AD 權限](active-directory-passwords-writeback.md#active-directory-permissions)。
3. [設定密碼回寫](active-directory-passwords-writeback.md#configuring-password-writeback)以將密碼從 Azure AD 回寫到您的內部部署目錄。
4. [指派和驗證所需的授權](active-directory-passwords-licensing.md)。
5. 如果您想要漸進式地推出 SSPR，您可以限制使用者群組的存取權，讓您可以對特定群組進行試驗。 若要這麼做，請將 [已啟用自助式密碼重設] 切換開關設定為 [已選取]，然後選取要啟用密碼重設的安全性群組。 
6. 為您的使用者填入[驗證資料](active-directory-passwords-data.md)，例如其辦公室電話、行動電話和備用電子郵件地址。
7. [自訂 Azure AD 登入體驗以包含您公司的商標](active-directory-passwords-customize.md)。
8. 將如何註冊及如何重設的指示傳送給使用者，教導他們如何使用 SSPR。
9. 您可以選擇在任何時間點強制註冊，並要求使用者在一段時間後重新確認其驗證資訊。
10. 經過一段時間，藉由檢視 [Azure AD 所提供的報告](active-directory-passwords-reporting.md)，來檢閱使用者的註冊和使用情形。
11. 當您準備好時，就可以為所有使用者啟用密碼重設，將 [已啟用自助式密碼重設] 切換開關設定為 [全部]。 

   > [!IMPORTANT]
   > 以使用者並非系統管理員測試 SSPR，因為 Microsoft 會強制執行 Azure 系統管理員類型帳戶的強式驗證需求。 如需有關系統管理員密碼原則的詳細資訊，請參閱我們的[密碼原則文章](active-directory-passwords-policy.md#administrator-password-policy-differences)。

## <a name="email-based-rollout"></a>以電子郵件為基礎的啟用

許多客戶發現電子郵件行銷活動 (包含易於使用的指示)，這是讓使用者使用 SSPR 的最簡單方法。 [我們建立了三個可以作為範本的簡單電子郵件，以協助您推出。](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* **即將推出**電子郵件範本，可使用於推出前幾週或前幾天，讓使用者知道他們需要執行一些動作。
* **立即可用**電子郵件範本，可在推出當天驅使使用者註冊並確認其驗證資料，以便他們在需要時使用 SSPR。
* **註冊提醒**電子郵件範本，可在部署後幾天以至幾週提醒使用者進行註冊並確認其驗證資料。

![電子郵件][Email]

## <a name="creating-your-own-password-portal"></a>建立您自己的密碼入口網站

很多大型客戶都選擇裝載網頁並建立 DNS 根項目，例如 https://passwords.contoso.com。他們會在此頁面中填入 Azure AD 密碼重設、密碼重設註冊、密碼變更入口網站和其他組織特定資訊的連結。 您送出的任何電子郵件或傳單都可以包含一個品牌化、令人印象深刻的 URL，使用者可以在需要使用服務時前往該 URL。

* 密碼重設入口網站 - https://aka.ms/sspr
* 密碼重設註冊入口網站 - http://aka.ms/ssprsetup
* 密碼變更入口網站 - https://account.activedirectory.windowsazure.com/ChangePassword.aspx

為方便您使用，我們建立了範例頁面，您可以使用並根據組織需求加以自訂，請從 [GitHub](https://github.com/ajamess/password-reset-page) 進行下載。

## <a name="using-enforced-registration"></a>使用強制註冊

如果您希望使用者註冊密碼重設，可以強制他們在使用 Azure AD 登入時進行註冊。 您可以從目錄的 [密碼重設] 刀鋒視窗啟用此選項，方法是啟用 [註冊] 索引標籤上的 [要求使用者在登入時註冊] 選項。

系統管理員可以將 [要求使用者重新確認其驗證資訊的等候天數] 設定為 0-730 天，要求使用者在一段時間後重新註冊。

啟用此選項之後，使用者會在登入時會看到一則訊息，告知系統管理員已要求他們確認其驗證資訊。

## <a name="populate-authentication-data"></a>填入驗證資料

如果您[為使用者填入驗證資料](active-directory-passwords-data.md)，則使用者不需要先註冊密碼重設，就能夠使用 SSPR。 只要使用者定義的驗證資料符合您定義的密碼重設原則，使用者就能夠重設其密碼。

## <a name="disabling-self-service-password-reset"></a>停用自助式密碼重設

停用自助式密碼重設很簡單，只要開啟 Azure AD 租用戶並移至 [密碼重設]、[屬性]，然後選擇 [已啟用自助式密碼重設]之下的 [沒有人]。

## <a name="next-steps"></a>後續步驟

* [重設或變更您的密碼](active-directory-passwords-update-your-own-password.md)。
* [註冊自助式密碼重設](active-directory-passwords-reset-register.md)。
* [您有授權問題嗎？](active-directory-passwords-licensing.md)
* [SSPR 使用哪些資料，以及您應該為使用者填入哪些資料？](active-directory-passwords-data.md)
* [使用 SSPR 的原則選項有哪些？](active-directory-passwords-policy.md)
* [什麼是密碼回寫，且為什麼我需要了解它？](active-directory-passwords-writeback.md)
* [如何回報 SSPR 中的活動？](active-directory-passwords-reporting.md)
* [SSPR 中的所有選項有哪些，以及它們有何意義？](active-directory-passwords-how-it-works.md)
* [我認為有中斷。如何針對 SSPR 進行疑難排解？](active-directory-passwords-troubleshoot.md)
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "自訂這些電子郵件範本，以符合您組織的需求"