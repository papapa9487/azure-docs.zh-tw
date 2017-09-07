---
title: "Azure Active Directory B2C：開始使用自訂原則 | Microsoft Docs"
description: "如何開始使用 Azure Active Directory B2C 自訂原則"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja;parahk;gsacavdm
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 4f14dbf4b66f10290cd4f98d56a005f97cc6a207
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C：開始使用自訂原則

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

完成本文中的步驟之後，您的自訂原則將支援透過電子郵件地址和密碼執行「本機帳戶」註冊或登入。 您也會準備環境以新增識別提供者 (例如 Facebook 或 Azure Active Directory)。 我們建議您先完成這些步驟，然後再了解 Azure Active Directory (Azure AD) B2C 識別體驗架構的其他用途。

## <a name="prerequisites"></a>必要條件

繼續之前，請確定具有 Azure AD B2C 租用戶。此租用戶是存放您的所有使用者、應用程式、原則等的容器。 如果您還沒有租用戶，則必須[建立 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)。 我們強烈建議所有開發人員完成 Azure AD B2C 內建原則的逐步解說，並使用內建原則來設定他們的應用程式，然後再繼續。 當您對原則名稱進行些微變更以叫用自訂原則之後，應用程式將會使用這兩種類型的原則。

>[!NOTE]
>為了存取自訂原則編輯功能，您需要已與租用戶連結的有效 Azure 訂用帳戶。 如果您尚未[將 Azure AD B2C 租用戶連結到 Azure 訂用帳戶](active-directory-b2c-how-to-enable-billing.md)或已停用 Azure 訂用帳戶，將無法使用 [身分識別體驗架構] 按鈕。

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>將簽署和加密金鑰新增至 B2C 租用戶以供自訂原則使用

1. 在您的 Azure AD B2C 租用戶設定中，開啟 [識別體驗架構] 刀鋒視窗。
2. 選取**原則金鑰**以檢視您的租用戶中可用的金鑰。
3. 如果 B2C_1A_TokenSigningKeyContainer 不存在，請加以建立：<br>
    a. 選取 [新增] 。 <br>
    b.這是另一個 C# 主控台應用程式。 選取 [產生]。<br>
    c. 針對 [名稱] 使用 `TokenSigningKeyContainer`。 <br> 
    可能會自動加入前置詞 `B2C_1A_`。<br>
    d. 針對 [金鑰類型] 使用 [RSA]。<br>
    e. 針對 [日期] 使用預設值。 <br>
    f. 針對 [金鑰使用方法] 使用 [簽章]。<br>
    g. 選取 [ **建立**]。<br>
4. 如果 B2C_1A_TokenEncryptionKeyContainer 不存在，請加以建立：<br>
 a. 選取 [新增] 。<br>
 b.這是另一個 C# 主控台應用程式。 選取 [產生]。<br>
 c. 針對 [名稱] 使用 `TokenEncryptionKeyContainer`。 <br>
   可能會自動加入前置詞 `B2C_1A`_。<br>
 d. 針對 [金鑰類型] 使用 [RSA]。<br>
 e. 針對 [日期] 使用預設值。<br>
 f. 針對 [金鑰使用方法] 使用 [加密]。<br>
 g. 選取 [ **建立**]。<br>
5. 建立 B2C_1A_FacebookSecret。 <br>
如果您已有 Facebook 應用程式的祕密，請將它當作原則金鑰來新增到您的租用戶。 否則，您必須建立具有預留位置值的金鑰，原則才能通過驗證。<br>
 a. 選取 [新增] 。<br>
 b.這是另一個 C# 主控台應用程式。 針對 [選項] 使用 [手動]。<br>
 c. 針對 [名稱] 使用 `FacebookSecret`。 <br>
 可能會自動加入前置詞 `B2C_1A_`。<br>
 d. 在 [祕密] 方塊中，輸入 developers.facebook.com 提供給您的 FacebookSecret，或輸入 `0` 作為預留位置。 這不是您的 Facebook 應用程式識別碼。 <br>
 e. 針對 [金鑰使用方法] 使用 [簽章]。 <br>
 f. 選取 [建立] 並確認建立。

## <a name="register-identity-experience-framework-applications"></a>註冊身分識別體驗架構應用程式

Azure AD B2C 會要求您註冊兩個額外的應用程式，由引擎用來註冊和登入使用者。

>[!NOTE]
>您必須建立兩個應用程式，以使用本機帳戶來啟用登入︰IdentityExperienceFramework (Web 應用程式) 和 ProxyIdentityExperienceFramework (原生應用程式)，且具有來自 IdentityExperienceFramework 應用程式的委派權限。 本機帳戶只存在於您的租用戶。 您的使用者會使用唯一的電子郵件地址/密碼組合進行註冊，以存取您租用戶所註冊的應用程式。

### <a name="create-the-identityexperienceframework-application"></a>建立 IdentityExperienceFramework 應用程式

1. 在 [Azure 入口網站](https://portal.azure.com)中，切換至[您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)。
2. 開啟 [Azure Active Directory] 刀鋒視窗 (不是 [Azure AD B2C] 刀鋒視窗)。 您可能需要選取 [更多服務]，才能找到它。
3. 選取 [應用程式註冊]。
4. 選取 [新增應用程式註冊]。
   * 針對 [名稱] 使用 `IdentityExperienceFramework`。
   * 針對 [應用程式類型] 使用 [Web 應用程式/API]。
   * 針對 [登入 URL] 使用 `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`，其中 `yourtenant` 是您的 Azure AD B2C 租用戶網域名稱。
5. 選取 [ **建立**]。
6. 建立之後，選取新建立的應用程式 **IdentityExperienceFramework**。<br>
   * 選取 [屬性] 。<br>
   * 複製應用程式識別碼並加以儲存，以供稍後使用。

### <a name="create-the-proxyidentityexperienceframework-application"></a>建立 ProxyIdentityExperienceFramework 應用程式

1. 選取 [應用程式註冊]。
1. 選取 [新增應用程式註冊]。
   * 針對 [名稱] 使用 `ProxyIdentityExperienceFramework`。
   * 針對 [應用程式類型] 使用 [原生]。
   * 針對 [重新導向 URI] 使用 `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`，其中 `yourtenant` 是您的 Azure AD B2C 租用戶。
1. 選取 [ **建立**]。
1. 建立之後，選取應用程式 **ProxyIdentityExperienceFramework**。<br>
   * 選取 [屬性] 。 <br>
   * 複製應用程式識別碼並加以儲存，以供稍後使用。
1. 選取 [必要權限]。
1. 選取 [新增] 。
1. 選取 [選取 API]。
1. 搜尋名稱 IdentityExperienceFramework。 在結果中選取 [IdentityExperienceFramework]，然後按一下 [選取]。
1. 選取 [存取 IdentityExperienceFramework] 旁的核取方塊，然後按一下 [選取]。
1. 選取 [完成] 。
1. 選取 [授與權限]，然後選取 [是] 加以確認。

## <a name="download-starter-pack-and-modify-policies"></a>下載入門套件和修改原則

自訂原則是一組需要上傳至 Azure AD B2C 租用戶的 XML 檔案。 我們提供了入門套件以協助您加快進展速度。 下列清單中的每個入門套件，針對要完成所述的案例，均包含所需的最小數目技術設定檔與使用者旅程圖：
 * LocalAccounts。 只能使用本機帳戶。
 * SocialAccounts。 只能使用社交 (或同盟) 帳戶。
 * **SocialAndLocalAccounts**。 我們將在逐步解說中使用此檔案。
 * SocialAndLocalAccountsWithMFA。 此處包含社交、本機及 Multi-Factor Authentication 選項。

每個入門套件均包含：

* 原則的[基底檔案](active-directory-b2c-overview-custom.md#policy-files)。 需要對基底做一些修改。
* 原則的[擴充檔案](active-directory-b2c-overview-custom.md#policy-files)。  大部分的設定變更都在這個檔案中完成。
* [信賴憑證者檔案](active-directory-b2c-overview-custom.md#policy-files)是工作特定檔案，由應用程式呼叫。

>[!NOTE]
>如果 XML 編輯器支援驗證，請根據入門套件根目錄中的 TrustFrameworkPolicy_0.3.0.0.xsd XML 結構描述來驗證檔案。 在上載之前，XML 結構描述驗證會識別錯誤。

 現在就開始吧！

1. 從 GitHub 下載 active-directory-b2c-custom-policy-starterpack。 [下載 .zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)或執行

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. 開啟 SocialAndLocalAccounts 資料夾。  此資料夾中的基底檔案 (TrustFrameworkBase.xml) 包含本機和社交/公司帳戶所需的內容。 社交內容不會干擾本機帳戶的啟動和執行步驟。
3. 開啟 TrustFrameworkBase.xml。 如果您需要 XML 編輯器，請[試用 Visual Studio 程式碼](https://code.visualstudio.com/download)，這是一個輕巧的跨平台編輯器。
4. 在 `TrustFrameworkPolicy` 根元素中，更新 `TenantId` 和 `PublicPolicyUri` 屬性，以您的 Azure AD B2C 租用戶網域名稱取代 `yourtenant.onmicrosoft.com`：
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId` 是您將在入口網站看到的原則名稱，也是其他原則檔案用來參考此原則檔案的名稱。

5. 儲存檔案。
6. 開啟 TrustFrameworkExtensions.xml。 以您的 Azure AD B2C 租用戶取代 `yourtenant.onmicrosoft.com` 來進行這兩項相同的變更。 加上在 `<TenantId>` 元素中進行相同的取代作業，總共進行三項變更。 儲存檔案。
7. 開啟 SignUpOrSignIn.xml。 在三個地方以您的 Azure AD B2C 租用戶取代 `yourtenant.onmicrosoft.com` 來進行相同的變更。 儲存檔案。
8. 開啟密碼重設和設定檔編輯檔案。 在每個檔案中的三個地方，以您的 Azure AD B2C 租用戶取代 `yourtenant.onmicrosoft.com` 來進行相同的變更。 儲存檔案。

### <a name="add-the-application-ids-to-your-custom-policy"></a>將應用程式識別碼新增至您的自訂原則
在擴充檔案 (`TrustFrameworkExtensions.xml`) 中新增應用程式識別碼：

1. 在擴充檔案 (TrustFrameworkExtensions.xml) 中，尋找 `<TechnicalProfile Id="login-NonInteractive">` 元素。
2. 以您稍早建立之身分識別體驗架構應用程式的應用程式識別碼，取代 `IdentityExperienceFrameworkAppId` 的兩個執行個體。 下列是一個範例：

   ```xml
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. 以您稍早建立之 Proxy 識別體驗架構應用程式的應用程式識別碼，取代 `ProxyIdentityExperienceFrameworkAppId` 的兩個執行個體。
4. 儲存擴充檔案。

## <a name="upload-the-policies-to-your-tenant"></a>將原則上傳至您的租用戶

1. 在 [Azure 入口網站](https://portal.azure.com)中，切換至[您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後開啟 [Azure AD B2C] 刀鋒視窗。
2. 選取 [識別體驗架構]。
3. 選取 [上傳原則]。

    >[!WARNING]
    >必須依下列順序上傳自訂原則檔︰

1. 上傳 TrustFrameworkBase.xml。
2. 上傳 TrustFrameworkExtensions.xml。
3. 上傳 SignUpOrSignin.xml。
4. 上傳其他原則檔案。

上傳檔案時，原則檔案的名稱前面會加上 `B2C_1A_`。

## <a name="test-the-custom-policy-by-using-run-now"></a>使用 [立即執行] 測試自訂原則

1. 開啟 [Azure AD B2C 設定]，然後移至 [識別體驗架構]。

   >[!NOTE]
   >[立即執行] 需要在租用戶上至少預先註冊一個應用程式。 您必須在 B2C 租用戶中 (使用 Azure AD B2C 中的 [應用程式] 功能表選取項目，或者使用識別體驗架構) 註冊應用程式，以同時叫用內建和自訂原則。 每個應用程式只需要一個註冊。<br><br>
   若要了解如何註冊應用程式，請參閱 Azure AD B2C [開始使用](active-directory-b2c-get-started.md)一文或[應用程式註冊](active-directory-b2c-app-registration.md)一文。  

2. 開啟 B2C_1A_signup_signin，此為您上傳的信賴憑證者 (RP) 自訂原則。 選取 [立即執行]。

3. 您應該可以使用電子郵件地址註冊。

4. 使用相同的帳戶登入，以確認您的設定正確。

>[!NOTE]
>登入失敗的常見原因是 IdentityExperienceFramework 應用程式設定不正確。


## <a name="next-steps"></a>後續步驟

### <a name="add-facebook-as-an-identity-provider"></a>將 Facebook 新增為識別提供者
若要設定 Facebook：
1. [在 developers.facebook.com 中設定 Facebook 應用程式](active-directory-b2c-setup-fb-app.md)。
2. [在 Azure AD B2C 租用戶中新增 Facebook 應用程式祕密](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies)。
3. 在 TrustFrameworkExtensions 原則檔案中，使用 Facebook 應用程式識別碼取代 `client_id` 的值：

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. 將 TrustFrameworkExtensions.xml 原則檔案上傳至您的租用戶。
5. 使用 [立即執行] 進行測試，或直接從您已註冊的應用程式中叫用原則。

### <a name="add-azure-active-directory-as-an-identity-provider"></a>將 Azure Active Directory 新增為識別提供者
此入門指南中所使用的基底檔案已經包含您新增其他識別提供者時所需的部分內容。 如需設定登入的相關資訊，請參閱 [Azure Active Directory B2C：使用 Azure AD 帳戶登入](active-directory-b2c-setup-aad-custom.md)一文。

如需使用識別體驗架構之 Azure AD B2C 中自訂原則的概觀，請參閱 [Azure Active Directory B2C：自訂原則](active-directory-b2c-overview-custom.md)一文。 

