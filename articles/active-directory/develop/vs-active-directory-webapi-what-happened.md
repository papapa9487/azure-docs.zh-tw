---
title: "連線到 Azure AD 時對 WebApi 專案所做的變更 | Microsoft Docs"
description: "說明當您使用 Visual Studio 來連線到 Azure AD 時，您的 WebApi 專案會發生什麼狀況"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8ca3234e54d8736aa187195640a103ac6eb145e7
ms.contentlocale: zh-tw
ms.lasthandoff: 03/21/2017

---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>我的 WebApi 專案發生什麼狀況 (Visual Studio Azure Active Directory 已連接服務)
> [!div class="op_single_selector"]
> * [開始使用](vs-active-directory-webapi-getting-started.md)
> * [發生什麼情形](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>已加入參考
### <a name="nuget-package-references"></a>NuGet 封裝參考
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

### <a name="net-references"></a>.NET 參考
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

## <a name="code-changes"></a>程式碼變更
### <a name="code-files-were-added-to-your-project"></a>程式碼檔案加入至專案
驗證啟動類別 **App_Start/Startup.Auth.cs** 加入至內含 Azure AD 驗證之啟動邏輯的專案。

### <a name="startup-code-was-added-to-your-project"></a>啟動程式碼已加入至專案
如果專案中已有啟動類別，則已更新 **Configuration`ConfigureAuth(app)` 方法來包含** 的呼叫。 否則已將啟動類別加入至專案。

### <a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>app.config 或 web.config 檔案有新的組態值。
已加入下列組態項目。

```
    <appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

### <a name="an-azure-ad-app-was-created"></a>已建立 Azure AD 應用程式
在您於精靈中選取的目錄中建立 Azure AD 應用程式。

[深入了解 Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>如果我核取了 [ *停用個別使用者帳戶驗證*]，我的專案會有什麼其他變更？
NuGet 封裝參考會被移除，檔案也會移除並加以備份。 根據您的專案狀態，您可能必須手動移除其他參考或檔案，或修改為適當的程式碼。

### <a name="nuget-package-references-removed-for-those-present"></a>移除的 NuGet 封裝參考 (如果存在)
* `Microsoft.AspNet.Identity.Core`
* `Microsoft.AspNet.Identity.EntityFramework`
* `Microsoft.AspNet.Identity.Owin`

### <a name="code-files-backed-up-and-removed-for-those-present"></a>備份和移除的程式碼檔案 (如果存在)
下列每個檔案都會備份，並且從專案中移除。 備份檔案位於專案目錄根目錄的「備份」資料夾。

* `App_Start\IdentityConfig.cs`
* `Controllers\AccountController.cs`
* `Controllers\ManageController.cs`
* `Models\IdentityModels.cs`
* `Providers\ApplicationOAuthProvider.cs`

### <a name="code-files-backed-up-for-those-present"></a>備份的程式碼檔案 (如果存在)
下列每個檔案會在取代之前備份。 備份檔案位於專案目錄根目錄的「備份」資料夾。

* `Startup.cs`
* `App_Start\Startup.Auth.cs`

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>如果我核取了 [ *讀取目錄資料*]，我的專案會有什麼其他變更？
### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>app.config 或 web.config 已進行其他變更
已加入下列其他組態項目。

```
    <appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

### <a name="your-azure-active-directory-app-was-updated"></a>Azure Active Directory 應用程式已更新
Azure Active Directory 應用程式已更新為包含「讀取目錄資料」權限，並已建立其他的金鑰做為 `web.config` 檔案中的 ida:Password。

## <a name="next-steps"></a>後續步驟
- [深入了解 Azure Active Directory](https://azure.microsoft.com/services/active-directory/)


