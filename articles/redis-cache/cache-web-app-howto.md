---
title: "如何使用 Redis 快取建立 Web 應用程式 | Microsoft Docs"
description: "了解如何使用 Redis 快取建立 Web 應用程式"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 05/09/2017
ms.author: sdanie
ms.openlocfilehash: 21dc87b3e8c26bfbda36202b31b3b4d44be32179
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>如何使用 Redis 快取建立 Web 應用程式
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

本教學課程示範如何使用 Visual Studio 2017，在 Azure App Service 的 Web 應用程式中建立和部署 ASP.NET Web 應用程式。 此範例應用程式會顯示資料庫中的隊伍統計資料清單，並示範各種使用 Azure Redis 快取在快取中儲存和擷取資料的方式。 完成本教學課程時您將會擁有執行中的 Web 應用程式，其可對資料庫進行讀取和寫入、已使用 Azure Redis 快取進行最佳化，並裝載在 Azure 中。

您將了解：

* 如何在 Visual Studio 中建立 ASP.NET MVC 5 Web 應用程式。
* 如何使用 Entity Framework 從資料庫存取資料。
* 如何透過使用 Azure Redis 快取來儲存和擷取資料，以改善資料輸送量並降低資料庫負載。
* 如何使用 Redis 的已排序集合來擷取前 5 名的隊伍。
* 如何使用 Resource Manager 範本為應用程式佈建 Azure 資源。
* 如何使用 Visual Studio 將應用程式發佈至 Azure。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您必須具備下列必要條件。

* [Azure 帳戶](#azure-account)
* [Visual Studio 2017 (含 Azure SDK for .NET)](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Azure 帳戶
您需要有 Azure 帳戶，才能完成本教學課程。 您可以：

* [免費申請 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero)。 您將獲得能用來試用 Azure 付費服務的額度。 即使在額度用完後，您仍可保留帳戶並使用免費的 Azure 服務和功能。
* [啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero)。 您的 MSDN 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。

### <a name="visual-studio-2017-with-the-azure-sdk-for-net"></a>Visual Studio 2017 (含 Azure SDK for .NET)
本教學課程是特別為 Visual Studio 2017 (含 [Azure SDK for .NET](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools)) 所撰寫。 Azure SDK 2.9.5 隨附於 Visual Studio 安裝程式。

如果您有 Visual Studio 2015，您可以遵循 [Azure SDK for.NET](../dotnet-sdk.md) 2.8.2 或更新版本的教學課程。 [在此下載最新的 Azure SDK for Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003)。 如果您沒有 Visual Studio，它會自動與 SDK 一起安裝。 有些畫面看起來可能與本教學課程所示插圖不同。

如果您有 Visual Studio 2013，您可以 [下載最新的 Azure SDK for Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322)。 有些畫面看起來可能與本教學課程所示插圖不同。

## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案
1. 開啟 Visual Studio，然後按一下檔案新增、專案。
2. 展開 [範本] 清單中的 [Visual C#] 節點、選取 [雲端]，然後按一下 [ASP.NET Web 應用程式]。 確定已選取 [.NET Framework 4.5.2] 或更新版本。  在 [名稱] 文字方塊中輸入 **ContosoTeamStats**，然後按一下 [確定]。
   
    ![建立專案][cache-create-project]
3. 選取 [MVC]  做為專案類型。 

    確保已針對 [驗證] 設定指定 [不需要驗證]。 視您的 Visual Studio 版本而言，預設值可能會設定為其他項目。 若要加以變更，請按一下 [變更驗證]，然後選取 [不需要驗證]。

    如果您密切注意 Visual Studio 2015，請清除 [雲端中的主機] 核取方塊。 在本教學課程的後續步驟中，您將會[佈建 Azure 資源](#provision-the-azure-resources)和[發佈應用程式至 Azure](#publish-the-application-to-azure)。 如需在保持核取 [雲端中的主機]  的狀態下從 Visual Studio 佈建 App Service Web 應用程式的範例，請參閱 [使用 ASP.NET 和 Visual Studio 在 Azure App Service 中開始使用 Web Apps](../app-service/app-service-web-get-started-dotnet.md)。
   
    ![選取專案範本][cache-select-template]
4. 按一下 [確定]  以建立專案。

## <a name="create-the-aspnet-mvc-application"></a>建立 ASP.NET MVC 應用程式
在本教學課程的這一節當中，您將會建立可從資料庫讀取並顯示隊伍統計資料的基本應用程式。

* [新增 Entity Framework NuGet 套件](#add-the-entity-framework-nuget-package)
* [新增模型](#add-the-model)
* [新增控制器](#add-the-controller)
* [設定檢視](#configure-the-views)

### <a name="add-the-entity-framework-nuget-package"></a>新增 Entity Framework NuGet 套件

1. 按一下 [NuGet 套件管理員]、[工具] 功能表中的 [套件管理員主控台]。
2. 從 [Package Manager Console] 視窗中執行下列命令。
    
    ```
    Install-Package EntityFramework
    ```

如需此套件的詳細資訊，請參閱 [EntityFramework](https://www.nuget.org/packages/EntityFramework/) NuGet 頁面。

### <a name="add-the-model"></a>新增模型
1. 在 [方案總管] 中以滑鼠右鍵按一下 [模型]，並選擇 [新增][類別]。 
   
    ![新增模型][cache-model-add-class]
2. 針對類別名稱輸入 `Team`，然後按一下 [新增]。
   
    ![新增模型類別][cache-model-add-class-dialog]
3. 將 `Team.cs` 檔案頂端的 `using` 陳述式替換為下列 `using` 陳述式。

    ```c#
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```


1. 將 `Team` 類別的定義取代為包含已更新的 `Team` 類別定義以及一些其他 Entity Framework 協助程式類別的下列程式碼片段。 如需本教學課程中使用之 Entity Framework 的 Code First 方法的詳細資訊，請參閱 [Code First 至新的資料庫](https://msdn.microsoft.com/data/jj193542)。

    ```c#
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }
    
        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();
    
            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }
    
    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }
    
        public DbSet<Team> Teams { get; set; }
    }
    
    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };
    
            Team.PlayGames(teams);
    
            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }
    
    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```


1. 在 [方案總管] 中，連按兩下 [web.config] 來加以開啟。
   
    ![Web.config][cache-web-config]
2. 新增下列 `connectionStrings` 區段。 連接字串的名稱必須符合 Entity Framework 資料庫內容類別的名稱，亦即 `TeamContext`。

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    您可以新增 `connectionStrings` 區段，以便它遵照如下列範例所示的 `configSections`。

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

    > [!NOTE]
    > 連接字串可能會依用來完成本教學課程的 Visual Studio 和 SQL Server Express Edition 而有所不同。 應該將 Web.config 範本設定為符合您的安裝，而且可能包含 `Data Source` 項目，例如 `(LocalDB)\v11.0`(從 SQL Server Express 2012) 或 `Data Source=(LocalDB)\MSSQLLocalDB` (從 SQL Server Express 2014 及更新版本)。 如需有關連接字串和 SQL Express 版本的詳細資訊，請參閱 [SQL Server 2016 Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。

### <a name="add-the-controller"></a>新增控制器
1. 按 **F6** 來建置專案。 
2. 在 [方案總管] 中，於 [控制器] 資料夾上按一下滑鼠右鍵，然後依序選擇 [新增] 和 [控制器]。
   
    ![新增控制器][cache-add-controller]
3. 選擇 [使用 Entity Framework，包含檢視的 MVC 5 控制器]，然後按一下 [新增]。 如果您在按一下 [新增] 後收到錯誤，請確定您已先建置專案。
   
    ![新增控制器類別][cache-add-controller-class]
4. 在 [模型類別] 下拉式清單中選取 [Team (ContosoTeamStats.Models)]。 在 [資料內容類別] 下拉式清單中選取 [TeamContext (ContosoTeamStats.Models)]。 在 [控制器名稱] 文字方塊中輸入 `TeamsController` (如果尚未自動填入)。 按一下 [新增]  以建立控制器類別並新增預設檢視。
   
    ![設定控制器][cache-configure-controller]
5. 在 [方案總管] 中展開 [Global.asax]，然後按兩下 [Global.asax.cs] 來加以開啟。
   
    ![Global.asax.cs][cache-global-asax]
6. 在檔案頂端的其他 `using` 陳述式底下新增下列兩個 `using` 陳述式。

    ```c#
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```


1. 在 `Application_Start` 方法的結尾新增下列程式碼行。

    ```c#
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```


1. 在 [方案總管] 中展開 `App_Start`，然後按兩下 `RouteConfig.cs`。
   
    ![RouteConfig.cs][cache-RouteConfig-cs]
2. 在 `RegisterRoutes` 方法的下列程式碼中，將 `controller = "Home"` 取代為 `controller = "Teams"`，如下列範例所示。

    ```c#
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```


### <a name="configure-the-views"></a>設定檢視
1. 在 [方案總管] 中依序展開 [檢視] 資料夾和 [共用] 資料夾，然後按兩下 **_Layout.cshtml**。 
   
    ![_Layout.cshtml][cache-layout-cshtml]
2. 變更 `title` 元素的內容，並將 `My ASP.NET Application` 取代為 `Contoso Team Stats`，如下列範例所示。

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```


1. 在 `body` 區段中，更新第一個 `Html.ActionLink` 陳述式，並將 `Application name` 取代為 `Contoso Team Stats` 以及將 `Home` 取代為 `Teams`。
   
   * 取代前： `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * 取代後： `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![程式碼變更][cache-layout-cshtml-code]
2. 按 **Ctrl+F5** 以建置並執行應用程式。 這個版本的應用程式會直接從資料庫讀取結果。 請注意透過 [使用 Entity Framework，包含檢視的 MVC 5 控制器] scaffold 自動新增至應用程式的 [建立新的]、[編輯]、[詳細資料] 和 [刪除] 動作。 在本教學課程的下一節裡，您將會新增 Redis 快取以將資料存取最佳化並為應用程式提供其他功能。

![起始應用程式][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>設定應用程式以使用 Redis 快取
在教學課程的這一節當中，您將會設定範例應用程式，讓其使用 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 快取用戶端在 Azure Redis 快取執行個體中儲存和擷取 Contoso 的隊伍統計資料。

* [設定應用程式以使用 StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [更新 TeamsController 類別以從快取或資料庫中傳回結果](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [更新用來使用快取的建立、編輯和刪除方法](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [更新用來使用快取的隊伍索引檢視](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>設定應用程式以使用 StackExchange.Redis
1. 若要在 Visual Studio 中使用 StackExchange.Redis NuGet 套件來設定用戶端應用程式，請按一下 [工具] 功能表中的 [NuGet 套件管理員]、[套件管理員主控台]。
2. 從 `Package Manager Console` 視窗執行下列命令。
    
    ```
    Install-Package StackExchange.Redis
    ```
   
    NuGet 封裝會為您的用戶端應用程式下載並加入必要的組件參考，以利用 StackExchange.Redis 快取用戶端來存取 Azure Redis 快取。 如果您偏好使用強式命名的 `StackExchange.Redis` 用戶端程式庫版本，請安裝 `StackExchange.Redis.StrongName` 套件。
3. 在 [方案總管] 中展開 [控制器] 資料夾，然後按兩下 [TeamsController.cs] 來加以開啟。
   
    ![隊伍控制器][cache-teamscontroller]
4. 在 **TeamsController.cs** 中加入下列兩個 `using` 陳述式。

    ```c#   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. 將下列兩個屬性加入至 `TeamsController` 類別。

    ```c#   
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

6. 在您的電腦上建立名為 `WebAppPlusCacheAppSecrets.config` 的檔案，並將它放在範例應用程式的原始程式碼不會簽入的位置 (如果您決定在某處簽入檔案)。 在此範例中，`AppSettingsSecrets.config` 檔案會放在 `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`。
   
    編輯 `WebAppPlusCacheAppSecrets.config` 檔案，並加入下列內容。 如果您是在本機執行此應用程式，這項資訊將會用來連線到您的 Azure Redis 快取執行個體。 在本教學課程稍後，您將會佈建 Azure Redis 快取執行個體，並更新快取名稱和密碼。 如果您不打算在本機執行範例應用程式，由於當您部署至 Azure 時，此應用程式會從 Web 應用程式的應用程式設定擷取快取連接資訊，而不是從這個檔案擷取，因此您可以將建立此檔案的步驟和參照此檔案的後續步驟略過。 由於 `WebAppPlusCacheAppSecrets.config` 不會隨著您的應用程式部署至 Azure，因此除非您要在本機執行此應用程式，否則您不需要它。

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. 在 [方案總管] 中，連按兩下 [web.config] 來加以開啟。
   
    ![Web.config][cache-web-config]
2. 將下列 `file` 屬性新增至 `appSettings` 元素。 如果您使用不同檔案名稱或位置，請以這些值取代範例中顯示的值。
   
   * 取代前： `<appSettings>`
   * 取代後： ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
     
   ASP.NET 執行階段會將外部檔案的內容與 `<appSettings>` 元素的標記合併。 如果找不到指定的檔案，則執行階段會略過檔案屬性。 您的密碼 (您的快取的連接字串) 不會包含在應用程式的原始程式碼中。 當您將 Web 應用程式部署至 Azure，將不會部署 `WebAppPlusCacheAppSecrests.config` 檔案 (這正是您要的結果)。 有許多方式可在 Azure 中指定這些密碼，在本教學課程中，當您在後續的教學課程步驟中 [佈建 Azure 資源](#provision-the-azure-resources) 時，系統會自動為您設定這些密碼。 如需在 Azure 中使用密碼的詳細資訊，請參閱 [將密碼和其他機密資料部署到 ASP.NET 和 Azure App Service 的最佳作法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)。

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>更新 TeamsController 類別以從快取或資料庫中傳回結果
在此範例中，隊伍統計資料可以擷取自資料庫或快取。 隊伍統計資料可儲存在快取中做為序列化的 `List<Team>`，也可以使用 Redis 資料類型做為已排序集合。 從已排序集合擷取項目時，您可以擷取部分或所有項目，或是查詢特定項目。 在此範例中，您將查詢已排序集合內獲勝次數排在前 5 名的隊伍。

> [!NOTE]
> 想要使用 Azure Redis 快取並不需要將隊伍統計資料以多種格式儲存在快取中。 本教學課程之所以使用多種格式，是為了示範某些可用來快取資料的不同方式和不同資料類型。
> 
> 

1. 在 `TeamsController.cs` 檔案頂端新增下列 `using` 陳述式，和其他 `using` 陳述式放在一起。

    ```c#   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. 將目前的 `public ActionResult Index()` 方法實作替換為下列實作。

    ```c#
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```


1. 在 `TeamsController` 類別中新增下列三種方法，實作來自先前程式碼片段中新增之 switch 陳述式中的 `playGames`、`clearCache` 和 `rebuildDB` 動作類型。
   
    `PlayGames` 方法會藉由模擬一個賽季的遊戲來更新隊伍統計資料、將結果儲存至資料庫，並清除快取中現已過時的資料。

    ```c#
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `RebuildDB` 方法會以一組預設的隊伍重新初始化資料庫、為這些隊伍產生統計資料，並清除快取中現已過時的資料。

    ```c#
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `ClearCachedTeams` 方法會移除快取中任何已快取過的隊伍統計資料。

    ```c#
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    } 
    ```


1. 在 `TeamsController` 類別中加入下列四種方法來實作各種從快取和資料庫中擷取隊伍統計資料的方式。 這四種方法皆會傳回 `List<Team>` 以在檢視中顯示。
   
    `GetFromDB` 方法會從資料庫讀取隊伍統計資料。
   
    ```c#
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t; 

        return results.ToList<Team>();
    }
    ```

    `GetFromList` 方法會從快取中讀取序列化 `List<Team>` 形式的隊伍統計資料。 如果發生快取遺漏情形，便會從資料庫讀取隊伍統計資料，然後儲存在快取中以供下次使用。 在此範例中，我們會使用 JSON.NET 序列化在快取中傳入和傳出序列化的 .NET 物件。 如需詳細資訊，請參閱 [如何在 Azure Redis 快取中使用 .NET 物件](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)。

    ```c#
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    `GetFromSortedSet` 方法會從快取的已排序集合讀取隊伍統計資料。 如果發生快取遺漏情形，便會從資料庫讀取隊伍統計資料，然後儲存在快取中做為已排序集合。

    ```c#
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    `GetFromSortedSetTop5` 方法會從快取的已排序集合讀取前 5 名的隊伍。 它會先檢查快取中是否有 `teamsSortedSet` 索引鍵。 如果這個索引鍵不存在，便會呼叫 `GetFromSortedSet` 方法來讀取隊伍統計資料，並將資料儲存在快取中。 接著便會查詢快取的已排序集合中傳回的前 5 名隊伍。

    ```c#
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>更新用來使用快取的建立、編輯和刪除方法
此範例進行過程所產生的樣板程式碼包括用來新增、編輯和刪除隊伍的方法。 每當您加入、編輯或移除隊伍時，快取中的資料就會變得過時。 在本節中，您將會修改這三種方法來清除已快取的隊伍，讓快取不會與資料庫失去同步。

1. 瀏覽至 `TeamsController` 類別中的 `Create(Team team)` 方法。 在 `ClearCachedTeams` 方法中新增呼叫，如下列範例所示。

    ```c#
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```


1. 瀏覽至 `TeamsController` 類別中的 `Edit(Team team)` 方法。 在 `ClearCachedTeams` 方法中新增呼叫，如下列範例所示。

    ```c#
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```


1. 瀏覽至 `TeamsController` 類別中的 `DeleteConfirmed(int id)` 方法。 在 `ClearCachedTeams` 方法中新增呼叫，如下列範例所示。

    ```c#
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>更新用來使用快取的隊伍索引檢視
1. 在 [方案總管] 中依序展開 [檢視] 資料夾和 [隊伍] 資料夾，然後按兩下 [Index.cshtml]。
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. 在檔案頂端附近尋找下列段落元素。
   
    ![動作資料表][cache-teams-index-table]
   
    這是用來建立新隊伍的連結。 請將此段落元素取代為下列資料表。 這個資料表內有動作連結，可供建立新的隊伍、進行新一賽季的遊戲、清除快取、從快取中以數種格式擷取隊伍、從資料庫中擷取隊伍，以及使用全新的範例資料重建資料庫。

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>    
    </table>
    ```


1. 捲動至 **Index.cshtml** 檔案底部並新增下列 `tr` 元素，使其成為檔案中最後一個資料表內的最後一個資料列。
   
    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
   
    這個資料列會顯示 `ViewBag.Msg` 的值，其包含目前作業的相關狀態報告。 當您按一下上一個步驟中的任何一個動作連結時，便會設定 `ViewBag.Msg`。   
   
    ![狀態訊息][cache-status-message]
2. 按 **F6** 來建置專案。

## <a name="provision-the-azure-resources"></a>佈建 Azure 資源
若要在 Azure 中裝載您的應用程式，您必須先佈建應用程式所需的 Azure 服務。 本教學課程中的範例應用程式會使用下列 Azure 服務。

* Azure Redis 快取
* App Service Web 應用程式
* SQL Database

若要在您選擇的新的或現有的資源群組部署這些服務，請按一下下面的 [部署至 Azure]  按鈕。

[![部署至 Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

這個 [部署至 Azure] 按鈕使用[建立 Web 應用程式、Redis 快取和 SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Azure 快速入門](https://github.com/Azure/azure-quickstart-templates)範本來佈建這些服務，並設定 SQL Database 的連接字串和 Azure Redis 快取連接字串的應用程式設定。

> [!NOTE]
> 如果您沒有 Azure 帳戶，只需要幾分鐘的時間就可以 [建立免費的 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) 。
> 
> 

按一下 [部署至 Azure]  按鈕會帶您前往 Azure 入口網站，並起始範本所述資源的建立程序。

![部署至 Azure][cache-deploy-to-azure-step-1]

1. 在 [基本] 區段中，選取要使用的 Azure 訂用帳戶，然後選取現有資源群組或建立新的資源群組，並指定資源群組的位置。
2. 在 [設定] 區段中，指定 [系統管理員登入] \(請勿使用 **admin**)、[系統管理員登入密碼] 和 [資料庫名稱]。 其他參數則針對免費的 App Service 主控方案進行設定，至於 SQL Database 和 Azure Redis 快取請設定較低成本選項，因為免費層不提供這兩種服務。

    ![部署至 Azure][cache-deploy-to-azure-step-2]

3. 設定所需的設定之後，捲動至頁面結尾，閱讀條款和條件，然後勾選 [我同意上方所述的條款及條件] 核取方塊。
4. 若要開始佈建資源，請按一下 [購買]。

若要檢視部署進度，請按一下 [通知] 圖示，然後按一下 [部署已開始] 。

![部署已開始][cache-deployment-started]

您可以在 [Microsoft.Template]  刀鋒視窗上檢視您的部署狀態。

![部署至 Azure][cache-deploy-to-azure-step-3]

佈建完成後，您就可以從 Visual Studio 將應用程式發佈至 Azure。

> [!NOTE]
> 佈建程序期間可能發生的任何錯誤皆會顯示在 [Microsoft.Template]**Microsoft.Template** 刀鋒視窗上。 常見錯誤包括 SQL Server 太多或每個訂用帳戶的免費 App Service 主控方案太多。 請解決所有錯誤，然後按一下 [Microsoft.Template] 刀鋒視窗上的 [重新部署] 或本教學課程中的 [部署至 Azure] 按鈕重新啟動程序。
> 
> 

## <a name="publish-the-application-to-azure"></a>將應用程式發佈至 Azure
在教學課程的這個步驟中，您會將應用程式發佈至 Azure 並在雲端中執行。

1. 在 Visual Studio 中以滑鼠右鍵按一下 [ContosoTeamStats] 專案，然後選擇 [發佈]。
   
    ![發佈][cache-publish-app]
2. 按一下 [Microsoft Azure App Service]，選擇 [選取現有的]，然後按一下 [發佈]。
   
    ![發佈][cache-publish-to-app-service]
3. 選取建立 Azure 資源時所使用的訂用帳戶、展開包含資源的資源群組，然後選取所需的 Web App。 如果您之前是使用 [部署至 Azure] 按鈕，您的 Web 應用程式名稱將會以 **webSite** 開頭，後面再接著一些額外字元。
   
    ![選取 Web 應用程式][cache-select-web-app]
4. 按一下 [確定]  即可開始發佈程序。 片刻過後，發佈程序會完成，而且會啟動具有執行中範例應用程式的瀏覽器。 如果您在驗證或發佈時收到 DNS 錯誤，而且應用程式的 Azure 資源佈建程序才剛完成不久，請稍候片刻，然後再試一次。
   
    ![已新增快取][cache-added-to-application]

下表描述範例應用程式中的每個動作連結。

| 動作 | 說明 |
| --- | --- |
| 建立新的 |建立新的隊伍。 |
| 遊戲賽季 |玩一個賽季的遊戲、更新隊伍統計資料，並清除快取中任何已過時的隊伍資料。 |
| 清除快取 |清除快取中的隊伍統計資料。 |
| 快取中的清單 |擷取快取中的隊伍統計資料。 如果發生快取遺漏情形，則載入資料庫中的統計資料，並儲存至快取中以供下次使用。 |
| 快取中的已排序集合 |使用已排序集合擷取快取中的隊伍統計資料。 如果發生快取遺漏的情形，請從資料庫中載入統計資料，並使用已排序集合儲存至快取中。 |
| 快取中的前 5 名隊伍 |使用已排序集合擷取快取中的前 5 名隊伍。 如果發生快取遺漏的情形，請從資料庫中載入統計資料，並使用已排序集合儲存至快取中。 |
| 從資料庫載入 |擷取資料庫中的隊伍統計資料。 |
| 重建資料庫 |重建資料庫並在其中重新載入範例隊伍資料。 |
| 編輯/詳細資料/刪除 |編輯隊伍、檢視隊伍的詳細資料、刪除隊伍。 |

對某些動作按一下，然後試驗從不同來源擷取資料。 請注意從資料庫和快取擷取資料的各種方式若要完成所需花費之時間的差異。

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>在完成應用程式時刪除資源
當您完成教學課程的範例應用程式時，您可以刪除使用的 Azure 資源以節省成本和資源。 如果您在[佈建 Azure 資源](#provision-the-azure-resources)一節使用 [部署至 Azure] 按鈕，而且您的所有資源都包含在相同的資源群組內，您可以透過刪除資源群組在單一作業中將它們一起刪除。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [資源群組]。
2. 在 [篩選項目...]  文字方塊中輸入您的資源群組名稱。
3. 按一下資源群組右邊的 [...]  。
4. 按一下 [刪除] 。
   
    ![刪除][cache-delete-resource-group]
5. 輸入您的資源群組名稱，然後按一下 [刪除] 。
   
    ![Confirm delete][cache-delete-confirm]

片刻過後，便會刪除資源群組及其所有內含的資源。

> [!IMPORTANT]
> 請注意，刪除資源群組是無法回復的動作，資源群組和其內的所有資源將會永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 如果您是在現有資源群組內建立用來裝載此範例的資源，您可以從每個資源各自的刀鋒視窗中個別刪除每個資源。
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>在您的本機電腦上執行範例應用程式
若要在本機電腦上執行應用程式，您需要 Azure Redis 快取執行個體以供快取資料。 

* 如果您已如上一節所述將應用程式發佈到 Azure，您可以使用該步驟期間佈建的 Azure Redis 快取執行個體。
* 如果您有其他現有的 Azure Redis 快取執行個體，您可以用它在本機執行此範例。
* 如果您需要建立 Azure Redis 快取執行個體，您可以依照 [建立快取](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)中的步驟來進行。

一旦您選取或建立了要使用的快取，請瀏覽至 Azure 入口網站中的快取，並擷取快取的[主機名稱](cache-configure.md#properties)和[存取金鑰](cache-configure.md#access-keys)。 如需相關指示，請參閱 [設定 Redis 快取設定](cache-configure.md#configure-redis-cache-settings)。

1. 使用您選擇的編輯器開啟本教學課程的[設定應用程式以使用 Redis 快取](#configure-the-application-to-use-redis-cache)步驟期間建立的 `WebAppPlusCacheAppSecrets.config` 檔案。
2. 編輯 `value` 屬性，並將 `MyCache.redis.cache.windows.net` 取代為快取的[主機名稱](cache-configure.md#properties)，然後將快取的[主要或次要金鑰](cache-configure.md#access-keys)指定為密碼。

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. 按 **CTRL+F5** 執行應用程式。

> [!NOTE]
> 請注意，因為包含資料庫的應用程式是在本機執行，且 Redis 快取是裝載在 Azure，因此快取可能會讓資料庫的效能變差。 為了獲得最佳效能，用戶端應用程式和 Azure Redis 快取執行個體應該位於相同的位置。 
> 
> 

## <a name="next-steps"></a>後續步驟
* 深入了解 [ASP.NET](http://asp.net/) 網站上的[開始使用 ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started)。
* 如需在 App Service 中建立 ASP.NET Web 應用程式的範例，請參閱 [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [示範](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)中的[在 Azure App Service 中建立和部署 ASP.NET Web 應用程式](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service)。
  * 如需來自 HealthClinic.biz 示範的更多快速入門，請參閱 [Azure 開發人員工具快速入門](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)。
* 深入了解本教學課程中使用之 Entity Framework 的 [Code First 至新的資料庫](https://msdn.microsoft.com/data/jj193542) 方法。
* 深入了解 [Azure App Service 中的 Web 應用程式](../app-service/app-service-web-overview.md)。
* 了解如何在 Azure 入口網站中 [監視](cache-how-to-monitor.md) 您的快取。
* 瀏覽 Azure Redis 快取進階功能
  
  * [如何設定高階 Azure Redis Cache 的永續性](cache-how-to-premium-persistence.md)
  * [如何設定高階 Azure Redis Cache 的叢集](cache-how-to-premium-clustering.md)
  * [如何設定高階 Azure Redis Cache 的虛擬網路支援](cache-how-to-premium-vnet.md)
  * 如需高階快取的大小、輸送量和頻寬等方面的詳細資訊，請參閱 [Azure Redis Cache 常見問題集](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) 。

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

