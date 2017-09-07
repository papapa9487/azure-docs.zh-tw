---
title: "Azure Cosmos DB 的設計模式：社交媒體應用程式 | Microsoft Docs"
description: "了解具有 Azure Cosmos DB 與其他 Azure 服務之儲存體彈性的社交網路設計模式。"
keywords: "社交媒體應用程式"
services: cosmos-db
author: ealsur
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 2dbf83a7-512a-4993-bf1b-ea7d72e095d9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 43025adeaf954fedfbcee32e636fb30935f2126b
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="going-social-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 跨足社交
在當今大幅互連的社會當中，我們的生活或多或少都成為 **社交網路**的一部分。 我們會使用社交網路與朋友、同事、家人保持聯絡，有時候還可以跟擁有共同興趣的人交流這份愛好。

身為工程師或開發人員的我們，可能會好奇這些網路如何儲存我們的資料以及與其互連，或可能已針對本身的特定利基市場，建立或建構新的社交網路。 這時候，最大的問題出現了︰該如何儲存這些所有資料？

好比說，我們要建立脫穎而出的全新社交網路，讓使用者可以張貼文章並內含相關媒體，例如圖片、影片，甚至音樂。 使用者可以評論貼文，並給予評等的點數。 他們可以看到貼文摘要，並可在主要的網站登陸頁面上進行互動。 乍聽之下似乎不複雜，但為了保持簡潔，我們先把話題停留在此 (畢竟若要深入探討受關聯性影響的自訂使用者摘要，已超過本文的主題了)。

那麼，這些資料到底要如何儲存，以及儲存在哪呢？

許多人可能有使用 SQL 資料庫的經驗，或至少具有 [關聯式資料模型化](https://en.wikipedia.org/wiki/Relational_model) 的概念，因此可能會著手繪製類似如下的內容︰

![說明相對關聯式模型的圖表](./media/social-media-apps/social-media-apps-sql.png) 

這麼正規且漂亮的資料結構... 卻無法調整。 

別誤會，我已經使用 SQL 資料庫很久了，知道它的確很棒，但它也跟其他的模式、做法和軟體平台一樣，不可能是萬能的。

為什麼 SQL 不是此案例的最佳選擇？ 讓我們看看一篇貼文的結構，如果我想要在網站或應用程式中顯示這篇貼文，則必須執行查詢並指定... 8 個資料表聯結 (！)，就只為了顯示單一貼文，現在，想像有一連串文章在螢幕上動態載入和出現，您可能會知道我想怎麼做。

當然，我們還是可以使用一個龐大的 SQL 執行個體，來求解含有眾多聯結的數千個查詢並提供內容，但如果有一個比較簡單的解決方案，為什麼還要這麼大費周章？

## <a name="the-nosql-road"></a>NoSQL 的方法
本文將引導您如何以具成本效益的方式使用 Azure 的 NoSQL 資料庫 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)，將社交平台的資料模型化，同時利用 [Gremlin 圖形 API](../cosmos-db/graph-introduction.md) 等其他 Azure Cosmos DB 功能。 如果使用 [NoSQL](https://en.wikipedia.org/wiki/NoSQL) 方法，將資料以 JSON 格式儲存，並套用[反正規化](https://en.wikipedia.org/wiki/Denormalization)，即可將之前複雜的貼文轉換成單一[文件](https://en.wikipedia.org/wiki/Document-oriented_database)：


    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

如此一來，即可利用單一查詢而不需要任何聯結，即可取得該文件。 這麼做不只更簡單、直接也節省成本，而且只需較少的資源，即可達到更佳的結果。

Azure Cosmos DB 可利用自身的自動索引編製作業，確保所有屬性都已編製成索引，甚至還可以進行[自訂](indexing-policies.md)。 無結構描述的方法可讓我們使用不同的動態結構來儲存文件，日後若我們希望貼文具有類別或已與其建立關聯的主題標籤清單時，不需進行任何額外的工作，即可由 Cosmos DB 來處理新的文件與新增的屬性。

某篇文章的回應可視為含父屬性的其他貼文 (如此可簡化物件的對應)。 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

接著，即可將所有社交互動儲存為個別物件上的計數器︰

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

若要建立摘要，只需建立文件，包含具有特定相關性順序的貼文識別碼清單即可︰

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

例如，我們可以設定依建立日期的「最新」貼文串流、過去 24 小時內獲得較多讚的「最熱門」貼文串流，甚至可以依據邏輯 (例如關注者與興趣) 為每位使用者實作自訂串流，而這仍屬於文章清單。 關鍵在於如何建立這些清單，而且讀取效能不會受到影響。 在取得其中一份清單之後，我們可以使用 [IN 運算子](documentdb-sql-query.md#WhereClause)向 Cosmos DB 發出單一查詢，一次取得貼文的頁面。

您可以使用 [Azure App Service](https://azure.microsoft.com/services/app-service/) 的背景處理序 [Webjobs](../app-service-web/web-sites-create-web-jobs.md) 來建置摘要串流。 建立貼文之後就會觸發背景處理，方法是使用 [Azure 儲存體](https://azure.microsoft.com/services/storage/)[佇列](../storage/queues/storage-dotnet-how-to-use-queues.md)，以及使用 [Azure Webjobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) 觸發的 Webjobs，根據我們的自訂邏輯，在串流內進行貼文的傳播。 

您也可以使用相同的技術，以延後方式來處理貼文的點數和按讚數，建立最終一致的環境。

至於粉絲，則需要有更多的技巧來處理。 Cosmos DB 擁有最大的文件大小限制，在讀取/寫入大型文件時可能會影響應用程式的延展性。 因此，您可能會考慮使用下列結構，以文件形式儲存粉絲：

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

這可能適用於有數千位粉絲的使用者，但是，如果有一些名人加入我們的行列，這個處理方法會導致產生大型文件，因此最終可能會觸達文件大小上限。

為了解決這個問題，我們可以使用混合的處理方法。 我們可以在使用者統計資料文件中儲存粉絲人數：

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

接著使用 Azure Cosmos DB [Gremlin 圖形 API](../cosmos-db/graph-introduction.md)，將實際的粉絲圖表儲存，並針對每位使用者和[邊緣](http://mathworld.wolfram.com/GraphEdge.html)建立[頂點](http://mathworld.wolfram.com/GraphVertex.html)，以維護「A-追蹤-B」的關聯性。 圖形 API 不僅可讓您取得特定使用者的粉絲，還可建立更複雜的查詢，甚至能建議具有共通點的人。 如果我們將圖表新增至大眾喜愛的內容類別，即可開始編排含有智慧型內容探索的體驗、建議我們追蹤與喜愛的內容，或尋找可能與我們有許多共通點的人。

您仍可運用使用者統計資料的文件，來建立 UI 或快速分析預覽中的卡片。

## <a name="the-ladder-pattern-and-data-duplication"></a>「階梯」模式與資料複製
您可能已經注意到，參考貼文的 JSON 文件中，有一位使用者出現多次。 您猜的沒錯，這意謂著在使用這種反正規化的情況下，代表使用者的資訊可能會出現在多個位置。

為了能更快速地查詢，所以產生了資料重複的情況。 這個副作用產生的問題是，如果某些動作導致使用者的資料變更，我們就得尋找該使用者進行過的所有活動，並全數更新。 聽起來不太實際，對吧？

我們可以找出應用程式中每項活動顯示的使用者關鍵屬性，來解決此問題。 如果我們會在應用程式中看到某篇貼文，且只會顯示建立者的名稱與圖片，那為什麼要將所有的使用者資料都儲存在 “createdBy” 屬性中呢？ 如果我們只要針對每個回應顯示使用者的圖片，就不需要使用者資訊的其餘部分。 這時就可以用到我所謂的「階梯模式」。

讓我們以使用者資訊當作範例︰

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

查看此資訊可以快速偵測出哪些是重要資訊而哪些不是，以建立出一道「階梯」：

![階梯模式的圖表](./media/social-media-apps/social-media-apps-ladder.png)

最小的一階稱為「使用者區塊」，其為可識別使用者並為資料重複使用的最基本部分。 只要將重複資料的大小縮減為僅限我們「顯示」的資訊，即可降低大量更新的可能性。

中間的階梯稱為「使用者」，其為在 Cosmos DB 中大多數與效能相依之查詢會用到的完整資料，也是最重要與最常存取的資料。 它包含「使用者區塊」所代表的資訊。

最大的一階為「擴充使用者」。 它包含所有重要的使用者資訊，加上其他並不需要快速讀取或最終使用情況的相關資料 (例如登入過程)。 這些資料可以儲存在 Cosmos DB 外部、Azure SQL Database 中或 Azure 儲存體資料表中。

為什麼我們要分割使用者，甚至在不同位置儲存這些資訊？ 因為從效能方面來看，文件越大，查詢也就越昂貴。 讓文件保持精簡，只包含適當資訊以針對社交網路進行所有與效能相依的查詢，並將用於最終情況 (例如完整的設定檔編輯、登入，甚至是針對資料採礦進行的使用情況分析和巨量資料計劃) 所需的其他額外資訊加以儲存。 我們不用擔心針對資料採礦進行的資料收集作業是否比較慢，因為這項作業執行於 Azure SQL Database 上，但我們必須保障使用者擁有快速且精簡的體驗。 儲存在 Cosmos DB 中的使用者，應類似如下︰

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

而貼文應該看起來像這樣：

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

如果影響到其中一個區塊屬性之處發生編輯，您也可以使用指向索引屬性的查詢 (SELECT * FROM posts p WHERE p.createdBy.id == “edited_user_id”)，然後更新區塊，即可輕鬆找到受影響的文件。

## <a name="the-search-box"></a>搜尋方塊
一般來說，使用者都能很幸運地產生許多內容。 即使內容不是直接在本身的內容串流中，我們也應該可以提供搜尋及尋找這些內容的功能，因為有時我們可能不想關注建立者，或只是想要尋找 6 個月前的舊貼文。

幸好，我們使用的是 Azure Cosmos DB，因此可以透過 [Azure 搜尋服務](https://azure.microsoft.com/services/search/) ，輕鬆地在幾分鐘的時間內實作搜尋引擎，而不需要輸入任何一行程式碼 (當然除了搜尋程序和 UI 以外)。

為什麼可以這麼輕鬆？

Azure 搜尋服務會實作[索引子](https://msdn.microsoft.com/library/azure/dn946891.aspx) (這是與您資料儲存機制連結的背景處理序)，並會自動新增、更新或移除您在索引中的物件。 它們支援 [Azure SQL Database 索引子](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/)、[Azure Blob 索引子](../search/search-howto-indexing-azure-blob-storage.md)，甚至也支援 [Azure Cosmos DB 索引子](../search/search-howto-index-documentdb.md)。 從 Cosmos DB 到 Azure 搜尋服務的資訊轉換相當簡單，因為兩者皆是以 JSON 格式儲存，我們只需要[建立索引](../search/search-create-index-portal.md)，並從我們要編製索引的文件來對應屬性即可。短短幾分鐘內 (取決於資料大小而定)，即可透過雲端基礎結構中的最佳搜尋即服務解決方案來搜尋所有內容。 

如需 Azure 搜尋服務的詳細資訊，請瀏覽 [搜尋漫遊指南](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)。

## <a name="the-underlying-knowledge"></a>基礎知識
儲存這些所有內容之後，隨著內容每天不斷增長，我們可能會考慮到該怎麼運用這些使用者的資訊的串流？

答案很簡單：讓這些內容發揮效益，並從中學習。

但是，我們可以學習到什麼呢？ 其中幾個簡單的範例包括 [情感分析](https://en.wikipedia.org/wiki/Sentiment_analysis)、根據使用者喜好設定的內容建議，甚至還包括自動化內容仲裁者，其可確保社交網路所發行的所有內容都適合闔家瀏覽。

現在，大家一定更感興趣了吧？您一定以為要有數學的博士學位，才能從簡單的資料庫和檔案中擷取這些模式和資訊，但您錯了。

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) 是一項完全受管理的雲端服務，其隨附於 [Cortana Intelligence Suite](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx)，可讓您透過簡單的拖放介面使用演算法來建立工作流程、以 [R](https://en.wikipedia.org/wiki/R_\(programming_language\)) 撰寫自己的演算法程式碼，或使用一些內建和現成的 API，例如︰[文字分析](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2)、[內容仲裁者](https://www.microsoft.com/moderator)或[建議](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2)。

為了達成上述任一個機器學習服務案例，我們可以使用 [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) 內嵌不同來源的資訊，並使用 [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) 來處理資訊，並產生可由 Azure Machine Learning 處理的輸出。

另一個可行的做法是使用 [Microsoft 辨識服務](https://www.microsoft.com/cognitive-services)來分析使用者內容；我們不但可以更了解它們 (以[文字分析 API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api) 分析它們撰寫的東西)，也可以利用[電腦願景 API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api) 偵測到不想要或成熟的內容，並採取適當動作。 辨識服務中有許多現成的解決方案，不需要任何機器學習的知識也能使用。

## <a name="a-planet-scale-social-experience"></a>全球規模的社交體驗
最後，我還有一項重要的主題必須和各位分享，那就是「延展性」。 設計架構時，讓每個元件都能各自延展是非常重要的，不論是因為需要處理更多資料，還是想要有更廣泛的地理涵蓋範圍 (或兩者皆是！)。 幸好，透過 Cosmos DB，我們便能輕鬆完成如此複雜的工作。

Cosmos DB 預設便支援[動態分割 (英文)](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/)，它會根據特定「資料分割索引鍵」(定義為您文件中的其中一項屬性) 自動建立資料分割。 定義正確的分割區索引鍵必須在設計時期完成，並留意可用的[最佳做法](../cosmos-db/partition-data.md#designing-for-partitioning)。以社交體驗的案例而言，您的分割策略必須符合您進行查詢 (理想情況為在相同分割區內進行讀取) 及寫入 (將寫入分散在多個分割區以避免「熱點」) 的方式。 一些選項為：根據時態索引鍵 (日期/月份/週)、依內容分類、依地理區域、依使用者等等的分割區。這完全視您查詢資料及將它顯示於社交體驗的方式而定。 

其中值得注意的一點是，Cosmos DB 會透明地在所有資料分割上執行您的查詢 (包括[彙總 (英文)](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/))，您不需要隨資料增加而新增任何邏輯。

流量最終會隨時間增長，而您的資源消耗 (以 [RU](request-units.md) (要求單位) 為單位) 也會增加。 隨著使用者數量的增長，讀取及寫入也會變得更頻繁，使用者將會建立及讀取更多內容，因此「調整輸送量」的能力極為重要。 增加 RU 非常容易，只要在 Azure 入口網站按幾下，或是[透過 API 發出命令 (英文)](https://docs.microsoft.com/rest/api/documentdb/replace-an-offer) 即可。

![相應增加及定義分割區索引鍵](./media/social-media-apps/social-media-apps-scaling.png)

如果很幸運地，有來自其他地區、國家或洲大陸的使用者注意到您的平台，並開始使用它，這還真是個好消息！

不過，您很快就發現他們無法從您的平台取得最佳的體驗，因為他們離您的作業區域太遠，使延遲變得非常嚴重。但您當然也不希望他們因此而放棄使用。 要是有方法能輕鬆「觸達全球使用者」就好了。當然有！

Cosmos DB 可讓您按幾下就能透明地[將資料複寫至全球](../cosmos-db/tutorial-global-distribution-documentdb.md)，並且自動從您的[用戶端程式碼](../cosmos-db/tutorial-global-distribution-documentdb.md)選取可用的區域。 這也表示您可以擁有[多個容錯移轉區域](regional-failover.md)。 

當您將資料複寫至全球時，您必須確保您的用戶端能充分利用它。 如果您是使用 Web 前端，或是從行動用戶端存取 API，您可以部署 [Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)並將您的 Azure App Service 複製到所有需要的區域，並使用[效能設定](../app-service-web/web-sites-traffic-manager.md)來支援擴展的全球涵蓋範圍。 當用戶端存取您的前端或 API 時，系統會將它們路由至最接近的 App Service，以便連接到當地的 Cosmos DB 複本。

![為您的社交平台加入全球涵蓋範圍](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>結論
本篇文章嘗試探討以低成本的服務在 Azure 上完整建立社交網路的替代方案，並鼓勵使用多層次儲存體解決方案和稱為「階梯」的資料分散方式，提供更好的結果。

![Azure 服務之間社交網路互動的圖表](./media/social-media-apps/social-media-apps-azure-solution.png)

事實上，這類案例並沒有萬靈丹，而是靠下列強大服務組合所建立的綜效，它們可讓我們建立絕佳體驗：Azure Cosmos DB 的高速和自由性，可提供強大的社交應用程式；Azure 搜尋服務這類頂級搜尋解決方案背後蘊藏的智慧；靈活的 Azure App Service，不只能主控不限語言的應用程式，還可主控功能強大的背景處理序；可儲存大量資料的可擴充 Azure 儲存體和 Azure SQL Database；具分析功能的 Azure Machine Learning，可建立知識與智慧，以提供處理序的意見反應並協助我們將正確的內容傳遞給適當的使用者。

## <a name="next-steps"></a>後續步驟
若要深入了解 Cosmos DB 的使用案例，請參閱[常見 Cosmos DB 使用案例](use-cases.md)。
