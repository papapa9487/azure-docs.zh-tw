---
title: "Azure AD v2 Windows Desktop 快速入門 - 測試 | Microsoft Docs"
description: "Windows Desktop .NET (XAML) 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 972cc48057c13271d725b0c973c3ccf651ad27c4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
## <a name="test-your-code"></a>測試您的程式碼

為測試您的應用程式，請按 `F5` 以在 Visual Studio 中執行專案。 您的主視窗應會出現：

![範例螢幕擷取畫面](media/active-directory-mobileanddesktopapp-windowsdesktop-test/samplescreenshot.png)

當您準備好進行測試時，請按一下 [呼叫 Microsoft 圖形 API]，然後使用 Microsoft Azure Active Directory (組織帳戶) 或 Microsoft 帳戶 (live.com、outlook.com) 帳戶登入。 如果這是第一次進行此操作，系統會顯示視窗要求使用者登入：

![登入](media/active-directory-mobileanddesktopapp-windowsdesktop-test/signinscreenshot.png)

### <a name="consent"></a>同意
第一次登入應用程式時，系統會向您顯示如下所示的類似同意畫面，其中包含您必須明確接受的事項：

![同意畫面](media/active-directory-mobileanddesktopapp-windowsdesktop-test/consentscreen.png)

### <a name="expected-results"></a>預期的結果
您應該會在 [API 呼叫結果] 畫面上看到由 Microsoft 圖形 API 傳回的使用者設定檔資訊。

您應該也會在 [權杖資訊] 方塊中，看到透過 `AcquireTokenAsync` 或 `AcquireTokenSilentAsync` 取得之權杖的相關基本資訊：

|屬性  |格式  |描述 |
|---------|---------|---------|
|名稱 | {使用者完整名稱} |使用者的名字和姓氏|
|使用者名稱 |<span>user@domain.com</span> |用來識別使用者的使用者名稱|
|權杖到期 |{DateTime}         |權杖的到期時間。 MSAL 將會在必要時更新權杖來為您延長到期日期|
|存取權杖 |{字串}         |傳送的權杖字串將傳送至需要授權標頭的 HTTP 要求|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊
圖形 API 需要 `user.read` 範圍才能讀取使用者設定檔。 根據預設值，在我們註冊入口網站上註冊的每個應用程式中都會自動新增此範圍。 您後端伺服器的部分其他圖形 API 和自訂 API 也會需要其他範圍。 例如，對於圖形 API 而言，就需要 `Calendars.Read` 來列出使用者的行事曆。 為了在應用程式內容中存取使用者的行事曆，您需要新增 `Calendars.Read` 委派應用程式註冊的資訊，然後將 `Calendars.Read` 新增至 `AcquireTokenAsync` 呼叫。 系統可能會在您增加範圍數目時，提示使用者同意其他事項。

<!--end-collapse-->



