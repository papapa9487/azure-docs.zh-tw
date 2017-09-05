---
title: "Azure Cosmos DB 的資料表 API 簡介 | Microsoft Docs"
description: "了解如何透過受歡迎的 OSS MongoDB API，使用 Azure Cosmos DB 來儲存及查詢大量索引鍵-值資料 (低延遲)。"
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/29/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3ccc3b176df2f0a5d864554a74508292d272bd5a
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB 簡介：資料表 API

[Azure Cosmos DB](introduction.md) 針對 Azure 資料表儲存體服務撰寫且需要下列進階功能的應用程式提供資料表 API (預覽)：[周全全域發佈](distribute-data-globally.md)、全球[專用輸送量](partition-data.md)、第 99 個百分位數的一位數毫秒延遲、保證高可用性，以及[自動次要索引](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)。 這些應用程式可使用資料表 API (不變更程式碼) 來移轉至 Azure Cosmos DB，並且利用進階功能。

我們建議從觀看下列影片開始，Aravind Ramachandran 會在影片中說明如何開始使用 Azure Cosmos DB 的資料表 API。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>進階和標準資料表 API
如果您目前使用 Azure 表格儲存體，移至 Azure Cosmos DB 的「進階資料表」預覽版即可獲得下列優點：

|  | Azure 表格儲存體 | Azure Cosmos DB：表格儲存體 (預覽) |
| --- | --- | --- |
| 延遲 | 快速，但延遲沒有上限 | 一位數毫秒的讀取和寫入延遲，並在世界各地支援任何規模的 <10 毫秒延遲讀取和 <15 毫秒延遲寫入 (第 99 個百分位數) |
| 輸送量 | 變動輸送量模型。 資料表每秒 20,000 個作業的延展性限制 | 高延展性且[每個資料表都有專用的保留輸送量](request-units.md) (由 SLA 支援)。 帳戶沒有輸送量上限，而且支援每個資料表每秒 > 1 千萬個作業 |
| 全域散發 | 具有一個選擇性 HA 可讀取次要讀取區域的單一區域。 您無法起始容錯移轉 | 1 至 30+ 個區域的[一站式全域散發](distribute-data-globally.md)，隨時隨地支援[自動和手動容錯移轉](regional-failover.md) |
| 編製索引 | PartitionKey 和 RowKey 只有主要索引。 沒有次要索引 | 對所有屬性自動執行完整的編製索引，但沒有索引管理 |
| 查詢 | 查詢執行作業會使用主索引鍵的索引，要不然會進行掃描。 | 查詢可以利用自動編製屬性的索引，加快查詢速度。 Azure Cosmos DB 的資料庫引擎能夠支援彙總、地理空間以及排序。 |
| 一致性 | 主要區域達到「強一致性」，次要地區達到「最終一致性」 | [五個定義完善的一致性層級](consistency-levels.md)，可根據您應用程式的需求，進行可用性、延遲、輸送量及一致性的取捨 |
| 價格 | 儲存體最佳化  | 輸送量最佳化 |
| SLA | 99.9% 的可用性 | 單一區域內可達 99.99% 的可用性，並且能夠新增更多區域來提高可用性。 在一般可用性上達到[業界頂尖的全面性 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) |

## <a name="how-to-get-started"></a>如何開始使用

在 [Azure 入口網站](https://portal.azure.com)中建立 Azure Cosmos DB 帳戶，並開始使用[使用 .NET 的資料表 API 快速入門](create-table-dotnet.md)。 

## <a name="next-steps"></a>後續步驟

以下是可讓您快速入門的一些指標：
* [使用資料表 API 來建置 .NET 應用程式](create-table-dotnet.md)
* [在 .NET 中利用資料表 API 進行開發](tutorial-develop-table-dotnet.md)
* [使用資料表 API 來查詢資料表資料](tutorial-query-table.md)
* [如何使用資料表 API 來設定 Azure Cosmos DB 全域散發](tutorial-global-distribution-table.md)
* [適用於 .NET 的 Azure Cosmos DB 資料表 API SDK](table-sdk-dotnet.md)

