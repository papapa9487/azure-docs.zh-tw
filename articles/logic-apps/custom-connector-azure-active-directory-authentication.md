---
title: "使用 Azure AD 來保護自訂連接器 - Azure Logic Apps | Microsoft Docs"
description: "使用 Azure Active Directory (Azure AD) 為您的 API 和連接器新增安全性和驗證"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: bcee842cb880002daaa3ae9d9110ee1734941b6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>使用 Azure Active Directory (Azure AD) 為您的 API 和連接器新增安全性

若要保護您 API 與自訂連接器之間的呼叫，請為您的 Web API 和連接器新增 Azure AD 驗證。 針對此案例，您需建立兩個 Azure Active Directory (Azure AD) 應用程式 - 一個 Azure AD 應用程式負責保護您的 Web API，另一個 Azure AD 應用程式則負責保護連接器註冊及新增委派的存取權。 

本教學課程會使用 Azure Resource Manager API 作為範例。 Azure Resource Manager 可協助您管理 Azure 中已建置之解決方案的元件，例如資料庫、虛擬機器及 Web 應用程式。 當您想要從工作流程管理 Azure 資源時，自訂的 Azure Resource Manager 連接器會相當有用。 如需詳細資訊，請參閱 [Azure 資源管理員概觀](../azure-resource-manager/resource-group-overview.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以從[免費 Azure 帳戶](https://azure.microsoft.com/free/)開始著手。 否則，請註冊[隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

* 針對本教學課程，[適用於 Azure Resources Manager 的範例 OpenAPI 檔案](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)

  > [!NOTE] 
  > 範例 OpenAPI 檔案並未定義所有 Azure Resource Manager 作業，且目前只包含[列出所有訂用帳戶](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List)的作業。 您可以使用[線上 OpenAPI 編輯器](http://editor.swagger.io/)，來編輯此 OpenAPI 檔案或建立另一個 OpenAPI 檔案。
  >
  > 您可以將本教學課程套用到任何您想要使用 Azure AD 驗證的 RESTful API。

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1.建立您的第一個 Azure AD 應用程式來保護 Web API

您的第一個 Azure AD 應用程式會在自訂連接器呼叫 API (在本範例中為 Azure Resource Manager API) 時執行驗證。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 如果您有多個 Azure Active Directory 租用戶，請檢查您使用者名稱底下的目錄，以確認您所登入的目錄正確。 

   ![確認您的目錄](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > 若要變更目錄，請選擇您的使用者名稱，然後選取您想要的目錄。

2. 在主要 Azure 功能表上，選擇 [Azure Active Directory] 來檢視目前的目錄。

   ![選擇 [Azure Active Directory]](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > 如果主要 Azure 功能表未顯示 [Azure Active Directory]，請選擇 [更多服務]。 在 [篩選] 方塊中輸入 "Azure Active Directory" 作為篩選條件，然後選擇 [Azure Active Directory]。

3. 在目錄功能表的 [管理] 底下，選擇 [應用程式註冊]。 在已註冊的應用程式清單中，選擇 [+ 新增應用程式註冊]。

   ![選擇 [應用程式註冊]、[+ 新增應用程式註冊]](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. 在 [建立] 底下，依照下表所述，提供您 Azure AD 應用程式的詳細資料，然後選擇 [建立]。 

   ![建立 Azure AD 應用程式](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | 設定 | 建議的值 | 說明 | 
   | ------- | --------------- | ----------- | 
   | **名稱** | *web-api-app-name* | Web API 之 Azure AD 應用程式的名稱 | 
   | **應用程式類型** | **Web 應用程式/API** | 您應用程式的類型 | 
   | **登入 URL** | `https://login.windows.net` | | 
   |||| 

5. 返回您目錄的 [應用程式註冊] 清單時，選取您的 Azure AD 應用程式。

   ![從清單中選取您的 Azure AD 應用程式](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. 應用程式的詳細資料頁面出現時，請務必**複製應用程式的「應用程式識別碼」並且儲存在安全的地方**。 您稍後將會需要用到此識別碼。

   ![儲存「應用程式識別碼」以供日後使用](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. 現在，為您的 Azure AD 應用程式提供一個回覆 URL。 在應用程式的 [設定] 功能表中，選擇 [回覆 URL]。 輸入此 URL，然後選擇 [儲存]。

   ![回覆 URL](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | 設定 | 建議的值 | 說明 | 
   | ------- | --------------- | ----------- | 
   | **回覆 URL** | 從您自己的 API，輸入以下 URL： </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **委派的權限** | {非必要} | | 
   | **用戶端金鑰** | {非必要} | | 
   |||| 

   > [!TIP]
   > 如果 [設定] 功能表先前沒有出現，請選擇這裡的 [設定]：
   >
   > ![選擇 [設定]](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2.為您的自訂連接器建立第二個 Azure AD 應用程式

您的第二個 Azure AD 應用程式會保護自訂連接器註冊，並針對受第一個 Azure AD 應用程式保護的 Web API 新增委派的存取權。 

> [!IMPORTANT]
> 請確定兩個 Azure AD 應用程式都存在於相同的目錄中。

1. 返回 [應用程式註冊] 清單，然後再次選擇 [+ 新增應用程式註冊]。

   ![選擇 [應用程式註冊]、[+ 新增應用程式註冊]](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. 在 [建立] 底下，依照下表所述，提供您第二個 Azure AD 應用程式的詳細資料，然後選擇 [建立]。 

   ![建立 Azure AD 應用程式](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | 設定 | 建議的值 | 說明 | 
   | ------- | --------------- | ----------- | 
   | **名稱** | *your-connector-name* | 您連接器 Azure AD 應用程式的名稱 | 
   | **應用程式類型** | **Web 應用程式/API** | 您應用程式的類型 | 
   | **登入 URL** | `https://login.windows.net` | | 
   |||| 

3. 返回您目錄的 [應用程式註冊] 清單時，選取您的第二個 Azure AD 應用程式。

   ![從清單中選取您的第二個 Azure AD 應用程式](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. 應用程式的詳細資料頁面出現時，請務必也**複製此應用程式的「應用程式識別碼」並且也儲存在安全的地方**。 您稍後將會需要用到此識別碼。

   ![儲存「應用程式識別碼」以供日後使用](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. 現在，為您的 Azure AD 應用程式提供一個回覆 URL。 在應用程式的 [設定] 功能表中，選擇 [回覆 URL]。 輸入此 URL，然後選擇 [儲存]。

   | 設定 | 建議的值 | 
   | ------- | --------------- | 
   | **回覆 URL** | 針對 Azure Resource Manager 自訂連接器，請輸入此 URL：`https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > 如果 [設定] 功能表先前沒有出現，請選擇這裡的 [設定]：
   >
   > ![選擇 [設定]](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. 返回 [設定] 功能表，選擇 [必要權限] > [新增]。

   ![[必要權限] > [新增]](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. 當 [加入 API 存取權] 功能表開啟時，選擇 [選取 API] > 
[Windows Azure 服務管理 API] > [選取]。

   ![選取 API](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. 在 [加入 API 存取權] 功能表上，選擇 [選取權限]。 在 [委派的權限] 底下，選擇 [以組織使用者的身分存取 Azure 服務管理] > [選取]。

   ![選擇 [委派的權限] > [以組織使用者的身分存取 Azure 服務管理]](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   否則，針對其他選項：

   | 選項 | 建議的值 | 說明 | 
   | ------ | --------------- | ----------- | 
   | **委派的權限** | | 選取權限以取得 Web API 的委派存取權 | 
   |||| 

9. 現在，在 [加入 API 存取權] 功能表上，選擇 [完成]。

   ![[加入 API 存取權] 功能表 > [完成]](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. 現在，為連接器的 Azure AD 應用程式產生一個「用戶端金鑰」或「密碼」。 

    1. 返回 [設定] 功能表，選擇 [金鑰]。 
    為您的金鑰提供一個最多 16 個字元的名稱，選取到期時間，然後選擇 [儲存]。

       ![建立用戶端金鑰](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. 當產生的金鑰出現時，**請務必複製此金鑰並且儲存在安全的地方**，然後才離開 [金鑰] 頁面。
    
       ![手動複製並儲存您的金鑰](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. 儲存金鑰之後，您可以放心地關閉 [設定] 功能表。

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3.為您的 Web API 應用程式新增 Azure AD 驗證

現在，請使用您的第一個 Azure AD 應用程式來為 Web API 開啟驗證。 如需詳細資訊，請了解[如何設定 App Service 應用程式使用 Azure Active Directory 登入](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)。

1. 在 [Azure 入口網站](https://portal.azure.com)中，找出先前在[從 Web API 建立自訂連接器](../logic-apps/custom-connector-build-web-api-app-tutorial.md)中發佈的 Web API 應用程式。

2. 在 [設定] 底下，選擇 [驗證/授權]。 

   1. 在 [App Service 驗證] 底下，選擇 [開啟]。
   2. 針對 [當要求未經驗證時所要採取的動作]，選取 [使用 Azure Active Directory 登入]。
   3. 針對 [驗證提供者]，選取 [Azure Active Directory]。 

   ![選擇 [驗證/授權]](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. 在 [Azure Active Directory 設定] 頁面上：

   1. 在 [管理模式] 底下，選擇 [快速]。

   2. 現在，選取您 Web API 應用程式用來進行驗證的 Azure AD 應用程式。 
   選擇 [選取現有的 AD 應用程式] > [Azure AD App]。

   3. 在 [Azure AD 應用程式] 底下，選取您稍早為 Web API 應用程式建立的 Azure AD 應用程式。 
   
   4. 選擇 [確定]，直到您返回 [驗證/授權] 頁面為止。

   例如：

   ![選擇代表您 Web API 應用程式的 Azure AD 應用程式](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. 在 [驗證/授權] 頁面上，選擇 [儲存]。

   ![儲存驗證設定](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   現在，您的 Web API 應用程式已可使用 Azure AD 來進行驗證。

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4.在您 Web API 的 OpenAPI 檔案中設定 Azure AD 驗證

開啟 Web API 的 OpenAPI 檔案，以在 `host` 屬性底下新增 `securityDefintions` 物件和 Azure AD 驗證。 以下是一個說明 `host` 屬性和 `securityDefinitions` 物件的範例：

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
現在，您已準備就緒，可以建立和註冊您的自訂連接器。

## <a name="next-steps"></a>後續步驟

* [註冊您的連接器](../logic-apps/logic-apps-custom-connector-register.md)
* [自訂連接器常見問題集](../logic-apps/custom-connector-faq.md)
