---
title: "運作方式 Azure AD SSPR | Microsoft Docs"
description: "Azure AD 自助式密碼重設深入探討"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 71310534ec62b62bcd408d75060859c79bc470cf
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Azure AD 中的自助式密碼重設深入探討

SSPR 的運作方式 該選項在介面中的意義為何？ 繼續閱讀以深入了解 Azure AD 自助式密碼重設。

## <a name="how-does-the-password-reset-portal-work"></a>密碼重設入口網站的運作方式

當使用者瀏覽至密碼重設入口網站時，工作流程就會開始判斷︰

   * 如何將頁面當地語系化？
   * 使用者帳戶是否有效？
   * 使用者屬於哪個組織？
   * 何處管理使用者的密碼？
   * 使用者是否已獲得授權使用此功能？


請看完下列步驟，以了解密碼重設頁面背後的邏輯。

1. 使用者按一下 [無法存取帳戶] 連結或直接移至 [https://aka.ms/sspr](https://passwordreset.microsoftonline.com)。
2. 根據瀏覽器的地區設定，此體驗會以適當的語言呈現。 密碼重設體驗會當地語系化為 Office 365 支援的相同語言。
3. 使用者輸入使用者識別碼並通過文字驗證。
4. Azure AD 執行下列檢查，驗證使用者是否能夠使用這項功能：
   * 檢查使用者是否已啟用這項功能，並已獲得 Azure AD 授權。
     * 如果使用者未啟用這項功能或未獲得授權，便會要求使用者連絡其系統管理員來重設其密碼。
   * 檢查使用者是否已按照系統管理員原則在其帳戶上定義正確的查問資料。
     * 如果原則只需要一項查問，則表示使用者必定已針對系統管理員原則所啟用的至少一項查問定義適當的資料。
       * 如果使用者未進行設定，便會建議使用者連絡其系統管理員來重設其密碼。
     * 如果原則需要兩項查問，則表示使用者必定已針對系統管理員原則所啟用的至少兩項查問定義適當的資料。
       * 如果使用者未進行設定，我們便會建議使用者連絡其系統管理員來重設其密碼。
   * 檢查使用者的密碼是否在內部部署進行管理 (同盟或密碼雜湊同步)。
     * 如果在內部部署中有部署回寫和管理使用者的密碼，則允許使用者進行驗證及重設其密碼。
     * 如果在內部部署中未部署回寫但有管理使用者的密碼，則會要求使用者連絡其系統管理員來重設其密碼。
5. 如果判斷使用者能夠成功重設其密碼，則會引導使用者完成重設程序。

## <a name="authentication-methods"></a>驗證方法

如果已啟用自助式密碼重設 (SSPR)，您必須針對驗證方法至少選取下面其中一個選項。 有時候您可能會聽到這些選項是指閘道。 我們強烈建議選擇至少兩個驗證方法，您的使用者才有更大的彈性。

* 電子郵件
* 行動電話
* 辦公室電話
* 安全性問題

![驗證][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-authentication-data"></a>哪些欄位使用於驗證資料的目錄中

* 辦公室電話對應至辦公室電話
    * 使用者本身無法設定此欄位，該欄位必須由系統管理員定義
* 行動電話對應至驗證電話 (非公開可見) 或行動電話 (公開可見)
    * 服務會先尋找驗證電話，如果找不到，則會退回到行動電話
* 替代電子郵件地址對應至驗證電子郵件 (非公開可見) 或替代電子郵件
    * 服務會先尋找驗證電子郵件，然後退回到替代電子郵件

依預設，只有雲端屬性辦公室電話和行動電話會從驗證資料的內部部署目錄同步處理至您的雲端目錄。

使用者只有在系統管理員已啟用且要求的驗證方法中有資料存在時，才能夠重設其密碼。

如果使用者不想要在目錄中顯示行動電話號碼，但仍想要將它使用於密碼重設，系統管理員不得在目錄中填入該行動電話號碼，而使用者應透過[密碼重設註冊入口網站](http://aka.ms/ssprsetup)填入其 [驗證電話] 屬性。 系統管理員可以在使用者的設定檔中看到此資訊，但該資訊不會發佈在其他地方。

### <a name="number-of-authentication-methods-required"></a>必要驗證方法數目

此選項可判斷使用者必須通過才能重設或將其密碼解除鎖定的可用驗證方法或閘道數目下限，並可設定為 1 或 2。

使用者可以選擇提供更多驗證方法 (如果是由系統管理員啟用)。

如果使用者並未註冊所需的最少方法，他們會看到錯誤頁面，引導他們要求系統管理員重設其密碼。

### <a name="how-secure-are-my-security-questions"></a>我的安全性問題有多安全

如果您使用安全性問題，我們建議搭配其他方法使用這些問題，因為它們可能會比其他方法更不安全，原因是有些人可能知道其他使用者問題的答案。

> [!NOTE] 
> 安全性問題會私密且安全地儲存在目錄中的使用者物件上，只能在註冊期間由使用者回答。 系統管理員無法讀取或修改使用者問題或答案。
>

### <a name="security-question-localization"></a>安全性問題當地語系化

下列所有預先定義的問題會依據使用者的瀏覽器地區設定，當地語系化成完整的 O365 語言集。

* 您在哪個城市遇見第一個配偶/伴侶？
* 您的父母在哪個城市相遇？
* 您最親近的手足住在哪個城市？
* 您的父親在哪個城市出生？
* 您的第一份工作是在哪個城市？
* 您的母親在哪個城市出生？
* 您在哪個城市渡過 2000 年的新年？
* 您最喜愛的中學老師姓什麼？
* 您已申請但未就讀的大專名稱為何？
* 您舉辦第一次婚宴的地點名稱為何？
* 您父親的中間名是什麼？
* 您最愛的食物是什麼？
* 您外婆的姓名為何？
* 您母親的中間名是什麼？
* 您最年長手足的生日月份和年度為何？ (例如 1985 年 11 月)
* 您最年長手足的中間名是什麼？
* 您祖父的姓名為何？
* 您最年幼手足的中間名稱是什麼？
* 您六年級時就讀哪間學校？
* 您童年最要好朋友的姓名為何？
* 您第一個密友的姓名為何？
* 您最喜愛的小學老師姓什麼？
* 第一輛汽車或機車的製造商和型號為何？
* 您就讀的第一所學校名稱為何？
* 您出生的醫院名稱為何？
* 您兒時第一個家的街道名稱為何？
* 您的童年英雄名稱為何？
* 您最喜愛的娃娃名稱為何？
* 您第一隻寵物的名稱為何？
* 您童年時期的綽號是什麼？
* 您中學最喜愛的運動是什麼？
* 您的第一份工作是什麼？
* 您兒時電話號碼的末四碼是什麼？
* 在您年輕時，您長大想做什麼？
* 您遇過最有名的人物是誰？

### <a name="custom-security-questions"></a>自訂安全性問題

自訂安全性問題不會針對不同的地區設定進行當地語系化。 所有自訂問題會以您在系統管理使用者介面中輸入它們的語言顯示，即使使用者的瀏覽器地區設定不同。 如果您需要已當地語系化的問題，請使用預先定義的問題。

自訂安全性問題的長度上限為 200 個字元。

### <a name="security-question-requirements"></a>安全性問題需求

* 答案字元限制下限為 3 個字元。
* 答案字元限制上限為 40 個字元。
* 使用者不能回答相同的問題一次以上。
* 使用者不能對一個以上的問題提供相同的答案。
* 可以使用任何字元集來定義問題和答案 (包括 Unicode 字元)。
* 定義的的問題數目必須大於或等於註冊所需的問題數目。

## <a name="registration"></a>註冊

### <a name="require-users-to-register-when-signing-in"></a>登入時要求使用者註冊

如果已啟用密碼重設的使用者使用 Azure AD 登入應用程式，進而登入下列各項，則啟用此選項會要求他們完成密碼重設註冊︰

* Office 365
* Azure 入口網站
* 存取面板
* 同盟應用程式
* 使用 Azure AD 自訂應用程式

停用這項功能時，使用者仍可手動註冊其連絡資訊，方法是造訪 [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) 或按一下存取面板中 [設定檔] 索引標籤底下的**註冊密碼重設**連結。

> [!NOTE]
> 使用者可以按一下 [取消] 或關閉視窗來關閉密碼重設註冊入口網站，但每次登入時都會出現提示，直到他們完成註冊為止。
>

### <a name="number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>要求使用者重新確認其驗證資訊的等候天數

此選項可決定設定和重新確認驗證資訊之間的期間，且只有在您啟用 [登入時要求使用者註冊] 選項才可以使用。

有效值為 0-730 天，0 表示從未要求使用者重新確認其驗證資訊

## <a name="notifications"></a>通知

### <a name="notify-users-on-password-resets"></a>通知使用者密碼重設

如果此選項設定為 [是]，則正在重設其密碼的使用者會收到一封電子郵件 (送至 Azure AD 中歸檔的主要和替代電子郵件地址)，通知他們已透過 SSPR 入口網站其密碼變更。 沒有人會接獲此重設事件的通知。

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>當其他系統管理員重設其密碼時通知所有系統管理員

如果此選項設定為 [是]，則**所有系統管理員**會收到一封電子郵件 (送至 Azure AD 中歸檔的主要電子郵件地址)，通知他們其他系統管理員已使用 SSPR 變更其密碼。

範例︰環境中有四個系統管理員。 系統管理員 "A" 使用 SSPR 重設其密碼。 系統管理員 B、C 和 D 收到一封電子郵件，警示他們發生此狀況。

## <a name="on-premises-integration"></a>內部部署整合

如果您已安裝、設定及啟用 Azure AD Connect，就會有下列其他的內部部署整合選項。

### <a name="write-back-passwords-to-your-on-premises-directory"></a>將密碼寫回至內部部署目錄

控制是否對此目錄啟用密碼回寫，如果回寫開啟，則表示內部部署回寫服務的狀態。 如果您需要暫時停用密碼回寫，而不重新設定 Azure AD Connect，此設定就很有用。

* 如果此參數設定為 [是]，則回寫會啟用，而且同盟和密碼雜湊同步使用者可以重設其密碼。
* 如果此參數設定為 [否]，則回寫會停用，而且同盟和密碼雜湊同步使用者無法重設其密碼。

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>允許使用者在不重設密碼的情況下解除鎖定帳戶

指定是否應為瀏覽密碼重設入口網站的使用者提供選項，讓他們在不重設密碼的情況下解除鎖定內部部署的 Active Directory 帳戶。 根據預設，Azure AD 執行密碼重設時會解除鎖定帳戶，此設定可讓您區隔這兩項作業。 

* 如果設為「是」，會提供使用者重設其密碼以解除鎖定帳戶的選項，或是在不重設密碼的情況下解除鎖定的選項。
* 如果設定為「否」，使用者將只能執行合併的密碼重設和帳戶解除鎖定作業。

## <a name="how-does-password-reset-work-for-b2b-users"></a>B2B 使用者的密碼重設運作方式
所有 B2B 組態完全支援密碼重設和變更。 B2B 使用者密碼重設支援下列三種案例。

1. **來自具備現有 Azure AD 租用戶之合作夥伴組織的使用者** - 如果您合作的組織具備現有的 Azure AD 租用戶，我們會**遵守該租用戶中啟用的任何密碼重設原則**。 若要讓密碼重設運作，合作夥伴組織只需要確定 Azure AD SSPR 已啟用，O365 客戶不須另外付費即可使用該功能，而且可以依照[開始使用密碼管理](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)指南中的步驟加以啟用。
2. **使用[自助式註冊](active-directory-self-service-signup.md)**功能註冊的使用者 - 如果您合作的組織使用[自助式註冊](active-directory-self-service-signup.md)功能進入租用戶，我們會讓他們利用其註冊的電子郵件進行重設。
3. **B2B 使用者** - 任何使用新的 [Azure AD B2B 功能](active-directory-b2b-what-is-azure-ad-b2b.md)建立的新 B2B 使用者，也能夠利用其在邀請程序期間註冊的電子郵件重設其密碼。

若要測試此情節，只要隨著其中一個合作夥伴使用者移至 http://passwordreset.microsoftonline.com 即可。 只要他們定義了替代電子郵件或驗證電子郵件，密碼重設就會如預期般運作。

## <a name="next-steps"></a>後續步驟

下列連結提供有關使用 Azure AD 重設密碼的其他資訊

* [如何完成 SSPR 成功首度發行？](active-directory-passwords-best-practices.md)
* [重設或變更您的密碼](active-directory-passwords-update-your-own-password.md)。
* [註冊自助式密碼重設](active-directory-passwords-reset-register.md)。
* [您有授權問題嗎？](active-directory-passwords-licensing.md)
* [SSPR 使用哪些資料，以及您應該為使用者填入哪些資料？](active-directory-passwords-data.md)
* [哪些驗證方法可供使用者使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [使用 SSPR 的原則選項有哪些？](active-directory-passwords-policy.md)
* [什麼是密碼回寫，且為什麼我需要了解它？](active-directory-passwords-writeback.md)
* [如何回報 SSPR 中的活動？](active-directory-passwords-reporting.md)
* [SSPR 中的所有選項有哪些，以及它們有何意義？](active-directory-passwords-how-it-works.md)
* [我認為有中斷。如何針對 SSPR 進行疑難排解？](active-directory-passwords-troubleshoot.md)
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)

[Authentication]: ./media/active-directory-passwords-how-it-works/sspr-authentication-methods.png "可供使用的 Azure AD 驗證方法和所需的數量"