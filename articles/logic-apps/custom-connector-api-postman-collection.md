---
title: "使用 Postman 來描述自訂 API 和連接器 - Azure Logic Apps | Microsoft Docs"
description: "建立 Postman 集合來描述、分組及組織您的自訂 API 和連接器"
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
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>使用 Postman 來描述自訂 API 和自訂連接器

若要讓開發[自訂 API](../logic-apps/logic-apps-create-api-app.md) 和[自訂連接器](../logic-apps/custom-connector-overview.md) 變得更快、更容易，您可以建立 [Postman](https://www.getpostman.com/) 集合 (而不是 OpenAPI 文件) 來描述您的 API 和連接器。 Postman 集合可協助您將相關的 API 要求加以組織和分組。 例如，您可以在為 Azure Logic Apps、Microsoft Flow 或 Microsoft PowerApps 建立連接器時使用 Postman。 

本教學課程將說明如何使用 [Azure 辨識服務文字分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)中的[偵測語言 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)來建立 [Postman 集合](https://www.getpostman.com/docs/postman/collections/creating_collections)。 此 API 會識別您傳送給 API 之文字中的語言、情緒及關鍵片語。

## <a name="prerequisites"></a>必要條件

* 如果您不熟悉 Postman，請[安裝 Postman 應用程式](https://www.getpostman.com/apps) \(英文\)。

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以從[免費 Azure 帳戶](https://azure.microsoft.com/free/)開始著手。 否則，請註冊[隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

* 如果您有 Azure 訂用帳戶，請完成[這裡的工作 1](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md)來註冊「文字分析 API」。 

## <a name="create-a-postman-collection"></a>建立 Postman 集合

您必須先為 API 端點建立 HTTP 要求，才能建立集合。 

### <a name="create-an-http-request-for-your-api"></a>為 API 建立 HTTP 要求

1. 開啟 Postman 應用程式來為 API 端點建立 [HTTP 要求](https://www.getpostman.com/docs/postman/sending_api_requests/requests) \(英文\)。 如需詳細資訊，請參閱 Postman 的[要求文件](https://www.getpostman.com/docs/postman/sending_api_requests/requests) \(英文\)。

   1. 在 [Builder] \(建立器\) 索引標籤上，選取 HTTP 方法、輸入您 API 端點的 URL，然後選取授權通訊協定 (如果有的話)。 
   準備就緒時，選擇 [Params] \(參數\)。

      針對本教學課程，您可以使用以下範例中的這些設定：

      ![建立要求：[HTTP 方法]、[要求 URL]、[授權]](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | 參數 | 建議的值 | 
      | --------- | --------------- | 
      | **HTTP method (HTTP 方法)** | POST | 
      | **Request URL (要求 URL)** | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **授權** | "No Auth" (無授權) | | 
      ||| 

   2. 現在，您可以輸入機碼值組來作為要求 URL 中的查詢或路徑參數。 Postman 會將這些項目一起結合成查詢字串。
   完成時，請選擇 [Headers] \(標頭\)。

      ![繼續建立要求：[參數]](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | 參數 | 建議的值 | 
      | --------- | --------------- | 
      | **Params (參數)** | **Key (機碼)**："numberOfLanguagesToDetect" </br>**Value (值)**："1" | 
      ||| 

   3. 輸入要求標頭的機碼值組。 
   針對標頭名稱，輸入您想要的任何字串。 針對一般 HTTP 標頭，您可以從下拉式清單中選取。 完成時，請選擇 [Body] \(本文\)。 
   
      ![繼續建立要求：[標頭]](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | 參數 | 值 | 
      | --------- | ----- | 
      | **標頭** | **Key (機碼)**"Ocp-Apim-Subscription-Key" </br>**Value (值)**：*your-API-subscription-key*，您可以在「辨識服務」帳戶中找到此值 <p>**Key (機碼)**"Content-Type" </br> **Value (值)**："application/json" | 
      ||| 

   4. 輸入您想要在要求本文中傳送的內容。 
   若要透過取得回應來檢查要求是否正常運作，請選擇 [Send] \(傳送\)。 
   
      ![繼續建立要求：[本文]](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | 參數 | 建議的值 | 
      | --------- | --------------- |    
      | **內文** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      回應欄位包含 API 的完整回應，其中包括結果或錯誤 (如果有發生的話)。

      ![取得要求回應](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. 確定要求正常運作之後，將要求儲存至 Postman 集合。 

   1. 選擇 [儲存]。 
      
      ![選擇 [儲存]](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. 在 [Save Request] \(儲存要求\) 底下，提供 [Request Name] \(要求名稱\)，並視需要提供 [Request description] \(要求描述\)。 

      > [!NOTE]
      > 您的自訂連接器稍後會用到這些值。 因此，請確定所提供的值與稍後用於自訂 API 之作業摘要和描述的值相同。

   3. 選擇 [+ Create Collection] \(+ 建立集合\)，然後提供集合名稱。 
   選擇核取記號 (這會建立集合資料夾)，然後選擇 [Save to *your-Postman-collection-name*] \(儲存至 your-Postman-collection-name\)。

      ![儲存要求](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>儲存要求回應

儲存要求之後，您便可以儲存回應。 這樣一來，當您稍後載入要求時，該回應就會顯示成一個範例。

1. 在回應視窗上方，選擇 [Save Response] \(儲存回應\)。 

   ![儲存回應](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   自訂連接器針對每個要求只支援一個回應。 
   如果您的每個要求有多個回應，則只會使用第一個回應。

2. 為您的範例提供一個名稱，然後選擇 [Save Example] \(儲存範例\)。

3. 繼續使用任何其他要求和回應來建置您的 Postman 集合。

### <a name="export-your-postman-collection"></a>匯出您的 Postman 集合

1. 完成時，請將您的集合匯出成 JSON 檔案。

   ![匯出集合](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. 選擇 [Collection v1] \(集合 v1\) 匯出格式，然後瀏覽至您想要儲存 JSON 檔案的位置。

   > [!NOTE]
   > 目前，自訂連接器只適用 V1。

   ![選擇匯出格式：[集合 v1]](./media/custom-connector-api-postman-collection/09-export-format.png)
   
您現在已可使用此 Postman 集合來建立自訂 API 和連接器。 匯出集合之後，您可以將 JSON 檔案匯入到 Logic Apps、Flow 或 PowerApps 中。

> [!IMPORTANT]
> 如果您從 Postman 集合建立自訂連接器，請務必從動作和觸發程序中移除 `Content-type` 標頭。 目標服務 (例如 Flow) 會自動新增此標頭。 此外，也請從動作和觸發程序中移除 `securityDefintions` 區段中的驗證標頭。

## <a name="next-steps"></a>後續步驟

* [Logic Apps：註冊自訂連接器](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow：註冊您的連接器](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps：註冊您的連接器](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [自訂連接器常見問題集](../logic-apps/custom-connector-faq.md)
