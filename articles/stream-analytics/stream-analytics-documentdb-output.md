---
title: "串流分析的 JSON 輸出 | Microsoft Docs"
description: "了解「串流分析」如何將 Azure Cosmos DB 設定為 JSON 輸出的目標，以針對非結構化 JSON 資料進行資料封存和低延遲查詢。"
keywords: "JSON 輸出"
documentationcenter: 
services: stream-analytics,documentdb
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: cc80b0080c806541362a1ef2d71b95862bd51ca2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="target-azure-cosmos-db-for-json-output-from-stream-analytics"></a>將 Azure Cosmos DB 設定為串流分析的 JSON 輸出目標
「串流分析」可以將 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 設定為 JSON 輸出的目標，讓您能夠針對非結構化的 JSON 資料進行資料封存和低延遲查詢。 本文件涵蓋實作這種組態的一些最佳作法。

如果您不熟悉 Cosmos DB，請參閱 [Azure Cosmos DB 的學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)來開始著手。 

注意：目前不支援 Mongo DB API 架構的 Cosmos DB 集合。 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>將 Cosmos DB 設定為輸出目標的基本概念
「串流分析」中的 Azure Cosmos DB 輸出可將串流處理結果以 JSON 輸出的形式寫入到您的 Cosmos DB 集合中。 串流分析不會在資料庫中建立集合，而是需要您預先建立集合。 如此一來，Cosmos DB 集合的計費成本對您來說就很透明，而您也能直接使用 [Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 來微調集合的效能、一致性及容量。 建議您讓每個串流作業使用一個 Cosmos DB 資料庫，以透過邏輯方式分隔串流作業的集合。

以下詳細說明一些 Cosmos DB 集合選項。

## <a name="tune-consistency-availability-and-latency"></a>微調一致性、 可用性及延遲
為了符合應用程式需求，Cosmos DB 允許您微調資料庫與集合，並在一致性、可用性及延遲之間進行取捨。 您可以視案例針對讀取與寫入延遲所需的讀取一致性層級，來選擇資料庫帳戶上的一致性層級。 此外，Cosmos DB 預設會在對您集合進行的每個 CRUD 作業進行同步索引編製。 這是另一個可在 Cosmos DB 中控制寫入/讀取效能的實用選項。 如需深入了解這個主題，請參閱 [變更資料庫及查詢的一致性層級](../documentdb/documentdb-consistency-levels.md) 。

## <a name="upserts-from-stream-analytics"></a>來自串流分析的 Upsert
「串流分析」與 Cosmos DB 的整合可讓您根據指定的「文件識別碼」資料行，在 Cosmos DB 集合中插入或更新記錄。 這也稱為「Upsert」 。

串流分析可利用最佳化的更新插入方法，而此方法只會在因為 Document ID 發生衝突而插入失敗時，才進行更新。 串流分析會將更新作為修補程式執行，以部分更新文件，也就是以漸進方式新增新屬性或取代現有屬性。 請注意，變更 JSON 文件中陣列屬性的值，會造成整個陣列遭到覆寫，也就是不會合併陣列。

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB 中的資料分割
Cosmos DB [分割集合](../cosmos-db/partition-data.md)是建議的資料分割方法。 

針對單一 Cosmos DB 集合，串流分析仍可讓您根據查詢模式和應用程式的效能需求來分割資料。 每個集合最多可包含 10GB 的資料 (上限)，且目前還無法相應增加 (或溢位) 集合。 如需相應放大，串流分析允許您使用指定的前置詞寫入多個集合 (請參閱下方的使用方式詳細資料)。 串流分析會根據使用者提供的 PartitionKey 欄，使用一致的 [雜湊分割解析程式](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) 策略來分割其輸出記錄。 系統會使用在串流作業開始時具有所指定前置詞的集合數量，作為該作業會同時寫入的輸出分割區計數 (Cosmos DB 集合數 = 輸出分割區數)。 對於延遲索引只進行插入的單一集合，預期會有約每秒 0.4 MB 的寫入輸送量。 使用多個集合可允許您達成更高的輸送量及增加容量。

如果您計劃在未來增加分割計數，您可能需要停止工作、將現有集合中的資料重新分割至新的集合，然後重新啟動串流分析工作。 有關使用 PartitionResolver 與重新分割的詳細資料，以及範例程式碼，都會包含在後續的文章中。 [Cosmos DB 中的分割與調整](../documentdb/documentdb-partition-data.md)一文也提供該主題的詳細說明。

## <a name="cosmos-db-settings-for-json-output"></a>適用於 JSON 輸出的 Cosmos DB 設定
如果在「串流分析」中建立 Cosmos DB 作為輸出，將會產生如以下所示的資訊提示。 本節說明屬性定義。

資料分割的集合 | 多個「單一資料分割」集合
---|---
![documentdb 串流分析輸出畫面](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![documentdb 串流分析輸出畫面](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> **多個「單一資料分割 」集合**案例需要資料分割索引鍵，而且是支援的組態。 

* **輸出別名**：在您的 ASA 查詢中參照此輸出時所用的別名。  
* **帳戶名稱**：Cosmos DB 帳戶的名稱或端點 URI。  
* **帳戶金鑰**：Cosmos DB 帳戶的共用存取金鑰。  
* **資料庫**：Cosmos DB 資料庫名稱。  
* **集合名稱模式**：要使用之集合的集合名稱或其模式。 您可以使用選用的 {partition} 語彙基元來建構集合名稱的格式，其中的資料分割會從 0 開始。 以下是有效的範例輸入：  
  1\) MyCollection – 必須要有一個名為 “MyCollection” 的集合存在。  
  2\) MyCollection{partition} – 這些集合必須存在 – "MyCollection0”、“MyCollection1”、“MyCollection2” 等，依此類推。  
* **資料分割索引鍵** - 選擇性。 只有當您在集合名稱模式中使用 {parition} 語彙基元時，才需要此索引鍵。 輸出事件中的欄位名稱會用來為跨集合的資料分割輸出指定索引鍵。 若為單一集合輸出，則可使用任何任意的輸出欄，例如 PartitionId。  
* **文件識別碼** ：可省略。 輸出事件中的欄位名稱會用來指定主索引鍵，此為插入或更新作業的依據。  
