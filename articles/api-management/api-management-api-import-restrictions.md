---
title: "在 Azure API 管理 API 匯入的限制和已知問題 | Microsoft Docs"
description: "有關使用 Open API、WSDL 或 WADL 格式匯入 Azure API 管理的已知問題和限制的詳細資料。"
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: apipm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: ac799d66b5038c207413086b0fa71239ff2a332f
ms.contentlocale: zh-tw
ms.lasthandoff: 03/15/2017

---
# <a name="api-import-restrictions-and-known-issues"></a>API 匯入的限制和已知問題
## <a name="about-this-list"></a>關於本清單
儘管我們盡力確保將 API 匯入 Azure API 管理的過程是順暢、沒問題的，偶爾還是要強制加上成功匯入之前需要加以改正的限制或身分識別問題。 這篇文章說明這些項目，依 API 的匯入格式整理說明。

## <a name="open-api"> </a>Open API/Swagger
一般情況下，如果您匯入 Open API 文件時收到錯誤，請務必確認您的文件 - 可使用新 Azure 入口網站中的設計工具 ([設計] - [前端] - [Open API 規格編輯器])，或使用第 3 方工具 (例如 <a href="http://www.swagger.io">Swagger 編輯器</a>)。

* **主機名稱** - 我們需要主機名稱屬性。
* **基本路徑** - 我們需要基本路徑屬性。
* **配置** - 我們需要配置陣列。 

## <a name="wsdl"> </a>WSDL
WSDL 檔案是用來產生 SOAP 傳遞的 API，或做為「SOAP 至 REST」API 的後端。

* **WSDL:Import** - 我們目前不支援使用這個屬性的 API。 客戶應該將匯入的項目合併成一份文件。
* **具有多個部分的訊息** - 目前不支援。
* **WCF wsHttpBinding** 使用 Windows Communication Foundation 建立的 SOAP 服務應該使用 basicHttpBinding - 不支援 wsHttpBinding。
* **MTOM** - 使用 MTOM 的服務「可能」<em></em>可以運作。 目前不提供官方支援。
* 不支援以遞迴方式定義的**遞迴**類型 (例如，參考自己的陣列)。

## <a name="wadl"> </a>WADL
目前沒有任何已知的 WADL 匯入問題。


[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md

