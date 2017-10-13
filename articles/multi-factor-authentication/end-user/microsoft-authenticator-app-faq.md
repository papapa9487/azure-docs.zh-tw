---
title: "Microsoft Authenticator 應用程式說明與支援 | Microsoft Docs"
description: "提供關於 Microsoft 驗證應用程式與 Azure Multi-Factor Authentication 的常見問題與答案清單。"
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 66c8e89a81285391a524ffb95ef48227cbdcd388
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-authenticator-app-faq"></a>Microsoft Authenticator 應用程式常見問題集

本文回答我們收到有關 Microsoft 驗證器應用程式的常見問題。 如果您沒有看到您問題的解答，請移至 [Microsoft 驗證器應用程式論壇](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)。 我們也有另一個關於應用程式特定功能的常見問題集，[使用您的電話常見問題集登入](microsoft-authenticator-app-phone-signin-faq.md)。

Microsoft Authenticator 應用程式取代了 Azure Authenticator 應用程式，當您使用 Azure Multi-Factor Authentication 時，建議使用此應用程式。 Microsoft 驗證器應用程式適用於 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>應用程式中的程式碼有什麼作用？ 為什麼數字持續倒數計時？

開啟 Microsoft 驗證器應用程式時，會看到您已新增的帳戶及每個帳戶旁邊的六或八位元數字。 您可能會看到三十秒倒數。

當您登入您的帳戶時，會使用這些程式碼。 您輸入使用者名稱和密碼之後，系統可能會要求您輸入驗證碼。 開啟 Microsoft 驗證器應用程式並複製目前顯示的程式碼。 在登入頁面中輸入該程式碼來完成。

程式碼每隔 30 秒會變更的原因，是讓您永遠不會使用相同的程式碼兩次。 不像您應該記住的密碼。 其概念是，只有可存取您電話的人會知道您的驗證碼。

程式碼不需要網際網路或資料，因此您不必擔心有電話服務需要登入。 關閉應用程式後，它不會在背景中持續執行，因此不會耗盡電力。 您可以關閉應用程式並忽略它，等到下次您登入為止。  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>我只有在開啟應用程式時才會收到通知。 如果未開啟應用程式，我就收不到任何通知。

如果您收到通知，但儘管響鈴卻未發出雜訊或震動，則請先檢查應用程式設定。 讓應用程式搭配使用音效或震動與其通知。

如果您根本未取得通知，請檢查下列情況︰

- 您的電話處於「請勿打擾」還是無訊息模式？ 此模式可以讓應用程式傳送通知。
- 您可以接收來自其他應用程式的通知嗎？ 如果接收不到，則可能是電話的網路連線或是 Android 或 Apple 的通知通道發生問題。 您可以在電話設定中解決第一個選項，但可能需要連絡服務提供者，以取得第二個選項的協助。
- 您可以在應用程式上接收到某些帳戶的通知，但接收不到其他帳戶的通知嗎？ 如果是，請從應用程式中移除有問題的帳戶，並再次新增該帳戶，以啟用推播通知。

如果嘗試了上述疑難排解的建議但仍有問題，請將您的記錄傳送給我們以供診斷。 移至應用程式設定，然後選取 [說明與意見反應] 和 [傳送記錄]。 接著，請移至 [Microsoft 驗證器應用程式論壇](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)，讓我們了解您遇到的問題，以及目前為止已採取的步驟。

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>我已經使用 Microsoft Authenticator 應用程式做為驗證碼。 如何切換到單鍵推播通知？
透過推播通知核准登入僅適用於個人的 Microsoft 帳戶或是工作和學校 Microsoft 帳戶，而不適用於 Google 或 Facebook 這類協力廠商帳戶。 如果您擁有工作或學校 Microsoft 帳戶，則您的組織可以選擇停用此選項。

如果您使用 Microsoft 帳戶做為個人帳戶，並想要切換到推播通知，則必須再次新增您的帳戶。 使用您的帳戶重新註冊裝置，並設定推播通知。  

如果您使用 Microsoft Authenticator 做為工作或學校帳戶，則您的組織會決定是否允許單鍵通知。

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>單鍵推播通知適用於非 Microsoft 帳戶嗎？
不適用，推播通知只適用於 Microsoft 帳戶與 Azure Active Directory 帳戶。 如果您的工作或學校使用 Azure AD 帳戶，它們可能會停用這項功能。  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>我從備份還原了我的裝置，但我的帳戶代碼遺漏或無法運作。 發生什麼情形？
基於安全考量，我們不會從應用程式的備份還原帳戶。  還原應用程式之後，請刪除您的帳戶，然後重新新增。

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>我收到新的裝置。 如何移除舊裝置的 Microsoft Authenticator 應用程式並移到新裝置？
將 Microsoft Authenticator 應用程式新增至新裝置，並不會自動從任何其他裝置將它移除。 若要管理針對您帳戶所設定的裝置，請瀏覽您用來管理雙步驟驗證的相同網站，並選擇移除舊的應用程式。

針對個人 Microsoft 帳戶，這個網站是您的[帳戶安全性](https://account.microsoft.com/security)頁面。 針對工作或學校 Microsoft 帳戶，這個網站可能是 [MyApps](https://myapps.microsoft.com) 或您的組織已設定的自訂入口網站。

### <a name="how-do-i-remove-an-account-from-the-app"></a>如何從應用程式移除帳戶？
* iOS︰從主畫面中，在帳戶圖格上往左撥動。 選取 [刪除] 。
* Windows Phone︰從主畫面中，選取 [功能表] 按鈕，然後 [編輯帳戶]。 點選帳戶名稱旁邊的 [X]。
* Android︰從主畫面中，選取 [功能表] 按鈕，然後 [編輯帳戶]。 點選帳戶名稱旁邊的 [X]。

如果已向您的組織註冊您的裝置，您可能需要完成額外的步驟才能移除您的帳戶。 在這些裝置上，Microsoft Authenticator 應用程式會自動註冊為裝置系統管理員。 如果您想要完全解除安裝應用程式，則需要先在應用程式設定中取消註冊應用程式。

### <a name="why-does-the-app-request-so-many-permissions"></a>為什麼應用程式要求這麼多權限？
以下是我們要求的完整權限清單，以及權限在應用程式中的使用方式。 您看到的特定權限取決於您擁有的電話類型。

* **相機**︰在您新增工作、學校或非 Microsoft 帳戶時，將會使用相機來掃描 QR 代碼。
* **連絡人和手機**：當您使用個人 Microsoft 帳戶登入時，我們嘗試尋找您在手機上使用的現有帳戶來簡化程序。
* **SMS**︰當您第一次使用個人 Microsoft 帳戶登入時，我們必須確定您的電話號碼符合我們記錄的電話號碼。 我們會將簡訊傳送到先前下載應用程式的手機。 這個訊息包含 6-8 位數的驗證碼。 我們會在簡訊中找到這個驗證碼，而不是要求您找到它，並將它輸入應用程式中。
* **透過其他應用程式進行**：當您收到確認您身分識別的通知時，我們會透過任何其他執行中應用程式來顯示該通知。
* **從網際網路接收資料**：需要有此權限，才能傳送通知。
* **防止手機進入休眠**：如果您向組織註冊裝置，則他們可以在您的手機上變更這個原則。
* **控制震動**︰您可以選擇是否要在收到確認身分識別的通知時震動。
* **使用指紋硬體**︰確認身分識別時，部分工作和學校帳戶需要額外的 PIN。 為了簡化此程序，我們可讓您使用指紋，而不是輸入 PIN。
* **檢視網路連接**︰當您新增 Microsoft 帳戶時，應用程式需要網路/網際網路連接。
* **讀取儲存體的內容**：只有在您透過應用程式設定來報告技術問題時，才會使用這個權限。 會收集您儲存體中的部分資訊來診斷問題。
* **完整網路存取**︰需要有此權限，才能傳送確認您身分識別的通知。
* **在啟動時執行**︰如果您重新啟動手機，此權限可確保您繼續收到確認您身分識別的通知。

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>為什麼 Microsoft Authenticator 應用程式可讓您不需解除鎖定裝置就能核准要求？

您不需解除鎖定裝置來核准驗證要求，因為您只需要證明您帶著您的電話。 雙步驟驗證需要證明兩件事 - 一件是您知道的，另一件則是您擁有的。 您知道的就是密碼。 您擁有的則是您的電話 (使用 Microsoft Authenticator 應用程式設定，並註冊為 MFA 證明)。因此，擁有電話及核准要求符合用於驗證第二要素的準則。

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>帳戶清單中的鎖定圖示代表什麼意義？

掛鎖圖示指出在 Azure AD 中註冊裝置，並向帳戶進行註冊。 iOS 的裝置註冊是在 Microsoft Intune 註冊期間進行。

## <a name="next-steps"></a>後續步驟

### <a name="contact-us"></a>與我們連絡
如果您無法在此處找到解答，我們希望能夠聆聽您的意見。 請移至 [Microsoft Authenticator 應用程式論壇 (英文)](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) 張貼您的問題並從社群取得協助，或在此頁面上留下您的意見。


### <a name="related-topics"></a>相關主題
* Microsoft 帳戶的[關於雙步驟驗證](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
* 針對您的工作或學校帳戶[使用雙步驟驗證遇到困難](multi-factor-authentication-end-user-troubleshoot.md)？
* [使用 Microsoft 驗證器從您的電話登入](microsoft-authenticator-app-phone-signin-faq.md)
