---
title: "Azure Cosmos DB 簡介 | Microsoft Docs"
description: "了解 Azure Cosmos DB。 這個全球散發的多模型資料庫是針對低延遲、彈性的延展性和高可用性所建置。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/14/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: fe4c79f4e71d885e22bbcf31869b809f5d7fe1a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="welcome-to-azure-cosmos-db"></a>歡迎使用 Azure Cosmos DB

Azure Cosmos DB 是 Microsoft 的全球散發多模型資料庫。 透過按鈕點選，Azure Cosmos DB 可讓您有彈性且獨立地跨任意數目的 Azure 地理區域調整輸送量和儲存體。 它利用完整的[服務等級協定](https://aka.ms/acdbsla) (SLA) 提供了輸送量、延遲、可用性和一致性的保證，這是其他資料庫服務無法提供的。

![Azure Cosmos DB 是 Microsoft 的全球散發資料庫服務，包含彈性的相應放大、低延遲保證、五個一致性模型，以及完整保證的 SLA](./media/introduction/azure-cosmos-db.png)

您可以[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，無須 Azure 訂用帳戶，也無須任何費用和約定付款。

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>受益於 Azure Cosmos DB 的解決方案

任何需要處理[全球](distribute-data-globally.md)大量讀取和寫入 (各種資料的回應時間都很短) 的 [Web、行動裝置、遊戲和 IoT 應用程式](use-cases.md)，都將受益於 Azure Cosmos DB 的[保證](https://azure.microsoft.com/support/legal/sla/cosmos-db/)可用性、高輸送量、低延遲度及可微調的一致性。

## <a name="key-capabilities"></a>主要功能
Azure Cosmos DB 是一個全域分散式資料庫服務，提供下列功能以協助您建置可調整且回應速度快的應用程式：

* **周全且立即可用的全域分散式資料庫**
    * 只要[按一下按鈕](tutorial-global-distribution-documentdb.md)，就可以[將資料分散](distribute-data-globally.md)至多個 [Azure 區域](https://azure.microsoft.com/regions/)。 這可讓您將資料放在使用者所在的區域，確保客戶的可能延遲最低。 
    * 使用 Azure Cosmos DB 的多路連接 API，應用程式一律知道最接近的區域為何，並將要求傳送至最接近的資料中心。 沒有任何組態變更也可行，您可設定寫入區域和所需數量的讀取區域，系統會為您處理其餘部分。

* **存取和查詢資料的多重資料模型與常用 API**
    * 建置 Azure Cosmos DB 所依據的 Atom-記錄-序列 (ARS) 型資料模型，原本支援多個資料模型，包括但不是限於文件、圖形、索引鍵-值、資料表和單欄式資料模型。
    * 透過以多種語言提供的 SDK，支援下列資料模型的 API：
        * [DocumentDB API](documentdb-introduction.md)
        * [MongoDB API](mongodb-introduction.md)
        * [資料表 API](table-introduction.md)
        * [Graph (Gremlin) API](graph-introduction.md)
        * 其他資料模型即將登場 

* **全球性依需求彈性調整輸送量和儲存體**
    * 輕鬆地以[每秒](request-units.md)的細微度調整資料庫輸送量，並隨時依需求變更。 
    * [在背景自動](partition-data.md)調整儲存體大小，不限次數處理您對於儲存體大小的需求。

* **建置回應速度快和關鍵任務應用程式**
    * Azure Cosmos DB 保證其客戶的端對端低延遲 (99 個百分位數)。 
    * 針對典型的 1 KB 項目，Cosmos DB 保證在相同的 Azure 區域內讀取低於 10 毫秒以及索引化寫入低於 15 毫秒的端對端延遲 (99 個百分位數)。 中間的延遲性就可大幅降低 (低於 5 毫秒)。

* **確保「永遠可用」可用性**
    * 單一區域內的 99.99% 可用性。
    * 針對更高的可用性部署至任意數目的 [Azure 區域](https://azure.microsoft.com/regions)。
    * 透過資料零遺失保證來[模擬一或多個區域的錯誤](regional-failover.md)。 

* **以正確方式撰寫全球散發應用程式**
    * 五個[一致性模型](consistency-levels.md)提供與強式 SQL 類似的一致性給類似 NoSQL 的最終一致性，以及其間的所有事。 
  
* **退款保證**
    * 保證快速取得您的資料，否則退款。 
    * 可用性、延遲、輸送量和一致性的[服務等級協定](https://aka.ms/acdbsla)。 

* **無資料庫結構描述/索引管理**
    * 不用再擔心維持您的資料庫結構描述和索引與您的應用程式結構描述之間的同步處理了。 我們完全不需要結構描述。 
    * Azure Cosmos DB 的資料庫引擎完全不需要結構描述 – 它會自動建立所內嵌之資料的索引，且不需要任何結構描述或索引，還能提供超級快速的查詢。 

* **降低擁有權成本**
    * 非管理式解決方案[更符合成本效益](https://aka.ms/cosmos-db-tco-paper)五到十倍。
    * 比 DynamoDB 便宜三倍。

## <a name="capability-comparison"></a>功能比較

Azure Cosmos DB 會提供關聯式與非關聯式資料庫的最佳功能。

| 功能 | 關聯式資料庫   | 非關聯式 (NoSQL) 資料庫 |    Azure Cosmos DB |
| --- | --- | --- | --- |
| 全球發佈 | 否 | 否 | 是，在 30 個以上的區域中周全且立即可用的散發，具有多路連接的 API|
| 水平調整 | 否 | 是 | 是，您可以獨立調整儲存體和輸送量 | 
| 延遲保證 | 否 | 是 | 是，99% 的讀取 <10 毫秒和寫入 <15 毫秒 | 
| 高可用性 | 否 | 是 | 是，Cosmos DB 一律可用，具有 PACELC 取捨，並提供自動和手動容錯移轉選項|
| 資料模型 + API | 關聯式 + SQL | 多模型 + OSS API | 多模型 + SQL + OSS API (更多即將推出) |
| SLA | 是 | 否 | 是，延遲、輸送量、一致性、可用性的完整 SLA |


## <a name="next-steps"></a>後續步驟
透過下列其中一個快速入門開始使用 Azure Cosmos DB：

* [開始使用 Azure Cosmos DB 的 DocumentDB API](create-documentdb-dotnet.md)
* [開始使用 Azure Cosmos DB 的 MongoDB API](create-mongodb-nodejs.md)
* [開始使用 Azure Cosmos DB 的圖形 API](create-graph-dotnet.md)
* [開始使用 Azure Cosmos DB 的資料表 API](create-table-dotnet.md)
