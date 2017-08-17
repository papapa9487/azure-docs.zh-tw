---
title: "快速入門︰Azure AD SSPR | Microsoft Docs"
description: "快速部署 Azure AD 自助式密碼重設"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 07c7f3ad066c735054cb339f6e09aa4d7d23f23a
ms.contentlocale: zh-tw
ms.lasthandoff: 08/11/2017

---
# <a name="quickstart-azure-ad-self-service-password-reset"></a>快速入門︰Azure AD 自助式密碼重設

> [!IMPORTANT]
> **您來到此處是因為有登入問題嗎？** 若是如此， [以下是如何變更和重設密碼的說明](active-directory-passwords-update-your-own-password.md)。

## <a name="rapidly-deploy-self-service-password-reset"></a>快速部署自助式密碼重設

自助式密碼重設 (SSPR) 提供了簡單的方法，讓 IT 系統管理員准許使用者重設或解除鎖定其密碼或帳戶。 系統包含詳細的報告，以追蹤使用者何時使用系統與通知來警示您誤用或濫用。

本指南假設您已經有有效的試用或經過授權的 Azure AD 租用戶。 如果您需要設定 Azure AD 的說明，請參閱[開始使用 Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/)一文。

1. 從現有的 Azure AD 租用戶，選取 [密碼重設]

2. 從 [屬性] 畫面的 [已啟用自助式密碼重設] 之下選擇下列其中一項：
    * 沒有人 - 沒有人能夠使用 SSPR 功能
    * 群組 - 只有您所選的特定 Azure AD 群組成員能夠使用 SSPR 功能
    * 每個人 - 在您的 Azure AD 租用戶中具有帳戶的所有使用者都能夠使用 SSPR 功能

3. 從 [驗證方法] 畫面選擇：
    * 需要重設的方法數 - 我們最少支援一種方法或最多支援兩種方法
    * 使用者可用的方法 - 我們至少需要一種方法，但是有額外的選擇可用也沒關係
        * **電子郵件**可透過使用者設定的驗證電子郵件地址代碼傳送電子郵件
        * **行動電話**可讓使用者選擇使用其設定的行動電話號碼代碼來接收電話或簡訊
        * **辦公室電話**可透過使用者設定的辦公室電話號碼代碼打電話給使用者
        * **安全性問題**會要求您選擇：
            * 註冊所需的問題數 - 註冊成功的最低限度，這表示使用者可以選擇回答更多問題來建立可提取的問題集區。 這個選項可以設定為 3 到 5 個問題，而且必須大於或等於重設所需的問題數目。
                * 選取安全性問題時按一下 [自訂] 按鈕，即可新增自訂問題
            * 重設所需的問題數 - 可以設定為在允許重設或解除鎖定使用者密碼之前，需正確回答的 3 到 5 個問題。

4. 建議︰[自訂] 可讓您變更「連絡系統管理員」連結，以指向您定義的網頁或電子郵件地址

5. 選擇性︰[註冊] 畫面會提供系統管理員下列選項︰
    * 登入時要求使用者註冊
    * 要求使用者重新確認其驗證資訊的等候天數

6. 選擇性︰[通知] 畫面會提供系統管理員下列選項︰
    * 通知使用者密碼重設
    * 當其他系統管理員重設其密碼時通知所有系統管理員

**此時，您已為 Azure AD 租用戶設定 SSPR**。 您可以在此停止，或繼續設定將密碼同步處理至內部部署 AD 網域。

> [!NOTE]
> 以使用者並非系統管理員測試 SSPR，因為 Microsoft 會強制執行 Azure 系統管理員類型帳戶的強式驗證需求。 如需有關系統管理員密碼原則的詳細資訊，請參閱我們的[密碼原則文章](active-directory-passwords-policy.md#administrator-password-policy-differences)。

## <a name="configure-synchronization-to-existing-identity-source"></a>設定現有身分識別來源的同步處理

若要啟用內部部署身分識別同步處理至 Azure AD，您必須在您組織中的伺服器上安裝及設定 [Azure AD Connect](./connect/active-directory-aadconnect.md)。 此應用程式會將您現有身分識別來源的使用者和群組同步到您的 Azure AD 租用戶。

* [從 DirSync 或 Azure AD Sync 升級至 Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [使用快速設定開始使用 Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md)
* [設定密碼回寫](active-directory-passwords-writeback.md#configuring-password-writeback)以將密碼從 Azure AD 回寫到您的內部部署目錄。

## <a name="disabling-self-service-password-reset"></a>停用自助式密碼重設

停用自助式密碼重設很簡單，只要開啟 Azure AD 租用戶並移至 [密碼重設]、[屬性] > 選擇 [已啟用自助式密碼重設] 之下的 [無]。

### <a name="learn-more"></a>詳細資訊
下列連結提供有關使用 Azure AD 重設密碼的其他資訊

* [**授權**](active-directory-passwords-licensing.md) - 設定 Azure AD 授權
* [**資料**](active-directory-passwords-data.md) -了解所需的資料以及如何將它使用於密碼管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此處提供的指引來規劃 SSPR 並將它部署至使用者
* [**自訂**](active-directory-passwords-customize.md) - 為您的公司自訂 SSPR 體驗的外觀及操作方式。
* [**原則**](active-directory-passwords-policy.md) - 了解並設定 Azure AD 密碼原則
* [**報告**](active-directory-passwords-reporting.md) - 探索您的使用者是否、何時、何地存取 SSPR 功能
* [**技術性深入探討**](active-directory-passwords-how-it-works.md) - 深入探索以了解其運作方式
* [**常見問題集**](active-directory-passwords-faq.md) - 如何？ 原因為何？ 何事？ 何地？ 何人？ 何時？ - -您一直想要詢問之問題的答案
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何解決我們看到的 SSPR 常見問題

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已學到如何為您的使用者設定自助式密碼重設。 若要繼續在 Azure 入口網站完成這些步驟，請遵循以下的入口網站連結。

> [!div class="nextstepaction"]
> [啟用自助密碼重設](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)


