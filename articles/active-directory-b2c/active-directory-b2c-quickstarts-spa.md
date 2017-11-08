---
title: "測試 Azure AD B2C 單一頁面應用程式 | Microsoft Docs"
description: "使用測試 Azure AD B2C 環境，測試登入、註冊、編輯設定檔和重設密碼使用者旅程圖"
services: active-directory-b2c
documentationcenter: 
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 5a8a46af-28bb-4b70-a7f0-01a5240d0255
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 22da1ae317ba685d32f93d3331cf794b568891ec
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-single-page-application-configured-with-azure-ad-b2c"></a>測試使用 Azure AD B2C 設定的單一頁面應用程式

## <a name="about-this-sample"></a>關於此範例

Azure Active Directory B2C 提供雲端身分識別管理，使您的應用程式、企業和客戶受到保護。  本快速入門使用範例單一頁面應用程式示範：

* 使用「註冊或登入」原則建立，或以電子郵件地址使用社交識別提供者或本機帳戶登入。 
* **呼叫 API** 以從受 Azure AD B2C 保護的資源擷取您的顯示名稱。

## <a name="prerequisites"></a>必要條件

* 使用下列工作負載安裝 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
    - **ASP.NET 和 Web 開發**

* 安裝 [Node.js](https://nodejs.org/en/download/)

* Facebook、Google、Microsoft 或 Twitter 社交帳戶。 如果您沒有社交帳戶，則需要有效的電子郵件地址。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下載範例

從 GitHub [下載或複製範例應用程式](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)。

## <a name="run-the-sample-application"></a>執行範例應用程式

從 Node.js 命令提示字元執行此範例： 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

主控台視窗會顯示在您電腦上執行之 Web 應用程式的連接埠號碼。

```
Listening on port 6420...
```

在網頁瀏覽器中開啟 `http://localhost:6420` 以存取 Web 應用程式。


![瀏覽器中的範例應用程式](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>建立帳戶

按一下 [登入] 按鈕以啟動 Azure AD B2C 的「註冊或登入」工作流程。 建立帳戶時，您可以使用現有的社交識別提供者帳戶或電子郵件帳戶。

### <a name="sign-up-using-a-social-identity-provider"></a>使用社交識別提供者註冊

若要使用社交識別提供者註冊，按一下您想要使用之識別提供者的按鈕。 如果您偏好使用電子郵件地址，請跳至[使用電子郵件地址註冊](#sign-up-using-an-email-address)一節。

![登入或註冊提供者](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

您需要使用您的社交帳戶認證進行驗證 (登入)，以及授權應用程式讀取您的社交帳戶資訊。 透過授與存取權，應用程式可以從社交帳戶擷取設定檔資訊，例如您的名稱與縣/市。 

![使用社交帳戶驗證和授權](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

您的新帳戶設定檔詳細資料會預先填入來自您社交帳戶的資訊。 

![新的帳戶註冊設定檔詳細資料](media/active-directory-b2c-quickstarts-spa/new-account-sign-up-profile-details-spa.png)

更新 [顯示名稱]、[職稱] 和 [縣/市] 欄位，然後按一下 [繼續]。  您輸入的值會用於您的 Azure AD B2C 使用者帳戶設定檔。

您已成功建立使用識別提供者的新 Azure AD B2C 使用者帳戶。 

下一步：[呼叫資源](#call-a-resource)一節。

### <a name="sign-up-using-an-email-address"></a>使用電子郵件地址註冊

如果您選擇不使用社交帳戶提供驗證，則可以建立使用有效電子郵件地址的 Azure AD B2C 使用者帳戶。 Azure AD B2C 本機使用者帳戶使用 Azure Active Directory 作為識別提供者。 若要使用電子郵件地址，請按一下 [還沒有帳戶嗎？立即註冊] 連結。

![使用電子郵件登入或註冊](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-email-spa.png)

輸入有效的電子郵件地址，然後按一下 [傳送驗證碼]。 您需要有效的電子郵件地址，才能從 Azure AD B2C 接收驗證碼。 

輸入您在電子郵件中收到的驗證碼，按一下 [驗證碼]。

新增設定檔資訊，然後按一下 [建立]。

![使用電子郵件註冊新帳戶](media/active-directory-b2c-quickstarts-spa/sign-up-new-account-profile-email-web.png)

您已成功建立新的 Azure AD B2C 本機使用者帳戶。

## <a name="call-a-resource"></a>呼叫資源

一旦登入，您可以按一下 [呼叫 Web API] 按鈕，使您的顯示名稱從 Web API 呼叫以 JSON 物件傳回。 

![Web API 回應](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

## <a name="next-steps"></a>後續步驟

下一個步驟是建立您自己的 Azure AD B2C 租用戶，並設定使用您的租用戶來執行的範例。 

> [!div class="nextstepaction"]
> [在 Azure 入口網站中建立 Azure Active Directory B2C 租用戶](active-directory-b2c-get-started.md)