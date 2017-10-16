---
title: "Azure Cosmos DB 圖形 API 簡介 | Microsoft Docs"
description: "了解如何使用 Azure Cosmos DB 並透過 Apache TinkerPop 的 Gremlin 圖形查詢語言，以低延遲的方式儲存、查詢和周遊巨大圖形。"
services: cosmos-db
author: dennyglee
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2017
ms.author: denlee
ms.openlocfilehash: b6cc51ad2bab294035f3ed8f300423d1d320df1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Azure Cosmos DB：圖形 API 簡介

[Azure Cosmos DB](introduction.md) 是 Microsoft 推出的全域散發多模型資料庫服務，適用於任務關鍵性應用程式。 Azure Cosmos DB 提供下列功能，這些功能都有[領先業界的 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) 作為後盾：

* [周全且立即可用的全域分散式資料庫](distribute-data-globally.md)
* 在全球[彈性調整輸送量與儲存體](partition-data.md)
* 99 百分位數的單一數字毫秒延遲
* [五個定義完善的一致性層級](consistency-levels.md)
* 保證高可用性 

Azure Cosmos DB 會[自動編製資料的索引](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)，您不需要處理結構描述和索引管理。 它是多重模型，可支援文件、索引鍵/值、圖表和單欄式資料模型。

我們建議您觀看下列影片，在此影片中，Kirill Gavrylyuk 會說明如何在 Azure Cosmos DB 上開始使用圖表：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Graphs-with-Azure-Cosmos-DB-Gremlin-API/player]
> 
> 

Azure Cosmos DB 圖形 API 提供：

- 圖形模型化。
- 周遊 API。
- 周全的全域發佈。
- 可彈性調整的儲存體和輸送量，小於 10 毫秒的讀取延遲和低於 15 毫秒 (第 99 個百分位數)。
- 透過立即查詢可用性自動編製索引。
- 可調式一致性層級。
- 完整的 SLA (包括 99.99% 可用性)。

若要查詢 Azure Cosmos DB，您可以使用 [Apache TinkerPop](http://tinkerpop.apache.org) 圖形周遊語言 [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)，或是其他 TinkerPop 相容的圖形系統 (如 [Apache Spark GraphX](spark-connector-graph.md))。

本文章提供 Azure Cosmos DB 圖形 API 的概觀，並說明如何使用它來儲存包含數十億個頂點和邊緣的巨大圖形。 您可以在幾毫秒延遲的情況下查詢圖形，並輕鬆地發展圖形結構和結構描述。

## <a name="graph-database"></a>圖形資料庫
實務上的資料會自然連線。 傳統的資料模型化著重於實體。 但對於許多應用程式來說，建立模型，或以自然方式建立實體和關聯性之模型的需求必不可少。

[圖形](http://mathworld.wolfram.com/Graph.html)是由[頂點](http://mathworld.wolfram.com/GraphVertex.html)和[邊緣](http://mathworld.wolfram.com/GraphEdge.html)組成的結構。 頂點和邊緣的屬性數量不限。 頂點代表特定的物件，例如人員、地點或事件。 邊緣代表頂點之間的關聯性。 比方說，某個人可能會知道其他人、參與某個事件，以及在最近前往某個位置。 屬性表達頂點和邊緣的相關資訊。 屬性的例子包括具有名稱、年齡的頂點，以及具有時間戳記和/或加權的邊緣。 更正式的說，這種模型稱為[屬性圖表](http://tinkerpop.apache.org/docs/current/reference/#intro)。 Azure Cosmos DB 支援屬性圖表模型。

例如，下圖中的範例圖表顯示人員、行動裝置、興趣和作業系統之間的關聯性：

![顯示人員、裝置和興趣的範例資料庫](./media/graph-introduction/sample-graph.png)

圖表適合用來了解科學、技術和商務領域中的各種資料集。 圖表資料庫可讓您自然又有效率地模型化和儲存圖表，很適合在許多案例中使用。 圖表資料庫通常是 NoSQL 資料庫，因為這些使用案例通常也需要結構描述彈性及快速的反覆運算。

圖表提供一種新穎又強大的資料模型化技術。 但這一點本身尚不足以構成使用圖表資料庫的理由。 在許多涉及圖表周遊的使用案例和模式中，圖表遠遠勝過傳統的 SQL 和 NoSQL 資料庫。 在周遊多個關聯性時，例如好友的好友，此效能差異會更明顯。

您可以結合圖表資料庫所提供的快速周遊和圖表演算法，例如深度優先搜尋、廣度優先搜尋、Dijkstra 的演算法等等，解決各種領域的問題，例如社交網路、內容管理、地理空間和推薦。

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>行星級圖表與 Azure Cosmos DB
Azure Cosmos DB 是一種完全受管理的圖表資料庫，提供全域散發、彈性調整的儲存體和輸送量、自動編製索引和查詢、可調整的一致性等級，而且支援 TinkerPop 標準。

![Azure Cosmos DB 圖表架構](./media/graph-introduction/cosmosdb-graph-architecture.png)

Azure Cosmos DB 提供以下有別於市場上其他圖表資料庫的功能：

* 可彈性調整的輸送量和儲存體

 實務上的圖表需要調整超過單一伺服器的產能。 Azure Cosmos DB 可讓您順暢地在多部伺服器之間調整圖表。 您也可以單獨根據存取模式來自行調整圖表的輸送量。 Azure Cosmos DB 支援的圖表資料庫可調整為幾乎不受限制的儲存體大小和佈建輸送量。

* 多重區域複寫

 Azure Cosmos DB 能以透明方式將圖表資料複寫到與帳戶相關聯的所有區域。 複寫可讓您開發需要全域資料存取權的應用程式。 您需要在一致性、可用性、效能等領域和對應擔保之間做出取捨。 Azure Cosmos DB 能透過多路連接 API 提供透明的區域性容錯移轉。 您可以全球性地彈性調整輸送量和儲存體。

* 以熟悉的 Gremlin 語法快速查詢和周遊

 儲存異質頂點和邊緣，並透過熟悉的 Gremlin 語法查詢這些文件。 Azure Cosmos DB 採用高度並行、無鎖定、記錄結構化索引技術，自動編製所有內容的索引。 此功能讓您不需要指定結構描述提示、次要索引或檢視，就能進行大量且即時的查詢和周遊。 深入了解[使用 Gremlin 查詢圖形](gremlin-support.md)。

* 受到完整管理

 Azure Cosmos DB 能消除資料庫和電腦資源的管理需求。 做為受到完整管理的 Microsoft Azure 服務，您不需要管理虛擬機器、部署和設定軟體、管理調整，或處理複雜的資料層升級。 每個圖表都會自動備份，以防區域性失敗。 您可以輕鬆地新增 Azure Cosmos DB 帳戶，並在需要時佈建容量，將精力投注在應用程式，不用浪費時間來操作和管理資料庫。

* 自動編製索引

 根據預設，Azure Cosmos DB 會自動為圖表中節點和邊緣內的屬性編製索引，而不要求或需要任何結構描述或建立次要索引。

* Apache TinkerPop 相容性

 Azure Cosmos DB 原生支援開放原始碼 Apache TinkerPop 標準，並可與其他支援 TinkerPop 的圖表系統整合。 因此，您可以輕鬆地從另一個圖表資料庫移轉，例如 Titan 或 Neo4j，或搭配圖表分析架構一起使用 Azure Cosmos DB，例如 [Apache Spark GraphX](spark-connector-graph.md)。

* 可調式一致性層級

 提供五個定義完善的一致性層級可選擇，讓您能在一致性與效能之間做出最好的取捨。 針對查詢和讀取作業，Azure Cosmos DB 提供五個不同的一致性層級：強式、限定過期、工作階段、一致的前置和最終。 這些細微且定義完善的一致性等級，可讓您在一致性、可用性與延遲三者間做出合理取捨。 深入了解 [使用一致性層級以最大化 DocumentDB 中的可用性和效能](consistency-levels.md)。

Azure Cosmos DB 也能在相同的容器/資料庫內使用多個模型，例如文件和圖表。 您可以使用文件集合來同時儲存圖表資料與文件。 您可以使用 JSON 的 SQL 查詢和 Gremlin 查詢，查詢與圖表相同的資料。

## <a name="get-started"></a>開始使用
您可以透過 Azure 命令列介面 (CLI)、Azure PowerShell 或 Azure 入口網站，搭配圖形 API 的支援，建立 Azure Cosmos DB 帳戶。 建立之後，Azure 入口網站會提供像 `https://<youraccount>.graphs.azure.com` 一樣的服務端點，提供 Gremlin 的 WebSocket 前端。 您可以設定 TinkerPop 相容性工具 (例如 [Gremlin 主控台](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console)) 連線至此端點，然後在 Java、Node.js 或任何 Gremlin 用戶端驅動程式中建置應用程式。

下表顯示您可以運用在 Azure Cosmos DB 上的常用 Gremlin 驅動程式︰

| 下載 | 文件 |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.js](https://www.npmjs.com/package/gremlin) |[Github 上的 Gremlin-JavaScript](https://github.com/jbmusso/gremlin-javascript) |
| [Gremlin 主控台](https://tinkerpop.apache.org/downloads.html) |[TinkerPop 文件](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

Azure Cosmos DB 也透過 NuGet 提供 .NET 程式庫，其中包含以 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 為基礎的 Gremlin 擴充方法。 此程式庫提供「同處理序」Gremlin 伺服器，可用來直接連線至 DocumenDB 資料分割。

| 下載 | 文件 |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

[Azure Cosmos DB 模擬器](local-emulator.md)可讓您使用上述的 .NET Graph API 在本機開發和測試，不需建立 Azure 訂用帳戶，也不會產生任何費用。 如果您滿意應用程式在模擬器中的運作方式，就可以切換成使用雲端的 Azure Cosmos DB 帳戶。

> [!NOTE]
> 針對 [Azure Cosmos DB 模擬器](local-emulator.md)驗證 Gremlin 查詢的支援，僅能透過 .NET Graph API 取用。

## <a name="scenarios-for-graph-support-of-azure-cosmos-db"></a>Azure Cosmos DB 的圖表支援案例
以下是某些可以使用 Azure Cosmos DB 圖表支援的案例︰

* 社交網路

 藉由結合客戶相關資料和他們與其他人的互動，您可以開發個人化體驗、預測客戶行為，或將興趣雷同的人們聯繫在一起。 Azure Cosmos DB 可用來管理社交網路並追蹤客戶的喜好設定和資料。

* 推薦引擎

 這是零售業常用的案例。 藉由結合產品、使用者和使用者互動的相關資訊，例如購物、瀏覽或商品評價，您可以建立自訂的推薦。 Azure Cosmos DB 憑藉其低延遲、彈性調整及原生的圖表支援，很適合將這些互動建立模型。

* 地理空間

 電信、物流和旅遊規劃方面的許多應用程式需要在區域內尋找有興趣的地點，或在兩個地點之間找出最短/最佳路線。 Azure Cosmos DB 很自然地可以解決這些問題。

* 物聯網

 以圖形模擬 IoT 裝置之間的網路和連線，可讓您更了解裝置和資產的狀態。 您也可以了解網路的某個部分變動時可能對其他部分造成什麼影響。

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure Cosmos DB 中的圖表支援，請參閱︰

* 開始使用 [Azure Cosmos DB 圖表教學課程](create-graph-dotnet.md)。
* 了解如何[使用 Gremlin 在 Azure Cosmos DB 中查詢圖表](gremlin-support.md)。
