---
title: "註冊自訂連接器 - Azure Logic Apps | Microsoft Docs"
description: "設定要在 Azure Logic Apps 中使用的自訂連接器"
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
ms.openlocfilehash: 2c384e27798bc4920ef73af1084600fdc4047f86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>在 Azure Logic Apps 中註冊自訂連接器

在您建置 REST API 之後，請設定驗證，然後取得 OpenAPI 定義檔或 Postman 集合，這樣您就已準備就緒，可以註冊您的連接器。 

## <a name="prerequisites"></a>必要條件

若要註冊自訂連接器，您需要下列項目：

* 用於在 Azure 中註冊連接器的詳細資料，例如名稱、Azure 訂用帳戶、Azure 資源群組，以及您想要使用的位置

* 描述您 API 的 OpenAPI (Swagger) 檔案或 Postman 集合

  針對本教學課程，您可以使用[範例 Azure Resources Manager OpenAPI 檔案](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)。

* 代表您連接器的圖示

* 您連接器的簡短描述

* 您 API 的主機位置

## <a name="1-create-your-connector"></a>1.建立您的連接器

1. 在 Azure 入口網站中，於主要 Azure 功能表上選擇 [新增]。 在搜尋方塊中輸入「logic apps 連接器」作為篩選條件，然後按 ENTER 鍵。

   ![新增、搜尋「logic apps 連接器」](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. 從結果清單中，選擇 [Logic Apps 連接器] > [建立]。

   ![建立 Logic Apps 連接器](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. 依照下表所述，提供用來註冊連接器的詳細資料。 完成時，請選擇 [釘選到儀表板] > [建立]。

   ![Logic App 自訂連接器詳細資料](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | 屬性 | 建議的值 | 說明 | 
   | -------- | --------------- | ----------- | 
   | **名稱** | *custom-connector-name* | 為您的連接器提供一個名稱。 | 
   | **訂用帳戶** | *Azure-subscription-name* | 選取 Azure 訂用帳戶。 | 
   | **資源群組** | *Azure-resource-group-name* | 建立或選取一個 Azure 群組來組織您的 Azure 資源。 | 
   | **位置** | *deployment-region* | 為您的連接器選取一個部署區域。 | 
   |||| 

   在 Azure 部署您的連接器之後，自訂連接器功能表便會自動開啟。 
   如果未開啟，請從 Azure 儀表板中選擇您的自訂連接器。

## <a name="2-define-your-connector"></a>2.定義您的連接器

現在，請指定用於建立您連接器的 OpenAPI 檔案或 Postman 集合、連接器所使用的驗證、自訂連接器所提供的動作和觸發程序，以及動作和觸發程序可以使用的參數。

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. 指定您連接器的 OpenAPI 檔案或 Postman 集合

1. 在您的連接器功能表中，如果尚未選取 [Logic Apps 連接器]，請選擇它。 在工具列中，選擇 [編輯]。

   ![編輯自訂連接器](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. 選擇 [一般]，以便您可以提供這些表格中的詳細資料，來建立、保護及定義自訂連接器的動作和觸發程序。

   1. 針對 [自訂連接器]，選取一個選項來提供描述您 API 的 OpenAPI (Swagger) 檔案。

      ![提供您 API 的 OpenAPI 檔案](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | 選項 | 格式 |說明 | 
      | ------ | ------ | ----------- | 
      | **上傳 OpenAPI 檔案** | *OpenAPI (Swagger)-json-file* | 瀏覽至 OpenAPI 檔案的位置，然後選取該檔案。 | 
      | **使用 OpenAPI URL** | http://*path-to-swagger-json-file* | 提供您 API OpenAPI 檔案的 URL。 | 
      | **上傳 Postman 集合 V1** | *exported-Postman-collection-V1-file* | 瀏覽至所匯出之 V1 格式的 Postman 集合。 | 
      |||| 

   2. 針對 [一般資訊]，上傳您連接器的圖示。 
   通常，系統會自動從您的 OpenAPI 檔案抓取資料來填入 [描述]主機及 [基底 URL] 欄位。 
   但如果並未填入，請依照下表所述，新增此資訊，然後選擇 [繼續]。 

      ![連接器詳細資料](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | 選項或設定 | 格式 | 說明 | 
      | ----------------- | ------ | ----------- | 
      | **上傳圖示** | *png-or-jpg-file-under-1-MB* | 代表您連接器的圖示 <p>色彩：最好是彩色背景搭配白色標誌。 <p>維度：230 像素的正方形，內部的標誌最大可達 160 像素 | 
      | **圖示背景色彩** | *icon-brand-color-hexadecimal-code* | <p>與您圖示檔中的背景色彩相符的圖示背景色彩。 <p>格式：十六進位。 例如，#007ee5 代表藍色。 | 
      | **說明** | *connector-description* | 提供您連接器的簡短描述。 | 
      | **Host** | *connector-host* | 提供您 Web API 的主機網域。 | 
      | **基底 URL** | *connector-base-URL* | 提供您 Web API 的基底 URL。 | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. 描述您連接器使用的驗證

1. 現在，選擇 [安全性] 來檢閱或描述您連接器使用的驗證。 驗證可確保使用者的身分識別在您的服務與任何用戶端之間以適當方式流通。

   ![驗證類型](./media/logic-apps-custom-connector-register/security.png)

   * 如果您上傳 OpenAPI 檔案，註冊精靈會自動偵測您 Web API 使用的驗證類型，然後根據該檔案中的 `securityDefinitions` 物件，自動填入到精靈的 [安全性] 區段中。 例如，以下是指定使用 OAuth2.0 的區段：

     ``` json
     "securityDefinitions": {
       "AAD": {
       "type": "oauth2",
       "flow": "accessCode",
       "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
       "tokenUrl": "https://login.windows.net/common/oauth2/token",
       "scopes": {}
       }
     },
     ```

   * 如果您的 OpenAPI 檔案並未填入驗證類型和屬性，請選擇 [編輯] 來新增此資訊。 
   
     例如，如果您先前[在這個教學課程中設定了 Azure AD 驗證](../logic-apps/custom-connector-azure-active-directory-authentication.md)，您便已建立 Azure AD 應用程式來保護 Web API 和連接器。 
     因此，現在您可以提供先前儲存的應用程式識別碼和用戶端金鑰：
    
     | 設定 | 建議的值 | 說明 | 
     | ------- | --------------- | ----------- | 
     | **驗證類型** | OAuth 2.0 | | 
     | **識別提供者** | Azure Active Directory | | 
     | **用戶端識別碼** | *application-ID-for-connector-Azure-AD-app* | 您先前為連接器的 Azure AD 應用程式儲存的應用程式識別碼 | 
     | **用戶端密碼** | *client-key-for-connector-Azure-AD-app* | 您連接器 Azure AD 應用程式的用戶端金鑰 | 
     | **登入 URL** | `https://login.windows.net` | | 
     | **資源 URL** | `https://management.core.windows.net/` | 請確定您新增的 URL 與所指定的完全相同，包括結尾的斜線。 | 
     |||| 

   * Postman 集合 (會自動為您產生 OpenAPI 檔案)「只有」在您使用支援的驗證類型 (例如 OAuth 2.0 或 Basic (基本)) 時，才會自動填入驗證類型。

2. 在輸入安全性資訊之後，若要儲存您的連接器，請在頁面頂端選擇 [更新連接器]，然後選擇 [繼續]。 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. 檢閱、更新或定義您連接器的動作和觸發程序

1. 現在，選擇 [定義] 來檢閱、編輯或定義使用者可以新增到其工作流程的新動作和觸發程序。

   動作和觸發程序會以您 OpenAPI 檔案或 Postman 集合中定義的作業為基礎，這會自動填入到 [定義] 頁面中，且包含要求和回應值。 因此，如果這裡已經顯示所需的作業，您便可以移至註冊程序的下一個步驟，而無須在此頁面上進行任何變更。

   ![連接器定義](./media/logic-apps-custom-connector-register/definition.png)

2. (選擇性) 如果您想要編輯現有的動作和觸發程序，或新增新的動作和觸發程序，請繼續進行下列步驟。

#### <a name="edit-or-add-actions-for-your-connector"></a>為您的連接器編輯或新增動作

1. 若要編輯現有的動作，請選擇該動作的號碼。 若要新增您 OpenAPI 檔案或 Postman 集合中所沒有的動作，請在 [動作] 底下，選擇 [新增動作]。

2. 在 [一般] 底下，為動作提供或編輯這項資訊：
   
   | 設定 | 建議的值 | 說明 | 
   | ------- | --------------- | ----------- | 
   | **摘要** | *operation-name* | 此動作的標題 | 
   | **說明** | *operation-description* | 此動作的描述。 <p>**秘訣**：請確定描述的結尾是點號。 |
   | **作業識別碼** | *operation-identifier* | 用來識別此動作的唯一名稱。 請使用駝峰式大小寫，例如："GetPullRequest" | 
   |**可見性**| **none****advanced**、**internal** 或 **important** | 使用者面對此動作時的可見性。 如需詳細資訊，請參閱 [OpenAPI 擴充功能](../logic-apps/custom-connector-openapi-extensions.md#visibility)。 | 
   |||| 

3. 現在，定義動作的要求。
  
   1. 在 [要求] 區段中，選擇 [從範例匯入]。 

   2. 在 [從範例匯入] 頁面上，貼上範例要求。 

      通常，API 文件中會提供範例要求，您可以從該處取得 [動詞][URL]、[標頭]及 [本文] 欄位的資訊。 例如，請參閱[文字分析 API 文件](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) \(英文\)。

      ![匯入範例要求](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > 如果您從 Postman 集合建立連接器，請務必從動作和觸發程序中移除 `Content-type` 標頭。 Logic Apps 會自動新增此標頭。 此外，也請從動作和觸發程序中移除您在 `Security` 區段中定義的驗證標頭。

      如需了解進階的 OpenAPI 功能，請參閱[適用於自訂連接器的 OpenAPI 擴充功能](../logic-apps/custom-connector-openapi-extensions.md)。

   3. 若要完成要求定義，請選擇 [匯入]。

4. 現在，定義動作的回應。

   1. 在 [回應] 底下，您可以指定預設回應。 
   選擇 [新增預設回應]。

   2. 在 [從範例匯入] 頁面上，貼上範例回應，然後選擇 [匯入]。

5. 最後，在 [驗證] 底下，檢閱並修正針對該動作識別出的任何可能問題。

#### <a name="edit-or-add-triggers-for-your-connector"></a>為您的連接器編輯或新增觸發程序

1. 若要編輯現有的觸發程序，請選擇該觸發程序的號碼。 若要新增您 OpenAPI 檔案或 Postman 集合中所沒有的觸發程序，請在 [觸發程序] 底下，選擇 [新增觸發程序]。

2. 在 [一般] 底下，為觸發程序提供或編輯這項資訊：

   | 設定 | 建議的值 | 說明 | 
   | ------- | --------------- | ----------- | 
   | **摘要** | *operation-name* | 此觸發程序的標題 | 
   | **說明** | *operation-description* | 此觸發程序的描述。 <p>**秘訣**：請確定描述的結尾是點號。 | 
   | **作業識別碼** | *operation-identifier* | 用來識別此觸發程序的唯一名稱。 請使用駝峰式大小寫，例如："TriggerOnGitHubPushEvent" | 
   |**可見性**| **none****advanced**、**internal** 或 **important** | 使用者面對此觸發程序時的可見性。 如需詳細資訊，請參閱 [OpenAPI 擴充功能](../logic-apps/custom-connector-openapi-extensions.md#visibility)。 | 
   | **觸發程序類型** | **Webhook** 或**輪詢** | 此觸發程序的類型。 例如，Webhook 觸發程序會等候特定的事件發生後才引發。 輪詢觸發程序會根據指定的間隔和頻率，定期檢查服務端點。 <p>如需有關 Webhook 和輪詢觸發程序模式的詳細資訊，請參閱[建立自訂 API](../logic-apps/logic-apps-create-api-app.md)。 | 
   |||| 

3. 現在，定義會建立觸發程序的要求。 

   1. 在 [要求] 區段中，選擇 [從範例匯入]。

   2. 在 [從範例匯入] 頁面上，貼上範例要求。 

      通常，API 文件中會提供範例要求，您可以從該處取得 [動詞][URL]、[標頭]及 [本文] 欄位的資訊。 例如，請參閱[文字分析 API 文件](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) \(英文\)。

      ![匯入範例要求](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > 如果您從 Postman 集合建立連接器，請務必從動作和觸發程序中移除 `Content-type` 標頭。 Logic Apps 會自動新增此標頭。 此外，也請從動作和觸發程序中移除您在 `Security` 區段中定義的驗證標頭。

      如需了解進階的 OpenAPI 功能，請參閱[適用於自訂連接器的 OpenAPI 擴充功能](../logic-apps/custom-connector-openapi-extensions.md)。

   3. 若要完成要求定義，請選擇 [匯入]。 

4. 現在，定義觸發程序的回應。 在 [回應] 區段中，根據觸發程序的類型，依照下列步驟進行操作：

   **Webhook 觸發程序**
   1. 在 [Webhook 回應] 中，選擇 [從範例匯入]。 

   2. 在 [從範例匯入] 頁面上，貼上範例回應，然後選擇 [匯入]。 若要檢視範例回應，請參閱[建立 Webhook 的 GitHub API 參考](https://developer.github.com/v3/repos/hooks/#create-a-hook) \(英文\)。

   3. 在 [觸發程序設定] 底下，選取 Webhook 建立要求所要使用的參數。 Logic Apps 會使用此參數值來填入您服務用來與觸發程序進行通訊的回呼 URL。

   **輪詢觸發程序**
   1. 在 [回應] 底下，您可以指定預設回應。 
   選擇 [新增預設回應]。

   2. 在 [從範例匯入] 頁面上，貼上範例回應，然後選擇 [匯入]。

   3. 在 [觸發程序設定] 底下，指定查詢參數、要為參數傳遞的值，以及一個指定下一個要求之適當輪詢間隔的「觸發程序提示」。

5. 最後，在 [驗證] 底下，檢閱並修正針對該觸發程序識別出的任何可能問題。

#### <a name="review-reference-definitions-for-your-connector"></a>檢閱您連接器的參考定義

[參考] 區段會自動從您的 API 描述填入資訊，並說明動作和觸發程序可以參考的所有參數欄位。 

1. 在 [參考]底下，選擇您想要檢閱之參考定義的號碼。

2. 在 [名稱] 底下，檢閱或更新參考定義名稱。

3. 在 [驗證] 底下，檢閱並修正針對該參考定義識別出的任何可能問題。

## <a name="3-finish-creating-your-connector"></a>3.完成連接器建立作業

當您準備就緒時，請選擇 [建立] 來部署連接器。 如果您要更新現有的連接器，請選擇 [更新連接器]。

恭喜！ 現在，當您建立邏輯應用程式時，便可在「Logic Apps 設計工具」中找到您的連接器，然後將它新增到您的邏輯應用程式中。

![在「Logic Apps 設計工具」中尋找您的連接器](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>將您的連接器與其他 Logic Apps 使用者共用

已註冊但未經認證之自訂連接器的運作方式與 Microsoft Managed 連接器類似，但「只有」該連接器的作者，以及在部署邏輯應用程式的區域中，就這些應用程式而言具有相同 Azure Active Directory 租用戶和 Azure 訂用帳戶的使用者，才可以看見和使用這些自訂連接器。 雖然共用是選擇性的，但您可能會有想要將連接器與其他使用者共用的情況。 

> [!IMPORTANT]
> 如果您共用連接器，其他使用者便可能開始倚賴該連接器。 
> ***刪除連接器會刪除與該連接器的所有連線。***
 
若要將您的連接器與這些界線以外的外部使用者共用，例如與所有 Logic Apps、Flow 及 PowerApps 使用者共用，請[提交您的連接器以進行 Microsoft 認證](../logic-apps/custom-connector-submit-certification.md)。

## <a name="faq"></a>常見問題集

**問：**自訂連接器是否有任何限制？ </br>
**答：**是，請參閱[這裡的自訂連接器限制](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits)。

## <a name="get-support"></a>取得支援

* 如需有關開發和上線的支援，或是要請求提供註冊精靈中未提供的功能，請連絡 [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)。Microsoft 會監控此帳戶來了解開發人員問題和疑難，然後將它們轉交給適當的小組。

* 若要提出問題、回答問題或查看其他 Azure Logic Apps 使用者的做法，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 若要協助改善 Logic Apps，請在 [Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)上票選或提交想法。 

## <a name="next-steps"></a>後續步驟

* [選擇性：認證您的連接器](../logic-apps/custom-connector-submit-certification.md)
* [自訂連接器常見問題集](../logic-apps/custom-connector-faq.md)