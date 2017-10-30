---
title: "在 Azure 入口網站中多重要素驗證報告的參考 | Microsoft Docs"
description: "在 Azure 入口網站中多重要素驗證報告的參考資訊"
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
ms.date: 10/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3b817c59658f4a5d102a3d65a17fade254e0257c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>在 Azure 入口網站中多重要素驗證報告的參考

透過 [Azure 入口網站](https://portal.azure.com)中的 [Azure Active Directory (Azure AD) 報告](active-directory-reporting-azure-portal.md)，您可以取得判斷您的環境執行狀況所需的資訊。

[登入活動報告](active-directory-reporting-activity-sign-ins.md)提供您受管理應用程式使用方式和使用者登入活動的相關資訊，包括多重要素驗證 (MFA) 使用方式的相關資訊。 

MFA 資料可讓您深入了解 MFA 如何在您的組織中運作。 它可讓您回答問題，例如： 

- 是否以 MFA 挑戰登入？ 

- 使用者如何完成 MFA？ 

- 為何使用者無法完成 MFA？  

彙總所有登入資料，您可以進一步了解您組織內的 MFA 體驗。 資料可協助您回答問題，例如： 

- 有多少使用者經過 MFA 挑戰？  

- 有多少使用者無法完成 MFA 挑戰？ 

- 使用者會遇到的常見 MFA 問題是什麼？ 


此資料可透過 Azure 入口網站和[報告 API](active-directory-reporting-api-getting-started-azure-portal.md) 取得。 


## <a name="data-structure"></a>資料結構


MFA 的登入活動報告可讓您存取下列資訊：

**需要 MFA：**登入是否需要 MFA。 依據每個使用者的 MFA、條件式存取或其他原因，可能需要 MFA。 可能的值為 `Yes` 或 `No`。

**MFA 驗證方法：**使用者用來完成 MFA 的驗證方法。 可能的值包括： 

- 簡訊 

- 行動應用程式通知 

- 撥打電話 (驗證電話) 

- 行動應用程式驗證碼 

- 撥打電話 (辦公室電話) 

- 撥打電話 (替代驗證電話) 

**MFA 驗證詳細資料：**電話號碼的清除版本，例如：+X XXXXXXXX64。 

**MFA 結果：**MFA 是否已滿足或拒絕的詳細資訊：

- 如果已滿足 MFA，此資料行提供如何滿足 MFA 的詳細資訊。 

- 如果已拒絕 MFA，此資料行會提供拒絕的原因。 可能的值為 `Satisfied` 或 `Denied`。 

下節列出 MFA 結果欄位的可能字串值。

## <a name="status-strings"></a>狀態字串

此區段會列出 MFA 結果狀態字串的可能值。

### <a name="satisfied-status-strings"></a>滿足的狀態字串


- Azure Multi-Factor Authentication

    - 在雲端中完成 

    - 由於租用戶上設定的原則所以已過期 

    - 註冊提示 

    - 因為在權杖中宣告而滿足 

    - 因為在權杖中宣告而滿足 

    - 因為在權杖中宣告而滿足 

    - 因為在權杖中宣告而滿足 

    - 因為外部提供者提供的宣告而滿足 

    - 因為強式驗證而滿足 

    - 因為運用的流程是 Windows 訊息代理程式登入流程而略過 

    - 因為運用的流程是 Windows 訊息代理程式登入流程而略過 

    - 因為應用程式密碼而略過 

    - 因為位置而略過 

    - 因為已註冊的裝置而略過 
    
    - 因為已記住的裝置而略過 

    - 已成功完成 

- 重新導向至外部提供者以進行多重要素驗證 

 
### <a name="denied-status-strings"></a>拒絕的狀態字串

Azure Multi-Factor Authentication 遭到拒絕； 

- 驗證進行中 

- 重複的驗證嘗試 

- 輸入太多次不正確的代碼 

- 無效的驗證 

- 無效的行動應用程式驗證碼 

- 設定錯誤 

- 撥打電話轉到語音信箱 

- 電話號碼的格式無效 

- 服務錯誤 

- 無法接通使用者的電話 

- 無法將行動應用程式通知傳送到裝置 

- 無法傳送行動應用程式通知 

- 使用者拒絕驗證 

- 使用者未回應行動應用程式通知 

- 使用者沒有任何已註冊的驗證方法 

- 使用者輸入不正確的代碼 

- 使用者輸入不正確的 PIN 

- 使用者未成功驗證即掛斷電話 

- 使用者遭到封鎖 

- 使用者從未輸入驗證碼 

- 找不到使用者 
 
- 驗證碼已使用過一次 



## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Azure Active Directory 報告](active-directory-reporting-azure-portal.md)。




























