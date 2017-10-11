---
title: "了解 Azure Active Directory 應用程式資訊清單 | Microsoft Docs"
description: "詳細說明 Azure Active Directory 應用程式資訊清單；此資訊清單代表應用程式在 Azure AD 租用戶中的身分識別組態，可用來協助進行 OAuth 授權、同意體驗等等。"
services: active-directory
documentationcenter: 
author: sureshja
manager: mbaldwin
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: d5e18f41d6eb69ccb7eafaa4de2646c4c38df5e2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="understanding-the-azure-active-directory-application-manifest"></a>了解 Azure Active Directory 應用程式資訊清單
與 Azure Active Directory (AD) 整合的應用程式必須向 Azure AD 租用戶註冊，提供應用程式的持續性身分識別組態。 在執行階段參考此組態，啟用允許應用程式透過 Azure AD 外部和代理驗證/授權的案例。 如需 Azure AD 應用程式模型的詳細資訊，請參閱[新增、更新及移除應用程式][ADD-UPD-RMV-APP]一文。

## <a name="updating-an-applications-identity-configuration"></a>更新應用程式的身分識別組態
實際上有多個可用的選項可更新應用程式的身分識別組態屬性，隨著功能與困難度而有所不同，包括下列選項：

* **[Azure 入口網站][AZURE-PORTAL] 的 Web 使用者介面** 可讓您更新應用程式的最常見屬性。 這是更新應用程式屬性最快速且最不容易有錯誤的方法，但無法提供您所有屬性的完整存取權，如同下面兩個方法。
* 對於更進階的案例 (您在其中必須更新 Azure 傳統入口網站中未公開的屬性)，您可以修改 **應用程式資訊清單**。 這是本文的重點，並且會在下一節中開始詳細討論。
* 它也可**撰寫使用[圖形 API][GRAPH-API] 的應用程式**來更新您的應用程式，這樣是最費力的。 如果您要撰寫管理軟體或需要自動定期更新應用程式屬性，這可能是個不錯的選擇。

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>使用應用程式資訊清單來更新應用程式的身分識別組態
透過 [Azure 入口網站][AZURE-PORTAL]，您可以使用內嵌資訊清單編輯器更新應用程式資訊清單，藉此管理應用程式的身分識別組態。 您也可以下載和上傳 JSON 檔案形式的應用程式資訊清單。 沒有任何實際檔案會儲存在目錄中。 應用程式資訊清單只是「Azure AD Graph API 應用程式」實體上的 HTTP GET 作業，而上傳則是「應用程式」實體上的 HTTP PATCH 作業。

因此，若要了解應用程式資訊清單的格式和屬性，您必須參考圖形 API [應用程式實體][APPLICATION-ENTITY]文件。 可透過應用程式資訊清單上傳執行的更新範例包括：

* **宣告您的 Web API 所公開的權限範圍 (oauth2Permissions)** 。 如需有關使用 oauth2Permissions 委派權限範圍來實作使用者模擬的資訊，請參閱[整合應用程式與 Azure Active Directory][INTEGRATING-APPLICATIONS-AAD]中的＜向其他應用程式公開 Web API＞主題。 如先前所述，應用程式實體屬性都記錄在圖形 API [實體和複雜類型][APPLICATION-ENTITY]參考文章中，包括屬於 [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION] 類型之集合的 oauth2Permissions 屬性。
* **宣告您的應用程式所公開的應用程式角色 (appRoles)**。 應用程式實體的 appRole 屬性是 [AppRole][APPLICATION-ENTITY-APP-ROLE] 類型的集合。 請參閱[雲端應用程式中使用 Azure AD 的角色型存取控制][RBAC-CLOUD-APPS-AZUREAD]一文以取得實作範例。
* **宣告已知的用戶端應用程式 (knownClientApplications)**，可讓您以邏輯方式將指定用戶端應用程式的同意繫結至資源/Web API。
* **要求 Azure AD 對登入使用者發出群組成員資格宣告 (groupMembershipClaims)** 。  這也可以設定為發出有關使用者目錄角色成員資格的宣告。 請參閱[雲端應用程式中使用 AD 群組的授權][AAD-GROUPS-FOR-AUTHORIZATION]一文以取得實作範例。
* **可讓您的應用程式支援 OAuth 2.0 隱含授權** 流程 (oauth2AllowImplicitFlow)。 此類型的授權流程可用於內嵌的 JavaScript 網頁或單一頁面應用程式 (SPA)。 如需有關隱含授權授與的詳細資訊，請參閱[了解 Azure Active Directory 中的 OAuth2 隱含授與流程][IMPLICIT-GRANT]。
* **啟用 X509 憑證做為秘密金鑰** (keyCredentials)。 如需實作範例，請參閱[在 Office 365 中建置服務與精靈應用程式][O365-SERVICE-DAEMON-APPS]和[利用 Azure Resource Manager API 進行驗證開發人員指南][DEV-GUIDE-TO-AUTH-WITH-ARM]等文章。
* 為應用程式**新增應用程式識別碼 URI** (identifierURIs[])。 「應用程式識別碼 URI」可用來在應用程式的 Azure AD 租用戶內 (或針對透過已驗證的自訂網域來限定資格的多租用戶案例，則可跨多個 Azure AD 租用戶)，唯一識別該應用程式。 在要求資源應用程式的權限或取得資源應用程式的存取權杖時，就會使用這些 URI。 當您更新此元素時，相同的更新也會套用到對應之服務主體的 servicePrincipalNames[] 集合 (位於應用程式的主要租用戶中)。

應用程式資訊清單也會提供追蹤應用程式註冊狀態的好方法。 因為它可供 JSON 格式使用，所以檔案表示法可以簽入您的原始檔控制，以及應用程式的原始程式碼。

## <a name="step-by-step-example"></a>逐步執行範例
現在可逐步了解透過應用程式資訊清單更新您的應用程式身分識別組態所需的步驟： 我們會加強說明上述其中一個範例，示範如何在資源應用程式上宣告一個新的權限範圍：

1. 登入 [Azure 入口網站][AZURE-PORTAL]。
2. 驗證後，在頁面右上角選取您的 Azure AD 租用戶。
3. 選取左邊的導覽面板中的 [Azure Active Directory] 擴充，按一下 [應用程式註冊]。
4. 找到您要在清單中更新的應用程式並按一下它。
5. 按一下應用程式頁面中的 [資訊清單]，以開啟內嵌資訊清單編輯器。 
6. 您可以使用此編輯器直接編輯資訊清單。 請注意，資訊清單遵循我們先前提到的[應用程式實體][APPLICATION-ENTITY]結構描述：例如，假設我們想要在資源應用程式 (API) 上實作/公開名為 "Employees.Read.All" 的新權限，您只需將新的/第二個元素新增至 oauth2Permissions 集合即可，即：
   
        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
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
        ],
   
    項目必須是唯一的，因此您必須為 `"id"` 屬性產生新的全域唯一識別碼 (GUID)。 在此情況下，由於我們已指定 `"type": "User"`，因此任何已由資源/API 應用程式註冊所在的 Azure AD 租用戶驗證的帳戶都可以對此權限表示同意。 這會授與用戶端應用程式代表帳戶存取它的權限。 在同意期間會使用說明和顯示名稱字串，並且顯示在 Azure 入口網站中。
6. 當您完成資訊清單更新時，按一下 [儲存] 儲存資訊清單。  
   
既然已儲存資訊清單，您便可以授權已註冊的用戶端應用程式存取我們在上面新增的權限。 此時，您可以使用 Azure 入口網站的 Web UI，而不是編輯用戶端應用程式的資訊清單：  

1. 首先，移至要新增新 API 之存取權的用戶端應用程式的 [設定] 刀鋒視窗，按一下 [必要的權限]，然後選擇 [選取 API]。
2. 畫面中會顯示租用戶中已註冊的資源應用程式 (API) 的清單。 按一下資源應用程式以選取它，或在搜尋方塊中輸入應用程式的名稱。 當您找到該應用程式時，按一下 [選取]。  
3. 這會將您帶到 [選取權限] 頁面，其中顯示該資源應用程式可使用的 [應用程式權限] 和 [委派權限] 的清單。 選取新的權限，以將其新增至用戶端要求的權限清單。 這個新的權限會儲存在用戶端應用程式身分識別組態的 "requiredResourceAccess" 集合屬性中。


就這麼簡單。 現在，您的應用程式將會使用其新的身分識別組態來執行。

## <a name="next-steps"></a>後續步驟
* 如需有關應用程式之「應用程式」和「服務主體」物件之間關係的詳細資訊，請參閱 [Azure AD 中的應用程式和服務主體物件][AAD-APP-OBJECTS]。
* 如需核心 Azure Active Directory (AD) 開發人員概念的一些定義，請參閱 [Azure AD 開發人員詞彙][AAD-DEVELOPER-GLOSSARY]。

請使用下方的註解區段來提供意見反應，並協助我們改善及設計我們的內容。

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

