---
title: "使用 Azure 搜尋服務 Blob 索引子編製索引 CSV Blob | Microsoft Docs"
description: "了解如何使用 Azure 搜尋服務編製索引 CSV Blob"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: ed3c9cff-1946-4af2-a05a-5e0b3d61eb25
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/15/2016
ms.author: eugenesh
ms.openlocfilehash: af9da85c37211d2436c23cc05400031c661ef51e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>使用 Azure 搜尋服務 Blob 索引子編製索引 CSV Blob
根據預設， [Azure 搜尋服務 Blob 索引子](search-howto-indexing-azure-blob-storage.md) 會將分隔符號文字 Blob 剖析為單一的文字區塊。 不過，使用包含 CSV 資料的 blob，您通常想要將 blob 中的每一行當做個別文件。 例如，假設有下列分隔符號文字： 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

您可能想要將它剖析為 2 個文件，每個都包含 [識別碼]、[發佈日期] 和 [標籤] 欄位。

在本文中，您將深入了解如何使用 Azure 搜尋服務 blob 索引子來剖析 CSV blob： 

> [!IMPORTANT]
> 這項功能目前為預覽版本。 僅適用於使用 **2015-02-28-Preview**版本的 REST API。 請記住，預覽 API 是針對測試與評估，不應該用於生產環境。 
> 
> 

## <a name="setting-up-csv-indexing"></a>設定 CSV 編製索引
若要對 CSV blob 編製索引，請使用 `delimitedText` 剖析模式建立或更新索引子定義︰  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

如需建立索引子 API 的詳細資訊，請參閱 [建立索引子](search-api-indexers-2015-02-28-preview.md#create-indexer)。

`firstLineContainsHeaders` 指出每個 blob 的第一個 (非空白) 行包含標頭。
如果 blob 不包含初始的標頭行，應該在索引子組態中指定標頭︰ 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

目前，只支援 UTF-8 編碼。 此外，僅支援逗號 `','` 字元做為分隔符號。 如果您需要支援其他編碼或分隔符號，請在 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search)讓我們知道。

> [!IMPORTANT]
> 當您使用分隔符號文字剖析模式時，Azure 搜尋服務會假設您的資料來源中所有 Blob 都為 CSV。 如果您需要支援在相同的資料來源中混用 CSV 和非 CSV Blob，請在 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search)讓我們知道。
> 
> 

## <a name="request-examples"></a>要求範例
總而言之，以下是完整的承載範例。 

資料來源： 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

索引子：

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>協助我們改進 Azure 搜尋服務
如果您有功能要求或改進的想法，請在我們的 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)與我們連絡。

