---
title: "將 API 匯入至 Azure API 管理 | Microsoft Docs"
description: "了解如何將 API 及其操作匯入至 Azure API 管理。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 40398b0a-ac2c-43f0-89e1-07e4abbf502f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: c851b88fc1067e65044266d07775717c028e75d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>如何在 Azure API 管理中連同操作一起匯入 API 的定義
在 API 管理中，可手動建立新的 API 並新增作業，或在一個步驟中連同作業一起匯入 API。

您可以使用下列格式來匯入 API 及其操作。

* WADL
* Swagger

本指南示範如何在一個步驟中建立新的 API 並匯入其操作。 如需有關手動建立 API 和新增作業的資訊，請參閱[如何建立 API][How to create APIs] 和[如何將作業新增至 API][How to add operations to an API]。

## <a name="import-api"> </a>匯入 API
API 是在發行者入口網站中建立和設定。 若要存取發行者入口網站，請在您「API 管理」服務的「Azure 入口網站」中按一下 [發行者入口網站]。 如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][Get started with Azure API Management]教學課程中的[建立 API 管理服務執行個體][Create an API Management service instance]。

![發行者入口網站][api-management-management-console]

從左邊的 API 管理 功能表中按一下 API，然後按一下匯入 API。

![Import API][api-management-import-apis]

[匯入 API]  視窗有三個索引標籤，分別對應至三種提供 API 規格的方式。

*  可讓您將 API 規格貼到指定的文字方塊中。
*  可讓您瀏覽並選取含有 API 規格的檔案。
*  可讓您提供指向 API 規格的 URL。

![Import API format][api-management-import-api-clipboard]

提供 API 規格之後，請使用右邊的選項按鈕來指出規格格式。 支援下列格式。

* WADL
* Swagger

接下來，輸入 [Web API URL 尾碼] 。 這會附加至 API 管理服務的基礎 URL 後面。 基礎 URL 是 API 管理服務的每一個執行個體上主控的所有 API 所共有。 API 管理依尾碼來區分 API，因此，特定 API 管理服務執行個體中的每一個 API 必須有唯一的尾碼。

輸入所有的值之後，按一下 [ **儲存** ]，以建立 API 和相關聯的操作。 

> [!NOTE]
> 如需以 Swagger 格式匯入基本計算機 API 的教學課程，請參閱 [在 Azure API 管理中管理您的第一個 API](api-management-get-started.md)。
> 
> 

## <a name="export-api"> </a> 匯出 API
除了匯入新的 API 之外，您還可以從發行者入口網站匯出 API 的定義。 若要這樣做，請從 **API** 的 [摘要] 索引標籤中按一下 [匯出 API]。

![Export API][api-management-export-api]

您可以使用 WADL 或 Swagger 來匯出 API。 選取所需的格式，按一下 [儲存] ，並選擇用來儲存檔案的位置。

![Export API format][api-management-export-api-format]

## <a name="next-steps"> </a>後續步驟
建立 API 並匯入操作之後，您就可以檢閱和設定其他任何設定、將 API 加入至產品，以及發佈它供開發人員使用。 如需詳細資訊，請參閱下列指南。

* [如何設定 API 設定][How to configure API settings]
* [如何建立和發佈產品][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create APIs]: api-management-howto-create-apis.md
[How to configure API settings]: api-management-howto-create-apis.md#configure-api-settings
