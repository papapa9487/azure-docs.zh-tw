---
title: "測試 Azure AD B2C 桌面應用程式 | Microsoft Docs"
description: "使用測試 Azure AD B2C 環境，測試登入、註冊、編輯設定檔和重設密碼使用者旅程圖"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 86293627-26fb-4e96-a76b-f263f9a945bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 9653d86dd5635016512bf6e6fafbf7195145ed07
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-desktop-application-configured-with-azure-ad-b2c"></a>測試使用 Azure AD B2C 設定的桌面應用程式

Azure Active Directory B2C 提供雲端身分識別管理，使您的應用程式、企業和客戶受到保護。  本快速入門使用範例 Windows Presentation Foundation (WPF) 桌面應用程式示範：

* 使用「註冊或登入」原則建立，或以電子郵件地址使用社交識別提供者或本機帳戶登入。 
* **呼叫 API** 以從受 Azure AD B2C 保護的資源擷取您的顯示名稱。

## <a name="prerequisites"></a>必要條件

* 使用下列工作負載安裝 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
    - **.NET 桌面開發**

* Facebook、Google、Microsoft 或 Twitter 社交帳戶。 如果您沒有社交帳戶，則需要有效的電子郵件地址。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下載範例

從 GitHub [下載或複製範例應用程式](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)。

## <a name="run-the-app-in-visual-studio"></a>在 Visual Studio 中執行應用程式

在範例應用程式專案資料夾中，於 Visual Studio 中開啟 `active-directory-b2c-wpf.sln` 方案。 

選取 [偵錯] > [開始偵錯]，以建置和執行應用程式。 

## <a name="create-an-account"></a>建立帳戶

按一下 [登入] 以開始「註冊或登入」工作流程。 建立帳戶時，您可以使用現有的社交識別提供者帳戶或電子郵件帳戶。

![範例應用程式](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

### <a name="sign-up-using-a-social-identity-provider"></a>使用社交識別提供者註冊

若要使用社交識別提供者註冊，按一下您想要使用之識別提供者的按鈕。 如果您偏好使用電子郵件地址，請跳至[使用電子郵件地址註冊](#sign-up-using-an-email-address)一節。

![登入或註冊提供者](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

您需要使用您的社交帳戶認證進行驗證 (登入)，以及授權應用程式讀取您的社交帳戶資訊。 透過授與存取權，應用程式可以從社交帳戶擷取設定檔資訊，例如您的名稱與縣/市。 

![使用社交帳戶驗證和授權](media/active-directory-b2c-quickstarts-desktop-app/twitter-authenticate-authorize-wpf.png)

您的新帳戶設定檔詳細資料會預先填入來自您社交帳戶的資訊。 視需要修改詳細資料，然後按一下 [繼續]。

![新的帳戶註冊設定檔詳細資料](media/active-directory-b2c-quickstarts-desktop-app/new-account-sign-up-profile-details-wpf.png)

您已成功建立使用識別提供者的新 Azure AD B2C 使用者帳戶。 登入之後，存取權杖會顯示在 [權杖資訊] 文字方塊。 存取 API 資源時，會使用存取權杖。

![授權權杖](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

下一步：跳至[編輯您的設定檔](#edit-your-profile)一節。

### <a name="sign-up-using-an-email-address"></a>使用電子郵件地址註冊

如果您選擇不使用社交帳戶提供驗證，則可以建立使用有效電子郵件地址的 Azure AD B2C 使用者帳戶。 Azure AD B2C 本機使用者帳戶使用 Azure Active Directory 作為識別提供者。 若要使用電子郵件地址，請按一下 [還沒有帳戶嗎？立即註冊] 連結。

![使用電子郵件登入或註冊](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-email-wpf.png)

輸入有效的電子郵件地址，然後按一下 [傳送驗證碼]。 您需要有效的電子郵件地址，才能從 Azure AD B2C 接收驗證碼。

輸入您在電子郵件中收到的驗證碼，按一下 [驗證碼]。

新增設定檔資訊，然後按一下 [建立]。

![使用電子郵件註冊新帳戶](media/active-directory-b2c-quickstarts-desktop-app/sign-up-new-account-profile-email-wpf.png)

您已成功建立新的 Azure AD B2C 本機使用者帳戶。 登入之後，存取權杖會顯示在 [權杖資訊] 文字方塊。 存取 API 資源時，會使用存取權杖。

![授權權杖](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="edit-your-profile"></a>編輯您的設定檔

Azure Active Directory B2C 提供讓使用者更新其設定檔的功能。 按一下 [編輯設定檔] 以編輯您建立的設定檔。

![編輯設定檔](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

選擇與您建立的帳戶相關聯的識別提供者。 例如，如果建立您的帳戶時使用 Twitter 作為識別提供者，請選擇 Twitter 以修改相關聯的設定檔詳細資料。

![選擇與設定檔相關聯的提供者來編輯](media/active-directory-b2c-quickstarts-desktop-app/edit-account-choose-provider-wpf.png)

變更您的 [顯示名稱] 或 [縣/市]。 

![更新設定檔](media/active-directory-b2c-quickstarts-desktop-app/update-profile-wpf.png)

新的存取權杖會顯示在 [權杖資訊] 文字方塊。 如果您想要檢查對設定檔所作的變更，請將存取權杖複製並貼到權杖解碼器 https://jwt.ms。

![授權權杖](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="access-a-resource"></a>存取資源

按一下 [呼叫 API] 向 Azure AD B2C 保護資源 https://fabrikamb2chello.azurewebsites.net/hello 提出要求。 

![呼叫 API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

應用程式會包含顯示於要求中之 [權杖資訊] 文字方塊中的權杖資訊。 API 會傳回存取權杖中所包含的顯示名稱。

## <a name="next-steps"></a>後續步驟

下一個步驟是建立您自己的 Azure AD B2C 租用戶，並設定使用您的租用戶來執行的範例。 

> [!div class="nextstepaction"]
> [在 Azure 入口網站中建立 Azure Active Directory B2C 租用戶](active-directory-b2c-get-started.md)
