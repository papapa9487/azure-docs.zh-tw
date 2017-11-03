---
title: "跨多個 Azure SQL Database 執行臨機操作分析查詢 | Microsoft Docs"
description: "在多租用戶應用程式範例中，跨多個 SQL 資料庫執行臨機操作分析查詢"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: billgib; sstein
ms.openlocfilehash: 849f0570fb1550f6c3676fc070d0f862450ade9a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>跨多個 Azure SQL Database 執行臨機操作分析查詢

在本教學課程中，您會在整個多租用戶資料庫集合執行分散式查詢以啟用臨機操作分析。 利用「彈性查詢」來啟用分散式查詢，這需要部署額外的分析資料庫 (到目錄伺服器)。 這些查詢可以擷取藏在 Wingtip SaaS 應用程式日常操作資料中的深入解析。


您會在本教學課程中學到：

> [!div class="checklist"]

> * 每個資料庫中可跨租用戶執行高效率查詢的全域檢視
> * 如何部署臨機操作分析資料庫
> * 如何對所有租用戶資料庫執行分散式查詢



若要完成本教學課程，請確定已完成下列必要條件：

* 已部署 Wingtip SaaS 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip SaaS 應用程式](sql-database-saas-tutorial.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* 已安裝 SQL Server Management Studio (SSMS)。 若要下載和安裝 SSMS，請參閱[下載 SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。


## <a name="ad-hoc-analytics-pattern"></a>臨機操作分析模式

SaaS 應用程式的其中一個絕佳機會是使用集中儲存在雲端的大量租用戶資料。 使用此資料來深入了解應用程式的作業與使用方式。 這些深入解析可以引導應用程式和服務中的功能開發、使用性改進及其他投資。

在單一多租用戶資料庫中存取此資料很容易，但資料大規模分散於可能數千個資料庫時則不太容易存取。 其中一個方法是使用[彈性查詢](sql-database-elastic-query-overview.md)，這可對一組具有共用結構描述的分散式資料庫啟用查詢。 彈性查詢會使用單一 head 資料庫，其中定義的外部資料表會鏡射分散式 (租用戶) 資料庫中的資料表或檢視。 提交至此 head 資料庫的查詢會經過編譯，以產生分散式查詢計劃 (包含視需要往下推送到租用戶資料庫的查詢部分)。 彈性查詢會使用目錄資料庫中的分區對應，提供租用戶資料庫的位置。 安裝程式和查詢會直接使用標準 [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)，以及支援從 Power BI 和 Excel 等工具進行臨機操作查詢。

彈性查詢將查詢分散到整個租用戶資料庫，能夠立即深入了解即時的實際執行資料。 不過，因為彈性查詢可能會從多個資料庫提取資料，所以查詢延遲有時可能會高於提交至單一多租用戶資料庫的對等查詢。 請務必設計查詢來最小化傳回的資料。 彈性查詢通常最適合查詢少量的即時資料，而非建立常用或複雜的分析查詢或報告。 如果查詢的效能不佳，請查看[執行計畫](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan)以查看查詢的哪個部分已向下推送至遠端資料庫，以及傳回多少資料。 需要複雜分析處理的查詢，在某些情況下透過將租用戶資料擷取到針對分析查詢最佳化的專用資料庫或資料倉儲來提供服務，可能會比較好。 [租用戶分析教學課程](sql-database-saas-tutorial-tenant-analytics.md)會說明此模式。 

## <a name="get-the-wingtip-application-scripts"></a>取得 Wingtip 應用程式指令碼

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得 Wingtip Tickets 指令碼和應用程式原始程式碼。 [用於下載 Wingtip SaaS 指令碼的步驟](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。

## <a name="create-ticket-sales-data"></a>建立票證銷售資料

若要針對更有趣的資料集執行查詢，請執行票證產生器來建立票證銷售資料。

1. 在 *PowerShell ISE* 中開啟 ...*Learning Modules*Operational Analytics\\Adhoc Analytics\\\\Demo-AdhocAnalytics.ps1\\ 指令碼，然後設定下列值：
   * **$DemoScenario** = 1，**購買各地事件的票證**。
2. 按 **F5** 以執行指令碼並產生票證銷售。 執行指令碼時，請繼續本教學課程中的步驟。 票證資料會在 [執行臨機操作分散式查詢] 區段中查詢，請等候它完成。

## <a name="explore-the-global-views"></a>瀏覽全域檢視

Wingtip SaaS 應用程式是使用 tenant-per-database 模型建置，因此會從單一租用戶的觀點定義租用戶資料庫結構描述。 租用戶特定資訊位於一個 *Venue* 資料表中，該資料表一律有單一資料列，且會實作成一個沒有主索引鍵的堆積。 結構描述中的其他資料表不必與 Venue 資料表相關，因為正常使用時，資料屬於哪個租用戶絕對毫無疑慮。

不過，在跨所有資料庫查詢時，務必讓彈性查詢可以將資料視為是依租用戶分區之單一邏輯資料庫的一部分。 若要模擬此模式，需將一組「全域」檢視新增至租用戶資料庫，將租用戶識別碼對應到全域查詢的每個資料表。 例如，*VenueEvents* 檢視將計算的 *VenueId* 新增至從 *Events* 資料表對應的資料行。 透過在 head 資料庫中針對 *VenueEvents* (而不是基礎 *Events* 資料表) 定義外部資料表，彈性查詢就能夠根據 *VenueId* 向下推送聯結，讓聯結可以在每個遠端資料庫 (而不是在 head 資料庫) 上平行執行。 這會大幅減少傳回的資料量，進而大幅增加許多查詢的效能。 這些全域檢視已在所有租用戶資料庫 (以及在*basetenantdb*) 中預先建立。

1. 開啟 SSMS 並[連線到 tenants1-&lt;USER&gt; 伺服器](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms)。
2. 展開 [資料庫]，以滑鼠右鍵按一下 **contosoconcerthall**，然後選取 [新增查詢]。
3. 執行下列查詢來探索單一租用戶資料表與全域檢視之間的差異︰

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

在這些檢視中，*VenueId* 是以 Venue 名稱的雜湊來計算，但您可以使用任何方法來導入唯一值。 這個方法與計算用於目錄中租用戶金鑰的方法類似。

檢查 *Venues* 檢視的定義：

1. 在 [物件總管] 中，展開 [contosoconcethall] > [檢視]：

   ![檢視](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

2. 以滑鼠右鍵按一下 **dbo.Venues**。
3. 選取 [產生檢視的指令碼為] > [CREATE To] > [新的查詢編輯器視窗]

產生其他任何 *Venue* 檢視的指令碼，以查看如何新增 *VenueId*。

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>部署用於臨機操作分散式查詢的資料庫

此練習會部署 adhocanalytics 資料庫。 這就是包含用來查詢所有租用戶資料庫之結構描述的 head 資料庫。 此資料庫會部署到現有的目錄伺服器，也就是在範例應用程式中供所有管理相關資料庫使用的伺服器。

1. 在 PowerShell ISE 中開啟 ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\Demo-AdhocAnalytics.ps1 並設定下列值：
   * **$DemoScenario** = 2，**部署臨機操作分析資料庫**。

2. 按 **F5** 以執行指令碼並建立 adhocanalytics 資料庫。

在下節中，您要將結構描述新增至資料庫，以便能夠用來執行分散式查詢。

## <a name="configure-the-database-for-running-distributed-queries"></a>設定資料庫來執行分散式查詢

此練習將結構描述 (外部資料來源和外部資料表定義) 新增至臨機操作分析資料庫，讓您能跨所有租用戶資料庫進行查詢。

1. 開啟 SQL Server Management Studio，並連線到您在上一個步驟中建立的臨機操作分析資料庫。 資料庫的名稱是 *adhocanalytics*。
2. 在 SSMS 中開啟 ...\Learning Modules\Operational Analytics\Adhoc Analytics\ *Initialize-AdhocAnalyticsDB.sql*。
3. 檢閱 SQL 指令碼並注意下列事項︰

   彈性查詢會使用資料庫範圍的認證來存取每個租用戶資料庫。 此認證必須可用於所有資料庫，且通常應獲得啟用這些臨機操作查詢所需的最小權限。

    ![建立認證](media/sql-database-saas-tutorial-adhoc-analytics/create-credential.png)

   外部資料來源，其已定義為在目錄資料庫中使用租用戶分區對應。 以此作為外部資料來源，在執行查詢時，查詢會分散到在目錄中註冊的所有資料庫。 因為每個部署的伺服器名稱不同，所以此初始化指令碼會擷取指令碼執行所在的目前伺服器 (@@servername)，取得目錄資料庫的位置。

    ![建立外部資料來源](media/sql-database-saas-tutorial-adhoc-analytics/create-external-data-source.png)

   參考前一節所述之全域檢視，並使用 **DISTRIBUTION = SHARDED(VenueId)** 定義的外部資料表。 因為每個 *VenueId* 都對應到單一資料庫，所以這可改善許多情況 (如下一節所示) 的效能。

    ![建立外部資料表](media/sql-database-saas-tutorial-adhoc-analytics/external-tables.png)

   已建立和填入的本機資料表 *VenueTypes*。 此參考資料表在所有租用戶資料庫中很常見，因此在此可以表示為本機資料表，並使用一般的資料填入。 對於某些查詢而言，這可能會減少租用戶資料庫與 *adhocanalytics* 資料庫之間移動的資料量。

    ![建立資料表](media/sql-database-saas-tutorial-adhoc-analytics/create-table.png)

   如果您以這種方式包含參考資料表，每當您更新租用戶資料庫時，請務必更新資料表結構描述和資料。

4. 按 **F5** 以執行指令碼並初始化 adhocanalytics 資料庫。 

現在您可以執行分散式查詢，然後收集所有租用戶之間的深入資訊！

## <a name="run-ad-hoc-distributed-queries"></a>執行臨機操作分散式查詢

既然 *adhocanalytics* 資料庫已完成設定，請繼續執行一些分散式查詢。 請包含執行計畫，以便進一步了解發生查詢處理的位置。 

檢查執行計畫時，將滑鼠停留在計畫圖示上方，以取得詳細資料。 

值得注意的是，我們定義外部資料來源時的設定 **DISTRIBUTION = SHARDED(VenueId)**，改善許多情況的效能。 因為每個 *VenueId* 都對應到單一資料庫，所以篩選會輕鬆地在遠端完成，只傳回我們所需的資料。

1. 在 SSMS 中開啟 ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\Demo-AdhocAnalyticsQueries.sql。
2. 確定您已連線到 **adhocanalytics** 資料庫。
3. 選取 [查詢] 功能表，然後按一下 [包括實際執行計畫]
4. 反白顯示 [Which venues are currently registered?] \(目前註冊哪些地點?\) 查詢，然後按 **F5**。

   此查詢會傳回整份地點清單，說明跨所有租用戶進行查詢非常快速而且容易，並傳回每個租用戶的資料。

   檢查計畫，您會發現全部成本都是遠端查詢，因為我們只是到每個租用戶資料庫，選取地點資訊。

   ![SELECT * FROM dbo.Venues](media/sql-database-saas-tutorial-adhoc-analytics/query1-plan.png)

5. 選取下一個查詢，然後按 **F5**。

   此查詢會聯結租用戶資料庫的資料和本機 *VenueTypes* 資料表的資料 (本機是因為它是 *adhocanalytics* 資料庫中的資料表)。

   檢查計畫，您會發現大多數的成本是遠端查詢，因為我們查詢每個租用戶的地點資訊 (dbo.Venues)，然後與本機 *VenueTypes* 資料表執行快速的本機聯結，以顯示易記名稱。

   ![遠端資料與本機資料的聯結](media/sql-database-saas-tutorial-adhoc-analytics/query2-plan.png)

6. 現在選取 [On which day were the most tickets sold?] \(哪一天銷售最多票證?\) 查詢，然後按 **F5**。

   此查詢會執行比較複雜的聯結和彙總。 值得注意的是，大部分處理都在遠端電腦上進行，同樣地，我們只取回我們所需的資料列，只傳回每天每個地點彙總的票證銷售計數的單一資料列。

   ![query](media/sql-database-saas-tutorial-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]

> * 對所有租用戶資料庫執行分散式查詢
> * 部署臨機操作分析資料庫，並將結構描述新增至資料庫中，以執行分散式查詢。


現在嘗試[租用戶分析教學課程](sql-database-saas-tutorial-tenant-analytics.md)，了解如何將資料擷取到另一個分析資料庫，進行更複雜的分析處理...

## <a name="additional-resources"></a>其他資源

* 其他[以 Wingtip SaaS 應用程式為基礎的教學課程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [彈性查詢](sql-database-elastic-query-overview.md)
