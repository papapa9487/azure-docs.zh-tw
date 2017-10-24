---
title: "自訂連接器概觀 - Azure Logic Apps | Microsoft Docs"
description: "有關建立自訂連接器來支援及擴充整合案例的概觀"
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
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>自訂連接器概觀

只要使用 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)[Microsoft Flow](https://flow.microsoft.com)或 [Microsoft PowerApps](https://powerapps.microsoft.com)，您無須撰寫任何程式碼，即可建置工作流程和應用程式。 為了協助您整合資料與商務程序，這些服務不僅為 Microsoft 服務和產品 (例如 Azure 和 SQL Server)，也為其他服務 (例如 GitHub、Salesforce、Twitter 等)，提供[超過 100 種連接器](../connectors/apis-list.md)。 

不過，有時您可能會想要呼叫預先建置之連接器所未提供的 API、服務及系統。 為了針對您使用者的業務和產能需求提供更多量身訂做的案例，您可以使用他們自己的觸發程序和動作來建置「自訂連接器」。
自訂連接器可擴充您服務或產品的整合、範圍、可探索性及使用，有助於提升和加速客戶採用率。

例如，下圖說明 Web API、為該 API 建立的自訂 Logic Apps 連接器及與該 API 搭配運作之邏輯應用程式之間，藉由使用自訂連接器所進行的互動：

![Web API、自訂連接器及邏輯應用程式的概念性概觀](./media/custom-connector-overview/custom-connector-conceptual.png)

本概觀簡述一般概要工作，這些工作可用來建立、保護、註冊和使用連接器，以及視需要共用或認證連接器：

![自訂連接器撰寫步驟](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>必要條件

若要從頭到尾建置您的連接器，您需要下列項目：

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以從[免費 Azure 帳戶](https://azure.microsoft.com/free/)開始著手。 否則，請註冊[隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

* 具備某種類型之已驗證存取權的 RESTful API。 如需詳細資訊，請參閱[從 Web API 建立自訂連接器](../logic-apps/custom-connector-build-web-api-app-tutorial.md)。 如需了解您可以針對連接器的觸發程序和動作使用的模式，請參閱[建立您可以從邏輯應用程式工作流程呼叫的自訂 API](../logic-apps/logic-apps-create-api-app.md)。

* 以下任何項目：

  * [OpenAPI 2.0 檔案](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)(以前稱為 Swagger)
  * 可開啟 OpenAPI 定義的 URL
  * 您 API 的 [Postman 集合](../logic-apps/custom-connector-api-postman-collection.md) 

  如果您沒有上述任何項目，我們會為您提供指引。

* 選擇性：用來作為您自訂連接器之圖示的影像

## <a name="1-build-your-restful-api"></a>1.建置您的 RESTful API

就技術上來說，連接器是一個包裝 REST API 的包裝函式，此 API 以 OpenAPI (以前稱為 Swagger) 規格為基礎，可讓您的基礎服務與 Logic Apps、Flow 或 PowerApps 進行通訊。 因此，首先您需要一個可完整運作的 API，才能建立自訂連接器。 

您可以針對 API 使用任何語言和平台。 就 Microsoft 技術而言，建議使用下列其中一個平台：

* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure Web Apps](https://azure.microsoft.com/services/app-service/web/)
* [Azure API 應用程式](https://azure.microsoft.com/services/app-service/api/)

例如，本教學課程將說明[如何從 Web API 建置自訂連接器](../logic-apps/custom-connector-build-web-api-app-tutorial.md)。 如需了解您可以針對連接器的觸發程序和動作使用的模式，請參閱[建立您可以從邏輯應用程式工作流程呼叫的自訂 API](../logic-apps/logic-apps-create-api-app.md)。

## <a name="2-secure-your-api"></a>2.保護您的 API

您可以針對 API 和連接器使用下列驗證標準：

   * [OAuth 2.0](https://oauth.net/2/)，包括 [Azure Active Directory](https://azure.microsoft.com/develop/identity/) 或特定服務，例如 Dropbox、GitHub 及 SalesForce
   * Generic OAuth 2.0
   * [基本驗證](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [API 金鑰](https://swagger.io/docs/specification/authentication/api-keys/)

您可以在 Azure 入口網站中為 API 設定 Azure Active Directory (Azure AD)，這樣您就不需要透過程式碼實作驗證。 或者，您可以透過您的 API 程式碼要求並強制執行驗證。 

如需詳細資訊，請依照下列適當的教學課程進行操作：

* [Logic Apps：保護您的自訂連接器](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Flow：保護您的自訂連接器](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [PowerApps：保護您的自訂連接器](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>3.描述您的 API 

假設您的 API 具備某種類型的已驗證存取權，您必須描述 API 的介面和作業，這樣 Logic Apps、Flow 或 PowerApps 才能與您的 API 進行通訊。
請使用下列其中一種業界標準定義：

* [OpenAPI 2.0 檔案](https://swagger.io/)：您可以從以現有的 OpenAPI 檔案進行建置開始著手。

  如果您不熟悉 OpenAPI，請瀏覽 swagger.io 網站上的[開始使用 Swagger](http://swagger.io/getting-started/) \(英文\)。
  如需範例 OpenAPI 檔案，請參閱[文字分析 API 文件](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure) \(英文\)。 

* Postman 集合：此集合會自動為您產生 OpenAPI 檔案。 當您還沒有 OpenAPI 檔案且不想要建立此檔案時，仍然可以藉由使用 Postman 集合來輕鬆建立自訂連接器。

  如果您不熟悉 Postman，請從其網站[安裝 Postman 應用程式](https://www.getpostman.com/apps) \(英文\)。 如需詳細資訊，請參閱[使用 Postman 來描述自訂連接器](../logic-apps/custom-connector-api-postman-collection.md)。

> [!IMPORTANT]
> 您的檔案大小必須小於 1 MB。

在幕後，Logic Apps、Flow 及 PowerApps 最終會使用 OpenAPI、剖析 Postman 集合，然後將該集合轉譯成 OpenAPI 定義檔。 雖然 OpenAPI 2.0 和 Postman 集合使用不同的格式，但兩者都是描述 API 之作業和參數且非特定語言專屬的機器可讀文件。 您可以根據 API 所使用的語言和平台，從各種工具產生這些文件。 您也可以在註冊連接器時，建置 OpenAPI 檔案。

例如，您可以從任何 REST API 端點建立 OpenAPI 檔案或 Postman 集合，這些端點包括：

* 公開可用的連接器，例如 [Spotify](https://developer.spotify.com/)、[Slack](https://api.slack.com/)、[Rackspace](http://docs.rackspace.com/) 等

* 您所建立並部署到任何雲端裝載提供者 (例如 Azure、Heroku、Google Cloud 等) 的 API

* 已部署到您網路的自訂企業營運 API (但前提是該 API 在公用網際網路上已公開)

## <a name="4-register-your-connector"></a>4.註冊您的連接器

註冊程序可協助 Logic Apps、Flow 或 PowerApps 了解您 API 的特性，包括描述、所需的驗證及作業 (包括每個作業的參數和輸出)。 當您開始註冊程序時，可以提供 OpenAPI 檔案或 Postman 集合，這會自動在註冊精靈中填入中繼資料欄位。 您可以隨時編輯這些欄位的值。

![描述您的 API](./media/custom-connector-overview/choose-api-definition-file.png)

若要註冊您的連接器，請依照適當的教學課程進行操作：

* [Logic Apps：註冊您的連接器](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow：註冊您的連接器](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps：註冊您的連接器](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5.在邏輯應用程式、流程或應用程式中使用您的連接器 

您可以像使用 Microsoft Managed 連接器一樣來使用您的連接器。 例如，在邏輯應用程式工作流程中，新增您的自訂連接器以便建立與 API 的連線，然後像呼叫 Microsoft Managed 連接器的作業一樣來呼叫 API 提供的任何作業。

## <a name="6-share-your-connector"></a>6.共用您的連接器 

您可以像在 Logic Apps、Flow 或 PowerApps 中共用資源一樣，與組織中的使用者共用您的連接器。 雖然共用是選擇性的，但您可能會有想要將連接器與其他使用者共用的情況。

已註冊但未經認證之自訂連接器的運作方式與 Microsoft Managed 連接器類似，但「只有」該連接器的作者，以及在部署邏輯應用程式的區域中，就這些應用程式而言具有相同 Azure Active Directory 租用戶和 Azure 訂用帳戶的使用者，才可以看見和使用這些自訂連接器。 下一步將討論如何將您的連接器與這些界線以外的外部使用者共用，例如與所有 Logic Apps、Flow 及 PowerApps 使用者共用。

> [!IMPORTANT]
> 如果您共用連接器，其他使用者便可能開始倚賴該連接器。 
> ***刪除連接器會刪除與該連接器的所有連線。***

* [Logic Apps：共用您的連接器](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow：共用您的連接器](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps：共用您的連接器](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7.認證您的連接器

若要視需要將您的連接器與 Logic Apps、Flow 及 PowerApps 中的所有使用者共用，請提交您的連接器以進行 Microsoft 認證。 此程序會先檢閱您的連接器、檢查技術和內容合規性，以及針對 Logic Apps、Flow 及 PowerApps 驗證功能，然後 Microsoft 才能發佈您的連接器。 了解[如何提交您的連接器以進行 Microsoft 認證](../logic-apps/custom-connector-submit-certification.md)。

## <a name="get-support"></a>取得支援

* 如需有關上線和開發的支援，請傳送電子郵件給 [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)。Microsoft 會積極監控此帳戶來了解開發人員問題和疑難，然後將它們轉交給適當的小組。 

* 如需常見問題集，請參閱[自訂連接器常見問題集](../logic-apps/custom-connector-faq.md)

## <a name="next-steps"></a>後續步驟

* [從 Web API 建置自訂連接器](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [設定自訂連接器的驗證](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [使用 Postman 集合來描述自訂連接器](../logic-apps/custom-connector-api-postman-collection.md)
* [提交自訂連接器以進行 Microsoft 認證](../logic-apps/custom-connector-submit-certification.md)
