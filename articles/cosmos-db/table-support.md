---
title: "Azure Cosmos DB 的 Azure 表格儲存體支援 | Microsoft Docs"
description: "了解 Azure Cosmos DB 資料表 API 和 Azure 儲存體資料表如何一起運作。"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: c0c8f1aee75c4ee5cc35758b71ef573637fd3edd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>使用 Azure Cosmos DB 資料表 API 和 Azure 表格儲存體進行開發

Azure Cosmos DB 資料表 API 和 Azure 表格儲存體共用同一個資料表資料模型，並透過本身的 SDK 公開相同的建立、刪除、更新和查詢作業。 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>使用 Azure Cosmos DB 資料表 API 進行開發

此時，[Azure Cosmos DB 資料表 API](table-introduction.md) 有四個 SDK 可供開發使用： 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK。 此文件庫具有與公用 [Windows Azure 儲存體 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 相同的類別和方法簽章，但它也可以使用資料表 API 來連線到 Azure Cosmos DB 帳戶。 
- [Python SDK](table-sdk-python.md)。 新的 Azure Cosmos DB Python SDK 是唯一支援 Azure 表格儲存體 (Python) 的 SDK。 此 SDK 與 Azure 表格儲存體和 Azure Cosmos DB 資料表 API 連線。
- [Java SDK](table-sdk-java.md)。 此 Azure 儲存體 SDK 能夠使用資料表 API 連線至 Azure Cosmos DB 帳戶。
- [Node.js SDK](table-sdk-nodejs.md)。 此 Azure 儲存體 SDK 能夠使用資料表 API 連線至 Azure Cosmos DB 帳戶。

[常見問題集：使用資料表 API 開發](faq.md#develop-with-the-table-api)一文提供使用資料表 API 的其他資訊。

## <a name="developing-with-the-azure-table-storage"></a>使用 Azure 表格儲存體進行開發

[Azure 表格儲存體](table-storage-overview.md)有許多可用的 SDK 和教學課程，所有這些現在均由 [Azure 表格儲存體](table-storage-overview.md)一節提供。 這些發行項正隨著 Azure 表格儲存體 SDK 和 Azure Cosmos DB 資料表 API 之間達到互通性而更新。  





