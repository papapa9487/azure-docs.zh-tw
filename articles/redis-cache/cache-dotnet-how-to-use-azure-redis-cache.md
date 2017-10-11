---
title: "如何使用 Azure Redis 快取 | Microsoft Docs"
description: "了解如何改善與 Azure Redis 快取的 Azure 應用程式的效能"
services: redis-cache,app-service
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/27/2017
ms.author: sdanie
ms.openlocfilehash: 3dfc026490093523446650c510dbebdd660e8b6b
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-use-azure-redis-cache"></a>如何使用 Azure Redis 快取
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

本指南示範如何開始使用 **Azure Redis 快取**。 Microsoft Azure Redis 快取是基於受歡迎的開放原始碼 Redis 快取。 它可讓您存取由 Microsoft 管理的安全、專用 Redis 快取。 使用 Azure Redis 快取建立的快取，可透過 Microsoft Azure 內的任何應用程式加以存取。

Microsoft Azure Redis 快取有下列階層：

* **基本** - 單一節點。 多種大小，最高為 53 GB。
* **標準** – 兩個節點 (主要/從屬)。 多種大小，最高為 53 GB。 99.9% SLA。
* **進階** – 兩個節點的主要/從屬，最多具有 10 個分區。 提供多種大小，範圍從 6 GB 到 530 GB。 所有「標準」層級的功能以及更多功能，可支援 [Redis 叢集](cache-how-to-premium-clustering.md)、[Redis 持續性](cache-how-to-premium-persistence.md)和 [Azure 虛擬網路](cache-how-to-premium-vnet.md)。 99.9% SLA。

每一個階層都有不同的功能和價格。 如需價格的相關資訊，請參閱 [快取價格詳細資料][Cache Pricing Details]。

本指南說明如何使用採用 C\# 程式碼的 [StackExchange.Redis][StackExchange.Redis] 用戶端。 涵蓋的案例包括**建立和設定快取**、**設定快取用戶端**，以及**新增和移除快取中的物件**。 如需使用 Azure Redis 快取的詳細資訊，請參閱 [後續步驟][Next Steps]。 如需使用 Redis 快取建置 ASP.NET MVC Web 應用程式的逐步教學課程，請參閱 [如何使用 Redis 快取建立 Web 應用程式](cache-web-app-howto.md)。

<a name="getting-started-cache-service"></a>

## <a name="get-started-with-azure-redis-cache"></a>開始使用 Azure Redis 快取
開始使用 Azure Redis 快取相當簡單。 若要開始，請佈建並設定快取。 接著，設定快取用戶端，以便它們可以存取快取。 一旦設定了快取用戶端，就可以開始使用它們。

* [建立快取][Create the cache]
* [設定快取用戶端][Configure the cache clients]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>建立快取
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>在建立好之後存取快取
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

如需設定快取的詳細資訊，請參閱 [如何設定 Azure Redis 快取](cache-configure.md)。

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>設定快取用戶端
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

一旦設定用戶端專案的快取功能，您就可以使用下列幾節中描述的技術來使用快取。

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>使用快取
本節中的步驟描述如何利用快取執行常見工作。

* [連接到快取][Connect to the cache]
* [從快取新增和擷取物件][Add and retrieve objects from the cache]
* [使用快取中的 .NET 物件](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>

## <a name="connect-to-the-cache"></a>連接到快取
若要以程式設計方式使用快取，您需要快取的參考。 將下面這一行加入至您想要從中使用 StackExchange.Redis 用戶端來存取 Azure Redis 快取之檔案的頂端。

    using StackExchange.Redis;

> [!NOTE]
> StackExchange.Redis 用戶端需要 .NET Framework 4 或更高版本。
> 
> 

與 Azure Redis 快取的連線是由 `ConnectionMultiplexer` 類別所管理。 此類別應要在用戶端應用程式中共用和重複使用，而不需要依據每個作業加以建立。 

若要連線至 Azure Redis 快取，並傳回已連線 `ConnectionMultiplexer` 的執行個體，請呼叫靜態 `Connect` 方法，並傳入快取端點和金鑰。 使用從 Azure 入口網站產生的金鑰做為密碼參數。

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

> [!IMPORTANT]
> 警告：請勿將認證儲存在原始程式碼中。 為了讓這個範例簡單明瞭，我會以原始程式碼來呈現認證內容。 如需如何儲存認證的相關資訊，請參閱[應用程式字串與連接字串的運作方式][How Application Strings and Connection Strings Work]。
> 
> 

如果您不想使用 SSL，請設定 `ssl=false` 或省略 `ssl` 參數。

> [!NOTE]
> 預設會為新快取停用非 SSL 連接埠。 如需啟用非 SSL 連接埠的指示，請參閱[存取連接埠](cache-configure.md#access-ports)。
> 
> 

在您的應用程式中共用 `ConnectionMultiplexer` 執行個體的其中一種方法，就是擁有可傳回已連接執行個體的靜態屬性，類似下列範例。 此方法提供安全執行緒方式，只初始化單一已連接的 `ConnectionMultiplexer` 執行個體。 在這些範例中， `abortConnect` 已設為 false，這表示即使無法建立與 Azure Redis 快取的連線，呼叫也會成功。 `ConnectionMultiplexer` 的主要功能之一，就是一旦網路問題或其他原因獲得解決，它就會自動恢復與快取的連接。

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

如需進階連線組態選項的詳細資訊，請參閱 [StackExchange.Redis 組態模型][StackExchange.Redis configuration model]。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

一旦建立連線，即會透過呼叫 `ConnectionMultiplexer.GetDatabase` 方法傳回 Redis 快取資料庫的參考。 透過 `GetDatabase` 方法傳回的物件是輕量型傳遞物件，而且不需要儲存。

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Azure Redis 快取具有可供用來以邏輯方式區隔 Redis 快取內資料的可設定數目資料庫 (預設值為 16 個)。 如需詳細資訊，請參閱 [Redis 資料庫是什麼？](cache-faq.md#what-are-redis-databases)和[預設 Redis 伺服器組態](cache-configure.md#default-redis-server-configuration)。

現在您知道如何連線至 Azure Redis 快取執行個體，並傳回快取資料庫的參考，讓我們來看看如何使用快取。

<a name="add-object"></a>

## <a name="add-and-retrieve-objects-from-the-cache"></a>從快取新增和擷取物件
您可以使用 `StringSet` 和 `StringGet` 方法，在快取中儲存或擷取項目。

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis 會將多數資料儲存為 Redis 字串，但這些字串可能包含許多類型的資料，包括序列化的二進位資料 (在快取中儲存 .NET 物件時可能會用到)。

呼叫 `StringGet` 時，如果物件已存在，即會傳回，如果物件不存在，則會傳回 `null`。 如果傳回 `null`，您可以從需要的資料來源中擷取值，並將它儲存在快取中供後續使用。 這種使用模式稱為另行快取模式。

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

您也可以使用 `RedisValue`，如下列範例所示。 `RedisValue` 有隱含的運算子適用於整數資料類型，而如果 `null` 是預期的快取項目值則很實用。


    RedisValue value = cache.StringGet("key1");
    if (!value.HasValue)
    {
        value = GetValueFromDataSource();
        cache.StringSet("key1", value);
    }


若要指定快取中項目的到期時間，請使用 `StringSet` 的 `TimeSpan` 參數。

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>使用快取中的 .NET 物件
Azure Redis 快取可以快取 .NET 物件及基本資料類型，但必須先將 .NET 物件序列化，才能加以快取。 .NET 物件序列化是應用程式開發人員的責任，同時賦與開發人員選擇序列化程式的彈性。

將物件序列化的其中一個簡單方法就是使用 [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) 中的 `JsonConvert` 序列化方法並進行 JSON 的雙向序列化。 下列範例使用 `Employee` 物件執行個體顯示 get 和 set。

    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>

## <a name="next-steps"></a>後續步驟
了解基礎概念之後，請依照下列連結深入了解 Azure Redis 快取。

* 查看 Azure Redis 快取的 ASP.NET 提供者。
  * [Azure Redis 工作階段狀態提供者](cache-aspnet-session-state-provider.md)
  * [Azure Redis 快取 ASP.NET 輸出快取提供者](cache-aspnet-output-cache-provider.md)
* [啟用快取診斷](cache-how-to-monitor.md#enable-cache-diagnostics)，以[監視](cache-how-to-monitor.md)您快取的健全狀況。 您可以在 Azure 入口網站中檢視度量，也可以使用您選擇的工具 [下載並檢閱](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) 它們。
* 請參閱 [StackExchange.Redis 快取用戶端文件][StackExchange.Redis cache client documentation]。
  * Azure Redis 快取可以透過許多 Redis 用戶端和開發語言進行存取。 如需詳細資訊，請參閱 [http://redis.io/clients][http://redis.io/clients]。
* Azure Redis 快取也可以與協力廠商服務和工具搭配使用 (例如 Redsmin 和 Redis Desktop Manager)。
  * 如需 Redsmin 的詳細資訊，請參閱 [如何擷取 Azure Redis 連接字串並將它與 Redsmin 搭配使用][How to retrieve an Azure Redis connection string and use it with Redsmin]。
  * 使用 [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager)，透過 GUI 存取和檢查 Azure Redis 快取中的資料。
* 請參閱 [redis][redis] 文件，並閱讀有關 [Redis 資料類型][redis data types]和 [Redis 資料類型的 15 分鐘簡介][a fifteen minute introduction to Redis data types]。

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[Connect to the cache]: #connect-to-cache
[Add and retrieve objects from the cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session


<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png







<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[How to retrieve an Azure Redis connection string and use it with Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis configuration model]: https://stackexchange.github.io/StackExchange.Redis/Configuration

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/pricing/details/cache/
[Azure portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis cache client documentation]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis data types]: http://redis.io/topics/data-types
[a fifteen minute introduction to Redis data types]: http://redis.io/topics/data-types-intro

[How Application Strings and Connection Strings Work]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


