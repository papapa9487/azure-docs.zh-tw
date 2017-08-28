---
title: "連線到 Azure AD 時對 MVC 專案所做的變更 | Microsoft Docs"
description: "說明您使用 Visual Studio 已連線服務連線至 Azure AD 時，MVC 專案會有何狀況"
services: active-directory
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
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
ms.openlocfilehash: 72cd94ba16cb4fe234c898b093c7de6a08f71239
ms.contentlocale: zh-tw
ms.lasthandoff: 03/21/2017

---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>我的 MVC 專案 (Visual Studio Azure Active Directory 已連線服務) 發生什麼事？
> [!div class="op_single_selector"]
> * [開始使用](vs-active-directory-dotnet-getting-started.md)
> * [發生什麼情形](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>已加入參考
### <a name="nuget-package-references"></a>NuGet 封裝參考
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>.NET 參考
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel**
* **System.IdentityModel.Tokens.Jwt**
* **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>已加入程式碼
### <a name="code-files-were-added-to-your-project"></a>程式碼檔案加入至專案
驗證啟動類別 **App_Start/Startup.Auth.cs** 加入至內含 Azure AD 驗證之啟動邏輯的專案。 另外也已加入控制器類別 Controllers/AccountController.cs，內含 **SignIn()** 和 **SignOut()** 方法。 最後，已加入部分檢視 **Views/Shared/_LoginPartial.cshtml**，內含 SignIn/SignOut 的動作連結。

### <a name="startup-code-was-added-to-your-project"></a>啟動程式碼已加入至專案
如果專案中已有啟動類別，則已更新 **Configuration** 方法來包含 **ConfigureAuth(app)** 的呼叫。 否則已將啟動類別加入至專案。

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>app.config 或 web.config 有新的組態值
已加入下列組態項目。

    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>建立 Azure Active Directory (AD) 應用程式
在您於精靈中選取的目錄中建立 Azure AD 應用程式。

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>如果我核取了 [停用個別使用者帳戶驗證] ，我的專案會有什麼其他變更？
NuGet 封裝參考會被移除，檔案也會移除並加以備份。 根據您的專案狀態，您可能必須手動移除其他參考或檔案，或修改為適當的程式碼。

### <a name="nuget-package-references-removed-for-those-present"></a>移除的 NuGet 封裝參考 (如果存在)
* **Microsoft.AspNet.Identity.Core**
* **Microsoft.AspNet.Identity.EntityFramework**
* **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>備份和移除的程式碼檔案 (如果存在)
下列每個檔案都會備份，並且從專案中移除。 備份檔案位於專案目錄根目錄的「備份」資料夾。

* **App_Start\IdentityConfig.cs**
* **Controllers\ManageController.cs**
* **Models\IdentityModels.cs**
* **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>備份的程式碼檔案 (如果存在)
下列每個檔案會在取代之前備份。 備份檔案位於專案目錄根目錄的「備份」資料夾。

* **Startup.cs**
* **App_Start\Startup.Auth.cs**
* **Controllers\AccountController.cs**
* **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>如果我核取了 [ *讀取目錄資料*]，我的專案會有什麼其他變更？
已加入其他參考。

### <a name="additional-nuget-package-references"></a>其他 NuGet 封裝參考
* **EntityFramework**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **System.Spatial**

### <a name="additional-net-references"></a>其他 .NET 參考
* **EntityFramework**
* **EntityFramework.SqlServer**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
* **System.Spatial**

### <a name="additional-code-files-were-added-to-your-project"></a>其他程式碼檔案已加入至專案
兩個檔案已加入以支援 Token 快取：**Models\ADALTokenCache.cs** 和 **Models\ApplicationDbContext.cs**。  已加入其他控制器和檢視，以說明使用 Azure 圖形 API 存取使用者設定檔資訊。  這些檔案是 **Controllers\UserProfileController.cs** 和 **Views\UserProfile\Index.cshtml**。

### <a name="additional-startup-code-was-added-to-your-project"></a>其他啟動程式碼已加入至專案
在 **startup.auth.cs** 檔案中，新的 **OpenIdConnectAuthenticationNotifications** 物件已加入 **OpenIdConnectAuthenticationOptions** 的 **Notifications** 成員。  這是為了啟用接收 OAuth 程式碼，並用它交換存取權杖。

### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>app.config 或 web.config 已進行其他變更
已加入下列其他組態項目。

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

已加入下列組態區段和連接字串。

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


### <a name="your-azure-active-directory-app-was-updated"></a>Azure Active Directory 應用程式已更新
Azure Active Directory 應用程式已更新為包含「讀取目錄資料」權限，並已建立其他的金鑰做為 **web.config** 檔案中的 ida:ClientSecret。

## <a name="next-steps"></a>後續步驟
- [深入了解 Azure Active Directory](https://azure.microsoft.com/services/active-directory/)


