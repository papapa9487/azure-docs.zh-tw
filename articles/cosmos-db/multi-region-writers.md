---
title: "使用 Azure Cosmos DB 的多重主機資料庫架構 | Microsoft Docs"
description: "了解如何設計應用程式架構，使用 Azure Cosmos DB 在多個地理區域進行本機讀取和寫入。"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cf1482ae7b1070023703f5dbe861d151f5d64fd8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="multi-master-globally-replicated-database-architectures-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 的多重主機全域複寫資料庫架構
Azure Cosmos DB 支援周全的[全域複寫](distribute-data-globally.md)，可讓您以低延遲存取工作負載中的任何位置，將資料散發到多個區域。 此模型通常用於發行者/取用者工作負載，寫入器在單一地理區域中，而讀取器 (讀取) 分散在世界各地的其他區域。 

您也可以使用 Azure Cosmos DB 的全域複寫支援，建置其寫入器和讀取器遍布全球的應用程式。 本文概述的模式可讓使用 Azure Cosmos DB 的分散式寫入器達成本機寫入及本機讀取存取。

## <a id="ExampleScenario"></a>內容發佈 - 範例案例
讓我們看看真實世界的案例，說明如何利用 Azure Cosmos DB 使用分散在世界各地多重區域/多重主機讀寫模式。 以建置在 Azure Cosmos DB 上的內容發佈平台為例。 這個平台必須符合以下一些需求，才能獲得絕佳發行者和取用者使用者體驗。

* 作者與訂閱者都分散在世界各地 
* 作者必須將 (寫入) 文章發佈到其本機 (最接近) 的區域
* 作者文章的讀取器/訂閱者遍及全球。 
* 發佈新文章時，訂閱者應該收到通知。
* 訂閱者必須能從其本機區域讀取文章。 他們也能將評論加入這些文章。 
* 包括文章作者在內的任何人都可以從本機區域檢視所有文章附加的評論。 

假設有數百萬取用者與發行者，而文章有數十億篇，我們很快會面臨調整規模以及保證本機存取的問題。 和大部分擴充性問題一樣，解決方案就是要有良好的資料分割策略。 接下來，讓我們看看如何將文章、評論及通知的模型建立為文件、設定 Azure Cosmos DB 帳戶以及實作資料存取層。 

如果您想要深入了解資料分割和資料分割索引鍵，請參閱 [Azure Cosmos DB 的資料分割與調整規模](partition-data.md)。

## <a id="ModelingNotifications"></a>建立模型通知
通知是使用者特有的資料輸入。 因此，通知文件的存取模式都是以單一使用者而言。 例如，您可以「發佈給使用者的通知」或「擷取指定使用者的所有通知」。 因此，這類資料分割索引鍵的最佳選擇會是 `UserId`。

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>建立模型訂閱
針對感興趣的特定文章類別或特定發行者的各種準則來建立訂閱。 因此 `SubscriptionFilter` 會是不錯的資料分割索引鍵選擇。

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>建立模型文件
一旦透過通知來識別文章，後續查詢通常會以 `Article.Id` 為基礎。 因此選擇 `Article.Id` 當成資料分割索引鍵，可提供在 Azure Cosmos DB 集合內儲存文章最佳的發佈方式。 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>建立模型評論
和文章一樣，評論大部分可在文章內容中撰寫和讀取。 選擇 `ArticleId` 當成資料分割索引鍵，可提供和文章相關聯的評論最佳的發佈方式並有效存取。 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>資料存取層方法
現在來看看我們必須實作的主要資料存取方法。 以下是 `ContentPublishDatabase` 需要的方法清單︰

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Azure Cosmos DB 帳戶組態
若要保證本機讀取和寫入，不只要針對資料分割索引鍵，也要根據區域的地理存取模式來分割資料。 模型依存於每個區域的異地複寫 Azure Cosmos DB 資料庫帳戶。 以兩個區域為例，多重區域寫入設定如下︰

| 帳戶名稱 | 寫入區域 | 讀取區域 |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

下圖顯示如何以這種設定在一般應用程式中執行讀取和寫入︰

![Azure Cosmos DB 多重主機架構](./media/multi-region-writers/multi-master.png)

如何在`West US`區域中執行的 DAL 中，初始化用戶端的程式碼片段如下。
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

利用上述設定，資料存取層可以根據部署位置，將所有寫入轉送至本機帳戶。 藉由讀取這兩個帳戶，以取得資料的整體觀點來執行讀取。 這種方法可以擴充到所需的各個區域。 例如，以下是三個地區設定︰

| 帳戶名稱 | 寫入區域 | 讀取區域 1 | 讀取區域 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>資料存取層實作
現在讓我們來對有兩個可寫入區域的應用程式實作資料存取層 (DAL)。 DAL 必須實作下列步驟︰

* 為每個帳戶建立 `DocumentClient` 的多個執行個體。 使用兩個區域，每個 DAL 執行個體會有一個 `writeClient` 和一個 `readClient`。 
* 根據已部署應用程式的區域，設定 `writeclient` 和 `readClient` 的端點。 例如，部署在 `West US` 的 DAL 使用 `contentpubdatabase-usa.documents.azure.com` 執行寫入。 部署在 `NorthEurope`的 DAL 使用 `contentpubdatabase-europ.documents.azure.com` 寫入。

使用上述設定，就可以實作資料存取方法。 寫入作業會將寫入轉送到對應的 `writeClient`。

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

對於讀取通知和評論，您必須從區域和聯集兩者來讀取，結果如下列程式碼片段所示︰

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

因此，選擇良好的分割索引鍵和靜態帳戶型的資料分割，您就可以使用 Azure Cosmos DB 達到多重區域本機寫入和讀取。

## <a id="NextSteps"></a>接續步驟
我們會在本文說明如何使用發佈為範例案例的內容，利用 Azure Cosmos DB 使用分散在世界各地的多重區域讀取和寫入模式。

* 了解 Azure Cosmos DB 如何支援[全域發佈](distribute-data-globally.md)
* 了解 [Azure Cosmos DB 中的自動化和手動容錯移轉](regional-failover.md)
* 了解 [Azure Cosmos DB 的全域一致性](consistency-levels.md)
* 使用 [Azure Cosmos DB - DocumentDB API](tutorial-global-distribution-documentdb.md) 進行多區域開發
* 使用 [Azure Cosmos DB - MongoDB API](tutorial-global-distribution-MongoDB.md) 進行多區域開發
* 使用 [Azure Cosmos DB - Table API](tutorial-global-distribution-table.md) 進行多區域開發
