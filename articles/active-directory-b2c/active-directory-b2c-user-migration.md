---
title: "Azure Active Directory B2C：使用者移轉方法"
description: "討論有關使用圖形 API (以及選擇性地使用 Azure AD B2C 自訂原則) 來進行使用者移轉的核心概念和進階概念。"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/04/2017
ms.author: yoelh
ms.openlocfilehash: b102edd997e69fb3568780a42dfe93fc9a90e332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C：使用者移轉
當您計劃將識別提供者移轉到 Azure AD B2C 時，您可能也需要移轉使用者帳戶。 本文說明如何將任何識別提供者的現有使用者帳戶移轉至 Azure AD B2C。 本文並非是為了做出規定，而是要說明幾種不同方法的其中兩種。  是否適合則屬開發人員的責任。

## <a name="user-migration-flows"></a>使用者移轉流程
Azure AD B2C 可讓您透過[圖形 API](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) 移轉使用者。 使用者移轉程序分為兩個流程：

* **移轉前** - 當您以純文字形式握有使用者認證 (使用者名稱和密碼) 的存取權時，便適用此流程。 認證也可能會加密，但您可以將其解密。 此程序包括：從舊識別提供者讀取使用者，並在 Azure AD B2C 目錄中建立新的帳戶。

* **移轉前和密碼重設** - 當您無法存取使用者的密碼時，便適用此流程。 例如：
    * 密碼以 HASH 格式儲存
    * 密碼儲存在識別提供者而無法存取。 舊識別提供者會藉由呼叫 Web 服務來驗證使用者認證

在這兩個流程中，您會先執行__移轉前__程序，從舊識別提供者讀取使用者，在 Azure AD B2C 目錄中建立新帳戶。 如果您沒有密碼，則可以使用您產生的隨機密碼建立帳戶。 您必須要求使用者變更其密碼。 否則，當使用者第一次登入時，B2C 會要求使用者重設密碼。

## <a name="password-policy"></a>密碼原則
Azure AD B2C 的本機帳戶密碼原則是以 Azure AD 原則為基礎的。 Azure AD B2C 的註冊或登入和密碼重設原則會使用「強式」密碼強度，而且不會讓任何密碼到期。 如需更詳細的資料，請閱讀 [Azure AD 中的密碼原則](https://msdn.microsoft.com/library/azure/jj943764.aspx) 。

如果您想要移轉的帳戶所使用的密碼強度比[由 Azure AD B2C 強制執行的強式密碼強度](https://msdn.microsoft.com/library/azure/jj943764.aspx)還低，您可以停用強式密碼需求。 若要變更預設密碼原則，請將 `passwordPolicies` 屬性設定為 `DisableStrongPassword`。 例如，您可以修改「建立使用者要求」，如下所示： 

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-using-graph-api-to-migrate-users"></a>步驟 1：使用圖形 API 來移轉使用者
您可以透過圖形 API (使用密碼或隨機密碼) 建立 Azure AD B2C 使用者帳戶。 本節說明有關如何在 Azure AD B2C 目錄中使用圖形 API 建立使用者帳戶的程序。

### <a name="step-11-register-your-application-in-your-tenant"></a>步驟 1.1 在您的租用戶中註冊您的應用程式
若要與圖形 API 通訊，您必須先獲得含有系統管理權限的服務帳戶。 在 Azure AD 中，您要註冊應用程式並向 Azure AD 驗證。 應用程式的認證包括：**應用程式識別碼**和**應用程式密碼**。 應用程式會以本身 (而非使用者的身分) 呼叫圖形 API。

首先，請在 Azure AD 中註冊移轉應用程式。 然後，建立應用程式金鑰 (應用程式密碼)，並使用正確權限設定應用程式。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在頁面右上角選取您的帳戶，以選擇您的 Azure AD **B2C** 租用戶。
3. 在左窗格中，按一下 [Azure Active Directory] (而非 Azure AD B2C)。 您可能需要選取 [更多服務]，才能找到它。
4. 選取 [應用程式註冊]。
5. 按一下 [新增應用程式註冊]。

    ![新增應用程式註冊](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
6. 遵照提示進行，並建立新的應用程式
    * 在 [名稱] 中使用 **B2CUserMigratioin** 或其他任何您喜歡的名稱。
    * 針對 [應用程式類型] 使用 [Web 應用程式/API]。
    * 在 [登入 URL] 中使用 https://localhost (因為它與此應用程式無關)。
    * 按一下 [建立] 。
7. 在應用程式建立後，於應用程式清單中選取新建立的應用程式 **B2CUserMigratioin**。
選取 [屬性]，複製**應用程式識別碼**並加以儲存，以供稍後使用。

### <a name="step-12-create-application-secret"></a>步驟 1.2 建立應用程式密碼
8. 繼續在 Azure 入口網站的已註冊應用程式中。 按一下 [金鑰] 來新增金鑰 (也稱為用戶端祕密)。 此外，複製該金鑰以供稍後使用。

    ![應用程式識別碼和金鑰](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>步驟 1.3 對您的應用程式授與系統管理權限
9. 繼續在 Azure 入口網站的**已註冊應用程式**中
10. 按一下 [所需的權限]。
11. 按一下 [Windows Azure Active Directory]。
12. 在 [啟用存取] 功能表的 [應用程式權限] 底下，選取 [讀取和寫入目錄資料權限]，然後按一下 [儲存]。
13. 最後，回到 [所需的權限]，按一下 [授與權限] 按鈕。

    ![應用程式權限](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

現在，您的應用程式具有從 B2C 租用戶建立、讀取和更新使用者的權限。

### <a name="step-14-optional-environment-cleanup"></a>步驟 1.4 [選用] 環境清除
讀取和寫入目錄資料權限不包含刪除使用者的能力。 如果您想要讓您的應用程式有能力刪除使用者 (以清除您的環境)，則需要進行額外的步驟。 此步驟包含執行 PowerShell 以設定__使用者帳戶管理員__權限，否則，您可以跳到下一節。


> [!IMPORTANT]
> 您必須使用 B2C 租用戶**本機**的 B2C 租用戶系統管理員帳戶。 這些帳戶看起來像這樣︰admin@contosob2c.onmicrosoft.com。

>[!NOTE]
> 下列 PowerShell 指令碼需要 [Azure Active Directory PowerShell **第 2 版**](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)。

在下列 PowerShell 指令碼中：
* 連線到您的線上服務。 若要這麼做，請在 Windows PowerShell 命令提示字元執行 `Connect-AzureAD`，並提供您的認證。 
* 使用**應用程式識別碼**來為應用程式指派使用者帳戶管理員角色。 這些角色具有已知的識別項，您只需要在指令碼中輸入您的**應用程式識別碼**即可。

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

使用您的 Azure AD **應用程式識別碼**變更 `$AppId` 值

## <a name="step-2-pre-migration-application-sample"></a>步驟 2：移轉前應用程式範例
下載範例程式碼並開始執行。 您可以[將範例程式碼下載為 .zip 檔案](http://www.github.com)。

### <a name="step-21-edit-the-migration-data-file"></a>步驟 2.1 編輯移轉資料檔案
應用程式範例會使用包含虛擬使用者資料的 JSON 檔案。 成功執行範例後，請將程式碼變更為取用您自己資料庫中的資料。 或是將使用者設定檔匯出至 JSON 檔案，並將應用程式設定為使用該檔案。
若要編輯 JSON 檔案，請開啟 `AADB2C.UserMigration.sln` Visual Studio 方案。 在 `AADB2C.UserMigration` 專案中，開啟 `UsersData.json` 檔案。


![使用者資料檔案](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

如您所見，此檔案包含使用者實體的清單。 每個使用者實體都有：
* 電子郵件
* displayName
* firstName
* lastName
* 密碼 (可空白)

> [!NOTE]
> 在編譯階段，Visual Studio 會將檔案複製到 `bin` 目錄。

### <a name="step-22-configure-the-application-settings"></a>步驟 2.2 設定應用程式設定
在 `AADB2C.UserMigration` 專案底下，開啟 App.config 檔案。 使用您自己的值取代下列應用程式設定：

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users data e.g. UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure Table string}" />
    
</appSettings>
```

> [!NOTE]
> * 後面幾節會說明如何使用 Azure 資料表的連接字串
> * B2C 租用戶的名稱是您在租用戶建立期間輸入的網域，並且會顯示在 Azure 入口網站上。 通常以尾碼 `.onmicrosoft.com` 結束，例如 `contosob2c.onmicrosoft.com`。
>

### <a name="step-23-run-the-pre-migration-process"></a>步驟 2.3 執行移轉前程序
以滑鼠右鍵按一下 `AADB2C.UserMigration` 方案並重建範例。 如果成功，您現在應該有一個 `UserMigration.exe` 可執行檔位於 `AADB2C.UserMigration\bin\Debug`。 若要執行移轉程序，請使用下列其中一個命令列參數：

* 若要**使用密碼移轉使用者**，請使用 `UserMigration.exe 1` 命令。

* 若要**使用隨機密碼移轉使用者**，則請使用 `UserMigration.exe 2` 命令。 這項作業也會建立 Azure 資料表實體。 稍後您可以設定 REST API 服務的呼叫原則。 此服務會使用 Azure 資料表來追蹤和管理移轉程序。

![移轉程序示範](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>步驟 2.4 檢查移轉前程序
若要檢查結果，請從 Azure 入口網站開啟 **Azure AD B2C**，然後按一下 [使用者和群組]。 在搜尋方塊中輸入其中一個使用者的顯示名稱，然後看一下使用者設定檔。 或者，您也可以使用此應用程式範例，根據**登入電子郵件地址**擷取使用者。 若要根據登入電子郵件地址來搜尋使用者，請執行下列命令

```Console
UserMigration.exe 3 {email address}
```

您也可以將輸出儲存至 JSON 檔案，如下所示：
```Console
UserMigration.exe 3 {email address} >> UserProfile.json
```


使用您想要的 JSON 編輯器開啟 UserProfile.json 檔案。 在 Visual Studio Code 中，您可以使用 `Shift+Alt+F` 或快顯功能表中的 [格式化文件] 將 JSON 文件格式化。

![使用者設定檔 json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

您也可以使用 `UserMigration.exe 4 <Display name>` 命令，以根據**顯示名稱**擷取使用者

### <a name="step-25-optional-environment-cleanup"></a>步驟 2.5 [選用] 環境清除
如果您想要清除 Azure AD 租用戶並從 Azure AD 目錄移除使用者，請執行 `UserMigration.exe 5` 命令

> [!NOTE]
> * 若要清除租用戶，請為應用程式設定__使用者帳戶管理員__權限
> * 移轉應用程式範例會清除 JSON 檔案中列出的所有使用者

### <a name="step-26-sing-in-with-migrated-users-with-password"></a>步驟 2.6 已移轉之使用者的登入 (使用密碼)
利用使用者的密碼執行移轉前程序之後，帳戶便已可供使用，使用者因此能使用 Azure AD B2C 登入您的應用程式。 如果您無法存取使用者的密碼，請繼續瀏覽下一節。

## <a name="step-3-password-reset"></a>步驟 3：密碼重設
如果您使用隨機密碼移轉使用者，使用者就必須重設其密碼。 若要重設密碼：
* 傳送具有重設密碼連結的歡迎電子郵件
* [選用] 變更您的原則，以處理使用者未重設密碼就嘗試登入的情況。 在登入時，您的原則會檢查移轉狀態。 如果使用者未變更密碼，則會擲回易懂的錯誤訊息，要求使用者按一下「忘記密碼？」

    > [!NOTE]
    > 若要檢查並變更使用者移轉狀態，您必須使用自訂原則。 完成[開始使用自訂原則](active-directory-b2c-get-started-custom.md)一文中的步驟。
    >

### <a name="step-31-send-a-welcome-email-with-link-to-reset-password"></a>步驟 3.1 傳送具有重設密碼連結的歡迎電子郵件
若要取得密碼重設原則的連結：

1.  開啟 [Azure AD B2C 設定]，然後開啟 [重設密碼] 原則屬性。

2. 選取您的應用程式
    >[!NOTE]
    >
    >[立即執行] 需要在租用戶上至少預先註冊一個應用程式。 若要了解如何註冊應用程式，請參閱 Azure AD B2C [開始使用](active-directory-b2c-get-started.md)一文或[應用程式註冊](active-directory-b2c-app-registration.md)一文。  

2.  選取 [立即執行] 並檢查原則
3.  **複製** URL 並將它傳送給您的使用者

    ![設定診斷記錄](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-user-migration-status"></a>步驟 4: [選用] 變更原則以檢查並設定使用者移轉狀態

當使用者不先重設密碼就嘗試登入時，您的原則應該會傳回易懂的錯誤訊息。 例如：您的密碼已過期，若要重設密碼，請按一下重設密碼連結。  此選用步驟需要利用自訂原則來使用 Azure AD B2C，如[開始使用自訂原則](active-directory-b2c-get-started-custom.md)一文所述。

在本節中，您會變更原則以在登入時檢查移轉狀態。 如果使用者未變更密碼，則會傳回 HTTP 409 錯誤訊息，要求使用者按一下「忘記密碼？」連結。 link. 若要追蹤密碼的變更，您可以使用 Azure 資料表。 使用命令列參數 `2` 執行移轉前程序，在 Azure 資料表中建立使用者實體。 您的服務：

* 在登入時，Azure AD B2C 原則會叫用您的移轉 RESTful 服務，傳送電子郵件來作為輸入宣告。 此服務會在 Azure 資料表中搜尋電子郵件地址。 如果存在，則擲回「您必須變更密碼」錯誤訊息。

* 在密碼重設時，請於使用者成功變更密碼之後從 Azure 資料表移除實體。


> [!NOTE]
>我們使用 Azure 資料表來簡化範例。 您可以將移轉狀態儲存在任何資料庫，或儲存為 Azure AD B2C 帳戶中的自訂屬性。

### <a name="41-application-settings"></a>4.1 應用程式設定
若要測試此 RESTful API 示範。 在 Visual Studio 中開啟 `AADB2C.UserMigration.sln` Visual Studio 方案。 在 `AADB2C.UserMigration.API` 專案中，開啟 App.config 檔案。使用您自己的值取代三個應用程式設定：

```XML
<appSettings>
    <add key="BlobStorageConnectionString" value="{The Azure Blob storage connection string"} />
</appSettings>
```

### <a name="step-42-deploy-your-web-application-to-azure-app-services"></a>步驟 4.2 將 Web 應用程式部署至 Azure App Service
將 API 服務發佈至 Azure App Service。 如需詳細資訊，請參閱：[將應用程式部署到 Azure App Service](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy)

### <a name="step-43-add-technical-profile-and-technical-profile-validation-to-your-policy"></a>步驟 4.3 新增技術設定檔和技術設定檔驗證到您的原則中 
1.  從您的工作目錄中開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。 
2. 尋找 `<ClaimsProviders>` 區段
3. 在 `ClaimsProviders` 元素下新增下列 XML 程式碼片段
4. 變更 `ServiceUrl` 的值，使其指向您的端點 URL 

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>

    <TechnicalProfile Id="LocalAccountSignIn">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountPasswordReset">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>

<ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>

    <!-- This technical profile uses a validation technical profile to authenticate the user. -->
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountSignIn" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountWritePasswordUsingObjectId">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountPasswordReset" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

技術設定檔會定義一個輸入宣告：`signInName` (以電子郵件傳送)。 在登入時，宣告就會傳送至 RESTful 端點。

在為 RESTful API 定義技術設定檔之後，請指示 Azure AD B2C 原則呼叫該技術設定檔。 XML 程式碼片段會覆寫定義於基底原則中的 `SelfAsserted-LocalAccountSignin-Email`。 XML 程式碼片段也會新增 ReferenceId 是指向技術設定檔 `LocalAccountUserMigration` 的 `ValidationTechnicalProfile`。 

### <a name="step-44-upload-the-policy-to-your-tenant"></a>步驟 4.4 將原則上傳至您的租用戶
1.  在 [Azure 入口網站](https://portal.azure.com)中，切換至[您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後按一下 [Azure AD B2C]。
2.  選取 [識別體驗架構]。
3.  按一下 [所有原則]。
4.  選取 [上傳原則]
5.  勾選 [覆寫已存在的原則] 方塊。
6.  **上傳** TrustFrameworkExtensions.xml，並確定它通過驗證
7.  重複最後一個步驟，並上傳 SignUpOrSignIn.xml

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>步驟 4.5 使用 [立即執行] 測試自訂原則
1.  開啟 [Azure AD B2C 設定]，然後移至 [識別體驗架構]。
2.  開啟 **B2C_1A_signup_signin**，此為您上傳的信賴憑證者 (RP) 自訂原則。 選取 [立即執行]。
3. 嘗試使用其中一個已移轉的使用者來登入，然後按一下 [登入]。 您應該就會看到您的 Rest API 發出下列錯誤訊息。

    ![設定診斷記錄](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshooting-your-rest-api"></a>步驟 4.6 [選用] 針對 REST API 進行疑難排解
您可以近乎即時地監視和查看記錄資訊。

1. 從 RESTful 應用程式的設定功能表，向下捲動至 [監視] 區段，然後按一下 [診斷記錄]。 
2. 啟用應用程式記錄 (檔案系統) 
3. 將 [層級] 設定為 [詳細資訊] 層級
4. 按一下 [儲存] 

    ![設定診斷記錄](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. 從設定功能表按一下 [記錄資料流]
6. 檢查 RESTful API 的輸出。

如需詳細資訊，請參閱：[串流記錄和主控台](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-streaming-logs-and-console)

> [!IMPORTANT]
> __診斷記錄__只應在開發與測試期間使用。 RESTful API 輸出可能包含不應該在生產環境中公開的機密資訊。
>

## <a name="download-the-complete-policy-files"></a>下載完整的原則檔案
選擇性：建議您在使用自訂原則逐步解說完成使用者入門後，使用自己的自訂原則檔案來建置您的情節，而非使用這些範例檔案。  [參考的範例原則檔案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration)