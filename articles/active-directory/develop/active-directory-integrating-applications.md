---
title: "整合應用程式與 Azure Active Directory | Microsoft Docs"
description: "如何在 Azure Active Directory (Azure AD) 中新增、更新或移除應用程式的詳細資料。"
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: mbaldwin
ms.assetid: ae637be5-0b71-4b1e-b1fe-b83df3eb4845
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: lenalepa
ms.custom: aaddev
ms.reviewer: luleon
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3be341bcb897a1481f145825429a1a94dfaae3b0
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="integrating-applications-with-azure-active-directory"></a>整合應用程式與 Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

企業開發人員和軟體即服務 (SaaS) 提供者可以開發可與 Azure Active Directory (Azure AD) 整合的商業雲端服務或企業營運應用程式，以提供安全的登入和授權給其服務。 若要整合應用程式或服務與 Azure AD，開發人員必須先透過 Azure 傳統入口網站，向 Azure AD 註冊他們的應用程式相關詳細資料。

本文將說明如何在 Azure AD 中新增、更新或移除應用程式。 您將了解可與 Azure AD 整合的不同類型應用程式，以及如何設定您的應用程式存取其他資源，例如 web API 等等。

若要深入了解代表已註冊應用程式的兩個 Azure AD 物件與其間的關係，請參閱[應用程式物件和服務主體物件](active-directory-application-objects.md)；若要深入了解利用 Azure Active Directory 開發應用程式時應使用的商標指導方針，請參閱[整合應用程式的商標指導方針](active-directory-branding-guidelines.md)。

## <a name="adding-an-application"></a>新增應用程式
任何想要使用 Azure AD 功能的應用程式都必須先在 Azure AD 租用戶中註冊。 此登錄程序牽涉到提供 Azure AD 應用程式的相關詳細資料，例如其所在的 URL、要在使用者驗證之後傳送回應的 URL，以及會識別應用程式的 URI 等。

如果您正在建置的 Web 應用程式只需要支援在 Azure AD 中登入使用者，您只需依照下列指示進行。 若您的應用程式需要認證或權限才能存取 Web API，或必須允許其他 Azure AD 租用戶的使用者進行存取，請參閱[更新應用程式](#updating-an-application)一節，以繼續設定您的應用程式。

### <a name="to-register-a-new-application-in-the-azure-portal"></a>在 Azure 入口網站註冊新的應用程式
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頁面右上角選取您的帳戶，以選擇您的 Azure AD 租用戶。
3. 在左側導覽窗格中，選擇 [更多服務]，按一下 [應用程式註冊]，然後按一下 [新增]。
4. 遵照提示進行，並建立新的應用程式。 如果您想要 Web 應用程式或原生應用程式的特定範例，請查看我們的[快速入門](active-directory-developers-guide.md)。
  * 若為 Web 應用程式，請提供屬於應用程式基礎 URL 的**登入 URL**以供使用者登入，例如 `http://localhost:12345`。
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * 若為原生應用程式，請提供 [重新導向 URI]，以供 Azure AD 用來傳回權杖回應。 輸入應用程式特定的值，例如 `http://MyFirstAADApp`
5. 完成註冊後，Azure AD 會為您的應用程式指派一個唯一用戶端識別碼 (應用程式識別碼)。 已新增您的應用程式，而且您將會進入應用程式的 [快速啟動] 頁面。 根據您的應用程式為 web 或原生應用程式，您會看到如何將其他功能加入至應用程式中的不同選項。 一旦新增您的應用程式之後，您就可以開始更新您的應用程式，讓使用者登入、在其他應用程式中存取 web API 或設定多租用戶應用程式 (可讓其他組織存取您的應用程式)。

> [!NOTE]
> 根據預設，新建立的應用程式註冊會設定為允許來自您目錄的使用者登入您的應用程式。
> 
> 

## <a name="updating-an-application"></a>更新應用程式
一旦您的應用程式已向 Azure AD 註冊，它可能需要更新，以提供 web API 的存取權、供其他組織使用等等。 本節說明您可能需要用來進一步設定其他應用程式的各種方式。 一開始我們將先概述同意架構，如果您正在建置資源/API 應用程式，而其將由貴組織或其他組織中的開發人員所建置的用戶端應用程式來使用，那麼請務必了解此架構。

如需如何在 Azure AD 中進行驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](active-directory-authentication-scenarios.md)。

### <a name="overview-of-the-consent-framework"></a>同意架構的概觀
Azure AD 的同意架構可讓您輕鬆地開發需要存取由 Azure AD 租用戶保護的多租用戶 Web 和原生用戶端應用程式 (不同於註冊用戶端應用程式的架構)。 除了自己的 Web API 之外，這些 Web API 包括 Microsoft Graph API (用以存取 Azure Active Directory、Intune 及 Office 365 中的服務) 和其他 Microsoft 服務 API。 此架構以使用者或系統管理員為根據，他們可同意讓應用程式在他們的目錄中註冊，並可能包括存取目錄資料。

例如，如果 Web 用戶端應用程式需要從 Office 365 讀取有關使用者的行事曆資訊，該使用者必須同意此用戶端應用程式。 取得同意之後，用戶端應用程式可以代表使用者呼叫 Microsoft Graph API，並視需要使用行事曆資訊。 [Microsoft Graph API (英文)](https://graph.microsoft.io) 可供存取 Office 365 中的資料 (例如 Exchange 中的行事曆和訊息、SharePoint 中的網站和清單、OneDrive 中的文件、OneNote 中的筆記本、Planner 中的工作、Excel 中的活頁簿等等)，以及 Azure AD 中的使用者和群組和多項 Microsoft 雲端服務中的其他資料物件。 

同意架構建置在 OAuth 2.0 和各種不同流程上，例如授權碼授與和用戶端認證授與，使用公用或機密的用戶端。 藉由使用 OAuth 2.0，Azure AD 就可以建置許多不同類型的用戶端應用程式，例如在電話、平板電腦、伺服器或 web 應用程式上，並且存取所需的資源。

如需同意架構的詳細資訊，請參閱 [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)、[Azure AD 的驗證案例](active-directory-authentication-scenarios.md)，而如需透過 Microsoft Graph 取得 Office 365 授權存取權的資訊，請參閱 [App authentication with Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview) (使用 Microsoft Graph 進行應用程式驗證)。

#### <a name="example-of-the-consent-experience"></a>同意體驗的範例
下列步驟將示範如何將同意體驗用於應用程式開發人員和使用者。

1. 在 Azure 入口網站的 Web 用戶端應用程式的組態頁面上，使用 [所需的權限] 區段中的功能表，設定您的應用程式所需的權限。
   
    ![其他應用程式的權限](./media/active-directory-integrating-applications/requiredpermissions.png)
2. 請考量您的應用程式權限是否已更新、是否正在執行應用程式，以及使用者是否即將第一次使用它。 如果應用程式尚未取得存取或重新整理權杖，應用程式必須移至 Azure AD 的授權端點以取得授權碼，用來取得新的存取和重新整理權杖。
3. 如果使用者尚未通過驗證，系統會要求他們登入 Azure AD。
   
    ![使用者或系統管理員登入 Azure AD](./media/active-directory-integrating-applications/usersignin.png)
4. 使用者登入之後，Azure AD 會判斷是否需要向使用者顯示同意頁面。 此判斷根據使用者 (或其組織的系統管理員) 是否已經同意應用程式。 如果尚未授與同意，Azure AD 會提示使用者取得同意，並顯示其運作所需的必要權限。 同意對話方塊中顯示的權限集，和在 Azure 入口網站 [委派的權限] 中選取的權限相同。
   
    ![使用者同意體驗](./media/active-directory-integrating-applications/consent.png)
5. 使用者同意後，授權碼會傳回您的應用程式，藉以兌換取得存取權杖和重新整理權杖。 如需此流程的詳細資訊，請參閱 [Azure AD 的驗證案例](active-directory-authentication-scenarios.md)中的 [Web 應用程式到 Web API 區段](active-directory-authentication-scenarios.md#web-application-to-web-api)一節。

6. 身為系統管理員，您也可以代表租用戶中的所有使用者，同意應用程式的委派權限。 這麼做的話，租用戶中的每個使用者便不會看到同意對話方塊。 您可以在 [Azure 入口網站](https://portal.azure.com) 中從您的應用程式頁面執行此作業。 在應用程式的 [設定] 刀鋒視窗中，按一下 [必要的權限]，然後按一下 [授與權限] 按鈕。 

    ![授與明確的系統管理員同意權限](./media/active-directory-integrating-applications/grantpermissions.png)
    
> [!NOTE]
> 目前，使用 ADAL.js 的單一頁面應用程式 (SPA) 需要使用 [授與權限] 按鈕來授與明確同意，因為系統會在沒有同意提示的情況下要求存取權杖，如果尚未獲得同意授與，要求將失敗。   

### <a name="configuring-a-client-application-to-access-web-apis"></a>設定用戶端應用程式以存取 Web API
為了讓 Web/機密用戶端應用程式能夠參與需要驗證的授權授與流程 (及取得存取權杖)，用戶端必須建立安全的憑證。 Azure 入口網站支援的預設驗證方法為用戶端識別碼 + 對稱金鑰。 本節將討論為用戶端的認證提供秘密金鑰所需的設定步驟。

此外，同意架構要先確保用戶端取得必要的權限授與 (根據所要求的權限)，用戶端才可以存取資源應用程式所公開的 Web API (即 Microsoft Graph API)。 根據預設，所有應用程式都可以利用已根據預設選取的 Azure AD 「啟用登入並讀取使用者的設定檔」權限，從 Azure Active Directory (圖形 API) 和 Azure 服務管理 API 選擇權限。 如果您的用戶端應用程式已在 Office 365 Azure AD 租用戶中註冊，則也可選取 SharePoint 與 Exchange Online 的 Web API 和權限。 您可以從所需 Web API 旁之下拉式功能表中的[兩種權限類型](active-directory-dev-glossary.md#permissions)選取：

* 應用程式權限：您的用戶端應用程式本身需要直接存取 Web API (沒有使用者內容)。 這種類型的權限需要系統管理員的同意，而且無法供原生用戶端應用程式使用。
* 委派權限：您的用戶端應用程式需要存取 Web API 做為已登入的使用者，但其存取權受到選取權限的限制。 這種類型的權限可由使用者授與，除非權限設定為需要系統管理員的同意。 

> [!NOTE]
> 新增對應用程式的委派權限不會自動將同意授與給租用戶內的使用者，和過去使用 Azure 傳統入口網站一樣。 使用者仍必須在執行階段手動同意新增委派的權限，除非系統管理員按一下 Azure 入口網站中應用程式頁面上 [必要的權限] 區段中的 [授與權限] 按鈕。 

#### <a name="to-add-credentials-or-permissions-to-access-web-apis"></a>新增用來存取 Web API 的認證或權限
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頁面右上角選取您的帳戶，以選擇您的 Azure AD 租用戶。
3. 在頂端功能表上，選擇 [Azure Active Directory]，按一下 [應用程式註冊]，然後按一下您想要設定的應用程式。 這會帶您前往應用程式的 [開始使用] 頁面，同時開啟應用程式的 [設定] 刀鋒視窗。
4. 若要為 Web 應用程式的認證新增秘密金鑰，按一下 [設定] 刀鋒視窗中的 [金鑰] 區段。  
   
   * 新增金鑰的描述，然後選取 1 或 2 年的持續時間。 
   * 在您儲存組態變更之後，最右邊的資料行會包含金鑰值。 請務必回到這個區段並且在按 [儲存] 後複製金鑰，所以您會在執行階段的驗證期間內將它用於用戶端應用程式。
5. 若要新增從用戶端存取資源 API 的權限，按一下 [設定] 刀鋒視窗中的 [必要的權限] 區段。 
   
   * 首先，按一下 [新增] 按鈕。
   * 按一下 [選取 API] 以選取您想要挑選的資源類型。
   * 瀏覽可用的 API 清單，或使用搜尋方塊在公開 Web API 的目錄中從可用的資源應用程式中選取。 按一下您感興趣的資源，然後按一下 [選取]。
   * 選取後，您可以移至 [選取權限] 功能表，從中為您的應用程式選取 [應用程式權限] 和 [委派的權限] 。
   
6. 完成後，按一下 [完成] 按鈕。

> [!NOTE]
> 按一下 [完成] 按鈕，也可以根據您設定的其他應用程式權限，在您的目錄中自動設定您的影用程式權限。  您可以查看應用程式的 [設定] 刀鋒視窗來檢視這些應用程式權限。
> 
> 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>設定資源應用程式以公開 Web API
您可以開發 Web API，並藉由公開存取[範圍](active-directory-dev-glossary.md#scopes)和[角色](active-directory-dev-glossary.md#roles)，使其可供用戶端應用程式使用。 正確設定的 web API 即可供使用，就像其他 Microsoft web API 一樣，包括 Graph API 和 Office 365 API。 存取範圍和角色會透過[應用程式的資訊清單](active-directory-dev-glossary.md#application-manifest) (此為代表應用程式身分識別組態的 JSON 檔案) 公開。  

下一節將示範如何藉由修改資源應用程式資訊清單來公開存取範圍。

#### <a name="adding-access-scopes-to-your-resource-application"></a>新增資源應用程式的存取範圍
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頁面右上角選取您的帳戶，以選擇您的 Azure AD 租用戶。
3. 在頂端功能表上，選擇 [Azure Active Directory]，按一下 [應用程式註冊]，然後按一下您想要設定的應用程式。 這會帶您前往應用程式的 [開始使用] 頁面，同時開啟應用程式的 [設定] 刀鋒視窗。
4. 按一下應用程式頁面中的 [資訊清單]，以開啟內嵌資訊清單編輯器。 
5. 以下列 JSON 程式碼片段取代 "oauth2Permissions" 節點。 此程式碼片段是如何公開範圍 (稱為「使用者模擬」) 的範例，讓資源擁有者可以為用戶端應用程式提供資源的委派存取類型。 確定您變更了自己的應用程式文字及值：
   
        "oauth2Permissions": [
        {
            "adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in     user",
            "adminConsentDisplayName": "Have full access to the Todo List service",
            "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
            "isEnabled": true,
            "type": "User",
            "userConsentDescription": "Allow the application full access to the todo service on your behalf",
            "userConsentDisplayName": "Have full access to the todo service",
            "value": "user_impersonation"
            }
        ],
   
    識別碼值必須是您使用 [GUID 產生工具](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) 或以程式設計方式建立的新產生 GUID。 它代表由 web API 所公開之權限的唯一識別碼。 一旦您的用戶端已正確設定為要求存取您的 web API 並且呼叫 web API，它會顯示 OAuth 2.0 JWT 權杖，其範圍 (scp) 宣告已設為上述值，在此案例中為 user_impersonation。
   
   > [!NOTE]
   > 稍後您可以視需要公開其他範圍。 請考慮您的 Web API 可能會公開多個與各種不同功能相關聯的範圍。 現在您可以使用範圍 (scp) 宣告在收到的 OAuth 2.0 JWT 權杖中控制 web API 的存取權。
   > 
   > 
6. 按一下 [儲存]  以儲存資訊清單。 您的 Web API 現在已設定為可供目錄中的其他應用程式使用。

#### <a name="to-verify-the-web-api-is-exposed-to-other-applications-in-your-directory"></a>確認已向目錄中的其他應用程式公開 web API
1. 在頂端功能表上，按一下 [應用程式註冊]，選取您想要設定 Web API 存取權的用戶端應用程式，然後瀏覽至 [設定] 刀鋒視窗。
2. 在 [必要的權限] 區段中，選取您剛才開放權限的 Web API。 從 [委派的權限] 下拉式功能表，選取新的權限。

![顯示待辦事項清單權限](./media/active-directory-integrating-applications/todolistpermissions.png)

#### <a name="more-on-the-application-manifest"></a>應用程式資訊清單的詳細資料
應用程式資訊清單實際上可做為更新應用程式實體的一種機制，其可定義 Azure AD 應用程式身分識別組態的所有屬性，包括我們所討論的 API 存取範圍。 如需應用程式實體的詳細資訊，請參閱 [圖形 API 應用程式實體文件](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)。 您會在其中找到用來指定您 API 權限的應用程式實體成員之完整參考資訊：  

* appRoles 成員為 [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) 實體集合，可用來定義 Web API 的 [應用程式權限]  
* oauth2Permissions 成員為 [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) 實體集合，可用來定義 Web API 的 [委派的權限]

如需應用程式資訊清單一般概念的詳細資訊，請參閱 [了解 Azure Active Directory 應用程式資訊清單](active-directory-application-manifest.md)。

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>透過 Microsoft Graph API 存取 Azure AD Graph 和 Office 365 API  
如前文所述，除了在自己的資源應用程式上公開/存取 API，您也可以更新用戶端應用程式以存取 Microsoft 資源所公開的 API。  Microsoft Graph API (在其他應用程式的 [權限] 清單中稱為 “Microsoft Graph”) 可供向 Azure AD 註冊的所有應用程式使用。 如果您在 Office 365 所佈建的 Azure AD 租用戶中註冊您的用戶端應用程式，也可以存取 Microsoft Graph API 對各種 Office 365 資源公開的所有權限。

如需 Microsoft Graph API 所公開之存取範圍的完整討論，請參閱[權限範圍 |Microsoft Graph API 概念](https://graph.microsoft.io/docs/authorization/permission_scopes)一文。

> [!NOTE]
> 由於目前的限制，如果原生用戶端應用程式使用「存取組織的目錄」權限，它們只能呼叫 Azure AD 圖形 API。  這項限制不適用於 web 應用程式。
> 
> 

### <a name="configuring-multi-tenant-applications"></a>設定多租用戶應用程式
將應用程式新增至 Azure AD 時，您可以只讓組織中的使用者存取您的應用程式。 或者，您可能想讓外部組織的使用者存取您的應用程式。 這兩個應用程式類型稱為單一租用戶和多租用戶應用程式。 您可以修改單一租用戶應用程式的組態，使其成為多組用戶應用程式，將於本章節下方討論。

請務必注意單一租用戶與多租用戶應用程式之間的差異：  

* 單一租用戶應用程式適合在一個組織中使用。 它們通常是由企業開發人員撰寫的企業營運 (LoB) 應用程式。 單一租用戶應用程式只需要由一個目錄中的使用者存取，因此，它只需要佈建在一個目錄中。
* 多租用戶應用程式適合在許多組織中使用。 它們是通常由獨立軟體廠商 (ISV) 撰寫的軟體即服務 (SaaS) Web 應用程式。 多租用戶應用程式需要佈建在將會用到它們的每個目錄中，而這需要使用者或系統管理員同意才能註冊 (透過 Azure AD 同意架構支援)。 請注意，所有原生用戶端應用程式預設有多個租用戶，因為其安裝於資源擁有者的裝置上。 如需同意架構的詳細資訊，請參閱上面的「同意架構的概觀」一節。

#### <a name="enabling-external-users-to-grant-your-application-access-to-their-resources"></a>讓外部使用者可以授與您的應用程式存取其資源的權限
如果您正在撰寫想要提供給組織外的客戶或合作夥伴使用的應用程式，您必須在 Azure 入口網站中更新應用程式定義。

> [!NOTE]
> 啟用多租用戶時，您必須確定應用程式的應用程式識別碼 URI 屬於已驗證的網域。 此外，傳回 URL 必須以 https:// 開頭。 如需詳細資訊，請參閱 [應用程式物件和服務主體物件](active-directory-application-objects.md)。
> 
> 

讓外部使用者存取您的應用程式： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頁面右上角選取您的帳戶，以選擇您的 Azure AD 租用戶。
3. 在頂端功能表上，選擇 [Azure Active Directory]，按一下 [應用程式註冊]，然後按一下您想要設定的應用程式。 這會帶您前往應用程式的 [開始使用] 頁面，同時開啟應用程式的 [設定] 刀鋒視窗。
4. 在 [設定] 刀鋒視窗中，按一下 [屬性]，並將 [多租用戶] 切換為 [是]。

一旦您進行上述變更，其他組織中的使用者和系統管理員將可以讓您的應用程式存取他們的目錄和其他資料。

#### <a name="triggering-the-azure-ad-consent-framework-at-runtime"></a>在執行階段觸發 Azure AD 同意架構
若要使用同意架構，多租用戶的用戶端應用程式必須使用 OAuth 2.0 要求授權。 [程式碼範例](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant)也可向您示範 Web 應用程式、原生應用程式或伺服器/精靈應用程式如何要求授權碼並存取權杖以呼叫 Web API。

Web 應用程式也可提供使用者的註冊體驗。 如果您已提供註冊體驗，預期使用者會按一下註冊按鈕，將瀏覽器重新導向至 Azure AD OAuth2.0 授權端點或 OpenID Connect userinfo 端點。 這些端點可讓應用程式藉由檢查 id_token 取得新使用者的相關資訊。 在註冊階段後，使用者將會看到類似上面「同意架構的概觀」一節所示的同意提示。

或者，您的 web 應用程式可能還會提供可允許系統管理員「註冊我的公司」的體驗。 這種體驗也會將使用者重新導向至 Azure AD OAuth 2.0 授權端點。 在此情況下，您會將 prompt=admin_consent 參數傳遞至授權端點，強迫系統管理員同意體驗，系統管理員會在其中代表其組織授與同意。 只有使用屬於全域管理員角色的帳戶進行驗證的使用者可以提供同意；其他人會收到錯誤。 成功同意時，回應將包含 admin_consent=true。 兌換存取權杖時，您也將獲得提供組織資訊的 id_token，以及會註冊您的應用程式的系統管理員。

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>啟用單一頁面應用程式的 OAuth 2.0 隱含授權
單一頁面應用程式 (SPA) 通常會利用執行於瀏覽器中的 JavaScript-heavy 前端進行結構化，此前端會呼叫應用程式的 web API 後端以執行其商務邏輯。 針對裝載於 Azure AD 中的 SPA，您會使用 OAuth 2.0 隱含授權驗證具備 Azure AD 的使用者，並取得您可以使用的權杖以保護從應用程式的 JavaScript 用戶端到其後端 web API 的呼叫。 使用者授與同意權之後，這個相同的驗證通訊協定可用來取得權杖以保護用戶端和其他為應用程式設定之 web API 資源之間的呼叫。 若要深入了解隱含授權授與，並協助您決定其是否適合您的應用程式案例，請參閱 [了解 Azure Active Directory 中的 OAuth2 隱含授與流程 ](active-directory-dev-understanding-oauth2-implicit-grant.md)。

根據預設，應用程式的 OAuth 2.0 隱含授權會停用。 您也可以設定[應用程式資訊清單](active-directory-application-manifest.md)中的 `oauth2AllowImplicitFlow` 值，以啟用應用程式的「OAuth 2.0 隱含授權」，此值為表示應用程式身分識別組態的 JSON 檔案。

#### <a name="to-enable-oauth-20-implicit-grant"></a>啟用 OAuth 2.0 隱含授權
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頁面右上角選取您的帳戶，以選擇您的 Azure AD 租用戶。
3. 在頂端功能表上，選擇 [Azure Active Directory]，按一下 [應用程式註冊]，然後按一下您想要設定的應用程式。 這會帶您前往應用程式的 [開始使用] 頁面，同時開啟應用程式的 [設定] 刀鋒視窗。
4. 按一下應用程式頁面中的 [資訊清單]，以開啟內嵌資訊清單編輯器。
   找到 "oauth2AllowImplicitFlow" 並將其值設為 “true”。 根據預設，它是 “false”。
   
    `"oauth2AllowImplicitFlow": true,`
5. 儲存更新後的資訊清單。 儲存之後，您的 Web API 現在已設定為使用 OAuth 2.0 隱含授權來驗證使用者。


## <a name="removing-an-application"></a>移除應用程式
本節說明如何從 Azure AD 租用戶移除應用程式。

### <a name="removing-an-application-authored-by-your-organization"></a>移除您的組織所編寫的應用程式
這些是 Azure AD 租用戶的主要 [應用程式] 頁面上 [我的公司擁有的應用程式] 篩選器之下顯示的應用程式。 在技術層面，這些是您透過 Azure 傳統入口網站以手動方式註冊的應用程式，或透過 PowerShell 或圖形 API 以程式設計方式註冊的應用程式。 更具體來說，它們會由您租用戶中的應用程式與服務主體物件所表示。 如需詳細資訊，請參閱 [應用程式物件和服務主體物件](active-directory-application-objects.md) 。

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>從您的目錄移除單一租用戶應用程式
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頁面右上角選取您的帳戶，以選擇您的 Azure AD 租用戶。
3. 在頂端功能表上，選擇 [Azure Active Directory]，按一下 [應用程式註冊]，然後按一下您想要設定的應用程式。 這會帶您前往應用程式的 [開始使用] 頁面，同時開啟應用程式的 [設定] 刀鋒視窗。
4. 在應用程式頁面上，按一下 [刪除]。
5. 在確認訊息處按一下 [是]  。

#### <a name="to-remove-a-multi-tenant-application-from-your-directory"></a>從您的目錄移除多租用戶應用程式
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頁面右上角選取您的帳戶，以選擇您的 Azure AD 租用戶。
3. 在頂端功能表上，選擇 [Azure Active Directory]，按一下 [應用程式註冊]，然後按一下您想要設定的應用程式。 這會帶您前往應用程式的 [開始使用] 頁面，同時開啟應用程式的 [設定] 刀鋒視窗。
4. 在 [設定] 刀鋒視窗中，選擇 [屬性]，並將 [多租用戶] 切換為 [否]。 這樣會將您的應用程式轉換為單一租用戶，但應用程式仍會留在授與其同意權的組織中。
5. 按一下應用程式頁面中的 [刪除] 按鈕。
6. 在確認訊息處按一下 [是]  。

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>移除其他組織授權的多租用戶應用程式
這些是 Azure AD 租用戶的主要 [應用程式] 頁面上 [我的公司使用的應用程式] 篩選器之下顯示的部份應用程式，特別是未列在 [我的公司擁有的應用程式] 清單下的應用程式。 在技術層面，這些是在同意處理期間註冊的多租用戶應用程式。 更具體來說，它們僅由您租用戶中的服務主體物件表示。 如需詳細資訊，請參閱 [應用程式物件和服務主體物件](active-directory-application-objects.md) 。

為了移除多租用戶應用程式對您的目錄的存取權 (在授與同意之後)，公司系統管理員必須具有 Azure 訂用帳戶才能透過 Azure 入口網站移除存取權。 或者，公司系統管理員可以使用 [Azure AD PowerShell Cmdlet](http://go.microsoft.com/fwlink/?LinkId=294151) 移除存取權。

## <a name="next-steps"></a>後續步驟
* 如需應用程式視覺化導引的祕訣，請參閱[整合應用程式的商標指導方針](active-directory-branding-guidelines.md)。
* 如需有關應用程式之「應用程式」和「服務主體」物件之間關係的詳細資訊，請參閱[應用程式物件和服務主體物件](active-directory-application-objects.md)。
* 若要深入了解應用程式資訊清單所扮演的角色，請參閱[了解 Azure Active Directory 應用程式資訊清單](active-directory-application-manifest.md)
* 如需核心 Azure Active Directory (AD) 開發人員概念的一些定義，請參閱 [Azure AD 開發人員詞彙](active-directory-dev-glossary.md)。
* 如需所有開發人員相關內容的概觀，請瀏覽 [Active Directory 開發人員指南](active-directory-developers-guide.md)。


