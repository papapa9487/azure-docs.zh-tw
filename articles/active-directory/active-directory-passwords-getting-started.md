---
title: "快速入門︰Azure AD SSPR | Microsoft Docs"
description: "快速部署 Azure AD 自助式密碼重設"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: c5ba922175a25776c6e27ee03fae9b2941235881
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>快速部署 Azure AD 自助式密碼重設

> [!IMPORTANT]
> **您來到此處是因為有登入問題嗎？** 若是如此，請參閱[忘記 Azure AD 密碼的說明](active-directory-passwords-update-your-own-password.md)。

自助式密碼重設 (SSPR) 提供了簡單的方法，讓 IT 系統管理員准許使用者重設或解除鎖定其密碼或帳戶。 系統包含詳細的報告，可追蹤使用者何時存取系統與通知來警示您誤用或濫用。

本指南假設您已經有有效的試用或經過授權的 Azure Active Directory (Azure AD) 租用戶。 如果您需要設定 Azure AD 的說明，請參閱[開始使用 Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/)。

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>啟用 Azure AD 租用戶的 SSPR

1. 從現有的 Azure AD 租用戶，選取 [密碼重設]。

2. 從 [屬性] 頁面的 [已啟用自助式密碼重設] 之下，選擇下列其中一項：
    * **無**：沒有人能夠使用 SSPR 功能。
    * **已選取**：只有您所選的特定 Azure AD 群組成員可以使用 SSPR 功能。 我們建議您定義使用者群組，並在部署此功能以用於概念證明時，使用這項設定。
    * **全部**：在您的 Azure AD 租用戶中具有帳戶的所有使用者都可以使用 SSPR 功能。 我們建議當您完成概念證明之後，準備要將此功能部署至整個租用戶時，使用這項設定。

3. 從 [驗證方法] 頁面，選擇下列項目：
    * **需要重設的方法數**：我們最少支援一種方法或最多支援兩種方法。
    * **使用者可用的方法**：我們至少需要一種方法，但是有額外的選擇可用也沒關係。
        * **電子郵件**：將具有代碼的電子郵件傳送至使用者設定的驗證電子郵件地址。
        * **行動電話**：讓使用者選擇使用其設定的行動電話號碼代碼來接收電話或簡訊。
        * **辦公室電話**：透過使用者設定的辦公室電話號碼代碼打電話給使用者。
        * **安全性問題**：要求您選擇：
            * **註冊所需的問題數目**：成功註冊的最小值。 使用者可以選擇回答更多問題，以建立可提取的問題集區。 這個選項可以設定為 3 到 5 個問題，而且必須大於或等於重設其密碼所需的問題數目。 當使用者選取其安全性問題時，如果他們選取 [自訂] 按鈕，即可新增自訂問題。
            * **重設所需的問題數**：可以設定為在允許重設或解除鎖定使用者密碼之前，需正確回答的 3 到 5 個問題。
            
    ![驗證][Authentication]

4. 建議︰在 [自訂] 之下，您可以變更「連絡系統管理員」連結，以指向您定義的網頁或電子郵件地址。 建議您將此連結設為諸如您的使用者已用於支援問題的電子郵件地址或網站等項目。

5. 選擇性︰[註冊] 頁面會提供系統管理員下列選項︰
    * 登入時要求使用者註冊。
    * 設定要求使用者重新確認其驗證資訊的等候天數。

6. 選擇性︰[通知] 頁面會提供系統管理員下列選項︰
    * 通知使用者密碼重設。
    * 當其他系統管理員重設其密碼時通知所有系統管理員。

此時，您已為 Azure AD 租用戶設定 SSPR。 您的使用者現在可以使用[註冊自助式密碼重設](active-directory-passwords-reset-register.md)和[重設或變更密碼](active-directory-passwords-update-your-own-password.md)文章中的指示，來更新他們的密碼，而不需要系統管理員介入。 如果您只要在雲端上設定，您可以在此停止。 或者繼續下一節，以設定將密碼同步處理至內部部署 Active Directory 網域。

> [!IMPORTANT]
> 以使用者而非系統管理員測試 SSPR，因為 Microsoft 會強制執行 Azure 系統管理員帳戶的強式驗證需求。 如需有關系統管理員密碼原則的詳細資訊，請參閱我們的[密碼原則](active-directory-passwords-policy.md#administrator-password-policy-differences)一文。

## <a name="configure-synchronization-to-an-existing-identity-source"></a>設定現有身分識別來源的同步處理

若要啟用內部部署身分識別同步處理至 Azure AD，您必須在您組織中的伺服器上安裝及設定 [Azure AD Connect](./connect/active-directory-aadconnect.md)。 此應用程式會將您現有身分識別來源的使用者和群組同步處理到您的 Azure AD 租用戶。 如需詳細資訊，請參閱：

* [從 DirSync 或 Azure AD Sync 升級至 Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [使用快速設定來開始使用 Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md)
* [設定密碼回寫](active-directory-passwords-writeback.md#configuring-password-writeback)以將密碼從 Azure AD 回寫到您的內部部署目錄

### <a name="on-premises-policy-change"></a>內部部署原則變更

如果您同步處理內部部署 Active Directory 網域的使用者，而且想要讓使用者立即重設他們的密碼，請在內部部署密碼原則中執行下列變更：

1. 瀏覽到 [電腦組態] > [原則] > [Windows 設定] > [安全性設定] > [帳戶原則] > [密碼原則]。

2. 將 [密碼有效期最小值] 設定為 [0 天]。

此安全性設定會決定在使用者可以變更密碼前，必須使用密碼的期間 (天數)。 如果您將最小使用量設為 [0 天]，使用者即可在他們的密碼被其支援小組變更後使用 SSPR。

![原則][Policy]

## <a name="disable-self-service-password-reset"></a>啟用自助式密碼重設

要停用自助式密碼重設很容易。 開啟您的 Azure AD 租用戶並移至 [密碼重設] > [屬性]，然後選取 [已啟用自助式密碼重設] 之下的 [無]。

### <a name="learn-more"></a>詳細資訊
下列文章提供有關透過 Azure AD 重設密碼的其他資訊：

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

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已學到如何為您的使用者設定自助式密碼重設。 若要完成下列步驟，請繼續使用 Azure 入口網站：

> [!div class="nextstepaction"]
> [啟用自助密碼重設](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "可供使用的 Azure AD 驗證方法和所需的數量"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "內部部署密碼的群組原則設定為 0 天"

