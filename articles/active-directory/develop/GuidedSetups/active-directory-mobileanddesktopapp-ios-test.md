---
title: "Azure AD v2 iOS 快速入門 - 測試 | Microsoft Docs"
description: "iOS (Swift) 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 4a88096d2b0a23708acdbc1798eac528599b4f71
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>從 iOS 應用程式測試查詢 Microsoft Graph API

按下 `Command` + `R` 以在模擬器中執行程式碼。

![範例螢幕擷取畫面](media/active-directory-mobileanddesktopapp-ios-test/iostestscreenshot.png)

當您準備好進行測試時，請點選 [呼叫 Microsoft Graph API]，系統會提示您輸入您的使用者名稱和密碼。

### <a name="consent"></a>同意
第一次登入應用程式時，系統會向您顯示如下所示的類似同意畫面，其中包含您必須明確接受的事項：

![同意畫面](media/active-directory-mobileanddesktopapp-ios-test/iosconsentscreen.png)

### <a name="expected-results"></a>預期的結果
您應該會在 [記錄] 區段中看到由 Microsoft Graph API 傳回的使用者設定檔資訊。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 `user.read` 範圍以讀取使用者的設定檔。 根據預設值，在我們註冊入口網站上註冊的每個應用程式中都會自動新增此範圍。 Microsoft Graph 的某些其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 例如，針對 Microsoft Graph，需要範圍 `Calendars.Read` 才能列出使用者的行事曆。 為了在應用程式內容中存取使用者的行事曆，您需要將 `Calendars.Read` 委派權限新增至應用程式註冊的資訊，然後將 `Calendars.Read` 範圍新增至 `acquireTokenSilent` 呼叫。 系統可能會在您增加範圍數目時，提示使用者同意其他事項。

<!--end-collapse-->



