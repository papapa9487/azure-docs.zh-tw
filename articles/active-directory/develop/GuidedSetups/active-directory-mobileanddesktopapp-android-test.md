---
title: "Azure AD v2 Android 快速入門 - 測試 | Microsoft Docs"
description: "Android 應用程式如何取得存取權杖，以及如何呼叫 Microsoft 圖形 API，或呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
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
ms.openlocfilehash: 6df64f4820f8409bd8897d5ac24f81bffeeef102
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>測試您的程式碼

1. 將您的程式碼部署到裝置/模擬器。
2. 當您準備好進行測試時，請使用 Microsoft Azure Active Directory (組織帳戶) 或 Microsoft 帳戶 (live.com、outlook.com) 帳戶登入。 

![範例螢幕擷取畫面](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![登入](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

### <a name="consent"></a>同意
使用者第一次登入您的應用程式時，系統會向他們顯示如下所示的類似同意畫面，其中包含他們必須明確接受的事項： 

![同意](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


### <a name="expected-results"></a>預期的結果
您應該會看到呼叫 Microsoft 圖形 API ‘me’ 端點的結果，該呼叫可用來取得使用者設定檔 - https://graph.microsoft.com/v1.0/me。 如需一般 Microsoft Graph 端點的清單，請參閱這篇[文章 (英文)](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 `user.read` 範圍以讀取使用者的設定檔。 根據預設值，在我們註冊入口網站上註冊的每個應用程式中都會自動新增此範圍。 Microsoft Graph 的某些其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 例如，針對 Microsoft Graph，需要範圍 `Calendars.Read` 才能列出使用者的行事曆。 為了在應用程式內容中存取使用者的行事曆，您需要將 `Calendars.Read` 委派權限新增至應用程式註冊的資訊，然後將 `Calendars.Read` 範圍新增至 `acquireTokenSilentAsync` 呼叫。 系統可能會在您增加範圍數目時，提示使用者同意其他事項。

<!--end-collapse-->
