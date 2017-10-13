---
title: "Azure Cosmos DB 簡介：適用於 MongoDB 的 API | Microsoft Docs"
description: "了解如何透過常用的 OSS MongoDB API，使用 Azure Cosmos DB 來儲存及查詢大量 JSON 文件 (低延遲)。"
keywords: "MongoDB 是什麼"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: anhoh
ms.openlocfilehash: 4dbf91a3c1d6a287d7337647f9e059566c7ddbe5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB 簡介：適用於 MongoDB 的 API

[Azure Cosmos DB](../cosmos-db/introduction.md) 是 Microsoft 全域散發的多模型資料庫服務，適用於任務關鍵性應用程式。 Azure Cosmos DB 提供[一站式全域散發](distribute-data-globally.md)、全球[彈性調整的輸送量和儲存體](partition-data.md)、達到第 99 個百分位數的個位數毫秒延遲、[五個定義完善的一致性層級](consistency-levels.md)，以及保證的高可用性，全部都由[領先業界的 SLA (英文)](https://azure.microsoft.com/support/legal/sla/cosmos-db/) 所支援。 Azure Cosmos DB 會[自動編製資料的索引](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)，您不需要處理結構描述和索引管理。 它是多重模型，可支援文件、索引鍵/值、圖表和單欄式資料模型。 

![Azure Cosmos DB：MongoDB API](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Cosmos DB 資料庫可作為針對 [MongoDB](https://docs.mongodb.com/manual/introduction/) 所撰寫之應用程式的資料存放區。 這表示，使用現有的[驅動程式](https://docs.mongodb.org/ecosystem/drivers/)，針對 MongoDB 所撰寫的應用程式現在可與 Cosmos DB 通訊，並使用 Cosmos DB 資料庫而非 MongoDB 資料庫。 在許多情況下，您只要變更連接字串，就可以從使用 MongoDB 切換到 Cosmos DB。 您可以使用這項功能，輕鬆地在 Azure 雲端建置及執行 MongoDB 資料庫應用程式，利用 Azure Cosmos DB 的全域散發和[領先業界的完整 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)，同時繼續使用 MongoDB 的熟悉技能和工具。


## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>於 MongoDB 應用程式使用 Azure Cosmos DB 有何好處？

**可彈性調整的輸送量和儲存體：**輕鬆相應增加或相應減少 MongoDB 資料庫，以符合您應用程式的需求。 您的資料會儲存在固態硬碟 (SSD) 中以便獲得可預測的低延遲。 Cosmos DB 支援的 MongoDB 集合可調整為幾乎不受限制的儲存體大小和佈建輸送量。 隨著應用程式的成長，您可以依據可預測的效能彈性且順暢地調整 Cosmos DB。 

**多重區域複寫**︰Cosmos DB 會自動將您的資料複寫至與您 MongoDB 帳戶相關聯的所有區域，讓您開發需要全域存取資料的應用程式，也能在一致性、可用性與效能之間做出取捨，而且全都有相對應的保證。 Cosmos DB 利用多路連接 API 提供自動的區域性容錯移轉，還能夠彈性調整世界各地的輸送量和儲存體。 請參閱[將資料分散到全球](distribute-data-globally.md)以深入了解。

**MongoDB 相容性**︰您可以使用現有的 MongoDB 專業知識、應用程式程式碼和工具。 您可以開發使用 MongoDB 的應用程式，並使用完全受管理的全域分散式 Cosmos DB 服務來部署這些應用程式。

**不需要任何伺服器管理工作**︰您不需要管理和調整 MongoDB 資料庫。 Cosmos DB 是完全受管理的服務，這表示您不需要自己管理任何基礎結構或虛擬機器。 Cosmos DB 可在 30 個以上的 [Azure 區域](https://azure.microsoft.com/regions/services/)中使用。

**可調整的一致性層級：**提供五個定義完善的一致性層級可選擇，讓您能在一致性與效能之間做出最好的取捨。 針對查詢和讀取作業，Cosmos DB 提供五個不同的一致性層級：強式、限定過期、工作階段、一致的前置和最終。 這些細微且定義完善的一致性層級可讓您在一致性、可用性與延遲三者間做出合理取捨。 深入了解[使用一致性層級將可用性和效能最大化](consistency-levels.md)。

**自動編製索引**：根據預設，Cosmos DB 會自動為 MongoDB 資料庫中文件內的所有屬性編製索引，且不預期或需要任何結構描述或建立次要索引。

**企業級** - Azure Cosmos DB 支援多個本機複本，在面對本機和區域故障時可提供 99.99% 的可用性和資料保護。 Azure Cosmos DB 有企業級的[合規性認證 (英文)](https://www.microsoft.com/trustcenter) 和安全性功能。 

在這段 Azure Friday 影片中，和 Scott Hanselman 與 Azure Cosmos DB 工程總經理 Kirill Gavrylyuk 一起深入了解。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Introducing-Azure-Cosmos-DB/player]
> 

## <a name="how-to-get-started"></a>如何開始使用

請遵循 MongoDB 快速入門建立 Cosmos DB 帳戶，然後移轉您現有的 Mongo DB 應用程式以使用 Cosmos DB，或建置一個新的應用程式：

* [移轉現有的 Node.js MongoDB Web 應用程式](create-mongodb-nodejs.md)。
* [使用 .NET 和 Azure 入口網站建置 MongoDB API Web 應用程式](create-mongodb-dotnet.md)
* [使用 Java 和 Azure 入口網站建置 MongoDB API 主控台應用程式](create-mongodb-java.md)

## <a name="next-steps"></a>後續步驟

Azure Cosmos DB 的 MongoDB API 相關資訊已整合至整體 Azure Cosmos DB 文件，但以下有幾個線索可讓您開始使用︰

* 依照下列[連線到 MongoDB 帳戶](connect-mongodb-account.md)教學課程，了解如何取得您的帳戶連接字串資訊。
* 依照[使用 MongoChef 搭配 Azure Cosmos DB](mongodb-mongochef.md) 教學課程來了解如何在 MongoChef 中建立 Azure Cosmos DB 資料庫和 MongoDB 應用程式之間的連線。
* 依照[將資料移轉至具有 MongoDB 通訊協定支援的 Azure Cosmos DB](mongodb-migrate.md) 教學課程，將資料匯入適用於 MongoDB 的 API資料庫。
* 使用 [Robomongo](mongodb-robomongo.md) 連線至 API for MongoDB 帳戶。
* 使用 [GetLastRequestStatistics 命令和 Azure 入口網站計量](request-units.md#GetLastRequestStatistics)，了解您的作業使用多少 RU。
* 了解如何[設定全球分散式應用程式的讀取喜好設定](../cosmos-db/tutorial-global-distribution-mongodb.md)。
