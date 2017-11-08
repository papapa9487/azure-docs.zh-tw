---
title: "疑難排解︰Azure AD SSPR | Microsoft Docs"
description: "針對 Azure AD 自助式密碼重設進行疑難排解"
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
ms.date: 09/21/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 24b8a9852395c26a40adb406bd706283e1a96d5d
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="how-to-troubleshoot-self-service-password-reset"></a>如何針對自助式密碼重設進行疑難排解

如果自助式密碼重設發生問題，下列項目可協助您更快順利運作。

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-may-see"></a>針對使用者可能會看到的自助式密碼重設錯誤進行疑難排解

| 錯誤 | 詳細資料 | 技術詳細資訊 |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | 很抱歉 <br> 因為您的系統管理員已為貴組織停用密碼重設，此時您無法重設密碼。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們啟用此功能。 若要深入了解，請參閱[忘記 Azure AD 密碼的說明](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions)。 | SSPR_0009：我們已偵測到您的系統管理員尚未啟用「密碼重設」。 請連絡您的系統管理員，並要求他們為組織啟用「密碼重設」。 |
| WritebackNotEnabled = 10 |很抱歉 <br> 因為您的系統管理員尚未為貴組織啟用必要的服務，此時您無法重設密碼。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們檢查貴組織的設定。 若要深入了解必要的服務，請參閱[設定密碼回寫](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configuring-password-writeback)。 | SSPR_0010：我們已偵測到「密碼回寫」尚未啟用。 請連絡您的系統管理員，並要求他們啟用「密碼回寫」。 |
| SsprNotEnabledInUserPolicy = 11 | 很抱歉  <br> 因為您的系統管理員尚未為貴組織設定密碼重設，此時您無法重設密碼。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們設定密碼重設。 若要深入了解密碼重設設定，請參閱[快速入門：Azure AD 自助式密碼重設](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)一文。 | SSPR_0011：您的組織尚未定義密碼重設原則。 請連絡您的系統管理員，並要求他們定義密碼重設原則。 |
| UserNotLicensed = 12 | 很抱歉 <br> 因為缺少貴組織所需要的授權，此時您無法重設密碼。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們檢查授權指派。 若要深入了解有關授權的詳細資訊，請參閱 [Azure AD 自助式密碼重設的授權需求](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing)一文。 | SSPR_0012：您的組織沒有執行密碼重設所需的必要授權。 請連絡您的系統管理員，並要求他們檢閱授權指派。 |
| UserNotMemberOfScopedAccessGroup = 13 | 很抱歉 <br> 因為您的系統管理員尚未設定您的帳戶以使用密碼重設，此時您無法重設密碼。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們設定您的帳戶以供密碼重設使用。 若要深入了解密碼重設的帳戶設定，請參閱[為使用者推出密碼重設](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices)一文。 | SSPR_0012：您不是已啟用密碼重設之群組的成員。 請連絡您的系統管理員，並要求加入群組。 |
| UserNotProperlyConfigured = 14 | 很抱歉 <br> 因為缺少您帳戶所需要的資訊，此時您無法重設密碼。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們為您重設密碼。 當您可以再次存取您的帳戶之後，可遵循[註冊自助式密碼重設](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register)文章中的步驟，了解如何註冊必要資訊。 | SSPR_0014：重設密碼所需要的其他安全性資訊。 若要繼續進行，請連絡您的系統管理員，並要求他們重設您的密碼。 當您可以存取您的帳戶之後，您可以在 https://aka.ms/ssprsetup 註冊其他安全性資訊。 您的系統管理員可以遵循[設定與閱讀密碼重設的驗證資料](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell)中的步驟，將其他安全性資訊加入您的帳戶中。 |
| OnPremisesAdminActionRequired = 29 | 很抱歉 <br> 因為貴組織的密碼重設設定發生問題，此時無法重設密碼。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們調查。 若要深入了解潛在問題，請參閱[疑難排解密碼回寫](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)一文。 | SSPR_0029：因為您的內部部署設定發生錯誤，我們無法重設您的密碼。 請連絡您的系統管理員，並要求他們調查。 |
| OnPremisesConnectivityError = 30 | 很抱歉 <br> 因為貴組織的連線發生問題，此時我們無法重設密碼。 現在無需採取任何動作，但如果您稍後再試，可能會解決問題。 如果問題持續發生，請連絡您的系統管理員，並要求他們調查。 若要深入了解連線問題，請參閱[疑難排解密碼回寫連線](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)。 | SSPR_0030：因為您內部部署環境的連線不佳，我們無法重設您的密碼。 請連絡您的系統管理員，並要求他們調查。|


## <a name="troubleshoot-password-reset-configuration-in-the-azure-portal"></a>在 Azure 入口網站中針對密碼重設設定進行疑難排解

| **錯誤** | 解決方法 |
| --- | --- |
| 我在 Azure 入口網站中的 Azure AD 之下看不到 [密碼重設] 區段 | 如果您未將 Azure AD Premium 或 Basic 授權指派給執行此作業的系統管理員，就會發生這種情況。 <br> 使用[指派、驗證授權及解決其問題](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)一文，將授權指派給有問題的系統管理員帳戶，即可解決此問題 |
| 我沒看到特定設定選項 | 許多 UI 元素只會在需要時出現。 如果您想要看到這些元素，請嘗試啟用所有選項。 |
| 我沒看到 [內部部署整合] 索引標籤 | 只有在您下載 Azure AD Connect 並設定密碼回寫後，這個選項才會出現。 如需此主題的詳細資訊，請參閱[使用快速設定開始使用 Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md)一文。 |

## <a name="troubleshoot-password-reset-reporting"></a>針對密碼重設報告進行疑難排解

| **錯誤** | 方案 |
| --- | --- |
| 我沒看到任何密碼管理活動類型出現在 [自助式密碼管理] 稽核事件類別中 | 如果您未將 Azure AD Premium 或 Basic 授權指派給執行此作業的系統管理員，就會發生這種情況。 <br> 使用 [指派、驗證授權及解決其問題] 一文，將授權指派給有問題的系統管理員帳戶，即可解決此問題 |
| 使用者註冊顯示多個時間 | 當使用者註冊時，我們目前會將所註冊的每一筆個別資料記錄為個別事件。 <br> 如果您想要彙總此資料，您可以下載報告，並在 Excel 樞紐分析表中開啟資料，以獲得更大的編輯彈性。

## <a name="troubleshoot-password-reset-registration-portal"></a>針對密碼重設註冊入口網站進行疑難排解

| **錯誤** | 方案 |
| --- | --- |
| 目錄未啟用密碼重設功能**您的系統管理員還沒為您啟用這項功能** | 將 [已啟用自助式密碼重設] 旗標切換為 [群組] 或 [每個人]，然後按一下 [儲存] |
| 使用者尚未獲得 Azure AD Premium 或 Basic 授權**您的系統管理員尚未讓您使用這項功能** | 如果您未將 Azure AD Premium 或 Basic 授權指派給執行此作業的系統管理員，就會發生這種情況。 <br> 使用[指派、驗證授權及解決其問題](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)一文，將授權指派給有問題的系統管理員帳戶，即可解決此問題 |
| 處理要求時發生錯誤 | 許多問題都會造成此情形，但這個錯誤通常是因為服務中斷或設定問題所導致。 如果您看到這個錯誤，而且它會影響您的業務，請連絡 Microsoft 支援服務人員以尋求其他協助。 |

## <a name="troubleshoot-password-reset-portal"></a>針對密碼重設入口網站進行疑難排解

| **錯誤** | 方案 |
| --- | --- |
| 目錄未啟用密碼重設功能。 | 將 [已啟用自助式密碼重設] 旗標切換為 [群組] 或 [每個人]，然後按一下 [儲存] |
| 使用者尚未獲得 Azure AD Premium 或 Basic 授權 | 如果您未將 Azure AD Premium 或 Basic 授權指派給執行此作業的系統管理員，就會發生這種情況。 <br> 使用[指派、驗證授權及解決其問題](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)一文，將授權指派給有問題的系統管理員帳戶，即可解決此問題 |
| 目錄已啟用密碼重設，但使用者的驗證資訊遺失或格式不正確 | 確定使用者已在目錄中登記格式正確的連絡資料，然後再繼續。 如需此主題的詳細資訊，請參閱 [Azure AD 自助式密碼重設所用的資料](active-directory-passwords-data.md)一文。 |
| 目錄已啟用密碼重設，原則設定為需要兩個驗證步驟但使用者只登記一個連絡資料 | 確定使用者有至少兩個已正確設定的連絡方法 (例如：行動電話**和**辦公室電話)，然後再繼續。 |
| 目錄已啟用密碼重設，並已正確設定使用者，但無法連絡到使用者 | 原因可能是暫時性的服務錯誤，或連絡資料不正確而無法正確偵測到。 <br> <br> 如果使用者等候 10 秒，便會出現再試一次和「連絡您的系統管理員」連結。 按一下再試一次會重試呼叫，而按一下「連絡您的系統管理員」會傳送表單電子郵件給全域系統管理員，要求對該使用者帳戶執行密碼重設。 |
| 使用者從未收到密碼重設 SMS 或來電 | 原因可能是目錄中的電話號碼格式不正確。 請確定電話號碼的格式是 “+ccc xxxyyyzzzzXeeee”。 <br> <br> 密碼重設並不支援分機號碼，即使您在目錄中有指定也是一樣 (發送呼叫前會將其刪除)。 請嘗試使用沒有分機號碼的電話號碼，或將分機號碼整合至 PBX 中的電話號碼。 |
| 使用者從未收到密碼重設電子郵件 | 這個問題最常見的原因是垃圾郵件篩選器拒絕了郵件。 請檢查您的垃圾郵件或刪除的郵件資料夾中是否有該電子郵件。 <br> 同時確定您是檢查正確電子郵件中的訊息。 |
| 我已設定密碼重設原則，但是當系統管理員帳戶使用密碼重設時，卻未套用該原則 | Microsoft 會管理及控制系統管理員的密碼重設原則，以確保最高層級的安全性。 |
| 使用者被禁止在一天當中嘗試重設密碼太多次 | 我們實作了自動節流機制來避免使用者在短時間內嘗試重設密碼太多次。 其發生條件如下： <br> 1.使用者嘗試在一個小時內驗證電話號碼 5 次。 <br> 2.使用者嘗試在一個小時內使用安全性問題關卡 5 次。 <br> 3.使用者嘗試在一個小時內重設相同使用者帳戶的密碼 5 次。 <br> 若要修正此問題，請指示使用者在最後一次嘗試後等候 24 小時，之後使用者就能重設其密碼。 |
| 使用者在驗證其電話號碼時看到錯誤 | 輸入的電話號碼與登記的電話號碼不符時，就會發生這個錯誤。 請確定使用者在嘗試使用電話式方法來重設密碼時有輸入完整的電話號碼，包括區碼和國碼。 |
| 處理要求時發生錯誤 | 許多問題都會造成此情形，但這個錯誤通常是因為服務中斷或設定問題所導致。 如果您看到這個錯誤，而且它會影響您的業務，請連絡 Microsoft 支援服務人員以尋求其他協助。 |

## <a name="troubleshoot-password-writeback"></a>針對密碼回寫進行疑難排解

| **錯誤** | 方案 |
| --- | --- |
| 內部部署未啟動密碼重設服務，Azure AD Connect 電腦的應用程式事件記錄檔中有錯誤 6800。 <br> <br> 上架之後，同盟或密碼雜湊同步使用者無法重設其密碼。 | 啟用密碼回寫時，同步處理引擎會與雲端上架服務交談，呼叫回寫資源庫來執行設定 (上架)。 在上架期間或啟動密碼回寫的 WCF 端點時遇到的任何錯誤，會導致 Azure AD Connect 電腦的事件記錄中出現錯誤。 <br> <br> 在 ADSync 服務重新啟動期間，若已設定回寫，則會啟動 WCF 端點。 不過，如果端點啟動失敗，我們會記錄事件 6800，並讓同步處理服務啟動。 出現此事件表示密碼回寫端點並未啟動。 此事件 (6800) 的事件記錄詳細資料以及 PasswordResetService 元件所產生的事件記錄項目會指出為何無法啟動端點。 如果密碼回寫仍然無法運作，請檢閱這些事件記錄錯誤，並嘗試重新啟動 Azure AD Connect。 如果此問題持續發生，請嘗試先停用再重新啟用密碼回寫。
| 當使用者嘗試在已啟用密碼回寫時重設密碼或解除鎖定帳戶，作業會失敗。 <br> <br> 此外，在解除鎖定作業發生之後，您會在 Azure AD Connect 事件記錄檔中看到事件，其中包含：「Synchronization Engine returned an error hr=800700CE, message=The filename or extension is too long」。 | 尋找 Azure AD Connect 的 Active Directory 帳戶，並將密碼重設成包含不超過 127 個字元。 然後從 [開始] 功能表開啟 [同步處理服務]。 瀏覽至 [連接器]，然後尋找 [Active Directory 連接器]。 選取它，然後按一下 [屬性]。 瀏覽至 [認證] 頁面，然後輸入新密碼。 選取 [確定] 以關閉頁面。 |
| 在 Azure AD Connect 安裝程序的最後一個步驟時，您看到錯誤指出無法設定密碼回寫。 <br> <br> Azure AD Connect 應用程式事件記錄檔包含錯誤 32009 以及「取得授權權杖時發生錯誤」文字。 | 下列兩種情況會發生此錯誤：<br> <br> a. 針對在 Azure AD Connect 安裝程序開始時所指定的全域系統管理員帳戶，指定了錯誤的密碼。<br> b.這是另一個 C# 主控台應用程式。 針對在 Azure AD Connect 安裝程序開始時所指定的全域系統管理員帳戶，嘗試使用同盟使用者。<br> <br> 若要修正此錯誤，請確定您並未針對在 Azure AD Connect 安裝程序開始時所指定的全域系統管理員使用同盟帳戶，而且所指定的密碼正確無誤。 |
| Azure AD Connect 電腦的事件記錄檔包含 PasswordResetService 所擲回的錯誤 32002。 <br> <br> 這個錯誤的內容是：「連線到 ServiceBus 時發生錯誤。權杖提供者無法提供安全性權杖...」 | 您的內部部署環境無法連線到雲端的服務匯流排端點。 這個錯誤是因為防火牆規則封鎖連往特定連接埠或網址的輸出連線所導致。 如需詳細資訊，請參閱[連線必要條件](./connect/active-directory-aadconnect-prerequisites.md)。 一旦您更新這些規則後，請重新啟動 Azure AD Connect 電腦，密碼回寫應該就會再次開始工作。 |
| 在運作一段時間後，同盟或密碼雜湊同步使用者無法重設其密碼。 | 在某些罕見情況下，重新啟動 Azure AD Connect 時可能無法重新啟動密碼回寫服務。 在這些情況下，請先檢查內部部署是否已啟用密碼回寫。 若要執行此作業，請使用 Azure AD Connect 精靈或 PowerShell (請參閱上面的「作法」章節)。如果此功能已啟用，請嘗試透過 UI 或 PowerShell 再次啟用或停用功能。 如果這麼做沒有效，請嘗試完整解除安裝再重新安裝 Azure AD Connect。 |
| 同盟或密碼雜湊同步使用者若嘗試重設其密碼，會在送出密碼後看到錯誤指出服務發生問題。 <br ><br> 此外，在密碼重設作業期間，您可能會在內部部署的事件記錄檔中看到關於管理代理程式存取遭拒的錯誤。 | 如果您在事件記錄中看到這些錯誤，請確認 AD MA 帳戶 (在設定時於精靈中所指定) 有密碼回寫的必要權限。 <br> <br> **一旦給予此權限，最多要 1 小時的時間，此權限才會透過 DC 上的 sdprop 背景工作往下傳遞。** <br> <br> 若要讓密碼重設正常運作，必須在要重設密碼的使用者物件安全性描述元上為權限加上戳記。 在使用者物件上出現此權限之前，密碼重設會繼續因存取遭拒而失敗。 |
| 同盟或密碼雜湊同步使用者若嘗試重設其密碼，會在送出密碼後看到錯誤指出服務發生問題。 <br> <br> 此外，在密碼重設作業期間，您可能會在 Azure AD Connect 服務的事件記錄檔中看到錯誤指出「找不到物件」錯誤。 | 這個錯誤通常表示同步處理引擎找不到 AAD 連接器空間中的使用者物件，或連結的 MV 或 AD 連接器空間物件。 <br> <br> 若要疑難排解這個問題，請確定使用者已確實透過 Azure AD Connect 的目前執行個體從內部部屬同步處理到 AAD，並檢查連接器空間和 MV 中物件的狀態。 確認 AD CS 物件透過 “Microsoft.InfromADUserAccountEnabled.xxx” 規則連線到 MV 物件。|
| 同盟或密碼雜湊同步使用者若嘗試重設其密碼，會在送出密碼後看到錯誤指出服務發生問題。 <br> <br> 此外，在密碼重設作業期間，您可能會在 Azure AD Connect 服務的事件記錄中看到錯誤指出「找到多個相符項目」錯誤。 | 此錯誤指出同步處理引擎偵測到 MV 物件透過 “Microsoft.InfromADUserAccountEnabled.xxx” 連線到多個 AD CS 物件。 這表示使用者在多個樹系中啟用帳戶。 **密碼回寫不支援此案例。** |
| 密碼作業因設定錯誤而失敗。 應用程式事件記錄包含 <br> <br> Azure AD Connect 錯誤 6329 和文字：0x8023061f (作業失敗，因為此管理代理程式上未啟用密碼同步處理。) | 如果在已經啟用「密碼回寫」功能之後，變更 Azure AD Connect 設定來新增 AD 樹系 (或移除現有樹系再重新新增)，就會發生此錯誤。 位於這類新增樹系中的使用者，其密碼作業會失敗。 若要修正此問題，請在完成樹系設定變更後，先停用再重新啟用密碼回寫功能。 |

## <a name="password-writeback-event-log-error-codes"></a>密碼回寫事件記錄檔錯誤碼

疑難排解密碼回寫問題時的最佳作法，是檢查 Azure AD Connect 電腦上的應用程式事件記錄檔。 這個事件記錄包含密碼回寫的兩個相關來源的事件。 PasswordResetService 來源會說明與密碼回寫作業相關的作業和問題。 ADSync 來源會說明與 AD 環境中的密碼設定相關的作業和問題。

### <a name="source-of-event-is-adsync"></a>事件來源為 ADSync

| 代碼 | 名稱/訊息 | 說明 |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619 – “有一項限制讓密碼無法變更為目前指定的密碼。” | 當密碼回寫服務嘗試在本機目錄上設定不符合密碼使用期限、歷程記錄、複雜度或網域篩選需求的密碼時，就會發生此錯誤。 <br> <br> 如果您有最短的密碼使用期限，且最近在該時段內變更過密碼，則必須在到達網域中指定的使用期限後，才能再次變更密碼。 若要進行測試，最短使用期限應該設定為 0。 <br> <br> 如果已啟用密碼歷程記錄需求，則必須選取最近 N 次未用過的密碼，其中 N 是密碼歷程記錄設定。 如果您選取最近 N 次用過的密碼，則會在此案例中看到失敗。 若要進行測試，歷程記錄應該設定為 0。 <br> <br> 如果您有密碼複雜度需求，則會在使用者嘗試變更或重設密碼時強制執行這些需求。 <br> <br> 如果您啟用密碼篩選功能，則當使用者選取的密碼不符合篩選準則時，重設或變更作業就會失敗。 |
| HR 8023042 | 同步處理引擎傳回的錯誤 hr= 80230402，訊息=嘗試取得物件失敗，因為存在具有相同錨點的重複項目 | 在多個網域中啟用相同的使用者識別碼時會發生此事件。 例如，如果您在同步處理的帳戶/資源樹系中皆存在相同的使用者識別碼且皆為啟用時，可能會發生此錯誤。 <br> <br> 如果您使用非唯一的錨點屬性 (例如別名或 UPN)，而且兩個使用者共用該相同的錨點屬性時，也會發生此錯誤。 <br> <br> 若要解決這個問題，請確定您的網域內沒有重複的使用者，以及每個使用者皆使用唯一的錨點屬性。 |

### <a name="source-of-event-is-passwordresetservice"></a>事件來源為 PasswordResetService

| 代碼 | 名稱/訊息 | 說明 |
| --- | --- | --- |
| 31001 | PasswordResetStart | 這個事件表示內部部署服務偵測到源自雲端、針對同盟或密碼雜湊同步使用者所提出的密碼重設要求。 這個事件是每個密碼重設回寫作業的第一個事件。 |
| 31002 | PasswordResetSuccess | 這個事件表示使用者在密碼重設作業期間選取了新的密碼，我們判斷此密碼符合公司的密碼需求，因此該密碼已成功回寫至本機 AD 環境。 |
| 31003 | PasswordResetFail | 這個事件表示使用者選取了密碼，該密碼已成功抵達內部部署環境，但是當我們嘗試在本機 AD 環境中設定密碼時，發生了失敗狀況。 此狀況有幾個可能原因： <br> <br> 使用者的密碼不符合使用期限、歷程記錄、複雜度或網域篩選需求。 請嘗試使用全新密碼以解決此問題。 <br> <br> MA 服務帳戶沒有適當的權限，無法對所提及的使用者帳戶設定新密碼。 <br> <br> 使用者的帳戶位於不允許密碼設定作業的受保護群組中，例如網域或企業系統管理員。 |
| 31004 | OnboardingEventStart | 此事件發生在您對 Azure AD Connect 啟用密碼回寫時，並且表示我們已開始將您的組織上架到密碼回寫 Web 服務。 |
| 31005 | OnboardingEventSuccess | 這個事件表示上架程序已順利完成，且密碼回寫功能已準備好可供使用。 |
| 31006 | ChangePasswordStart | 這個事件表示內部部署服務偵測到源自雲端、針對同盟或密碼雜湊同步使用者所提出的密碼變更要求。 這個事件是每個密碼變更回寫作業的第一個事件。 |
| 31007 | ChangePasswordSuccess | 這個事件表示使用者在密碼變更作業期間選取了新的密碼，我們判斷此密碼符合公司的密碼需求，因此該密碼已成功回寫至本機 AD 環境。 |
| 31008 | ChangePasswordFail | 這個事件表示使用者選取了密碼，該密碼已成功抵達內部部署環境，但是當我們嘗試在本機 AD 環境中設定密碼時，發生了失敗狀況。 此狀況有幾個可能原因： <br> <br> 使用者的密碼不符合使用期限、歷程記錄、複雜度或網域篩選需求。 請嘗試使用全新密碼以解決此問題。 <br> <br> MA 服務帳戶沒有適當的權限，無法對所提及的使用者帳戶設定新密碼。 <br> <br> 使用者的帳戶位於不允許密碼設定作業的受保護群組中，例如網域或企業系統管理員。 |
| 31009 | ResetUserPasswordByAdminStart | 內部部署服務偵測到源自系統管理員代表使用者針對同盟或密碼雜湊同步使用者所提出的密碼重設要求。 這個事件是每個系統管理員所起始的密碼重設回寫作業的第一個事件。 |
| 31010 | ResetUserPasswordByAdminSuccess | 系統管理員在系統管理員所起始的密碼重設作業期間選取了新的密碼，我們判斷此密碼符合公司的密碼需求，因此該密碼已成功回寫至本機 AD 環境。 |
| 31011 | ResetUserPasswordByAdminFail | 系統管理員代表使用者選取了密碼，該密碼已成功抵達內部部署環境，但是當我們嘗試在本機 AD 環境中設定密碼時，發生了失敗狀況。 此狀況有幾個可能原因： <br> <br> 使用者的密碼不符合使用期限、歷程記錄、複雜度或網域篩選需求。 請嘗試使用全新密碼以解決此問題。 <br> <br> MA 服務帳戶沒有適當的權限，無法對所提及的使用者帳戶設定新密碼。 <br> <br> 使用者的帳戶位於不允許密碼設定作業的受保護群組中，例如網域或企業系統管理員。 |
| 31012 | OffboardingEventStart | 此事件發生在您對 Azure AD Connect 停用密碼回寫時，並且表示我們已開始將您的組織下架到密碼回寫 Web 服務。 |
| 31013| OffboardingEventSuccess| 這個事件表示下架程序已順利完成，且密碼回寫功能已順利停用。 |
| 31014| OffboardingEventFail| 這個事件表示下架程序未成功。 原因可能是雲端或設定期間指定之內部部署系統管理員帳戶的權限發生錯誤，或您嘗試在停用密碼回寫時使用已同盟的雲端全域系統管理員。 若要修正此問題，請檢查您的系統管理權限，並確認您沒有在設定密碼回寫功能時使用任何已同盟的帳戶。|
| 31015| WriteBackServiceStarted| 這個事件表示密碼回寫服務已成功啟動，並已準備好接受來自雲端的密碼管理要求。|
| 31016| WriteBackServiceStopped| 這個事件表示密碼回寫服務已停止，且來自雲端的所有密碼管理要求都不會成功。|
| 31017| AuthTokenSuccess| 這個事件表示我們已順利擷取 Azure AD Connect 設定期間所指定之全域系統管理員的授權權杖，以便開始下架或上架程序。|
| 31018| KeyPairCreationSuccess| 這個事件表示我們已成功建立密碼加密金鑰，此金鑰用來加密從雲端傳送至內部部署環境的密碼。|
| 32000| UnknownError| 這個事件表示密碼管理作業期間發生未知的錯誤。 請查看事件中的例外狀況文字，以獲得詳細資訊。 如果您遇到問題，請嘗試停用再重新啟用密碼回寫。 如果這麼做沒有用，請將事件記錄檔複本連同內部指定的追蹤識別碼交給支援工程師。|
| 32001| ServiceError| 此事件表示連線到雲端密碼重設服務時發生錯誤，且此事件通常發生在內部部署服務無法連線到密碼重設 Web 服務時。|
| 32002| ServiceBusError| 這個事件表示連線到租用戶的服務匯流排執行個體時發生錯誤。 可能原因是您在內部部署環境中封鎖了輸出連線。 請檢查您的防火牆來確保您允許透過 TCP 443 及連到 https://ssprsbprodncu-sb.accesscontrol.windows.net/ 的連線，然後再試一次。 如果仍遇到問題，請嘗試停用再重新啟用密碼回寫。|
| 32003| InPutValidationError| 這個事件表示傳遞到我們的 Web 服務 API 的輸入無效。 請重試一次此作業。|
| 32004| DecryptionError| 這個事件表示在解密從雲端抵達的密碼時發生錯誤。 原因可能是雲端服務和內部部署環境的解密金鑰不符。 若要解決此問題，請停用再重新啟用內部部署環境的密碼回寫。|
| 32005| ConfigurationError| 上架期間，我們將租用戶特定的資訊儲存在內部部署環境的設定檔中。 這個事件表示儲存此檔案時發生錯誤，或啟動服務時發生檔案讀取錯誤。 若要修正此問題，請嘗試停用再重新啟用密碼回寫，強制重新寫入此組態檔。|
| 32007| OnBoardingConfigUpdateError| 上架期間，我們會從雲端傳送資料到內部部署密碼重設服務。 該資料接著會寫入至記憶體內檔案，然後再傳送至同步處理服務，以將此資訊安全地儲存在磁碟上。 這個事件表示在記憶體中寫入或更新該資料時發生問題。 若要修正此問題，請嘗試停用再重新啟用密碼回寫，強制重新寫入此組態檔。|
| 32008| ValidationError| 這個事件表示我們從密碼重設 Web 服務所收到的回應無效。 若要修正此問題，請嘗試停用再重新啟用密碼回寫。|
| 32009| AuthTokenError| 這個事件表示我們無法取得 Azure AD Connect 設定期間所指定之全域系統管理員帳戶的授權權杖。 造成這個錯誤的原因可能是全域系統管理員帳戶所指定的使用者名稱或密碼錯誤，或是指定的全域系統管理員帳戶已同盟。 若要修正此問題，請以正確的使用者名稱和密碼重新執行設定，並確保系統管理員是受管理的 (僅限雲端或已密碼同步處理的) 帳戶。|
| 32010| CryptoError| 這個事件表示在產生密碼加密金鑰或解密從雲端服務抵達的密碼時發生錯誤。 此錯誤可能表示您的環境有問題。 請查看事件記錄檔的詳細資料，深入了解此問題並加以解決。 您也可以嘗試停用再重新啟用密碼回寫服務來解決這個問題。|
| 32011| OnBoardingServiceError| 這個事件表示內部部署服務無法與密碼重設 Web 服務正確地通訊，來起始上架程序。 這可能是因為有防火牆規則或是取得租用戶的授權權杖時發生問題。 若要修正此問題，請確定您沒有封鎖透過 TCP 443 和 TCP 9350-9354 或連到 https://ssprsbprodncu-sb.accesscontrol.windows.net/ 的輸出連線，並且您要用來上線的 AAD 管理帳戶並未同盟。|
| 32013| OffBoardingError| 這個事件表示內部部署服務無法與密碼重設 Web 服務正確地通訊，來起始下架程序。 這可能是因為有防火牆規則或是取得租用戶的授權權杖時發生問題。 若要修正此問題，請確定您沒有封鎖透過 443 或連到 https://ssprsbprodncu-sb.accesscontrol.windows.net/ 的輸出連線，並且您要用來下線的 AAD 管理帳戶並未同盟。|
| 32014| ServiceBusWarning| 這個事件表示我們必須重試連線到租用戶的服務匯流排執行個體。 在正常情況下，您不必在意此事件，但如果您多次看到此事件，請考慮檢查您的服務匯流排網路連線，尤其是如果它是高延遲或低頻寬連線的話。|
| 32015| ReportServiceHealthError| 為了監視密碼回寫服務的健全狀況，我們每隔 5 分鐘就會傳送活動訊號資料給我們的密碼重設 Web 服務。 這個事件表示在將此健全狀況資訊傳回到雲端 Web 服務時發生錯誤。 此健全狀況資訊不包含 OII 或 PII 資料，純綷只有活動訊號和基本服務統計資料，以便我們可以在雲端中提供服務狀態資訊。|
| 33001| ADUnKnownError| 這個事件表示 AD 傳回未知的錯誤，請檢查 Azure AD Connect 伺服器事件記錄檔中來自 ADSync 來源的事件，以獲得關於此錯誤的詳細資訊。|
| 33002| ADUserNotFoundError| 這個事件表示在內部部署目錄中找不到嘗試重設或變更密碼的使用者。 當使用者已自內部部署中刪除卻不在雲端中，或如果同步處理發生問題，就可能發生這個事件。請檢查您的同步處理記錄，以及最後幾次執行的同步處理詳細資料，以獲得詳細資訊。|
| 33003| ADMutliMatchError| 當密碼重設或變更要求源自雲端時，我們會使用 Azure AD Connect 設定程序期間所指定的雲端錨點，來判斷如何將該要求往回連結到內部部署環境中的使用者。 這個事件表示我們發現內部部署目錄中有兩位使用者具有相同的雲端錨點屬性。 請檢查您的同步處理記錄，以及最後幾次執行的同步處理詳細資料，以獲得詳細資訊。|
| 33004| ADPermissionsError| 這個事件表示管理代理程式服務帳戶沒有適當的指定帳戶權限，因此無法設定新密碼。 請確定使用者樹系中的 MA 帳戶有樹系中所有物件的重設和變更密碼權限。 如需有關如何執行此操作的詳細資訊，請參閱步驟 4：設定適當的 Active Directory 權限。|
| 33005| ADUserAccountDisabled| 這個事件表示我們嘗試對內部部署中已停用的帳戶重設或變更密碼。 請啟用帳戶，然後再試一次此作業。|
| 33006| ADUserAccountLockedOut| 這個事件表示我們嘗試對內部部署中已鎖定的帳戶重設或變更密碼。 當使用者在短時間內嘗試進行變更或重設密碼作業太多次，就可能發生鎖定。 請解除鎖定帳戶，然後再試一次此作業。|
| 33007| ADUserIncorrectPassword| 這個事件表示使用者在執行密碼變更作業時，指定的目前密碼不正確。 請指定正確的目前密碼，然後再試一次。|
| 33008| ADPasswordPolicyError| 當密碼回寫服務嘗試在本機目錄上設定不符合密碼使用期限、歷程記錄、複雜度或網域篩選需求的密碼時，就會發生此錯誤。 <br> <br> 如果您有最短的密碼使用期限，且最近在該時段內變更過密碼，則必須在到達網域中指定的使用期限後，才能再次變更密碼。 若要進行測試，最短使用期限應該設定為 0。 <br> <br> 如果已啟用密碼歷程記錄需求，則必須選取最近 N 次未用過的密碼，其中 N 是密碼歷程記錄設定。 如果您選取最近 N 次用過的密碼，則會在此案例中看到失敗。 若要進行測試，歷程記錄應該設定為 0。 <br> <br> 如果您有密碼複雜度需求，則會在使用者嘗試變更或重設密碼時強制執行這些需求。 <br> <br> 如果您啟用密碼篩選功能，則當使用者選取的密碼不符合篩選準則時，重設或變更作業就會失敗。|
| 33009| ADConfigurationError| 這個事件表示 Active Directory 的設定有問題，因此在將密碼回寫到內部部署目錄時發生問題。 請檢查 Azure AD Connect 電腦的應用程式事件記錄檔中來自 ADSync 服務的訊息，以獲得所發生錯誤的詳細資訊。|

## <a name="troubleshoot-password-writeback-connectivity"></a>疑難排解密碼回寫連線

如果 Azure AD Connect 的密碼回寫元件發生服務中斷，以下是可供用來解決此問題的一些快速步驟：

* [檢查網路連線](#confirm-network-connectivity)
* [重新啟動 Azure AD Connect 同步處理服務](#restart-the-azure-ad-connect-sync-service)
* [停用再重新啟用密碼回寫功能](#disable-and-re-enable-the-password-writeback-feature)
* [安裝最新版的 Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [疑難排解密碼回寫](#troubleshoot-password-writeback)

一般而言，我們會建議您依上述順序執行這些步驟，以最快速的方式復原服務。

### <a name="confirm-network-connectivity"></a>檢查網路連線

最常見的失敗點是防火牆和 Proxy 連接埠，以及閒置逾時的設定不正確。 如需詳細資訊，請在 [Azure AD Connect 的必要條件](./connect/active-directory-aadconnect-prerequisites.md)一文中檢閱連線必要條件。

### <a name="restart-the-azure-ad-connect-sync-service"></a>重新啟動 Azure AD Connect 同步處理服務

重新啟動 Azure AD Connect 同步處理服務有助於解決服務的連線問題或其他暫時性問題。

1. 以系統管理員身分，在執行 **Azure AD Connect** 的伺服器上按一下 [啟動]。
2. 在搜尋方塊中輸入 **“services.msc”**，然後按 **Enter**。
3. 找出 **Microsoft Azure AD Sync** 項目。
4. 以滑鼠右鍵按一下服務項目，然後按一下 [ **重新啟動**]，並等候作業完成。

   ![重新啟動 Azure AD Sync 服務][Service Restart]

這些步驟會重新建立您與雲端服務的連線，解決您可能會遇到的任何中斷問題。 如果重新啟動同步處理服務無法解決您的問題，建議您接下來試著停用再重新啟用密碼回寫功能。

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>停用再重新啟用密碼回寫功能

停用再重新啟用密碼回寫功能有助於解決連線問題。

1. 以系統管理員身分，開啟 [ **Azure AD Connect 設定精靈**]。
2. 在 [連線到 Azure AD] 對話方塊上，輸入您的「Azure AD 全域系統管理員認證」
3. 在 [連線到 AD DS] 對話方塊上，輸入您的「AD Domain Services 系統管理員認證」。
4. 在 [專門識別您的使用者] 對話方塊上，按 [下一步] 按鈕。
5. 在 [選用功能] 對話方塊上，取消核取 [密碼回寫] 核取方塊。
6. 在其餘的對話方塊頁面上逐一點選 [下一步] 而不變更任何項目，直到到達 [準備好設定] 頁面為止。
7. 確認設定頁面顯示**密碼回寫選項為停用**，然後按一下綠色的 [設定] 按鈕來認可變更。
8. 在 [已完成] 對話方塊上，取消選取 [立即同步處理] 選項，然後按一下 [完成] 來關閉精靈。
9. 重新開啟 [Azure AD Connect 設定精靈]。
10. **重複步驟 2-8**，但請確定您已在 [選用功能] 畫面上**勾選 [密碼回寫] 選項** 來重新啟用服務。

這些步驟會重新建立您與雲端服務的連線，解決您可能會遇到的中斷問題。

如果停用再重新啟用密碼回寫功能無法解決您的問題，建議您接下來試著重新安裝 Azure AD Connect。

### <a name="install-the-latest-azure-ad-connect-release"></a>安裝最新版的 Azure AD Connect

重新安裝 Azure AD Connect 可以解決我們的雲端服務和您的本機 AD 環境之間的設定和連線問題。

建議您只在嘗試過上述前兩個步驟後，才執行此步驟。

> [!WARNING]
> 如果您已自訂現成可用的同步處理規則，**先備份這些規則，然後再繼續進行升級，並於完成後以手動方式重新部署這些規則**。

1. 從 [Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=615771)下載最新版的 Azure AD Connect。
2. 由於您已安裝 Azure AD Connect，您需要執行就地升級，即可將 Azure AD Connect 安裝更新為最新版。
3. 執行下載的封裝，並遵循螢幕上的指示來更新您的 Azure AD Connect 電腦。

這些步驟會重新建立您與雲端服務的連線，解決您可能會遇到的任何中斷問題。

如果安裝最新版的 Azure AD Connect 伺服器無法解決您的問題，建議您最後在安裝最新版本後試著停用再重新啟用密碼回寫。

## <a name="verify-whether-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>確認 Azure AD Connect 是否有執行密碼回寫所需的權限
Azure AD Connect 需要 AD **重設密碼**權限才能執行密碼回寫。 若要了解 Azure AD Connect 是否有給定內部部署 AD 使用者帳戶的權限，您可以使用 Windows 有效權限功能：

1. 登入 Azure AD Connect 伺服器，並啟動**同步處理服務管理員** (開始 → 同步處理服務)。
2. 在 [連接器] 索引標籤下，選取內部部署的 **AD 連接器**，然後按一下 [屬性]。  
![有效權限 - 步驟 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
3. 在快顯對話方塊中，選取 [連線到 Active Directory 樹系] 索引標籤，然後記下 [使用者名稱] 屬性。 Azure AD Connect 會使用這個 AD DS 帳戶來執行目錄同步作業。 若要讓 Azure AD Connect 能夠執行密碼回寫，AD DS 帳戶必須有「重設密碼」權限。  
![有效權限 - 步驟 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
4. 登入內部部署網域控制站，然後啟動 **Active Directory 使用者和電腦**應用程式。
5. 按一下 [檢視]，並確定 [進階功能] 選項已啟用。  
![有效權限 - 步驟 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
6. 尋找您想要確認的 AD 使用者帳戶。 以滑鼠右鍵按一下該帳戶，然後選取 [屬性]。  
![有效權限 - 步驟 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 
7. 在快顯對話方塊中，移至 [安全性] 索引標籤，然後按一下 [進階]。  
![有效權限 - 步驟 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
8. 在 [進階安全性設定] 快顯對話方塊中，移至 [有效存取權] 索引標籤。
9. 按一下 [選取使用者]，然後選取 Azure AD Connect 所使用的 AD DS 帳戶 (請參閱步驟 3)。 然後按一下 [檢視有效存取權]。  
![有效權限 - 步驟 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
10. 向下捲動並尋找 [重設密碼]。 如果該項目是勾選狀態，則表示 AD DS 帳戶有權重設選定 AD 使用者帳戶的密碼。  
![有效權限 - 步驟 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD 論壇

如果您有關於 Azure AD 和自助式密碼重設的一般問題，您可以在 [Azure AD 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD)尋求社群協助。 社群的成員包括工程師、產品經理、MVP 和 IT 專業人員。

## <a name="contact-microsoft-support"></a>連絡 Microsoft 支援

如果找不到問題的答案，我們的支援團隊一直都能進一步協助您。

為了正確地提供協助，我們會要求您在開啟案例時提供盡可能詳細的資料，包括︰

* **錯誤的一般描述** - 錯誤為何？ 注意到何種行為？ 我們如何才能重現錯誤？ 請提供盡可能詳細的資料。
* **頁面** - 您注意到錯誤時的所在頁面。 如果您能夠，請包含 URL 和螢幕擷取畫面。
* **支援碼** – 使用者看到錯誤時所產生的支援碼。
    * 若要找到支援碼，請重現錯誤，然後按一下畫面底部的 [支援碼] 連結，將所產生的 GUID 傳送給支援工程師。
    ![尋找畫面底部的支援碼][Support Code]
    * 如果您所在的頁面底部沒有支援碼，請按 F12，搜尋 SID 和 CID，然後將這兩個結果傳送給支援工程師。
* **日期、時間和時區** - 請包含發生錯誤的精確日期和時間 (**含時區**)。
* **使用者識別碼** – 看到錯誤的使用者是誰？ (user@contoso.com)
    * 這是同盟使用者嗎？
    * 這是密碼雜湊同步使用者嗎？
    * 這是僅限雲端的使用者嗎？
* **授權** - 使用者是否已獲得 Azure AD Premium 或 Basic 授權？
* **應用程式事件記錄** - 如果您使用密碼回寫，而且錯誤位於您的內部部署基礎結構中，請在連絡支援人員時包含來自 Azure AD Connect 伺服器的應用程式事件記錄壓縮複本。



[Service Restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "重新啟動 Azure AD Sync 服務"
[Support Code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "支援程式碼位於視窗右下角"

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
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)
