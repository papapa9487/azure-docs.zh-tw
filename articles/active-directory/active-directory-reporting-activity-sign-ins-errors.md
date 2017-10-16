---
title: "Azure Active Directory 入口網站中的登入活動報告錯誤碼 | Microsoft Docs"
description: "登入活動報告錯誤碼參考。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2a1b7b87df2cd8fa2e98f217480b46f5f6334297
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的登入活動報告錯誤碼

利用使用者登入報告所提供的資訊，您可以找到下列問題的解答︰

- 誰已使用 Azure Active Directory 登入？
- 已登入哪些應用程式？
- 哪些登入失敗，原因為何？

本主題列出錯誤碼及相關說明。 

## <a name="how-can-i-display-failed-sign-ins"></a>如何顯示失敗的登入？ 

所有登入活動資料的第一個進入點是 **Azure Active** 的 [活動] 區段中的[登 入](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)。


![登入活動](./media/active-directory-reporting-activity-sign-ins-errors/61.png "登入活動")


在登入報告中，您可以選取 [失敗] 作為 [登入狀態]，以顯示所有失敗的登入。


![登入活動](./media/active-directory-reporting-activity-sign-ins-errors/06.png "登入活動")

按一下所顯示清單中的項目，會開啟 [活動詳細資料：登入] 刀鋒視窗。 此檢視提供 Azure Active Directory 追蹤的所有登入詳細資料，包括 [登入錯誤碼] 和 [失敗原因]。

![登入活動](./media/active-directory-reporting-activity-sign-ins-errors/05.png "登入活動")


除了使用 Azure 入口網站來存取登入資料以外，您也可以使用[報告 API](active-directory-reporting-api-getting-started-azure-portal.md)。


下一節提供所有可能錯誤的完整概觀以及相關的說明。 

## <a name="error-codes"></a>錯誤碼

| 錯誤| 說明 |
| --- | --- |
| 50001| 在名為 Y 的租用戶中找不到名為 X 的服務主體。如果租用戶的系統管理員尚未安裝此應用程式，也可能會發生此錯誤。 或在目錄中找不到資源主體或為無效|
| 50008| 權杖中的 SAML 判斷提示遺漏或設定不正確。|
| 50011| 回覆位址遺漏、設定不正確或不符合為應用程式設定的回覆位址。|
| 50053| 帳戶遭到鎖定，因為使用者嘗試使用不正確的使用者識別碼或密碼登入太多次。|
| 50054| 使用舊密碼進行驗證。|
| 50055| 無效的密碼，或輸入的密碼過期。|
| 50057| 使用者帳戶已停用。|
| 50058| 在所提供的認證中找不到使用者的身分識別相關資訊，或在租用戶中找不到使用者，或已傳送無訊息的登入要求，但沒有使用者登入或服務無法驗證使用者。|
| 50074| 強式驗證 (第二個因素) 是必要的|
| 50079| 使用者需要註冊第二個因素驗證|
| 50126| 使用者名稱、密碼無效，或內部部署使用者名稱或密碼無效。|
| 50131| 使用於各種條件式存取錯誤。 例如，不正確的 Windows 裝置狀態，要求因為可疑的活動、存取原則和安全性原則決策而遭到封鎖。|
| 50133| 工作階段因為到期或近期密碼變更而無效。|
| 50144| 使用者的 Active Directory 密碼已到期。|
| 65001| 應用程式 X 沒有存取應用程式 Y 的權限，或已撤銷此權限。 或者，使用者或系統管理員尚未同意使用識別碼為 X 的應用程式。針對此使用者和資源傳送互動式授權要求。 或者，使用者或系統管理員尚未同意使用識別碼為 X 的應用程式。將授權要求傳送給租用戶管理員，以代表應用程式 Y 對資源 Z 採取行動。|
| 65005| 應用程式所需資源存取清單不包含資源可探索的應用程式，或用戶端應用程式已要求存取未在其所需資源存取清單中指定的資源，或 Graph 服務傳回不正確的要求或找不到資源。|
| 70001| 在名為 Y 的租用戶中找不到名為 X 的應用程式。如果租用戶的系統管理員尚未安裝此應用程式或租用戶中的任何使用者尚未同意使用此應用程式，也可能會發生此錯誤。 您可能會將驗證要求傳送至錯誤的租用戶。|
| 80001| 沒有可用的驗證代理程式。|
| 80002| 驗證代理程式的密碼驗證要求已逾時。|
| 80003| 驗證代理程式收到的回應無效。|
| 80004| 登入要求中使用的使用者主體名稱 (UPN) 不正確。|
| 80005| 驗證代理程式：發生錯誤。|
| 80007| 驗證代理程式無法連線至 Active Directory。|
| 80010| 驗證代理程式無法連線將密碼解密。|
| 81001| 使用者的 Kerberos 票證太大。|
| 81002| 無法驗證使用者的 Kerberos 票證。|
| 81003| 無法驗證使用者的 Kerberos 票證。|
| 81004| Kerberos 驗證嘗試失敗。|
| 81008| 無法驗證使用者的 Kerberos 票證。|
| 81009| 無法驗證使用者的 Kerberos 票證。|
| 81010| 無縫式 SSO 失敗，因為使用者的 Kerberos 票證已過期或無效。|
| 81011| 找不到以使用者的 Kerberos 票證中資訊為基礎的使用者物件。|
| 81012| 嘗試登入 Azure AD 的使用者與登入裝置的使用者不同。|
| 81013| 找不到以使用者的 Kerberos 票證中資訊為基礎的使用者物件。|
| 90014| 當認證中未出現預期的欄位時，使用於各種情況。|
| 90093| 傳回的圖表包含要求的禁止錯誤碼。|



## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Azure Active Directory 入口網站中的登入活動報告](active-directory-reporting-activity-sign-ins.md)。

