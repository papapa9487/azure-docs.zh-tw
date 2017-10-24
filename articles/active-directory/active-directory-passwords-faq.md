---
title: "常見問題集︰Azure AD SSPR | Microsoft Docs"
description: "有關 Azure AD 自助式密碼重設的常見問題集。"
services: active-directory
keywords: "Active directory 密碼管理, 密碼管理, Azure AD 自助式密碼重設"
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
ms.date: 10/06/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: a457ba3109625ab43ae3a88b95a3e7e1e9641921
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="password-management-frequently-asked-questions"></a>密碼管理常見問題集

以下是與密碼重設相關之所有事項的一些常見問題。

如果您有關於 Azure AD 和自助式密碼重設的一般問題，但在這裡找不到答案，您可以在 [Azure Ad 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD)尋求社群協助。 社群的成員包括工程師、產品經理、MVP 和 IT 專業人員。

此常見問題集分成下列各節：

* [**密碼重設註冊的相關問題**](#password-reset-registration)
* [**密碼重設的相關問題**](#password-reset)
* [**密碼變更的相關問題**](#password-change)
* [**密碼管理報告的相關問題**](#password-management-reports)
* [**密碼回寫的相關問題**](#password-writeback)

## <a name="password-reset-registration"></a>密碼重設註冊

* **問：我的使用者是否可以註冊自己的密碼重設資料？**

  > **答：**是，只要已啟用密碼重設功能且使用者已獲得授權，他們就可以前往位於 http://aka.ms/ssprsetup 的「密碼重設註冊」入口網站來註冊其驗證資訊。 使用者也可以前往位於 http://myapps.microsoft.com 的存取面板，按一下 [設定檔] 索引標籤，然後按一下 [註冊密碼重設] 選項，來進行註冊。
  >
  >
* **問：我是否可以代表我的使用者定義密碼重設資料？**

  > **答：**是，您可以透過 Azure AD Connect、PowerShell、[Azure 入口網站](https://portal.azure.com) 或 Office Admin 入口網站來進行此動作。 如需詳細資訊，請參閱 [Azure AD 自助式密碼重設所用的資料](active-directory-passwords-data.md)一文。
  >
  >
* **問：我是否可以從內部部署環境同步處理安全性問題的資料？**

  > **答：** 目前無法這麼做。
  >
  >
* **問：我的使用者是否可以用讓其他使用者看不到資料的方式來註冊該資料？**

  > **答：**是，當使用者使用「密碼重設註冊入口網站」來註冊資料時，該資料會儲存到私密的驗證欄位中，只有「全域管理員」和使用者能夠看見。
    >
    > [!NOTE]
    > 如果 **Azure 系統管理員帳戶**註冊其驗證電話號碼，該號碼也會填入行動電話欄位中並顯示出來。
    >
  >
  >
* **問：我的使用者是否必須先註冊才能使用密碼重設？**

  > **答：**否，如果您代表使用者定義足夠的驗證資訊，使用者就不需要註冊。 只要您已正確地格式化儲存在目錄中適當欄位的資料，密碼重設就能運作。
  >
  >
* **問：我是否可以代表我的使用者同步處理或設定驗證電話、驗證電子郵件或備用驗證電話欄位？**

  > **答：** 目前無法這麼做。
  >
  >
* **問：註冊入口網站如何得知要對我的使用者顯示哪些選項？**

  > **答：**密碼重設註冊入口網站只會顯示您為使用者啟用的選項。 在目錄的 [設定] 索引標籤的 [使用者密碼重設原則] 區段之下可以找到這些選項。例如，這表示如果您未啟用安全性問題，使用者便無法註冊該選項。
  >
  >
* **問：使用者何時會被視為已註冊？**

  > **答︰**使用者至少已註冊您在 [Azure 入口網站](https://portal.azure.com)中設定的 [重設所需的方法數目]，才算完成 SSPR 註冊。
  >
  >

## <a name="password-reset"></a>密碼重設

* **問：密碼重設之後，我應該等待多久才能收到電子郵件、SMS 或來電？**

  > **答：**電子郵件、SMS 訊息及電話應該在 1 分鐘內即可送達，正常的情形下為 5-20 秒。
    >如果您未在此時間範圍內收到通知︰
        > * 檢查您的垃圾郵件資料夾。
        > * 檢查正在聯繫的數字或電子郵件是您預期的項目。
        > * 檢查目錄中的驗證資料是否正確地格式化。
                >     * 範例："+1 4255551234" 或 "user@contoso.com"
  >
  >
* **問：密碼重設支援哪些語言？**

  > **答：**密碼重設 UI、SMS 訊息及語音通話都已採用 Office 365 支援的相同語言當地語系化。
  >
  >
* **問：當我在目錄的 [設定] 索引標籤中設定組織商標時，哪個密碼重設體驗部分會有商標？**

  > **答：**密碼重設入口網站會顯示您的組織商標，並且讓您設定「連絡您的系統管理員」連結以指向自訂的電子郵件或 URL。 密碼重設所傳送的任何電子郵件都會包含您的組織商標、顏色、電子郵件內文中的名稱，以及自訂的寄件者名稱。
  >
  >
* **問：如何教育我的使用者要在哪裡重設其密碼？**

  > **答：**請嘗試 [SSPR 部署文章](active-directory-passwords-best-practices.md#email-based-rollout)中的一些建議
  >
  >
* **問：我是否可以從行動裝置使用此頁面？**

  > **答：** 是，此頁面可在行動裝置上運作。
  >
  >
* **問：當使用者重設其密碼時，您是否支援將本機 Active Directory 帳戶解除鎖定？**

  > **答：**是，當使用者重設其密碼並使用 Azure AD Connect 部署密碼回寫時，該使用者的帳戶會在其重設密碼時自動解除鎖定。
  >
  >
* **問：我要如何將密碼重設直接整合到我使用者的桌面登入體驗？**

  > **答：**如果您是 Azure AD Premium 客戶，您不需要額外成本即可安裝 Microsoft Identity Manager，並且部署內部部署密碼重設解決方案，以符合這項需求。
  >
  >
* **問：我是否可以針對不同的地區設定，設定不同的安全性問題？**

  > **答：** 目前無法這麼做。
  >
  >
* **問：我們可以為安全性問題驗證選項設定多少問題？**

  > **答：**您可以在 [Azure 入口網站](https://portal.azure.com)中設定最多 20 個自訂安全性問題。
  >
  >
* **問：安全性問題的長度限制為何？**

  > **答：** 安全性問題的長度可以介於 3 到 200 個字元之間。
  >
  >
* **問：安全性問題答案的長度限制為何？**

  > **答：** 答案的長度可以介於 3 到 40 個字元之間。
  >
  >
* **問：重複的安全性問題答案是否會遭到拒絕？**

  > **答：** 是，我們會拒絕重複的安全性問題答案。
  >
  >
* **問：使用者是否可以多次註冊相同的安全性問題？**

  > **答：**否，使用者註冊一旦特定的問題，便不可以再次註冊該問題。
  >
  >
* **問：是否可以針對註冊和重設設定安全性問題的最小限制？**

  > **答：** 是，可以針對註冊設定一個限制，針對重設設定另一個限制。 註冊可能需要 3-5 個安全性問題，重設也需要 3-5 個安全性問題。
  >
  >
* **問：我將原則設定為要求使用者使用安全性問題進行重設，但 Azure 系統管理員似乎是以不同方式設定。**

  > **答：**這是預期中的行為。 Microsoft 會針對任何 Azure 系統管理員角色強制執行強式預設雙閘道密碼重設原則。 這會讓系統管理員無法使用安全性問題。 在 [Azure Active Directory 的密碼原則和限制](active-directory-passwords-policy.md#administrator-password-policy-differences)中可以找到此原則的相關詳細資訊
  >
  >
* **問：如果使用者註冊的問題數目超過重設所需的問題數目上限，在重設期間會如何選取安全性問題？**

  > **答：**會在使用者已註冊的問題總數當中隨機選取 N 個安全性問題，其中 N 是 [重設所需的問題數目]。 例如，如果使用者已註冊 5 個安全性問題，但是重設只需要 3 個，則會在 5 個問題當中隨機選取 3 個並且在重設時顯示。 如果使用者回答問題的答案錯誤，則選取程序會重新發生以避免問題 Hammering。
  >
  >
* **問：您是否禁止使用者在短時間內嘗試多次密碼重設？**

  > **答：**是，密碼重設有一些內建的安全性功能，以防止濫用。 使用者在一個小時之內只能嘗試 5 次密碼重設，否則會鎖定 24 小時。 使用者在一個小時之內只能嘗試驗證電話號碼 5 次，否則會鎖定 24 小時。 使用者在一個小時之內只能嘗試單一驗證方法 5 次，否則會鎖定 24 小時。
  >
  >
* **問：電子郵件和 SMS 單次密碼的有效期限是多久？**

  > **答：**密碼重設的工作階段存留期為 15 分鐘。 從密碼重設作業開始，使用者有 15 分鐘可以重設其密碼。 這段時間之後，電子郵件和 SMS 單次密碼就會無效。
  >
  >

## <a name="password-change"></a>密碼變更

* **問︰我的使用者應該到何處變更密碼？**

  > **答︰**使用者可以在出現個人資料圖片或圖示的任何位置變更密碼 (例如操作 [Office 365](https://portal.office.com) 或[存取面板](https://myapps.microsoft.com)時的右上角)。 使用者可以從[存取面板個人資料頁面](https://account.activedirectory.windowsazure.com/r#/profile)變更密碼。 如果使用者的密碼已過期，Azure AD 登入畫面也可能自動要求使用者變更密碼。 最後，如果使用者想要變更密碼，可以直接瀏覽至 [Azure AD 密碼變更入口網站](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)。
  >
  >
* **問︰當使用者的內部部署密碼過期時，Office 入口網站會加以通知嗎？**

  > **答︰**如果您使用 ADFS，依照此處的指示進行就會收到通知：[使用 ADFS 傳送密碼原則宣告](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396)。 如果您使用密碼雜湊同步處理，則目前還不可能。 這是因為我們不會從內部部署來同步處理密碼原則，所以無法在雲端操作過程中發佈到期通知。 在任一情況下，也可以[使用 PowerShell 通知使用者密碼即將到期](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx)。
  >
  >

## <a name="password-management-reports"></a>密碼管理報告

* **問：資料需要多久的時間才會顯示在密碼管理報告上？**

  > **答：** 資料應該會在 5-10 分鐘內顯示在密碼管理報告上。 某些情況下，可能會花費多達一小時才顯示。
  >
  >
* **問：我要如何篩選密碼管理報告？**

  > **答：**您可以在接近報告頂端處，按一下欄標籤最右邊的小放大鏡圖示，以篩選密碼管理報告。 如果您想要做更豐富的篩選，您可以將報告下載到 excel，並建立樞紐分析表。
  >
  >
* **問：密碼管理報告中最多可以儲存多少事件？**

  > **答：**密碼管理報告中最多可以儲存 75,000 個密碼重設或密碼重設註冊事件，最多可回溯 30 天。  我們正在擴展此數目，以包含更多的事件。
  >
  >
* **問：密碼管理報告可以回溯到多久以前？**

  > **答：** 密碼管理報告會顯示過去 30 天內發生的作業。 現在，如果您需要封存這項資料，您可以定期下載報告並將它們儲存在不同的位置。
  >
  >
* **問：密碼管理報告可以顯示的資料列是否有數目上限？**

  > **答：**是，不論是在 UI 中顯示或是下載，任一密碼管理報告最多只能顯示 75,000 個資料列。
  >
  >
* **問：是否有可用來存取密碼重設或註冊報告資料的 API？**

  > **答：**是，請參閱下列文件，以了解如何存取密碼重設報告資料流。  [了解如何以程式設計方式存取密碼重設報告事件](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)。
  >
  >

## <a name="password-writeback"></a>密碼回寫

* **問：密碼回寫如何在幕後運作？**

  > **答：**請參閱[密碼回寫的運作方式](active-directory-passwords-writeback.md)，以了解啟用「密碼回寫」時會發生的情況，以及資料如何透過系統回流到您的內部部署環境。
  >
  >
* **問：密碼回寫需要多久時間才會運作？是否有像是使用密碼雜湊同步處理的同步處理延遲？**

  > **答：**密碼回寫會立即進行。 它是同步管線，基本上的運作與密碼雜湊同步處理不同。 密碼回寫可以讓使用者即時取得密碼重設或變更作業成功的回應。 成功回寫密碼的平均時間低於 500 毫秒。
  >
  >
* **問︰如果我的內部部署帳戶已停用，這對我的雲端帳戶/存取有何影響？**

  > **答︰**如果您的內部部署識別碼已停用，您的雲端識別碼/存取也會透過 AAD Connect 在下一個同步處理間隔 (預設每隔 30 分鐘) 停用。
  >
  >
* **問︰如果我的內部部署帳戶受到內部部署 Active Directory 密碼原則限制，則當我變更密碼時 SSPR 是否會遵守此原則時？**

  > **答︰**是，SSPR 會依循並遵守內部部署 AD 密碼原則，包括一般的 AD 網域密碼原則，以及任何以特定使用者為目標而定義的細微密碼原則。
  >
  >
* **問：密碼回寫適用於哪些類型的帳戶？**

  > **答：**密碼回寫適用於「同盟」和「密碼雜湊同步」使用者。
  >
  >
* **問：密碼回寫是否會強制執行我的網域密碼原則？**

  > **答：**是，密碼回寫會強制執行密碼使用期限、歷程記錄、複雜度、篩選及任何其他您可能在本機網域中的密碼上適當加諸的限制。
  >
  >
* **問：密碼回寫是否安全？我要如何確定不會受到駭客入侵？**

  > **答：**是，密碼回寫很安全。 若要進一步了解密碼回寫服務所實作的 4 層安全性，請參閱＜密碼回寫的運作方式＞中的[密碼回寫的安全性模型](active-directory-passwords-writeback.md#password-writeback-security-model)一節。
  >
  >

## <a name="next-steps"></a>後續步驟

下列連結提供有關使用 Azure AD 重設密碼的其他資訊

* [**快速入門**](active-directory-passwords-getting-started.md) - 開始執行 Azure AD 自助式密碼管理 
* [**授權**](active-directory-passwords-licensing.md) - 設定 Azure AD 授權
* [**資料**](active-directory-passwords-data.md) -了解所需的資料以及如何將它使用於密碼管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此處提供的指引來規劃 SSPR 並將它部署至使用者
* [**自訂**](active-directory-passwords-customize.md) - 為您的公司自訂 SSPR 體驗的外觀與風格。
* [**報告**](active-directory-passwords-reporting.md) - 探索您的使用者是否、何時、何地存取 SSPR 功能
* [**原則**](active-directory-passwords-policy.md) - 了解並設定 Azure AD 密碼原則
* [**密碼回寫**](active-directory-passwords-writeback.md) - 密碼回寫如何用在您的內部部署目錄
* [**技術性深入探討**](active-directory-passwords-how-it-works.md) - 深入探索以了解其運作方式
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何解決我們看到的 SSPR 常見問題
