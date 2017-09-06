---
title: "DocumentDB API 效能等級 | Microsoft Docs"
description: "了解 DocumentDB API 效能等級如何可讓您根據每個容器來保留輸送量。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 6692d5b75954b2162862e6be7c2e39c63fa8408b
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>淘汰 S1、S2 和 S3 效能層級

> [!IMPORTANT] 
> 本文所討論的 S1、S2 和 S3 效能層級將會淘汰，而且也無法再供新的 DocumentDB API 帳戶使用。
>

本文概述 S1、S2 和 S3 效能層級，並討論使用這些效能層級的集合如何在 2017 年底移轉至單一資料分割集合。 閱讀本文後，您將能夠回答下列問題：

- [為何 S1、S2 和 S3 效能層級將要淘汰？](#why-retired)
- [單一分割區集合和資料分割的集合與 S1、S2、S3 效能層級之比較為何？](#compare)
- [我該怎麼做才能確保不中斷地的存取我的資料？](#uninterrupted-access)
- [在移轉之後我的集合會如何變更？](#collection-change)
- [我移轉至單一資料分割集合之後，我的帳單將如何變更呢？](#billing-change)
- [如果我需要超過 10 GB 的儲存體會如何？](#more-storage-needed)
- [在計劃性移轉之前，我可以在 S1、S2 和 S3 效能層級之間變更嗎？](#change-before)
- [如何得知我的收藏何時已移轉？](#when-migrated)
- [如何自己從 S1、S2、S3 效能層級移轉至單一資料分割集合？](#migrate-diy)
- [如果我是 EA 客戶會受到什麼影響？](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>為何 S1、S2 和 S3 效能層級將要淘汰？

S1、S2 和 S3 效能層級不提供 DocumentDB API 集合所提供的彈性。 使用 S1、S2、S3 效能層級，輸送量和儲存容量皆會預先設定且不提供彈性。 Azure Cosmos DB 現在可讓您自訂輸送量和儲存體，提供您更大的彈性能夠隨您的需求進行變更。

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>單一分割區集合和資料分割的集合與 S1、S2、S3 效能層級之比較為何？

下表比較單一資料分割集合、資料分割集合和 S1、S2、S3 效能層級中可用的輸送量和儲存體選項。 美國東部 2 區域的範例如下︰

|   |資料分割的集合|單一資料分割集合|S1|S2|S3|
|---|---|---|---|---|---|
|最大輸送量|無限制|10K RU/秒|250 RU/秒|1 K RU/秒|2.5 K RU/秒|
|輸送量下限|2.5K RU/秒|400 RU/秒|250 RU/秒|1 K RU/秒|2.5 K RU/秒|
|儲存體上限|無限制|10 GB|10 GB|10 GB|10 GB|
|價格 (每月)|輸送量：$6 / 100 RU/秒<br><br>儲存體：$0.25/GB|輸送量：$6 / 100 RU/秒<br><br>儲存體：$0.25/GB|$25 美元|$50 美元|$100 美元|

您是 EA 客戶嗎？ 如果是，請參閱[如果我是 EA 客戶會受到什麼影響？](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>我該怎麼做才能確保不中斷地的存取我的資料？

無須執行任何動作，Cosmos DB 會處理您的移轉。 如果您有 S1、S2 或 S3 集合，您目前的集合將會在 2017 年底移轉至單一磁碟分割集合。 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>在移轉之後我的集合會如何變更？

如果您有一個 S1 集合，將會以 400 RU/秒的輸送量移轉至單一資料分割集合。 400 RU/秒是適用於單一資料分割集合的最低輸送量。 不過，單一資料分割集合中 400 RU/秒的成本，大致上與您已支付的 S1 集合與 250 RU/秒相同 – 因此您不需要支付供您使用的額外 150 RU/秒。

如果您有一個 S2 集合，將會以 1 K RU/秒移轉至單一資料分割集合。 您不會看到您的輸送量層級有任何變更。

如果您有一個 S3 集合，將會以 2.5 K RU/秒移轉至單一資料分割集合。 您不會看到您的輸送量層級有任何變更。

在每一個情況下，在移轉您的集合之後，您將能夠自訂您的輸送量層級，或視需要將它相應增加及減少，為使用者提供低延遲存取。 若要在移轉您的集合之後變更輸送量層級，只要在 Azure 入口網站中開啟 Cosmos DB 帳戶，按一下 [調整]、選擇您的集合，然後調整輸送量層級，如下列螢幕擷取畫面所示︰

![如何在 Azure 入口網站中調整輸送量](./media/performance-levels/portal-scale-throughput.png)

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-im-migrated-to-the-single-partition-collections"></a>我移轉至單一資料分割集合之後，我的帳單將如何變更呢？

假設您在美國東部區域有 10 個 S1 集合，每個有 1 GB 的儲存空間，您將這 10 個 S1 集合以 400 RU/秒 (最小層級) 移轉至 10 個單一資料分割集合。 如果您一整個月保留 10 個單一資料分割集合，您的帳單會看起來如下︰

![10 個集合的 S1 價格與使用單一資料分割集合的 10 個集合價格比較會如何](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>如果我需要超過 10 GB 的儲存空間怎麼辦？

無論您擁有具有 S1、S2 或 S3 效能層級的集合，或是擁有單一資料分割集合，全部都有 10 GB 的可用儲存空間，您可以利用幾乎不受限制的儲存空間使用 Cosmos DB 資料移轉工具將您的資料移轉至資料分割的集合。 如需資料分割集合優點的相關詳細資訊，請參閱 [Azure Cosmos DB 中的資料分割與規模調整](documentdb-partition-data.md)。 如需將 S1、S2、S3 或單一資料分割集合移轉到資料分割集合的詳細資訊，請參閱[從單一資料分割移轉至資料分割集合](documentdb-partition-data.md#migrating-from-single-partition)。 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>在計劃性移轉之前，我可以在 S1、S2 和 S3 效能層級之間變更嗎？

只有 S1、S2 和 S3 效能的現有帳戶可以變更，且透過入口網站或以程式設計的方式變更效能層級層。 如果您從 S1、S3 或 S3 變更為單一資料分割集合，將無法回到 S1、S2 或 S3 效能層級。

<a name="when-migrated"></a>

## <a name="how-will-i-know-when-my-collection-has-migrated"></a>如何得知我的收藏何時已移轉？

移轉會在 2017 年底發生。 如果您擁有的集合是使用 S1、S2 或 S3 效能層級，Cosmos DB 小組會在進行移轉之前，透過電子郵件與您連絡。 一旦完成移轉，Azure 入口網站會顯示您的集合使用標準價格。

![如何確認您的集合已移轉至標準定價層](./media/performance-levels/portal-standard-pricing-applied.png)

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>如何自己從 S1、S2、S3 效能層級移轉至單一資料分割集合？

您可以使用 Azure 入口網站或以程式設計的方式，從 S1、S2 和 S3 效能層級移轉至單一資料分割集合。 您可以計劃性移轉之前自行進行此動作，以受益於單一資料分割的集合具彈性的可用輸送量，或者我們會在 2017 年底為您移轉集合。

**若要使用 Azure 入口網站移轉至單一資料分割集合**

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [Azure Cosmos DB]，然後選取要修改的 Cosmos DB 帳戶。 
 
    如果 [Azure Cosmos DB] 不在動態工具列中，按一下 >，捲動到 [資料庫]，選取 [Azure Cosmos DB]，然後選取 DocumentDB 帳戶。  

2. 在 [資源] 功能表上，於 [容器] 下，按一下 [級別]，從下拉式清單選取要修改的集合，然後按一下 [定價層]。 使用預先定義的輸送量的帳戶具有 S1、S2 或 S3 定價層。  在 [選擇定價層] 刀鋒視窗中按一下 [標準] 來變更使用者定義的輸送量，然後按一下 [選取] 以儲存變更。

    ![[設定] 刀鋒視窗的螢幕擷取畫面，其中顯示可供變更輸送量值的位置](./media/performance-levels/change-performance-set-thoughput.png)

3. 回到 [調整] 刀鋒視窗中，[定價層] 已變更為 [標準]，並且 [輸送量 (RU/秒)] 方塊會顯示預設值 400。 將輸送量設定為介於 400 到 10,000 個 [要求單位](request-units.md)/秒 (RU/秒)。 頁面底部的 [估計每月帳單] 會自動更新以提供每月成本估計。 

    >[!IMPORTANT] 
    > 一旦您儲存變更並移至標準定價層後，將無法復原為 S1、S2 或 S3 效能層級。

4. 按一下 [確定] 儲存變更。

    如果您判斷您需要更多輸送量 (大於 10,000 RU/秒) 或更多儲存體 (大於 10 GB)，您可以建立資料分割的集合。 若要將單一資料分割集合移轉到資料分割的集合，請參閱[從單一資料分割移轉至資料分割集合](documentdb-partition-data.md#migrating-from-single-partition)。

    > [!NOTE]
    > 從 S1、S2 或 S3 變更為標準可能需要 2 分鐘。
    > 
    > 

**若要使用 .NET SDK 移轉至單一資料分割集合**

變更集合的效能層級的另一個選項是透過我們的 SDK。 本節只涵蓋使用我們的 [DocumentDB .NET API](documentdb-sdk-dotnet.md)來變更集合的效能層級，但程序類似於我們的其他 SDK。

以下是變更集合輸送量為每秒 5,000 要求單位的程式碼片段：
    
```C#
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

請造訪 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) 以檢視其他範例，並深入了解我們提供的方法：

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>如果我是 EA 客戶會受到什麼影響？

EA 客戶將會受保護的價格，直到其目前合約結束。

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure Cosmos DB 的價格和管理資料，請探索這些資源：

1.  [在 Cosmos DB 中分割資料](documentdb-partition-data.md)。 了解單一資料分割容器和資料分割容器的差異，以及實作資料分割策略以順暢地調整的秘訣。
2.  [Cosmos DB 定價](https://azure.microsoft.com/pricing/details/cosmos-db/)。 深入了解佈建輸送量和使用儲存體的成本。
3.  [要求單位](request-units.md)。 了解不同作業類型，例如讀取、寫入、查詢的輸送量耗用量。

