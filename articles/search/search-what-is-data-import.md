---
title: "在 Azure 搜尋服務中上傳資料 | Microsoft Docs"
description: "了解如何將資料上傳至 Azure 搜尋服務中的索引"
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: ashmaka
ms.openlocfilehash: 5a601b75ec67824e72d8736bc3c45f8e1231ca86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="upload-data-to-azure-search"></a>上傳資料至 Azure 搜尋服務
> [!div class="op_single_selector"]
> * [概觀](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

有兩種方法可以在索引中填入資料。 第一種方法是使用 Azure 搜尋服務 [REST API](search-import-data-rest-api.md) 或 [.NET SDK](search-import-data-dotnet.md) 來以手動方式將資料推送到索引中。 第二種方法是[將支援的資料來源指向](search-indexer-overview.md)您的索引，並讓 Azure 搜尋服務自動提取資料。

## <a name="push-data-to-an-index"></a>將資料推送到索引
本方法涉及以程式設計方式將資料傳送至 Azure 搜尋服務以便可供搜尋。 若應用程式需要極低的延遲 (例如，如果您需要讓搜尋作業與動態庫存資料庫保持同步)，推送模式是您的唯一選擇。

您可以使用 [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) 或 [.NET SDK](search-import-data-dotnet.md) 將資料推送到索引。 目前沒有工具可支援透過入口網站推送資料。

這種方法比提取模型更有彈性，因為您可以個別或批次上傳文件 (每個批次最多 1000 個或 16 MB，視何者先到達)。 推送模型也可讓您將文件上傳至 Azure 搜尋服務，無論資料是存放在哪裡。

Azure 搜尋服務認得的資料格式為 JSON，而資料集中的所有文件都必須具有對應至索引結構描述中所定義欄位的欄位。 

## <a name="pull-data-into-an-index"></a>將資料提取到索引
提取模型會將支援的資料來源編目，並自動將資料上傳到您的索引。 在 Azure 搜尋服務中，這項功能是透過索引子來實作，其目前可供 [Blob 儲存體](search-howto-indexing-azure-blob-storage.md)、[表格儲存體](search-howto-indexing-azure-tables.md)、[Azure Cosmos DB](http://aka.ms/documentdb-search-indexer)、[Azure SQL Database 和 Azure VM 上的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) 使用。 

索引子可將索引連接到資料來源 (通常是資料表、檢視或對等結構)，並將來源欄位對應至索引中的對等欄位。 在執行期間，資料列集會自動轉換為 JSON 並載入指定的索引。 所有索引子都支援排程，以便您指定資料重新整理的頻率。 如果資料來源支援索引子，則大部分的索引子都會提供變更追蹤。 除了辨識新文件，索引子還會追蹤現有文件的變更和刪除，讓您不必主動管理索引中的資料。 

索引子功能會在 [Azure 入口網站](search-import-data-portal.md)、[REST API](/rest/api/searchservice/Indexer-operations) 以及 [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations)中出現。 

使用入口網站的優點是 Azure 搜尋服務通常可藉由讀取來源資料集的中繼資料，為您產生預設的索引結構描述。 直到處理索引後，您才可以修改產生的索引，而後只允許不需要重新編製索引的結構描述編輯。 如果您想要進行的變更會直接影響結構描述，您必須重建索引。 

填入索引之後，您可以使用入口網站命令列中的 [搜尋總管] 作為驗證步驟。

## <a name="query-an-index-using-search-explorer"></a>使用搜尋總管查詢索引

對文件上傳執行初步檢查的快速方法是使用入口網站中的 [搜尋總管]。 此總管可讓您查詢索引，而不需撰寫任何程式碼。 搜尋體驗是以預設設定為基礎，例如[簡單語法](/rest/api/searchservice/simple-query-syntax-in-azure-search)和預設 [searchMode 查詢參數](/rest/api/searchservice/search-documents)。 結果會以 JSON 格式傳回，以便您檢查整份文件。

> [!TIP]
> 許多 [Azure 搜尋服務程式碼範例](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)包含內嵌或隨手可得的資料集，並提供簡單的開始使用方式。 入口網站也會提供範例索引子和資料來源 (由稱為 "realestate-us-sample" 的小型不動產資料集所組成)。 當您在範例資料來源上執行預先設定的索引子時，系統會建立索引並隨著文件載入，而後即可在 [搜尋總管] 中或由您撰寫的程式碼查詢索引。