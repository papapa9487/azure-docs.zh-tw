---
title: "Azure AD v2 JS SPA 指引設定 - 測試 | Microsoft Docs"
description: "JavaScript SPA 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
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
ms.date: 06/01/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: c559c80781da3631a783d96539622c4c89fe7e17
ms.contentlocale: zh-tw
ms.lasthandoff: 08/19/2017

---
## <a name="test-your-code"></a>測試您的程式碼

> ### <a name="testing-with-visual-studio"></a>使用 Visual Studio 進行測試
> 如果您使用 Visual Studio，請按 `F5` 來執行您的專案：瀏覽器會開啟並將您導引至 http://localhost:{port}，您會在其中看見 [呼叫 Microsoft Graph API] 按鈕。

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>使用 Python 或另一部 web 伺服器進行測試
> 如果您不是使用 Visual Studio，請確定您的 Web 伺服器已啟動，而且已設定為接聽 TCP 連接埠 (以含有 index.html 檔案的資料夾為基礎)。 對於 Python，您可以在命令提示字元 / 終端機從應用程式的資料夾執行，開始接聽此連接埠：
> 
> ```bash
> python -m http.server 8080
> ```
>  然後，開啟瀏覽器並輸入 http://localhost:8080 或 http://localhost:{port} - 其中的 port 對應至您的 Web 伺服器正在接聽的連接埠。 使用 [呼叫 Microsoft Graph API] 按鈕，您應會看到 index.html 頁面的內容。

## <a name="test-your-application"></a>測試您的應用程式

在瀏覽器載入您的 index.html 之後，按一下 [呼叫 Microsoft Graph API] 按鈕。 如果這是第一次，瀏覽器會將您重新導向到 Microsoft Azure Active Directory v2 端點，系統會在此提示您登入。
 
![範例螢幕擷取畫面](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>同意
第一次登入應用程式時，系統會顯示如下所示的類似同意畫面，其中包含您必須接受的事項：

 ![同意畫面](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>預期的結果
您應該會看到 Microsoft Graph API 回應所傳回的使用者設定檔資訊。
 
 ![結果](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

您也會在 [存取權杖] 和 [ID 權杖宣告] 方塊中看到取得之權杖的相關基本資訊。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 `user.read` 範圍以讀取使用者的設定檔。 根據預設值，在我們註冊入口網站上註冊的每個應用程式中都會自動新增此範圍。 Microsoft Graph 的某些其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 例如，針對 Microsoft Graph，需要範圍 `Calendars.Read` 才能列出使用者的行事曆。 為了在應用程式內容中存取使用者的行事曆，您需要將 `Calendars.Read` 委派權限新增至應用程式註冊的資訊，然後將 `Calendars.Read` 範圍新增至 `acquireTokenSilent` 呼叫。 系統可能會在您增加範圍數目時，提示使用者同意其他事項。

如果後端 API 不需要範圍 (不建議)，您可以在 `acquireTokenSilent` 和/或 `acquireTokenRedirect` 呼叫中使用 `clientId` 作為範圍。

<!--end-collapse-->

