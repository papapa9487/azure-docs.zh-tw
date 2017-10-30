---
title: "Azure 搜尋服務中的索引工具 | Microsoft Docs"
description: "將 Azure SQL Database、Azure Cosmos DB 或 Azure 儲存體耙梳，以擷取可搜尋的資料並填入 Azure 搜尋服務索引。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/17/2017
ms.author: heidist
ms.openlocfilehash: c1d393064313ea65d5226378172530115c338f86
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="indexers-in-azure-search"></a>Azure 搜尋服務中的索引子
> [!div class="op_single_selector"]
>
> * [概觀](search-indexer-overview.md)
> * [入口網站](search-import-data-portal.md)
> * [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [Azure Cosmos DB](search-howto-index-documentdb.md)
> * [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)
> * [Azure 資料表儲存體](search-howto-indexing-azure-tables.md)
>

Azure 搜尋服務中的 *索引子* 是一種編目程式，其可從外部資料來源擷取可搜尋的資料和中繼資料，並根據索引和資料來源之間的欄位對欄位對應填入索引。 這種方法有時稱為「提取模型」，因為您不需要撰寫任何程式碼來將資料推送至索引，服務就會自行提取資料。

索引子是以資料來源類型或平台為根據，包含適用於 Azure 上的 SQL Server、Cosmos DB、Azure 資料表儲存體和 Blob 儲存體等的個別索引子。

您可以使用索引子做為擷取資料的唯一手段，或結合使用多項技術 (包含使用索引子) 來僅載入索引中的某些欄位。

您可以依需要執行索引子，也可以依週期性的資料重新整理排程，最多每十五分鐘執行一次。 若想更頻繁地進行更新，則 Azure 搜尋服務和外部資料來源中都必須要有可同時更新資料的發送模型。

## <a name="approaches-for-creating-and-managing-indexers"></a>建立與管理索引子的方法

您可以使用這些方法建立和管理索引子：

* [入口網站 > 匯入資料精靈](search-get-started-portal.md)
* [服務 REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

一開始，會宣布新的索引子作為預覽功能。 預覽功能會在 API (REST 和 .NET) 中引進，然後在准許正式推出之後整合到入口網站中。 如果您正在評估新的索引子，您應該計劃撰寫程式碼。

## <a name="basic-configuration-steps"></a>基本組態步驟
索引子可以提供資料來源特有的功能。 在這方面，索引子或資料來源組態的某些層面會因索引子類型而所有不同。 不過，所有索引子都有共用的的基本組成和需求。 下文涵蓋所有的索引子的通用步驟。

### <a name="step-1-create-a-data-source"></a>步驟 1：建立資料來源
索引子會從保有連接字串或認證等資訊的「資料來源」提取資料。 目前支援下列資料來源：

* [Azure SQL Database 或 Azure 虛擬機器中的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* 適用於所選內容類型的 [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)
* [Azure 資料表儲存體](search-howto-indexing-azure-tables.md)

資料來源和使用資料來源的索引子是各自獨立設定與管理，這表示多個索引子可使用同一個資料來源來一次載入多個索引。

### <a name="step-2-create-an-index"></a>步驟 2：建立索引
索引子能將有關資料擷取的某些工作自動化，不過不包括建立索引。 若要滿足必要條件，您必須擁有預先定義的索引，且欄位必須與外部資料來源中的欄位相符。 如需建構索引的詳細資訊，請參閱 [建立索引 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index)。 如需欄位關聯的說明，請參閱 [Azure 搜尋服務索引子中的欄位對應](search-indexer-field-mappings.md)。

### <a name="step-3-create-and-schedule-the-indexer"></a>步驟 3：建立和排程索引子
索引子定義是指定索引、資料來源和排程的結構。 索引子可以參考另一個服務的資料來源，只要該資料來源來自相同訂用帳戶即可。 如需建構索引的詳細資訊，請參閱 [建立索引子 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)。

## <a name="next-steps"></a>後續步驟
既然您已瞭解基本概念，下一個步驟是檢閱需求和每個資料來源類型特有的工作。

* [Azure SQL Database 或 Azure 虛擬機器中的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)
* [Azure 資料表儲存體](search-howto-indexing-azure-tables.md)
* [使用 Azure 搜尋服務 Blob 索引子編製索引 CSV Blob](search-howto-index-csv-blobs.md)
* [使用 Azure 搜尋服務 Blob 索引子編製索引 JSON Blob](search-howto-index-json-blobs.md)
