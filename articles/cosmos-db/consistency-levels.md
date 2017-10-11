---
title: "Azure Cosmos DB 中的一致性層級 | Microsoft Docs"
description: "Azure Cosmos DB 具有五個一致性層級，有助於在最終一致性、可用性和延遲的取捨之間取得平衡。"
keywords: "最終一致性, azure cosmos db, azure, Microsoft azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: cgronlun
documentationcenter: 
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a1ebec2285982c70aa9dc49950769fe18e2e2d0d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 Tunable 資料一致性層級
Azure Cosmos DB 是針對每個資料模型考量到全球發佈的全新設計。 它的設計目的是提供可預測的低延遲保證、99.99% 的可用性 SLA，以及多個定義完善且寬鬆的一致性層級模型。 Azure Cosmos DB 目前提供五種一致性層級：強式、限定過期、工作階段、一致的前置和最終。 

除了**強式**和**最終一致性**模型通常是由分散式資料庫所提供，Azure Cosmos DB 還提供其他三個已仔細編碼且可運作的一致性模型，並已根據真實世界的使用案例驗證過它們的實用性。 這些模型是**限定過期**、**工作階段**和**一致前置詞**一致性層級。 總言之，這五個一致性層級可讓您在一致性、可用性與延遲三者間做出合理取捨。 

## <a name="distributed-databases-and-consistency"></a>分散式資料庫和一致性
商業散發資料庫分為兩類：完全不會提供完善且可證明之一致性選擇的資料庫，以及提供兩個極端的可程式性選擇 (強式與最終一致性) 的資料庫。 

前者增加了應用程式開發人員其複寫通訊協定細節的負擔，並期望他們在一致性、可用性、延遲和輸送量之間做出困難的取捨。 後者則是將壓力放在選擇兩個極端其中之一。 儘管有超過 50 個一致性模型的研究和建議，散發資料庫社群仍無法商業化超越強式與最終一致性的一致性層級。 Cosmos DB 讓開發人員在五個完善定義的一致性模型，以及一致性頻譜之間選擇 – 強式、限定過期、[工作階段](http://dl.acm.org/citation.cfm?id=383631)、一致前置詞和最終。 

![Azure Cosmos DB 提供多個定義完善 (寬鬆) 的一致性模型，讓您可從中選擇](./media/consistency-levels/five-consistency-levels.png)

下表說明每個一致性層級提供的特定保證。
 
**一致性層級和保證**

| 一致性層級 | 保證 |
| --- | --- |
| 強式 | 線性化能力 |
| 限定過期 | 一致前置詞。 讀取落後寫入 (k 前置詞或 t 間隔) |
| 工作階段   | 一致前置詞。 單純讀取、單純寫入、讀取您的寫入、讀取後接寫入 |
| 一致前置詞 | 傳回的更新是所有更新的部分前置詞 (沒有間隔) |
| 最終  | 次序錯誤讀取 |

您可以在 Cosmos DB 帳戶上設定預設一致性層級 (並在稍後覆寫特定讀取要求的一致性)。 就內部而言，預設的一致性層級適用於跨區域之分割區集合內的資料。 大約 73% 的租用戶使用工作階段一致性，而 20% 的租用戶則偏好限定過期。 我們發現，大約有 3% 的客戶在一開始試驗不同的一致性層級，然後才決定應用程式的特定一致性選擇。 我們也發現只有 2% 的租用戶在個別要求基礎上覆寫一致性層級。 

在 Cosmos DB 中，在工作階段、一致前置詞和最終一致性提供服務的讀取，比起使用強式或限定過期一致性的讀取，其成本便宜兩倍。 Cosmos DB 有領先業界的完整 99.99% SLA，包括一致性保證以及可用性、輸送量和延遲。 我們採用[線性化能力檢查程式](http://dl.acm.org/citation.cfm?id=1806634)，在我們的服務遙測上持續運作，並且公開向您報告任何一致性違規。 針對限定過期，我們監視以及報告任何違反 k 和 t 界限的違規。 針對所有五個寬鬆的一致性層級，我們也會直接向您報告[概率限定過期度量](http://dl.acm.org/citation.cfm?id=2212359)。  

## <a name="scope-of-consistency"></a>一致性的範圍
已將一致性的規模範圍限制為單一使用者要求。 寫入要求可能會對應至插入、取代、更新插入或刪除交易。 如同寫入，讀取/查詢交易也會將範圍限制為單一使用者要求。 使用者可能需要在大型結果集 (橫跨多個分割區) 上編頁，但是每一個讀取交易的範圍都會限制為單一頁面，並從單一分割區中提供服務。

## <a name="consistency-levels"></a>一致性層級
您可以設定資料庫帳戶的「預設一致性層級」，以套用至 Cosmos DB 帳戶底下的所有集合 (和資料庫)。 所有對使用者定義的資源發出的讀取和查詢，預設都會使用資料庫帳戶上所指定的預設一致性層級。 您可以使用每個支援的 API，放寬特定讀取/查詢要求的一致性層級。 Azure Cosmos DB 複寫通訊協定支援五種類型的一致性層級，可在特定一致性保證和效能之間明確進行取捨，如本章節所述。

**重要事項**： 

* 強式一致性利用保證傳回項目最新版本的讀取來提供[線性化](https://aphyr.com/posts/313-strong-consistency-models)保證。 
* 「增強式」一致性保證只有在複本的多數仲裁持久認可寫入之後，才會顯示寫入。 寫入不是會被主要和次要仲裁同步地持久認可，就是會被中止。 讀取的認可一律是交由多數讀取仲裁來負責；用戶端絕不會看到未認可或不完整的寫入，而且一律保證會讀取最新認可的寫入。 
* 設定為使用強式一致性的 Azure Cosmos DB 帳戶，無法將一個以上的 Azure 區域關聯至它們的 Azure Cosmos DB 帳戶。 
* 具有強式一致性的讀取作業成本 (就取用的 [要求單位](request-units.md) 而言) 會高於工作階段和最終，但與限定過期相同。

**限定過期**： 

* 限定過期一致性保證讀取可能會落後寫入最多 K 個項目版本或首碼或是 t 個時間間隔。 
* 因此，當選擇限定過期時，有兩種方式可以設定「過期」︰項目的版本數 K，它是讀取落後寫入的數目，以及時間間隔 t 
* 限定過期提供全域訂單總數，但「過期期間」內的除外。 在區域內，「過期期間」內外都有單純的讀取保證存在。 
* 限定過期可提供比工作階段或最終一致性還要強大的一致性保證。 針對全球發佈的應用程式，如果您想要強式一致性但也想要 99.99% 的可用性和低度延遲，我們建議您使用限定過期。 
* 使用限定過期一致性設定的 Azure Cosmos DB 帳戶可以將任意數目的 Azure 區域關聯至它們的 Azure Cosmos DB 區域。 
* 具有限定過期的讀取作業成本 (就取用的 RU 而言) 會高於工作階段和最終一致性，但與強式一致性相同。

**工作階段**： 

* 與強式和限定過期一致性層級所提供的全域一致性模型不同，工作階段一致性範圍會限制為用戶端工作階段。 
* 工作階段一致性適用於所有牽涉到裝置或使用者工作階段的案例，因為它保證單純讀取、單純寫入和讀取您自己的寫入 (RYW) 保證。 
* 工作階段一致性可為工作階段提供可預測的一致性，以及在提供最低延遲的寫入和讀取時提供最高的讀取輸送量。 
* 使用工作階段一致性設定的 Azure Cosmos DB 帳戶可以將任意數目的 Azure 區域關聯至它們的 Azure Cosmos DB 區域。 
* 具有工作階段一致性的讀取作業成本 (就取用的 RU 而言) 會低於強式和限定過期，但高於最終一致性。

<a id="consistent-prefix"></a>
**一致前置詞**： 

* 一致前置詞保證之後如果沒有再收到任何寫入，群組內的複本最後會只剩一個。 
* 一致前置詞保證讀取永遠不會看到沒有順序的寫入。 如果寫入以 `A, B, C` 順序執行，則用戶端會看到 `A`、`A,B` 或 `A,B,C`，但是永遠不會看到沒有順序的情形，像是 `A,C` 或 `B,A,C`。
* 使用一致前置詞一致性設定的 Azure Cosmos DB 帳戶可以將任意數目的 Azure 區域關聯至它們的 Azure Cosmos DB 區域。 

**最終**： 

* 最終一致性保證之後如果沒有再收到任何寫入，群組內的複本最後會只剩一個。 
* 最終一致性是最差的一致性形式，用戶端可能會取得比之前所看到的值還要舊的值。
* 「最終一致性」提供最差的讀取一致性，但是讀取和寫入的延遲皆是最低。
* 使用最終一致性設定的 Azure Cosmos DB 帳戶可以將任意數目的 Azure 區域關聯至它們的 Azure Cosmos DB 區域。 
* 具有最終一致性的讀取作業成本 (就取用的 RU 而言) 是所有 Azure Cosmos DB 一致性層級中最低的。

## <a name="configuring-the-default-consistency-level"></a>設定預設一致性層級
1. 在 [Azure 入口網站](https://portal.azure.com/)的 Jumpbar 中，按一下 [Azure Cosmos DB]。
2. 在 [Azure Cosmos DB] 刀鋒視窗中，選取要修改的資料庫帳戶。
3. 在帳戶刀鋒視窗中，按一下 [預設一致性] 。
4. 在 [預設一致性] 刀鋒視窗中，選取新的一致性層級，然後按一下 [儲存]。
   
    ![此螢幕擷取畫面反白顯示 [設定] 圖示和 [預設一致性] 項目](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>查詢的一致性層級
根據預設，針對使用者定義的資源，查詢的一致性層級會與讀取的一致性層級相同。 每次在集合中插入、取代或刪除項目時，預設會同步更新索引到 Cosmos DB 容器。 這個行為讓查詢能夠使用與點的讀取相同的一致性層級。 雖然 Azure Cosmos DB 的寫入已經過最佳化處理，並支援持續的寫入數量、同步索引維護，以及提供一致的查詢，但您還是可以設定特定集合，讓集合的索引更新速度變慢。 讓索引速度變慢可提升寫入效能，而且適合用於工作負載主要是進行大量讀取的大量擷取案例。  

| 索引模式 | 讀取 | 查詢 |
| --- | --- | --- |
| 一致 (預設) |從強式、限定過期、工作階段、一致前置詞或最終等選項中選取 |從強式、限定過期、工作階段或最終等選項中選取 |
| 緩慢 |從強式、限定過期、工作階段、一致前置詞或最終等選項中選取 |最終 |
| None |從強式、限定過期、工作階段、一致前置詞或最終等選項中選取 |不適用 |

和讀取要求相同，您可以在每個 API 中降低特定查詢要求的一致性層級。

## <a name="next-steps"></a>後續步驟
如果您想要詳細了解一致性層級和取捨，我們建議下列資源：

* Doug Terry。 透過棒球來解說複寫的資料一致性 (影片)。   
  [https://www.youtube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
* Doug Terry。 透過棒球來解說複寫的資料一致性。   
  [http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* Doug Terry。 弱式一致複寫資料的工作階段保證。   
  [http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
* Daniel Abadi。 現代分散式資料庫系統設計的一致性取捨：CAP 是整個過程中的唯一解決方案」。   
  [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* Peter Bailis、Shivaram Venkataraman、Michael J. Franklin、Joseph M. Hellerstein、Ion Stoica。 實際部分仲裁的機率界限-陳舊 (PBS)。   
  [http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* Werner Vogels。 再論最終一致。    
  [http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* Moni Naor、Avishai Wool，仲裁系統的負載、容量及可用性，SIAM 計算期刊，v.27 n.2，p.423-447，1998 年 4 月。
  [http://epubs.siam.org/doi/abs/10.1137/S0097539795281232](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* Sebastian Burckhardt，Chris Dern，Macanal Musuvathi，Roy Tan，陣容：完整和自動的線性化能力檢查程式，程式設計語言設計和實作的 2010 ACM SIGPLAN 會議論文集，2010 年 6 月 05-10 日，加拿大，安大略，多倫多 [doi>10.1145/1806596.1806634] [http://dl.acm.org/citation.cfm?id=1806634](http://dl.acm.org/citation.cfm?id=1806634)
* Peter Bailis，Shivaram Venkataraman，Michael J. Franklin，Joseph M. Hellerstein，Ion Stoica，實用部分仲裁的概率限定過期，VLDB Endowment 論文集，v.5 n.8，p.776-787，2012 年 4 月 [http://dl.acm.org/citation.cfm?id=2212359](http://dl.acm.org/citation.cfm?id=2212359)
