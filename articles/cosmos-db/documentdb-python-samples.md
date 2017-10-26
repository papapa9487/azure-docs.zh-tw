---
title: "適用於 Azure Cosmos DB 的 DocumentDB API Python 範例 | Microsoft Docs"
description: "在 GitHub 上找到適用於 Azure Cosmos DB 中一般工作 (包括 CRUD 作業) 的 Python 範例。"
keywords: "Python 範例"
services: cosmos-db
author: moderakh
manager: jhubbard
editor: monicar
documentationcenter: python
ms.assetid: 7f4f8db3-e9db-4645-92ef-7819d486a349
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2016
ms.author: moderakh
ms.openlocfilehash: 98c00f23a3ce7569c9cc85a62840500cc50ed3a1
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2017
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python 範例
> [!div class="op_single_selector"]
> * [.NET 範例](documentdb-dotnet-samples.md)
> * [Node.js 範例](documentdb-nodejs-samples.md)
> * [Python 範例](documentdb-python-samples.md)
> * [Azure 程式碼範例庫](https://azure.microsoft.com/documentation/samples/?service=documentdb&ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
> 
> 

[azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) GitHub 存放庫中包含可對 Azure Cosmos DB 資源執行 CRUD 作業和其他常見作業的範例解決方案。 本文提供：

* 每個 Python 範例專案檔中各項工作的連結。 
* 相關 API 參考內容的連結。

**必要條件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- 您可以 [啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：您的 Visual Studio 訂用帳戶每個月都會提供額度，供您用在 Azure 付費服務。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

您也需要 [Python SDK](documentdb-sdk-python.md)。 
   
   > [!NOTE]
   > 每個範例都各自獨立，自己設定，並自行清理。 據此，這些範例對 [document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) 發出多個呼叫。 每當執行此動作時，即會根據所建立之集合的效能層，對您的訂用帳戶計入一小時的使用量費用。 
   > 
   > 

## <a name="database-examples"></a>資料庫範例
[DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) 專案的 [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) 檔案說明如何執行下列工作。

| Task | API 參考資料 |
| --- | --- |
| [建立資料庫](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) |[document_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [查詢帳戶中的資料庫](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) |[document_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [依識別碼讀取資料庫](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) |[document_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [列出帳戶的資料庫](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) |[document_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [刪除資料庫](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) |[document_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |

## <a name="collection-examples"></a>集合範例
[CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) 專案的 [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) 檔案說明如何執行下列工作。

| Task | API 參考資料 |
| --- | --- |
| [建立集合](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) |[document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [讀取資料庫中所有集合的清單](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) |[document_client.ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [依識別碼取得集合](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) |[document_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [取得集合的效能層](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) |[DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [變更集合的效能層](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) |[document_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [刪除集合](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) |[document_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |

