---
title: "Azure Cosmos DB Cassandra API 簡介 | Microsoft Docs"
description: "了解如何透過您已熟悉的 Cassandra 驅動程式和 CQL 使用 Cassandra API，來利用 Azure Cosmos DB 「隨即轉移」現有應用程式和建立新的應用程式。"
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: 
ms.assetid: 73839abf-5af5-4ae0-a852-0f4159bc00a0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: govindk
ms.openlocfilehash: fe911b69278021262f1d90f2157bb78ca48db0db
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="introduction-to-apache-cassandra-api-for-azure-cosmos-db"></a>簡介適用於 Azure Cosmos DB 的 Apache Cassandra API

Azure Cosmos DB 提供的 Cassandra API (預覽) 適用於針對 Apache Cassandra 所撰寫、且需要進階功能的應用程式，例如：

* [可調整的儲存體大小和輸送量](partition-data.md)。
* [周全的全域發佈](distribute-data-globally.md)
* 99 百分位數的單一數字毫秒延遲。
* [五個定義完善的一致性層級](consistency-levels.md)
* [自動編製資料的索引](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)，您不需要處理結構描述和索引管理。 
* 保證高可用性，且皆受到[領先業界的 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) 支援

## <a name="what-is-the-azure-cosmos-db-apache-cassandra-api"></a>什麼是 Azure Cosmos DB Apache Cassandra API？

透過使用 Apache Cassandra API，Azure Cosmos DB 可為針對 [Apache Cassandra](https://cassandra.apache.org/) 所撰寫的應用程式，作資料存放區使用。 這表示，透過使用現有[符合 CQLv4 的 Apache 授權驅動程式](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)，針對 Cassandra 所撰寫的應用程式現在可以與 Azure Cosmos DB Cassandra API 通訊。 在許多情況下，只需簡單變更連接字串，您就可以從使用 Apache Cassandra 切換至使用 Azure Cosmos DB 的 Apache Cassandra API。 您可以使用這項功能，輕鬆地在 Azure 雲端建置及執行 Cassandra API 資料庫應用程式，利用 Azure Cosmos DB 的全域發佈和[領先業界的完整 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)，同時繼續使用 Cassandra API 的熟悉技能和工具。

![Azure Cosmos DB Cassandra API](./media/cassandra-introduction/cosmosdb-cassandra.png)

透過使用 Cassandra 查詢語言型工具 (例如 CQLSH)，以及您熟悉的 Cassandra 用戶端驅動程式，Cassandra API 可讓您與儲存在 Azure Cosmos DB 中的資料互動。 透過此 Microsoft Mechanics 影片和首席工程經理 Kirill Gavrylyuk 一起深入了解。

> [!VIDEO https://www.youtube.com/embed/1Sf4McGN1AQ]
>

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>使用適用於 Azure Cosmos DB 的 Apache Cassandra API 有什麼優點？

**沒有作業管理**：Azure Cosmos DB 是完全受管理的服務，可確保 Cassandra API 系統管理員不需要為管理及監視各個作業系統、JVM 和 yaml 檔案及其之間相互作用的各種設定而擔心。 Azure Cosmos DB 可監視輸送量、延遲、儲存體和可用性，並可設定警示。 

**效能管理**：Azure Cosmos DB 提供受 SLA 支援的低度延遲讀取和寫入 (第 99 個百分位數)。 使用者不需要擔心提供良好的讀取和寫入 SLA 會有很多作業負擔。 這些通常包括排程壓縮、管理標記、Bloom 篩選設定和複本延遲。 Azure Cosmos DB 讓您無須為管理這些問題而擔心，並可專注於應用程式的交付項目。

**自動編製索引**：Azure Cosmos DB 會自動為 Cassandra API 資料庫中所有資料表欄位編製索引。 Azure Cosmos DB 不需要建立次要索引以加速查詢。 而是以自動的一致性索引，來提供低延遲讀取和寫入體驗。 

**可使用現有的程式碼和工具**：Azure Cosmos DB 提供與現有 SDK 和工具相容的有線通訊協定層級。 此相容性可確保您可以透過 Azure Cosmos DB 的 Cassandra API 來使用現有程式碼基底執行瑣碎的變更。

**輸送量與儲存體彈性**：Azure Cosmos 平台透過簡單的入口網站、PowerShell 或 CLI 作業，提供彈性的跨區域保證輸送量。 隨著應用程式的成長，您可以依據可預測的效能彈性且順暢地調整 Azure Cosmos DB 資料表。 Azure Cosmos DB 支援的Cassandra API 資料表可調整為幾乎不受限制的儲存體大小。 

**全域發佈及可用性**：Azure Cosmos DB 可讓您將資料發佈至所有 Azure 區域，讓使用者在擁有低延遲體驗的同時確保可用性。 Azure Cosmos DB 在區域內會提供 99.99% 的可用性，以及在區域間提供 99.999% 的讀取可用性，且不會有作業負擔。 Azure Cosmos DB 可在 30 個以上的 [Azure 區域](https://azure.microsoft.com/regions/services/)中使用。 請參閱[將資料分散到全球](distribute-data-globally.md)以深入了解。 

**一致性選擇：**Azure Cosmos DB 提供五個定義完善的一致性層級可選擇，讓您能在一致性與效能之間做出最好的取捨。 一致性層級有強式、限定過期、工作階段、一致前置詞或最終。 這些細微且定義完善的一致性層級可讓開發人員在一致性、可用性與延遲三者間做出合理取捨。 深入了解[使用一致性層級將可用性和效能最大化](consistency-levels.md)。 

**企業級**：安全且相容 (預設) – Azure cosmos DB 會提供[相容性認證](https://www.microsoft.com/trustcenter)以確保使用者可以使用平台，而不需擔心相容性問題。 Azure Cosmos DB 也會為控制層活動提供待用加密和移動中加密、IP 防火牆和稽核記錄。  

<a id="sign-up-now"></a>
## <a name="sign-up-now"></a>立即註冊 

如果您已經有 Azure 訂用帳戶，就可以在 [Azure 入口網站](https://aka.ms/cosmosdb-cassandra-signup)中註冊加入 Cassandra API (預覽) 計劃。  如果您剛接觸到 Azure，可註冊[免費試用版](https://azure.microsoft.com/free)來取得 12 個月的免費 Azure Cosmos DB 存取權。 請完成下列步驟以要求 Cassandra API (預覽) 計劃的存取權。

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [新增] > [資料庫] > [Azure Cosmos DB]。 

2. 在 [新增帳戶] 頁面中，於 API 方塊中選取 [Cassandra]。 

3. 在 [訂用帳戶] 方塊中，選取要用於此帳戶的 Azure 訂用帳戶。

4. 按一下 [立即註冊以預覽]。

    ![Azure Cosmos DB Cassandra API](./media/cassandra-introduction/cassandra-sign-up.png)

3. 在 [立即註冊以預覽] 窗格中，按一下 [確定]。 

    提交要求之後，[新增帳戶] 窗格中的狀態就會變更為 [待核准]。 

提交要求之後，請等候說明要求已核准的電子郵件通知。 由於要求的數目非常龐大，因此您應該會在一週內收到通知。 您並不需要建立支援票證來完成要求。 我們將會依照收到要求的先後順序來檢閱要求。 

## <a name="how-to-get-started"></a>如何開始使用
加入預覽計劃之後，請依照 Cassandra API 快速入門中的說明使用 Cassandra API 建立應用程式：

* [快速入門：使用 Node.js 和 Azure Cosmos DB 建置 Cassandra Web 應用程式](create-cassandra-nodejs.md)
* [快速入門：使用 Java 和 Azure Cosmos DB 建置 Cassandra Web 應用程式](create-cassandra-java.md)
* [快速入門：使用 .NET 和 Azure Cosmos DB 建置 Cassandra Web 應用程式](create-cassandra-dotnet.md)
* [快速入門：使用 Python 和 Azure Cosmos DB 建置 Cassandra Web 應用程式](create-cassandra-python.md)

## <a name="next-steps"></a>後續步驟

Azure Cosmos DB MongoDB API 相關資訊已整合至整體 Azure Cosmos DB 文件，但以下有幾個線索可讓您開始使用︰

* 請遵循[快速入門](create-cassandra-nodejs.md)來使用 Git 範例建立帳戶和新應用程式
* 請遵循[教學課程](tutorial-develop-cassandra-java.md)以程式設計方式建立新的應用程式。
* 請遵循[匯入 Cassandra 資料教學課程](cassandra-import-data.md)來將現有資料匯入到 Azure Cosmos DB。
* 請參閱[常見問題集](faq.md#cassandra)。
