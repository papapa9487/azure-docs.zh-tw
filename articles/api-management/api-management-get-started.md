---
title: "在 Azure API 管理中管理您的第一個 API | Microsoft Docs"
description: "了解如何建立 API、加入作業，以及開始使用 API 管理。"
services: api-management
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51b7df8b-1c43-43c6-90c9-0aa24f48206b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 0866b94003111267951a0d7917d616a370961880
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2017
---
# <a name="manage-your-first-api-in-azure-api-management"></a>在 Azure API 管理中管理您的第一個 API
## <a name="overview"> </a>概觀
本指南示範如何快速開始使用 Azure API 管理，以及進行您的第一個 API 呼叫。

## <a name="concepts"> </a>什麼是 Azure API 管理？
您可以使用 Azure API 管理來取得任何後端，並根據該後端來啟用完善的 API 方案。

常見案例包括：

* **保護行動基礎結構** 。
* **促成 ISV 合作夥伴生態系統** 。
* **執行內部 API 方案** 。

系統是由下列元件所組成：

* **API 閘道** 是可執行以下作業的端點：
  
  * 接受 API 呼叫，並將這些呼叫路由傳送到您的後端。
  * 驗證 API 金鑰、JWT 權杖、憑證和其他認證。
  * 強制採用使用量配額和頻率限制。
  * 即時轉換您的 API 且不需修改程式碼。
  * 快取設定的後端回應。
  * 記錄呼叫中繼資料以供分析使用。
* **發行者入口網站** 是您設定 API 方案的管理介面。 使用方式：
  
  * 定義或匯入 API 結構描述。
  * 將 API 封裝至產品。
  * 設定原則，例如 API 的配額或轉換。
  * 從分析中取得見解。
  * 管理使用者。
* **開發人員入口網站** 是供開發人員執行以下作業的主要網站空間：
  
  * 閱讀 API 文件。
  * 透過互動式主控台試用 API。
  * 建立帳戶，並訂閱以取得 API 金鑰。
  * 存取他們的使用量分析資料。

## <a name="create-service-instance"> </a>建立 API 管理執行個體
> [!NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][Azure Free Trial]。
> 
> 

使用 API 管理的第一個步驟是建立服務執行個體。 登入 [Azure 入口網站][Azure Portal]，然後按一下 [新增]、[Web + 行動]、[API 管理]。

![API Management new instance][api-management-create-instance-menu]

針對 [名稱]，指定要用於服務 URL 的唯一子網域名稱。

針對您的服務執行個體，選擇需要的 [訂用帳戶]、[資源群組] 和 [位置]。

輸入 **Contoso Ltd.** 做為 [組織名稱]，然後在 [系統管理員電子郵件] 欄位中輸入您的電子郵件地址。

> [!NOTE]
> 此電子郵件地址將用於自 API 管理系統傳送通知。 如需詳細資訊，請參閱[如何在 Azure API 管理中設定通知和電子郵件範本][How to configure notifications and email templates in Azure API Management]。
> 
> 

![New API Management service][api-management-create-instance-step1]

API 管理服務執行個體共有三種層次：開發人員、標準和高階。

> [!NOTE]
> 開發人員階層可用來針對不注重高可用性的 API 程式進行開發、測試及試驗。 在「標準」和「高階」層次中，您可以調整保留的單位計數以處理更多流量。 「標準」和「高階」層次可為您的 API 管理服務提供最高的處理能力和效能。 您可以使用任何階層來完成本教學課程。 如需關於 API 管理層次的詳細資訊，請參閱 [API 管理價格][API Management pricing]。
> 
> 

按一下 [建立] 以開始佈建您的服務執行個體。

![New API Management service][api-management-instance-created]

建立服務執行個體之後，下一步是建立或匯入 API。

## <a name="create-api"> </a>匯入 API
API 包含可自用戶端應用程式叫用的一組作業。 API 作業會代理到現有的 Web 服務。

您可以手動建立 API (並可加入作業)，或是匯入這兩者。 在本教學課程中，我們將針對由 Microsoft 所提供且裝載在 Azure 上的範例計算機 Web 服務來匯入 API。

> [!NOTE]
> 如需建立 API 和手動新增作業的指引，請參閱[如何建立 API](api-management-howto-create-apis.md) 和[如何將作業新增至 API](api-management-howto-add-operations.md)。
> 
> 

API 是從發佈者入口網站進行設定。 若要觸達它，請從服務工具列按一下 [發佈者入口網站]。

![發行者入口網站][api-management-management-console]

若要匯入計算機 API，請從左邊的 [API 管理] 功能表中按一下 [API]，然後按一下 [匯入 API]。

![匯入 API 按鈕][api-management-import-api]

執行下列步驟來設定計算機 API：

1. 按一下 [從 URL]，在 [規格文件 URL] 文字方塊中輸入 **http://calcapi.cloudapp.net/calcapi.json**，然後按一下 [Swagger] 選項按鈕。
2. 在 [Web API URL 尾碼] 文字方塊中，輸入 **calc**。
3. 按一下 [產品 (選擇性)] 方塊，然後選擇 [入門]。
4. 按一下 [ **儲存** ] 匯入 API。

![Add new API][api-management-import-new-api]

> [!NOTE]
> **API 管理**目前支援匯入 1.2 和 2.0 版本的 Swagger 文件。 即使 [Swagger 2.0 規格](http://swagger.io/specification)表示 `host`、`basePath` 和 `schemes` 是選擇性的屬性，請確定您的 Swagger 2.0 文件**必須**包含這些屬性，否則無法匯入。 
> 
> 

匯入 API 之後，API 的摘要頁面隨即會顯示在發行者入口網站中。

![API summary][api-management-imported-api-summary]

API 區段有一些索引標籤。 [摘要]  索引標籤會顯示 API 的基本度量和相關資訊。 [設定](api-management-howto-create-apis.md#configure-api-settings) 索引標籤可用來檢視和編輯 API 的組態。 [作業](api-management-howto-add-operations.md) 索引標籤可用來管理 API 的作業。 [安全性] 索引標籤可用來將後端伺服器的閘道驗證設定為使用基本驗證或[相互憑證驗證](api-management-howto-mutual-certificates.md)，以及用來設定[使用 OAuth 2.0 的使用者授權](api-management-howto-oauth2.md)。  [問題]  索引標籤是用來檢視使用您 API 的開發人員所報告的問題。 [產品]  索引標籤是用來設定包含此 API 的產品。

依預設，每個 API 管理執行個體會隨附兩個範例產品：

* **入門**
* **無限制**

在本教學課程中，Basic Calculator API 已在匯入 API 時加入至「入門」產品。

若要呼叫 API，開發人員必須先訂閱能夠讓他們存取 API 的產品。 開發人員可以在開發人員入口網站中訂閱產品，或者管理員也可以在發行者入口網站中為開發人員訂閱產品。 由於您在先前的教學課程步驟中建立了 API 管理執行個體，因此您是管理員，且已根據預設訂閱了所有產品。

## <a name="call-operation"> </a>從開發人員入口網站呼叫作業
您可以從開發人員入口網站直接呼叫作業，以便檢視和測試 API 的操作。 在本教學課程步驟中，您將會呼叫 Basic Calculator API 的 **Add two integers** 作業。 從發行者入口網站右上角的功能表中，按一下 [ **開發人員入口網站** ]。

![開發人員入口網站][api-management-developer-portal-menu]

按一下頂端功能表中的 [API]，然後按一下 [Basic Calculator] 來查看可用作業。

![開發人員入口網站][api-management-developer-portal-calc-api]

請記下和 API 及作業一起匯入的範例說明與參數，為要使用此作業的開發人員提供文件。 手動加入作業時，也可以加入這些說明。

若要呼叫 **Add two integers** 作業，請按一下 [試試看]。

![試試看][api-management-developer-portal-calc-api-console]

您可以輸入部分參數值或保留預設值，然後按一下 [傳送] 。

![HTTP Get][api-management-invoke-get]

叫用作業之後，開發人員入口網站會顯示 [回應狀態]、[回應標頭]，以及任何的 [回應內容]。

![Response][api-management-invoke-get-response]

## <a name="view-analytics"> </a>檢視分析
若要檢視 Basic Calculator 的分析，可從開發人員入口網站右上角的功能表中選取 [管理]  ，以切換回發行者入口網站。

![管理][api-management-manage-menu]

發行者入口網站的預設檢視為 **儀表板**，其中提供您 API 管理執行個體的概觀。

![儀表板][api-management-dashboard]

將滑鼠游標移到 **Basic Calculator** 的圖表上，以查看指定時段中 API 使用量的特定度量。

> [!NOTE]
> 如果您在圖表上看不見任何線條，請切換回開發人員入口網站，並對 API 進行一些呼叫，等候片刻，然後返回儀表板。
> 
> 

按一下 [檢視詳細資料]  來檢視 API 的摘要頁面，包括已顯示度量的較大版本。

![Analytics][api-management-mouse-over]

![摘要][api-management-api-summary-metrics]

如需詳細度量和報告，請從左側的 [API 管理] 功能表按一下 [分析]。

![Overview][api-management-analytics-overview]

[分析]  區段具有下列四個索引標籤：

*  提供整體的使用量和健康情況度量，以及名列前茅的開發人員、產品、API 和作業等相關資訊。
*  提供 API 呼叫和頻寬的深入檢視，包括地理區域的呈現。
* **Health** 著重在狀態碼、快取成功率、回應時間，以及 API 與服務回應時間。
* **Activity** 提供的報告依開發人員、產品、API 和作業向下鑽研特定活動。

## <a name="next-steps"> </a>後續步驟
* 了解 [以頻率限制保護您的 API](api-management-howto-product-with-rules.md)。

[Azure Free Trial]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[How to configure notifications and email templates in Azure API Management]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[API Management pricing]: http://azure.microsoft.com/pricing/details/api-management/

[Azure Portal]: https://portal.azure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
