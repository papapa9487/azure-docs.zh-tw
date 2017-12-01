---
title: "建立與註冊 SOAP 連接器 - Azure Logic Apps | Microsoft Docs"
description: "設定要在 Azure Logic Apps 中使用的 SOAP 連接器"
author: divyaswarnkar
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
ms.date: 10/24/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 0323b0f7ee03dce209d5a71c6711988a34ba7633
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>在 Azure Logic Apps 中建立與註冊 SOAP 連接器

若要在您的邏輯應用程式工作流程中整合 SOAP 服務，您可以使用描述您 SOAP 服務的 Web 服務描述語言 (WSDL)，來建立與註冊自訂的簡易物件存取通訊協定 (SOAP) 連接器。 SOAP 連接器的運作與預先建立的連接器一樣，因此您可以如同使用邏輯應用程式中其他連接器一樣地使用它。


## <a name="prerequisites"></a>必要條件

若要註冊 SOAP 連接器，您需要下列項目：

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以從[免費 Azure 帳戶](https://azure.microsoft.com/free/)開始著手。 否則，請註冊[隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

* 以下任何項目：
  * 定義 SOAP 服務和 API 的 WSDL URL
  * 定義 SOAP 服務和 API 的 WSDL 檔案

  此教學課程中，您可以使用我們的範例[訂單的 SOAP 服務](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl)。

* 選擇性：用來作為您自訂連接器之圖示的影像


## <a name="1-create-your-connector"></a>1.建立您的連接器

1. 在 Azure 入口網站中，於主要 Azure 功能表上選擇 [新增]。 在搜尋方塊中輸入「logic apps 連接器」作為篩選條件，然後按 ENTER 鍵。

   ![新增、搜尋「logic apps 連接器」](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. 從結果清單中，選擇 [Logic Apps 連接器] > [建立]。

   ![建立 Logic Apps 連接器](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. 依照下表所述，提供用來註冊連接器的詳細資料。 完成時，請選擇 [釘選到儀表板] > [建立]。

   ![Logic App 自訂連接器詳細資料](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | 屬性 | 建議的值 | 說明 | 
   | -------- | --------------- | ----------- | 
   | **名稱** | *soap-connector-name* | 為您的連接器提供一個名稱。 | 
   | **訂用帳戶** | *Azure-subscription-name* | 選取 Azure 訂用帳戶。 | 
   | **資源群組** | *Azure-resource-group-name* | 建立或選取一個 Azure 群組來組織您的 Azure 資源。 | 
   | **位置** | *deployment-region* | 為您的連接器選取一個部署區域。 | 
   |||| 

   在 Azure 部署您的連接器之後，邏輯應用程式連接器功能表便會自動開啟。 
   如果未開啟，請從 Azure 儀表板中選擇您的 SOAP 連接器。

## <a name="2-define-your-connector"></a>2.定義您的連接器

現在指定建立連接器需要的 WSDL 檔案或 URL、連接器使用的驗證和 SOAP 連接器會提供的動作和觸發程序


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. 為您的連接器指定 WSDL 檔案或 URL

1. 在您的連接器功能表中，如果尚未選取 [Logic Apps 連接器]，請選擇它。 在工具列中，選擇 [編輯]。

   ![編輯自訂連接器](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. 選擇 [一般]，以便您可以提供這些表格中的詳細資料，來建立、保護及定義 SOAP 連接器的動作和觸發程序。

   1. 針對 [自訂連接器]，選取 **SOAP** 作為您的 **API 端點**，那麼您就可以提供描述您 API 的 WSDL 檔案。

      ![提供您 API 的 WSDL 檔案](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | 選項 | 格式 |說明 | 
      | ------ | ------ | ----------- | 
      | **從檔案上傳 WSDL** | *WSDL-file* | 瀏覽至 WSDL 檔案的位置，然後選取該檔案。 | 
      | **從 URL 上傳 WSDL** | http://*path-to-wsdl-file* | 提供您服務的 WSDL 檔案 URL。 | 
      | **從 SOAP 轉換為 REST** |   | 將 SOAP 服務中的 API 轉換為 REST API。 | 
      |||| 

   2. 針對 [一般資訊]，上傳您連接器的圖示。 
   通常，系統會自動從您的 WSDL 檔案抓取資料來填入 [描述]、[主機]及 [基底 URL] 欄位。 
   但如果並未填入，請依照下表所述，新增此資訊，然後選擇 [繼續]。 

      ![連接器詳細資料](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | 選項或設定 | 格式 | 說明 | 
      | ----------------- | ------ | ----------- | 
      | **上傳圖示** | *png-or-jpg-file-under-1-MB* | 代表您連接器的圖示 <p>色彩：最好是彩色背景搭配白色標誌。 <p>維度：230 像素的正方形，內部的標誌最大可達 160 像素 | 
      | **圖示背景色彩** | *icon-brand-color-hexadecimal-code* | <p>與您圖示檔中的背景色彩相符的圖示背景色彩。 <p>格式：十六進位。 例如，#007ee5 代表藍色。 | 
      | **說明** | *connector-description* | 提供您連接器的簡短描述。 | 
      | **Host** | *connector-host* | 提供您 SOAP 服務的主機網域。 | 
      | **基底 URL** | *connector-base-URL* | 提供您 SOAP 服務的基底 URL。 | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. 描述您連接器使用的驗證

1. 現在，選擇 [安全性] 來檢閱或描述您連接器使用的驗證。 驗證可確保使用者的身分識別在您的服務與任何用戶端之間以適當方式流通。

   依預設，連接器的**驗證類型**會設訂為**不需要驗證**。
   
   ![驗證類型](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   若要變更驗證類型，請選擇 [編輯]。 您可以選取 [基本驗證]。 若要使用預設值以外的參數標籤，請在**參數標籤**下更新。

   ![基本驗證](./media/logic-apps-soap-connector-create-register/security.png)

   
2. 在輸入安全性資訊之後，若要儲存您的連接器，請在頁面頂端選擇 [更新連接器]，然後選擇 [繼續]。 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. 檢閱、更新或定義您連接器的動作和觸發程序

1. 現在，選擇 [定義] 來檢閱、編輯或定義使用者可以新增到其工作流程的新動作和觸發程序。

   動作和觸發程序會以您 WSDL 檔案中定義的作業為基礎，這會自動填入到 [定義] 頁面中，且包含要求和回應值。 因此，如果這裡已經顯示所需的作業，您便可以移至註冊程序的下一個步驟，而無須在此頁面上進行任何變更。

   ![連接器定義](./media/logic-apps-soap-connector-create-register/definition.png)

2. (選擇性) 如果您想要編輯現有的動作和觸發程序，或新增新的動作和觸發程序，[請繼續進行下列步驟](logic-apps-custom-connector-register.md#add-action-or-trigger)。


## <a name="3-finish-creating-your-connector"></a>3.完成連接器建立作業

當您準備就緒時，請選擇 [更新連接器] 來部署連接器。 

恭喜！ 現在，當您建立邏輯應用程式時，便可在「Logic Apps 設計工具」中找到您的連接器，然後將它新增到您的邏輯應用程式中。

![在「Logic Apps 設計工具」中尋找您的連接器](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>將您的連接器與其他 Logic Apps 使用者共用

已註冊但未經認證之自訂連接器的運作方式與 Microsoft Managed 連接器類似，但「只有」該連接器的作者，以及在部署邏輯應用程式的區域中，就這些應用程式而言具有相同 Azure Active Directory 租用戶和 Azure 訂用帳戶的使用者，才可以看見和使用這些自訂連接器。 雖然共用是選擇性的，但您可能會有想要將連接器與其他使用者共用的情況。 

> [!IMPORTANT]
> 如果您共用連接器，其他使用者便可能開始倚賴該連接器。 
> ***刪除連接器會刪除與該連接器的所有連線。***
 
若要將您的連接器與這些界線以外的外部使用者共用，例如與所有 Logic Apps 使用者共用，請[提交您的連接器以進行 Microsoft 認證](../logic-apps/custom-connector-submit-certification.md)。

## <a name="faq"></a>常見問題集

**問：**SOAP 連接器是否已正式運作 (GA)？ </br>
**答：**SOAP 連接器目前是**預覽**狀態，尚未成為 GA 服務。

**問：**SOAP 連接器是否有任何限制和已知問題？ </br>
**答：**是，請參閱 [SOAP 連接器的限制和已知問題](../api-management/api-management-api-import-restrictions.md#wsdl)。

**問：**自訂連接器是否有任何限制？ </br>
**答：**是，請參閱[這裡的自訂連接器限制](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits)。

## <a name="get-support"></a>取得支援

* 如需有關開發和上線的支援，或是要請求提供註冊精靈中未提供的功能，請連絡 [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)。Microsoft 會監控此帳戶來了解開發人員問題和疑難，然後將它們轉交給適當的小組。

* 若要提出問題、回答問題或查看其他 Azure Logic Apps 使用者的做法，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 若要協助改善 Logic Apps，請在 [Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)上票選或提交想法。 

## <a name="next-steps"></a>後續步驟

* [選擇性：認證您的連接器](../logic-apps/custom-connector-submit-certification.md)
* [自訂連接器常見問題集](../logic-apps/custom-connector-faq.md)