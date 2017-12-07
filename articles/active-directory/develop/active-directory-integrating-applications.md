---
title: "整合應用程式與 Azure Active Directory"
description: "如何在 Azure Active Directory (Azure AD) 中新增、更新或移除應用程式。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: ae637be5-0b71-4b1e-b1fe-b83df3eb4845
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2017
ms.author: bryanla
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: 8a5eab88e10b330bf4da88c01d24a11e95277439
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="integrating-applications-with-azure-active-directory"></a>整合應用程式與 Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

企業開發人員和軟體即服務 (SaaS) 提供者可以開發可與 Azure Active Directory (Azure AD) 整合的商業雲端服務或企業營運應用程式，以提供安全的登入和授權給其服務。 若要整合應用程式或服務與 Azure AD，開發人員必須先向 Azure AD 註冊應用程式。

本文將說明如何在 Azure AD 中新增、更新或移除應用程式的註冊。 您將了解可與 Azure AD 整合的不同類型應用程式，以及如何設定您的應用程式存取其他資源，例如 web API 等等。

若要深入了解代表已註冊應用程式的兩個 Azure AD 物件與其間的關係，請參閱[應用程式物件和服務主體物件](active-directory-application-objects.md)；若要深入了解利用 Azure Active Directory 開發應用程式時應使用的商標指導方針，請參閱[整合應用程式的商標指導方針](active-directory-branding-guidelines.md)。

## <a name="adding-an-application"></a>新增應用程式
任何想要使用 Azure AD 功能的應用程式都必須先在 Azure AD 租用戶中註冊。 此登錄程序牽涉到提供 Azure AD 應用程式的相關詳細資料，例如其所在的 URL、要在使用者驗證之後傳送回應的 URL，以及會識別應用程式的 URI 等。

### <a name="to-register-a-new-application-using-the-azure-portal"></a>使用 Azure 入口網站註冊新的應用程式
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 如果您的帳戶可讓您存取多個帳戶，請在右上角按一下您的帳戶，並將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
3. 在左側導覽窗格中，依序按一下 [Azure Active Directory] 服務、[應用程式註冊] 和 [新增應用程式註冊]。

   ![註冊新的應用程式](./media/active-directory-integrating-applications/add-app-registration.png)

4. 當 [建立] 頁面出現時，輸入您應用程式的註冊資訊： 

  - **名稱：**輸入有意義的應用程式名稱
  - **應用程式類型：** 
    - 針對在裝置本機上安裝的[用戶端應用程式](active-directory-dev-glossary.md#client-application) 選取 [原生]。 此設定適用於 OAuth 公用[原生用戶端](active-directory-dev-glossary.md#native-client)。
    - 針對在安全伺服器上所安裝的[用戶端應用程式](active-directory-dev-glossary.md#client-application)和[資源/API 應用程式](active-directory-dev-glossary.md#resource-server)選取 [Web 應用程式/API]。 此設定適用於 OAuth 機密 [Web 用戶端](active-directory-dev-glossary.md#web-client)和公用[使用者代理程式型用戶端](active-directory-dev-glossary.md#user-agent-based-client)。 相同的應用程式也可以公開用戶端和資源/API。
  - **登入 URL：**在 [Web 應用程式/API] 應用程式中，提供您應用程式的基底 URL。 例如，`http://localhost:31544` 可能是在您的本機電腦上執行之 Web 應用程式的 URL。 使用者會使用此 URL 來登入 Web 用戶端應用程式。 
  - **重新導向 URI：**在 [原生] 應用程式中，提供 URI 以供 Azure AD 用來傳回權杖回應。 輸入應用程式特定的值，例如 `http://MyFirstAADApp`

   ![註冊新的應用程式 - 建立](./media/active-directory-integrating-applications/add-app-registration-create.png)

   如果您想要 Web 應用程式或原生應用程式的特定範例，請查看我們的[快速入門](active-directory-developers-guide.md#get-started)。

5. 完成後，按一下 [建立]。 Azure AD 會將唯一的應用程式識別碼指派給您的應用程式，然後系統會帶您進入應用程式的主要註冊頁面。 根據您的應用程式是 Web 還是原生應用程式，系統會提供您不同選項以供您在應用程式中新增其他功能。 請參閱下一節，以了解同意概觀以及有關在應用程式註冊 (認證、權限、為其他租用戶的使用者啟用登入) 中啟用其他組態功能的詳細資料。

  > [!NOTE]
  > 根據預設，新註冊的應用程式會設定為**只**允許相同租用戶的使用者登入您的應用程式。
  > 
  > 

## <a name="updating-an-application"></a>更新應用程式
一旦您的應用程式已向 Azure AD 註冊，它可能需要更新，以提供 web API 的存取權、供其他組織使用等等。 本節說明您可以用來進一步設定應用程式的各種方式。 我們先從同意架構的概觀來開始，在建置必須供其他使用者或應用程式使用的應用程式時，務必要了解同意架構。

### <a name="overview-of-the-consent-framework"></a>同意架構的概觀

Azure AD 的同意架構可讓您輕鬆地開發多租用戶的 Web 和原生用戶端應用程式，包括多層式應用程式。 這些應用程式允許與應用程式註冊所在的租用戶不同之 Azure AD 租用戶的使用者帳戶登入。 這些應用程式除了存取自己的 Web API 之外，可能也需要存取 Microsoft Graph API 之類的 Web API (用以存取 Azure Active Directory、Intune 及 Office 365 服務) 以及其他 Microsoft 服務的 API。 此架構以使用者或系統管理員為根據，他們可同意讓應用程式在他們的目錄中註冊，並可能包括存取目錄資料。

例如，如果 Web 用戶端應用程式需要從 Office 365 讀取有關使用者的行事曆資訊，該使用者必須先同意此用戶端應用程式。 取得同意之後，用戶端應用程式可以代表使用者呼叫 Microsoft Graph API，並視需要使用行事曆資訊。 [Microsoft Graph API (英文)](https://graph.microsoft.io) 可供存取 Office 365 中的資料 (例如 Exchange 中的行事曆和訊息、SharePoint 中的網站和清單、OneDrive 中的文件、OneNote 中的筆記本、Planner 中的工作、Excel 中的活頁簿等等)，以及 Azure AD 中的使用者和群組和多項 Microsoft 雲端服務中的其他資料物件。 

同意架構建置在 OAuth 2.0 和各種不同流程上，例如授權碼授與和用戶端認證授與，使用公用或機密的用戶端。 藉由使用 OAuth 2.0，Azure AD 就可以建置許多不同類型的用戶端應用程式，例如在電話、平板電腦、伺服器或 web 應用程式上，並且存取所需的資源。

如需有關搭配 OAuth2.0 授權授與使用同意架構的詳細資訊，請參閱[使用 OAuth 2.0 和 Azure AD 授權存取 Web 應用程式](active-directory-protocols-oauth-code.md)和 [Azure AD 的驗證案例](active-directory-authentication-scenarios.md)。 如需透過 Microsoft Graph 取得 Office 365 的授權存取權的相關資訊，請參閱[使用 Microsoft Graph 進行應用程式驗證](https://graph.microsoft.io/docs/authorization/auth_overview)。

#### <a name="example-of-the-consent-experience"></a>同意體驗的範例

下列步驟會示範如何將同意體驗用於應用程式開發人員和使用者。

1. 假設您擁有一個 Web 用戶端應用程式，它需要要求特定權限以存取資源/API。 您會在下一節了解如何進行這項設定，但基本上會使用 Azure 入口網站在設定階段宣告權限要求。 就和其他組態設定一樣，它們會成為應用程式之 Azure AD 註冊的一部分：
   
  ![其他應用程式的權限](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. 請考量您的應用程式權限是否已更新、是否正在執行應用程式，以及使用者是否即將第一次使用它。 首先，應用程式必須從 Azure AD 的 `/authorize` 端點取得授權碼。 授權碼接著可用來取得新的存取權和重新整理權杖。

3. 如果使用者尚未經過驗證，Azure AD 的 `/authorize` 端點會提示其進行登入。
   
  ![使用者或系統管理員登入 Azure AD](./media/active-directory-integrating-applications/usersignin.png)

4. 使用者登入之後，Azure AD 會判斷是否需要向使用者顯示同意頁面。 此判斷根據使用者 (或其組織的系統管理員) 是否已經同意應用程式。 如果尚未授與同意，Azure AD 會提示使用者取得同意，並顯示其運作所需的必要權限。 同意對話方塊中顯示的權限集，會和在 Azure 入口網站 [委派的權限] 中選取的權限相符。
   
  ![使用者同意體驗](./media/active-directory-integrating-applications/consent.png)

5. 使用者同意後，授權碼會傳回您的應用程式，藉以兌換取得存取權杖和重新整理權杖。 如需此流程的詳細資訊，請參閱 [Azure AD 的驗證案例中的 Web 應用程式到 Web API 一節](active-directory-authentication-scenarios.md#web-application-to-web-api)。

6. 身為系統管理員，您也可以代表租用戶中的所有使用者，同意應用程式的委派權限。 系統管理員同意可避免對租用戶的每個使用者都顯示同意對話方塊，並且會在 [Azure 入口網站](https://portal.azure.com)的應用程式頁面中完成。 在應用程式的 [設定] 頁面中，按一下 [必要的權限]，然後按一下 [授與權限] 按鈕。 

  ![授與明確的系統管理員同意權限](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > 使用 ADAL.js 的單一頁面應用程式 (SPA) 目前必須使用 [授與權限] 按鈕來授與明確的同意。 否則，應用程式會在要求存取權杖時失敗。   

### <a name="configure-a-client-application-to-access-web-apis"></a>設定用戶端應用程式以存取 Web API
為了讓 Web/機密用戶端應用程式能夠參與需要驗證的授權授與流程 (及取得存取權杖)，用戶端必須建立安全的憑證。 Azure 入口網站支援的預設驗證方法為用戶端識別碼 + 祕密金鑰。 本節會討論為用戶端的認證提供秘密金鑰所需的設定步驟。

此外，同意架構要先確保用戶端取得必要的權限授與 (根據所要求的權限)，用戶端才可以存取資源應用程式所公開的 Web API (例如 Microsoft Graph API)。 根據預設，所有應用程式都可以從「Windows Azure Active Directory」(圖形 API) 和「Windows Azure 服務管理 API」選擇權限。 依預設也會選取[圖形 API「登入及讀取使用者設定檔」權限](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails)。 如果您的用戶端將會註冊在擁有已訂閱 Office 365 之帳戶的租用戶中，則也可選取 SharePoint 與 Exchange Online 的 Web API 和權限。 針對每一個所要的 Web API，您可以從[兩種權限類型](active-directory-dev-glossary.md#permissions)中做選擇：

- 應用程式權限：您的用戶端應用程式本身需要直接存取 Web API (沒有使用者內容)。 這種類型的權限需要系統管理員的同意，而且無法供原生用戶端應用程式使用。

- 委派權限：您的用戶端應用程式需要存取 Web API 做為已登入的使用者，但其存取權受到選取權限的限制。 這種類型的權限可由使用者授與，除非權限需要系統管理員的同意。 

  > [!NOTE]
  > 新增對應用程式的委派權限不會自動將同意授與給租用戶內的使用者，和過去使用 Azure 傳統入口網站一樣。 使用者仍必須在執行階段手動同意新增委派的權限，除非系統管理員按一下 Azure 入口網站中應用程式頁面上 [必要的權限] 區段中的 [授與權限] 按鈕。 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>新增用來存取 Web API 的應用程式認證或權限
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 如果您的帳戶可讓您存取多個帳戶，請在右上角按一下您的帳戶，並將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
3. 在左側導覽窗格中，依序按一下 [Azure Active Directory] 服務和 [應用程式註冊]，然後尋找/按一下您要設定的應用程式。

   ![更新應用程式的註冊](./media/active-directory-integrating-applications/update-app-registration.png)

4. 系統會將您帶往應用程式的主要註冊頁面，以開啟應用程式的 [設定] 頁面。 若要為 Web 應用程式的認證新增秘密金鑰：
  - 按一下 [設定] 頁面上的 [金鑰] 區段。  
  - 為金鑰新增描述。
  - 選取一年或兩年的持續時間。
  - 按一下 [儲存] 。 在您儲存組態變更之後，最右邊的資料行會包含金鑰值。 **請務必複製此金鑰**，以供在用戶端應用程式的程式碼中使用，因為您一旦離開此頁面就無法再存取此金鑰。

  ![更新應用程式的註冊 - 金鑰](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. 若要新增權限以從用戶端存取資源 API
  - 按一下 [設定] 頁面上的 [所需的權限] 區段。 
  - 按一下 [新增]  按鈕。
  - 按一下 [選取 API] 以選取您想要挑選的資源類型。
  - 瀏覽可用的 API 清單，或使用搜尋方塊在公開 Web API 的目錄中從可用的資源應用程式中選取。 按一下您感興趣的資源，然後按一下 [選取]。
  - 系統會將您帶往 [啟用存取] 頁面。 選取您的應用程式在存取 API 時需要的應用程式權限及/或委派的權限。
   
  ![更新應用程式的註冊 - 權限 API](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![更新應用程式的註冊 - 權限 perms](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. 完成後，按一下 [啟用存取] 頁面上的 [選取] 按鈕，然後按一下 [新增 API 存取] 頁面上的 [完成] 按鈕。 您會回到 [所需的權限] 頁面，其中新的資源已新增到 API 清單。

  > [!NOTE]
  > 按一下 [完成] 按鈕，也可以根據您設定的其他應用程式權限，在您的目錄中自動設定您的影用程式權限。  您可以查看應用程式的 [設定] 頁面來檢視這些應用程式權限。
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>設定資源應用程式以公開 Web API

您可以開發 Web API，並藉由公開存取[範圍](active-directory-dev-glossary.md#scopes)和[角色](active-directory-dev-glossary.md#roles)，使其可供用戶端應用程式使用。 正確設定的 web API 即可供使用，就像其他 Microsoft web API 一樣，包括 Graph API 和 Office 365 API。 存取範圍和角色會透過[應用程式的資訊清單](active-directory-dev-glossary.md#application-manifest) (此為代表應用程式身分識別組態的 JSON 檔案) 公開。 

下一節會示範如何藉由修改資源應用程式資訊清單來公開存取範圍。

#### <a name="adding-access-scopes-to-your-resource-application"></a>新增資源應用程式的存取範圍

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 如果您的帳戶可讓您存取多個帳戶，請在右上角按一下您的帳戶，並將您的入口網站工作階段設定為想要的 Azure AD 租用戶。

3. 在左側導覽窗格中，依序按一下 [Azure Active Directory] 服務和 [應用程式註冊]，然後尋找/按一下您要設定的應用程式。

   ![更新應用程式的註冊](./media/active-directory-integrating-applications/update-app-registration.png)

4. 系統會將您帶往應用程式的主要註冊頁面，以開啟應用程式的 [設定] 頁面。 從應用程式的 [註冊] 頁面按一下 [資訊清單]，以切換至 [編輯資訊清單] 頁面。 Web 式的資訊清單編輯器隨即開啟，以供您在入口網站內**編輯**資訊清單。 或者，您也可以按一下 [下載] 並在本機編輯，然後使用 [上傳] 以將其重新套用到您的應用程式。

5. 在此範例中，我們會藉由將下列 JSON 元素新增至 `oauth2Permissions` 集合，從而在資源/API 上公開稱為 `Employees.Read.All` 的新範圍。 在註冊期間，預設會提供現有的 `user_impersonation` 範圍。 `user_impersonation` 可讓用戶端應用程式依據登入使用者的身分識別要求資源存取權限。 請務必在現有 `user_impersonation` 範圍元素後方加上逗號，並將屬性值變更為符合您的資源需求。 

  ```json
  {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
  }
  ```
  > [!NOTE]
  > 「識別碼」的值必須使用 GUID 產生工具 (例如 [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx)) 或以程式設計方式產生。 它代表由 Web API 所公開之範圍的唯一識別碼。 一旦為用戶端設定了適當的 Web API 存取權限，Azure AD 便會對用戶端發出 OAuth2.0 存取權杖。 用戶端在呼叫 Web API 時會出示存取權杖，此權杖的範圍 (scp) 宣告已設定為其應用程式註冊中所要求的權限。
  >
  > 稍後您可以視需要公開其他範圍。 請考慮您的 Web API 可能會公開多個與各種不同功能相關聯的範圍。 在執行階段，您的資源可藉由評估所收到之 OAuth 2.0 存取權杖中的範圍 (`scp`) 宣告，來控制 Web API 的存取。
  > 

6. 完成時，按一下 [儲存]。 您的 Web API 現在已設定為可供目錄中的其他應用程式使用。  

  ![更新應用程式的註冊](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>確認已向租用戶中的其他應用程式公開 Web API
1. 返回您的 Azure AD 租用戶，再次按一下 [應用程式註冊]，然後尋找/按一下您想要設定的用戶端應用程式。

   ![更新應用程式的註冊](./media/active-directory-integrating-applications/update-app-registration.png)

2. 重複您在[設定用戶端應用程式以存取 Web API](#configure-a-client-application-to-access-web-apis) 所進行過的步驟 5。 當您進入 [選取 API] 步驟，請在 [搜尋] 欄位中輸入資源的應用程式名稱來搜尋資源，然後按一下 [選取]。 

3. 在 [啟用存取] 頁面上，您應該會看到可供用戶端權限要求使用的新範圍。

  ![新的權限顯示出來](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>應用程式資訊清單的詳細資料

應用程式資訊清單實際上可做為更新應用程式實體的一種機制，其可定義 Azure AD 應用程式身分識別組態的所有屬性，包括我們所討論的 API 存取範圍。 如需應用程式實體和其結構描述的詳細資訊，請參閱[圖形 API 應用程式實體文件](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)。 該文章包含可用來指定您 API 權限之應用程式實體成員的完整參考資訊，包括：  

- appRoles 成員，此成員為 [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) 實體的集合，可用來定義 Web API 的[應用程式權限](active-directory-dev-glossary.md#permissions)。 
- oauth2Permissions 成員，此成員為 [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) 實體的集合，可用來定義 Web API 的[委派的權限](active-directory-dev-glossary.md#permissions)。

如需應用程式資訊清單一般概念的詳細資訊，請參閱[了解 Azure Active Directory 應用程式資訊清單](active-directory-application-manifest.md)。

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>透過 Microsoft Graph API 存取 Azure AD Graph 和 Office 365 API  

如前文所述，除了在自己的應用程式上公開/存取 API，您也可以註冊用戶端應用程式以存取 Microsoft 資源所公開的 API。 Microsoft Graph API (在入口網站的資源/API 清單中稱為「Microsoft Graph」) 適用於所有已向 Azure AD 註冊的應用程式。 如果您在包含已註冊 Office 365 訂用帳戶之帳戶的租用戶中註冊用戶端應用程式，您也可以存取各種 Office 365 資源所公開的範圍。

如需 Microsoft Graph API 所公開之範圍的完整討論，請參閱[權限範圍 | Microsoft Graph API 概念](https://graph.microsoft.io/docs/authorization/permission_scopes)一文。

> [!NOTE]
> 由於目前的限制，如果原生用戶端應用程式使用「存取組織的目錄」權限，它們只能呼叫 Azure AD 圖形 API。 這項限制不適用於 web 應用程式。
> 
> 

### <a name="configuring-multi-tenant-applications"></a>設定多租用戶應用程式

在 Azure AD 註冊應用程式時，您可以只讓組織中的使用者存取您的應用程式。 或者，您也可能想讓外部組織的使用者可以存取您的應用程式。 這兩個應用程式類型稱為單一租用戶和多租用戶應用程式。 本節會討論如何修改單一租用戶應用程式的組態，使其成為多組用戶應用程式。

請務必注意單一租用戶與多租用戶應用程式之間的差異：  

- 單一租用戶應用程式適合在一個組織中使用。 它通常是由企業開發人員撰寫的企業營運 (LoB) 應用程式。 單一租用戶應用程式只能由所擁有之帳戶位於和應用程式註冊相同之租用戶中的使用者來存取。 因此，它只需要佈建到一個目錄中。
- 多租用戶應用程式適合在許多組織中使用。 這種應用程式稱為軟體即服務 (SaaS) Web 應用程式，通常是由獨立軟體廠商 (ISV) 所撰寫出來的。 多租用戶應用程式必須佈建在使用者需要存取的每個租用戶中。 至於應用程式註冊所在之租用戶以外的租用戶，則需要使用者或系統管理員同意才能註冊這些租用戶。 請注意，原生用戶端應用程式預設有多個租用戶，因為其安裝於資源擁有者的裝置上。 如需同意架構的詳細資訊，請參閱前面的[同意架構的概觀](#overview-of-the-consent-framework)一節。

讓應用程式具有多租用戶性質需要同時變更應用程式註冊以及 Web 應用程式本身。 下列幾節會說明這兩項。

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>變更應用程式註冊以支援多租用戶

如果您正在撰寫想要提供給組織外的客戶或合作夥伴使用的應用程式，您必須在 Azure 入口網站中更新應用程式定義。

> [!IMPORTANT]
> Azure AD 會要求多租用戶應用程式的應用程式識別碼 URI 必須是全域唯一的。 「應用程式識別碼 URI」是其中一種可在通訊協定訊息中識別應用程式的方式。 在單一租用戶應用程式中，只要該租用戶內有唯一的應用程式識別碼 URI 就已足夠。 就多租用戶應用程式而言，該 URI 則必須具全域唯一性，Azure AD 才能在所有租用戶中找到該應用程式。 系統會透過要求「應用程式識別碼 URI」必須具有與已驗證的 Azure AD 租用戶網域相符的主機名稱，來強制執行全域唯一性。 例如，如果租用戶的名稱是 contoso.onmicrosoft.com，則有效的「應用程式識別碼 URI」會是 https://contoso.onmicrosoft.com/myapp。 如果您的租用戶有驗證過的網域 contoso.com，則有效的應用程式識別碼 URI 也會是 https://contoso.com/myapp。 如果「應用程式識別碼 URI」沒有按照這個模式，將應用程式設定成多租用戶時就會失敗。
> 

若要讓外部使用者能夠存取您的應用程式： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 如果您的帳戶可讓您存取多個帳戶，請在右上角按一下您的帳戶，並將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
3. 在左側導覽窗格中，依序按一下 [Azure Active Directory] 服務和 [應用程式註冊]，然後尋找/按一下您要設定的應用程式。 系統會將您帶往應用程式的主要註冊頁面，以開啟應用程式的 [設定] 頁面。
4. 在 [設定] 頁面中，按一下 [屬性]，並將 [多租用戶] 變更為 [是]。

進行變更之後，其他組織中的使用者和系統管理員就能夠授與其使用者登入您應用程式的能力，讓您的應用程式能夠存取這些組織的租用戶所保護的資源。

#### <a name="changing-the-application-to-support-multi-tenant"></a>變更應用程式以支援多租用戶

多租用戶應用程式支援極度仰賴 Azure AD 同意架構。 同意機制可讓其他租用戶的使用者，對應用程式授與使用者的租用戶所保護之資源的存取權。 這項體驗稱為「使用者同意」。

您的 Web 應用程式可能還會提供：

- 讓系統管理員「註冊我的公司」的能力。 此體驗稱為「系統管理員同意」，可讓系統管理員有能力代替其組織中的*所有使用者*授與同意。 只有使用屬於全域管理員角色的帳戶進行驗證的使用者可以提供系統管理員同意；其他人會收到錯誤。

- 使用者的註冊體驗。 它應該會對使用者提供 [註冊] 按鈕，以將瀏覽器重新導向至 Azure AD OAuth2.0 `/authorize` 端點或 OpenID Connect `/userinfo` 端點。 這些端點可讓應用程式藉由檢查 id_token 取得新使用者的相關資訊。 在註冊階段後，使用者將會看到類似[同意架構的概觀](#overview-of-the-consent-framework)一節所示的同意提示。

如需有關要支援多租用戶存取和登入/註冊體驗所需進行之應用程式變更的詳細資訊，請參閱：

- [如何使用多租用戶應用程式模式登入任何 Azure Active Directory (AD) 使用者](active-directory-devhowto-multi-tenant-overview.md)
- [多租用戶程式碼範例](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant)的清單。 
- [快速入門：在 Azure AD 中將公司商標新增至登入頁面](../customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>啟用單一頁面應用程式的 OAuth 2.0 隱含授權

單一頁面應用程式 (SPA) 通常會利用執行於瀏覽器中的 JavaScript-heavy 前端進行結構化，此前端會呼叫應用程式的 web API 後端以執行其商務邏輯。 針對裝載於 Azure AD 中的 SPA，您會使用 OAuth 2.0 隱含授權驗證具備 Azure AD 的使用者，並取得您可以使用的權杖以保護從應用程式的 JavaScript 用戶端到其後端 web API 的呼叫。 

使用者授與同意權之後，這個相同的驗證通訊協定可用來取得權杖以保護用戶端和其他為應用程式設定之 web API 資源之間的呼叫。 若要深入了解隱含授權授與，並協助您決定其是否適合您的應用程式案例，請參閱 [了解 Azure Active Directory 中的 OAuth2 隱含授與流程](active-directory-dev-understanding-oauth2-implicit-grant.md)。

根據預設，應用程式的 OAuth 2.0 隱含授權會停用。 您可以在應用程式的[應用程式資訊清單](active-directory-application-manifest.md)中設定 `oauth2AllowImplicitFlow` 值，為應用程式啟用 OAuth 2.0 隱含授與。

#### <a name="to-enable-oauth-20-implicit-grant"></a>啟用 OAuth 2.0 隱含授權

> [!NOTE]
> 如需如何編輯應用程式資訊清單的詳細資訊，請務必先檢閱上一節[設定資源應用程式以公開 Web API](#configuring-a-resource-application-to-expose-web-apis)。
>

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 如果您的帳戶可讓您存取多個帳戶，請在右上角按一下您的帳戶，並將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
3. 在左側導覽窗格中，依序按一下 [Azure Active Directory] 服務和 [應用程式註冊]，然後尋找/按一下您要設定的應用程式。 系統會將您帶往應用程式的主要註冊頁面，以開啟應用程式的 [設定] 頁面。
4. 從應用程式的 [註冊] 頁面按一下 [資訊清單]，以切換至 [編輯資訊清單] 頁面。 Web 式的資訊清單編輯器隨即開啟，以供您在入口網站內**編輯**資訊清單。 找到 "oauth2AllowImplicitFlow" 並將其值設為 "true"。 預設會設定為 "false"。
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. 儲存更新後的資訊清單。 儲存之後，您的 Web API 現在已設定為使用 OAuth 2.0 隱含授權來驗證使用者。

## <a name="removing-an-application"></a>移除應用程式
本節說明如何從 Azure AD 租用戶移除應用程式的註冊。

### <a name="removing-an-application-authored-by-your-organization"></a>移除您的組織所編寫的應用程式
您的組織已註冊的應用程式會顯示在租用戶主要 [應用程式註冊] 頁面的 [我的應用程式] 篩選器底下。 這些應用程式是您透過 Azure 入口網站以手動方式註冊的應用程式，或透過 PowerShell 或圖形 API 以程式設計方式註冊的應用程式。 更具體來說，它們會由您租用戶中的應用程式與服務主體物件所表示。 如需詳細資訊，請參閱 [應用程式物件和服務主體物件](active-directory-application-objects.md)。

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>從您的目錄移除單一租用戶應用程式
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 如果您的帳戶可讓您存取多個帳戶，請在右上角按一下您的帳戶，並將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
3. 在左側導覽窗格中，依序按一下 [Azure Active Directory] 服務和 [應用程式註冊]，然後尋找/按一下您要設定的應用程式。 系統會將您帶往應用程式的主要註冊頁面，以開啟應用程式的 [設定] 頁面。
4. 在應用程式的主要註冊頁面上，按一下 [刪除]。
5. 在確認訊息處按一下 [是]  。

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>從主目錄移除多租用戶應用程式
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 如果您的帳戶可讓您存取多個帳戶，請在右上角按一下您的帳戶，並將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
3. 在左側導覽窗格中，依序按一下 [Azure Active Directory] 服務和 [應用程式註冊]，然後尋找/按一下您要設定的應用程式。 系統會將您帶往應用程式的主要註冊頁面，以開啟應用程式的 [設定] 頁面。
4. 在 [設定] 頁面上選擇 [屬性]，然後將 [多租用戶] 開關變更為 [否]，以便先將您的應用程式變更為單一租用戶，然後按一下 [儲存]。 應用程式的服務主體物件則仍留在已同意應用程式之所有組織的租用戶中。
5. 按一下應用程式主要註冊頁面中的 [刪除] 按鈕。
6. 在確認訊息處按一下 [是]  。

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>移除其他組織授權的多租用戶應用程式
在租用戶的主要 [應用程式註冊] 頁面上的 [所有應用程式] 篩選器 (不包括 [我的應用程式] 註冊) 底下顯示的應用程式子集，都是多租用戶應用程式。 就技術上來說，這些多租用戶應用程式是來自其他租用戶，且已在同意程序期間註冊到您的租用戶中。 更具體來說，它們僅由您租用戶中的服務主體物件表示，而沒有對應的應用程式物件。 如需有關應用程式物件與服務主體物件之差異的詳細資訊，請參閱 [Azure AD 中的應用程式和服務主體物件](active-directory-application-objects.md)。

為了移除多租用戶應用程式對您的目錄的存取權 (在授與同意之後)，公司系統管理員必須移除其服務主體。 系統管理員必須擁有全域系統管理員存取權，並且可透過 Azure 入口網站或 [Azure AD PowerShell Cmdlet](http://go.microsoft.com/fwlink/?LinkId=294151) 來移除存取權。

## <a name="next-steps"></a>後續步驟
- 如需如何在 Azure AD 中進行驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](active-directory-authentication-scenarios.md)。
- 如需應用程式視覺化導引的祕訣，請參閱[整合應用程式的商標指導方針](active-directory-branding-guidelines.md)。
- 如需有關應用程式之「應用程式」和「服務主體」物件之間關係的詳細資訊，請參閱[應用程式物件和服務主體物件](active-directory-application-objects.md)。
- 若要深入了解應用程式資訊清單所扮演的角色，請參閱[了解 Azure Active Directory 應用程式資訊清單](active-directory-application-manifest.md)
- 如需核心 Azure Active Directory (AD) 開發人員概念的一些定義，請參閱 [Azure AD 開發人員詞彙](active-directory-dev-glossary.md)。
- 如需所有開發人員相關內容的概觀，請瀏覽 [Active Directory 開發人員指南](active-directory-developers-guide.md)。

