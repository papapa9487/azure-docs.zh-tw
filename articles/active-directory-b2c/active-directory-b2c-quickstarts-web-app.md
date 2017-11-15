---
title: "測試已啟用 Azure AD B2C 的 Web 應用程式 | Microsoft Docs"
description: "使用測試 Azure AD B2C 環境，測試登入、註冊、編輯設定檔和重設密碼使用者旅程圖"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: patricka
ms.openlocfilehash: 07f2c21409176d30f4570e267a4472745f843f85
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2017
---
# <a name="test-drive-an-azure-ad-b2c-enabled-web-app"></a>測試已啟用 Azure AD B2C 的 Web 應用程式

Azure Active Directory B2C 提供雲端身分識別管理，使您的應用程式、企業和客戶受到保護。 本快速入門使用範例待辦事項清單應用程式示範：

> [!div class="checklist"]
> * 使用自訂登入頁面登入。
> * 使用社交識別提供者登入。
> * 建立和管理您的 Azure AD B2C 帳戶及使用者設定檔。
> * 呼叫受 Azure AD B2C 保護的 Web API。

## <a name="prerequisites"></a>必要條件

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)，其中包含 **ASP.NET 和 Web 部署**工作負載。 
* Facebook、Google、Microsoft 或 Twitter 社交帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下載範例

從 GitHub [下載或複製範例應用程式](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi)。

## <a name="run-the-app-in-visual-studio"></a>在 Visual Studio 中執行應用程式

在範例應用程式專案資料夾中，於 Visual Studio 中開啟 `B2C-WebAPI-DotNet.sln` 方案。 

此方案是待辦事項清單範例應用程式，內含兩個專案：

* **TaskWebApp** – ASP.NET MVC Web 應用程式，使用者可以在其中管理其待辦事項清單項目。  
* **TaskService** – ASP.NET Web API 後端，可管理針對使用者待辦事項清單項目執行的作業。 Web 應用程式會呼叫這個 Web API 並顯示結果。

在本快速入門中，您需要同時執行 `TaskWebApp` 和 `TaskService` 專案。 

1. 在 Visual Studio 功能表中，選取 [專案] > [設定啟始專案...]。 
2. 選取 [多個啟始專案] 選項按鈕。
3. 將兩個專案的 [動作] 變更為 [啟動]。 按一下 [確定] 。

![在 Visual Studio 中設定啟動頁面](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

選取 [偵錯] > [開始偵錯]，以建置和執行這兩個應用程式。 每個應用程式會在自己的瀏覽器索引標籤中開啟：

`https://localhost:44316/` - 此頁面是 ASP.NET Web 應用程式。 在本快速入門中，您直接與此應用程式互動。
`https://localhost:44332/` - 此頁面是 ASP.NET Web 應用程式呼叫的 Web API。

## <a name="create-an-account"></a>建立帳戶

按一下 ASP.NET Web 應用程式中的 [註冊/登入] 連結，以啟動 [註冊或登入] 工作流程。 建立帳戶時，您可以使用現有的社交識別提供者帳戶或電子郵件帳戶。 在本快速入門中，請使用 Facebook、Google、Microsoft 或 Twitter 的社交身分識別提供者帳戶。

![範例 ASP.NET Web 應用程式](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>使用社交識別提供者註冊

若要使用社交識別提供者註冊，按一下您想要使用之識別提供者的按鈕。 

![登入或註冊提供者](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

您需要使用您的社交帳戶認證進行驗證 (登入)，以及授權應用程式讀取您的社交帳戶資訊。 透過授與存取權，應用程式可以從社交帳戶擷取設定檔資訊，例如您的名稱與縣/市。 

完成識別提供者的登入程序。 比方說，如果您選擇 Twitter，請輸入 Twitter 認證，然後按一下 [登入]。

![使用社交帳戶驗證和授權](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

新的 Azure AD B2C 帳戶設定檔詳細資料會預先填入來自您社交帳戶的資訊。

更新 [顯示名稱]、[職稱] 和 [縣/市] 欄位，然後按一下 [繼續]。  您輸入的值會用於您的 Azure AD B2C 使用者帳戶設定檔。

![新的帳戶註冊設定檔詳細資料](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

您已成功進行下列作業：

> [!div class="checklist"]
> * 使用身分識別提供者進行驗證。
> * 建立 Azure AD B2C 使用者帳戶。 

## <a name="edit-your-profile"></a>編輯您的設定檔

Azure Active Directory B2C 提供讓使用者更新其設定檔的功能。 在 Web 應用程式功能表列中，按一下設定檔名稱，然後選取 [編輯設定檔] 以編輯您建立的設定檔。

![編輯設定檔](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

變更您的 [顯示名稱] 和 [縣/市]。  按一下 [繼續] 以更新設定檔。

![更新設定檔](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

請注意，頁面右上部分的顯示名稱顯示已更新的名稱。 

## <a name="access-a-secured-web-api-resource"></a>存取受保護的 Web API 資源

按一下 [待辦事項清單]，輸入並修改您的待辦事項清單項目。 ASP.NET Web 應用程式會在對 Web API 資源的要求中包含存取權杖，以要求對使用者待辦事項清單項目執行作業的權限。 

在 [新項目] 文字方塊中輸入文字。 按一下 [新增] 以呼叫受 Azure AD B2C 保護的 Web API，而該 Web API 會加入待辦事項清單項目。

![加入待辦事項清單項目](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

您已成功使用 Azure AD B2C 使用者帳戶，對受 Azure AD B2C 保護的 Web API 提出授權呼叫。

## <a name="next-steps"></a>後續步驟

本快速入門中使用的範例可用來嘗試其他 Azure AD B2C 案例，包括：

* 使用電子郵件地址，建立新的本機帳戶。
* 重設您的本機帳戶密碼。

如果已準備好研究如何建立您自己的 Azure AD B2C 租用戶，並將範例設定為使用您自己的租用戶執行，請嘗試下列教學課程。

> [!div class="nextstepaction"]
> [建立支援 Azure Active Directory B2C 註冊、登入、設定檔編輯及密碼重設的 ASP.NET Web 應用程式](active-directory-b2c-devquickstarts-web-dotnet-susi.md)