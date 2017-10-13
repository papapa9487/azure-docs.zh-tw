---
title: "使用 Log Analytics 搭配 SQL Database 多租用戶應用程式 | Microsoft Docs"
description: "設定及使用 Log Analytics (OMS) 搭配多租用戶 Azure SQL Database SaaS 應用程式"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: billgib; sstein
ms.openlocfilehash: f9428871eab19f56e439ba529a9dcfde833d4fbf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="setup-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>設定及使用 Log Analytics (OMS) 搭配多租用戶 Azure SQL Database SaaS 應用程式

在本教學課程中，您會設定及使用 *Log Analytics([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))*，以便監視彈性集區和資料庫。 本教學課程是架構在[效能監視與管理教學課程](sql-database-saas-tutorial-performance-monitoring.md)之上。 示範如何使用 *Log Analytics*，以增強 Azure 入口網站中提供的監視和警示功能。 Log Analytics 適合用於大規模監視和警示，因為它可支援數百個集區和數十萬個資料庫。 它也提供單一監視解決方案，可以跨多個 Azure 訂用帳戶，整合不同應用程式和 Azure 服務的監視。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 安裝及設定 Log Analytics (OMS)
> * 使用 Log Analytics 來監視集區和資料庫

若要完成本教學課程，請確定已完成下列必要條件：

* 已部署 Wingtip SaaS 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip SaaS 應用程式](sql-database-saas-tutorial.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

請參閱[效能監視與管理教學課程](sql-database-saas-tutorial-performance-monitoring.md)，以取得 SaaS 案例和模式的討論區，及其對於監視解決方案需求的影響。

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>使用 Log Analytics (OMS) 監視和管理效能

針對 SQL Database，可使用資料庫和集區監視與警示功能。 此內建監視與警示為資源特定功能且便於處理少量資源，但比較不適合監視大型安裝，或提供橫跨不同資源和訂用帳戶的統一檢視。

對於高容量的情況，可以使用 Log Analytics。 這是個別的 Azure 服務，可針對發出的診斷紀錄和 Log Analytics 工作區中蒐集的遙測提供分析，這可以收集來自許多服務的遙測並用來查詢和設定警示。 Log Analytics 可提供內建的查詢語言和資料視覺化工具，以便進行操作資料分析和視覺化。 SQL 分析解決方案提供了數個預先定義的彈性集區及資料庫監視與警示檢視和查詢，讓您新增自己的臨機操作查詢並視需要加以儲存。 OMS 也提供自訂檢視設計工具。

您可以在 Azure 入口網站和 OMS 中開啟 Log Analytics 工作區和分析解決方案。 Azure 入口網站是較新的存取點，但在某些區域可能位於 OMS 入口網站的後面。

### <a name="create-data-by-starting-the-load-generator"></a>藉由啟動負載產生器來建立資料 

1. 在 **PowerShell ISE** 中開啟 **Demo-PerformanceMonitoringAndManagement.ps1**。 讓此指令碼保持開啟，因為您會在本教學課程期間執行數個負載產生案例。
1. 如果您的租用戶少於五個，請佈建一批租用戶以提供更有趣的監視內容︰
   1. 設定 **$DemoScenario = 1**，**佈建一批租用戶**
   1. 若要執行指令碼，按 **F5**。

1. 設定 **$DemoScenario** = 2，**產生一般強度負載 (約為 40 DTU)**。
1. 若要執行指令碼，按 **F5**。

## <a name="get-the-wingtip-application-scripts"></a>取得 Wingtip 應用程式指令碼

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得 Wingtip Tickets 指令碼和應用程式原始程式碼。 指令碼檔案位於 [[Learning Modules] 資料夾](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)中。 請將 **Learning Modules** 資料夾下載到您的本機電腦，並維持其資料夾結構。

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>安裝及設定 Log Analytics 和 Azure SQL 分析解決方案

Log Analytics 是需要設定的個別服務。 Log Analytics 會收集 Log Analytics 工作區中的記錄資料和遙測以及計量。 就像 Azure 中的其他資源一樣，工作區是必須建立的資源。 雖然工作區不一定要建立在與其監視之應用程式相同的資源群組中，但這麼做通常最有意義。 針對 Wingtip SaaS 應用程式，這可讓工作區輕鬆地與應用程式一起刪除，只要刪除資源群組即可。

1. 在 **PowerShell ISE** 中開啟 ...\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\*Demo-LogAnalytics.ps1*。
1. 若要執行指令碼，按 **F5**。

此時，您應可在 Azure 入口網站 (或 OMS 入口網站) 中開啟 Log Analytics。 需要花幾分鐘的時間才能在 Log Analytics 工作區中收集遙測並顯示出來。 您讓系統蒐集資料的時間越長，體驗將會更有趣。 現在是放鬆一下的好時機 - 只要確定負載產生器仍在執行中！


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>使用 Log Analytics 和 SQL 分析解決方案來監視集區和資料庫


在此練習中，開啟 Log Analytics 和 OMS 入口網站以查看針對資料庫和集區蒐集的遙測資料。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)，按一下 [更多服務] 以開啟 Log Analytics，然後搜尋 Log Analytics︰

   ![開啟 Log Analytics](media/sql-database-saas-tutorial-log-analytics/log-analytics-open.png)

1. 選取名為 wtploganalytics-&lt;USER&gt; 的工作區。

1. 選取 [概觀] 以在 Azure 入口網站中開啟 Log Analytics 解決方案。
   ![overview-link](media/sql-database-saas-tutorial-log-analytics/click-overview.png)

    **重要**︰解決方案可能需要幾分鐘的時間才能變成作用中。 請耐心等候！

1. 按一下 [Azure SQL 分析] 圖格加以開啟。

    ![概觀](media/sql-database-saas-tutorial-log-analytics/overview.png)

    ![分析](media/sql-database-saas-tutorial-log-analytics/analytics.png)

1. 解決方案刀鋒視窗中的檢視會橫向捲動，其在底部有自己的捲軸列 (視需要重新整理刀鋒視窗)。

1. 若要探索各種檢視，請按一下檢視或個別資源來開啟向下鑽研總管，您可以在其中使用左上方的時間滑桿或按一下垂直列，將焦點放在較小的時間配量。 在這個檢視中，您可以選取個別的資料庫或集區，將焦點放在特定的資源︰

    ![圖表](media/sql-database-saas-tutorial-log-analytics/chart.png)

1. 回到解決方案刀鋒視窗，如果您捲動到最右邊，您會看到一些已儲存的查詢，按一下即可開啟及探索這些查詢。 您可以試著修改這些查詢，然後儲存您產生的任何有趣查詢，之後您可重新開啟查詢並使用於其他資源。

1. 回到 Log Analytics 工作區刀鋒視窗，選取 OMS 入口網站以開啟解決方案。

    ![oms](media/sql-database-saas-tutorial-log-analytics/oms.png)

1. 您可以在 OMS 入口網站中設定警示。 按一下資料庫 DTU 檢視的警示部分。

1. 在顯示的 [記錄搜尋] 檢視中，您會看到所表示計量的長條圖。

    ![log search](media/sql-database-saas-tutorial-log-analytics/log-search.png)

1. 如果您按一下工具列中的 [警示]，您將能夠看到警示組態並可加以變更。

    ![新增警示規則](media/sql-database-saas-tutorial-log-analytics/add-alert.png)

在 Log Analytics 和 OMS 中監視和警示是以工作區中的資料查詢為基礎，不同於每個資源刀鋒視窗上的資源特定警示。 因此，您可以定義可查看所有資料庫的警示，而不是為每個資料庫定義一個警示。 或是撰寫可使用多種資源類型之複合查詢的警示。 查詢只會受限於工作區中可用的資料。

SQL Database 的 Log Analytics 需根據工作區中的資料量付費。 在本教學課程中，您建立了一個免費工作區，其每天的限制為 500MB。 一旦達到該限制，工作區就不會再新增資料。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 安裝及設定 Log Analytics (OMS)
> * 使用 Log Analytics 來監視集區和資料庫

[租用戶分析教學課程](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>其他資源

* [以初始 Wingtip SaaS 應用程式部署為基礎的其他教學課程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
