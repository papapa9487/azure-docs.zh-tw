---
title: "Azure AD v2 JS SPA 指引設定 - 設定 | Microsoft Docs"
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
ms.openlocfilehash: adff529bfdc40006666cc643d49a302d7f1d09ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
## <a name="register-your-application"></a>註冊您的應用程式

有多種方式可建立應用程式，請選取其中一個：

### <a name="option-1-register-your-application-express-mode"></a>選項 1：註冊您的應用程式 (快速模式)
現在您需要在「Microsoft 應用程式註冊入口網站」註冊應用程式：

1.  透過 [Microsoft 應用程式註冊入口網站 (英文)](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure) 註冊您的應用程式
2.  輸入應用程式的名稱和您的電子郵件
3.  確定已選取 [引導式設定] 選項
4.  依照指示取得應用程式識別碼並貼到您的程式碼中

### <a name="option-2-register-your-application-advanced-mode"></a>選項 2：註冊您的應用程式 (進階模式)

1. 前往 [Microsoft 應用程式註冊入口網站 (英文)](https://apps.dev.microsoft.com/portal/register-app) 註冊應用程式
2. 輸入應用程式的名稱和您的電子郵件 
3. 確定已取消選取 [引導式設定] 選項
4.  按一下 `Add Platform`，然後選取 `Web`
5. 以您的 web 伺服器作為基礎，新增對應到應用程式之 URL 的 `Redirect URL`。 如需有關如何設定/取得 Visual Studio 和 Python 中重新導向 URL 的相關指示，請參閱下列各節。
6. 按一下 [儲存] 

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>取得重新導向 URL 的 Visual Studio 指示
> 請遵循指示取得重新導向 URL：
> 1.    在「方案總管」中，選取專案並查看 `Properties` 視窗 (如果您沒有看到屬性視窗，請按 `F4`)
> 2.    將此值從 `URL` 複製到剪貼簿：<br/> ![專案屬性](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    切換回應用程式註冊入口網站，並貼上 `Redirect URL`的值，然後按一下 [儲存]

<p/>

> #### <a name="setting-redirect-url-for-python"></a>設定 Python 的重新導向 URL
> 對於 Python，您可以透過命令列設定 Web 伺服器連接埠。 此指引設定會使用連接埠 8080 作為參考，但是可以隨意使用任何其他可用的通訊埠。 在任何情況下，請遵循下列指示以在應用程式註冊資訊中設定重新導向 URL：<br/>
> - 切換回應用程式註冊入口網站，並將 `http://localhost:8080/` 設定為 `Redirect URL`，或如果您是使用自訂 TCP 通訊埠 (其中[通訊埠] 是自訂 TCP 通訊埠號碼)，請使用 `http://localhost:[port]/`，然後按一下 [儲存]


#### <a name="configure-your-javascript-spa"></a>設定您的 JavaScript SPA

1.  建立名為 `msalconfig.js` 的檔案，其中包含應用程式註冊資訊。 如果您使用 Visual Studio，請選取專案 (專案根資料夾)，以滑鼠右鍵按一下並選取：`Add` > `New Item` > `JavaScript File`。 將它命名為 `msalconfig.js`
2.  將下列程式碼新增至 `msalconfig.js` 檔案：

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
用您剛剛註冊的應用程式識別碼取代 <code>Enter_the_Application_Id_here</code>
</li>
</ol>
