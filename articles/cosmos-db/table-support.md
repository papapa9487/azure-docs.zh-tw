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
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 1513fc53501f1cfec93134841fbef9a8552dd43c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>使用 Azure Cosmos DB 資料表 API 和 Azure 表格儲存體進行開發

Azure Cosmos DB 資料表 API 和 Azure 表格儲存體共用同一個資料表資料模型，並透過本身的 SDK 公開相同的建立、刪除、更新和查詢作業。 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>使用 Azure Cosmos DB 資料表 API 進行開發

此時，[Azure Cosmos DB 資料表 API](table-introduction.md) 有一個 .NET SDK，也就是 [Windows Azure 儲存體進階資料表 (預覽)](https://aka.ms/premiumtablenuget)。 此文件庫具有與公用 [Windows Azure 儲存體 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 相同的類別和方法簽章，但它也可以使用資料表 API (預覽) 來連線到 Azure Cosmos DB 帳戶。 如需使用 Azure Cosmos DB 資料表 API 的快速入門和教學課程，請參閱下列文章：
- 快速入門：[Azure Cosmos DB：使用資料表 API 來建置 .NET 應用程式](create-table-dotnet.md)
- 教學課程：[Azure Cosmos DB：使用 .NET 搭配資料表 API 進行開發](tutorial-develop-table-dotnet.md)

[常見問題集：使用資料表 API 開發](faq.md#develop-with-the-table-api-preview)一文提供使用資料表 API 的其他資訊。

## <a name="developing-with-the-azure-table-storage"></a>使用 Azure 表格儲存體進行開發

[Azure 表格儲存體](table-storage-overview.md)有許多可用的 SDK 和教學課程，所有這些現在均由 [Azure 表格儲存體](table-storage-overview.md)一節提供。 這些發行項正隨著 Azure 表格儲存體 SDK 和 Azure Cosmos DB 資料表 API 之間達到互通性而更新。  





