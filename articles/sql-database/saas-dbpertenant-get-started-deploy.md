---
title: "多租用戶 SaaS 教學課程 - Azure SQL Database | Microsoft Docs"
description: "部署及探索 Wingtip Tickets SaaS 多租用戶應用程式，其使用 Azure SQL Database 示範每一租用戶一個資料庫和其他 SaaS 模式。"
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
ms.date: 11/10/2017
ms.author: sstein
ms.openlocfilehash: 9b1ae219eb1278b818e3e1d4237d04fe54c980ec
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>使用 Azure SQL Database，部署及探索使用 每一租用戶一個資料庫模式的多租用戶 SaaS 應用程式

在本教學課程中，您會部署及探索 Wingtip Tickets SaaS 每一租用戶一個資料庫應用程式。 應用程式使用每一租用戶一個資料庫模式來儲存多租用戶的資料。 應用程式是設計成用來展示簡化啟用 SaaS 案例的 Azure SQL Database 功能。

按一下下面的 [部署至 Azure] 按鈕五分鐘之後，您會擁有一個在雲端執行的多租用戶 SaaS 應用程式 (使用 SQL Database)。 應用程式部署為包含三個範例租用戶，每個都有自己的資料庫，且全部都部署到 SQL 彈性集區中。 應用程式會部署到您的 Azure 訂用帳戶，讓您有完整的存取權可以探索及使用個別應用程式元件。 在 WingtipTicketsSaaS-DbPerTenant GitHub 存放庫可取得應用程式原始程式碼和管理指令碼。


您會在本教學課程中學到：

> [!div class="checklist"]

> * 如何部署 Wingtip SaaS 應用程式
> * 在何處取得應用程式原始程式碼和管理指令碼
> * 關於組成應用程式的伺服器、集區和資料庫
> * 如何使用「目錄」將租用戶對應到其資料
> * 如何佈建新租用戶
> * 如何監視應用程式中的租用戶活動

若要探索各種 SaaS 設計和管理模式，有以此初始部署為基礎而建置的[一系列相關教學課程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)可供參考。 在進行教學課程的時候，您可以鑽研所提供的指令碼，並檢查不同 SaaS 模式的實作方式。 逐步執行每個教學課程中的指令碼，進而深入了解如何實作可簡化 SaaS 應用程式開發的多種 SQL Database 功能。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定已完成下列必要條件：

* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-saas-application"></a>部署 Wingtip Tickets SaaS 應用程式

部署應用程式：

1. 按一下 [部署至 Azure] 按鈕，可在 Azure 入口網站中開啟 Wingtip Tickets SaaS 每一租用戶一個資料庫的部署範本。 範本需要兩個參數值：新資源群組的名稱，以及區分此部署與 Wingtip Tickets SaaS 每一租用戶一個資料庫應用程式中其他部署的使用者名稱。 下一個步驟將詳細說明如何設定這些值。

   請務必記下您使用的確切值，因為稍後您必須將其輸入到設定檔。

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. 輸入部署所需的參數值：

    > [!IMPORTANT]
    > 為了示範的目的，已刻意將某些驗證和伺服器防火牆設為不安全。 **建立新的資源群組**。 不要使用現有資源群組、伺服器或集區。 請不要將此應用程式、指令碼或任何部署的資源用於生產環境。 不使用應用程式之後請刪除此資源群組，以停止相關計費。

    * **資源群組** - 選取 [建立新的] 並提供資源群組的 [名稱]。 
    * **位置** - 從下拉式清單中選取 [位置]。
    * **使用者** - 部分資源需要全域唯一的名稱。 若要確保唯一性，每次部署應用程式時，都請提供值，以區別您建立的資源與 Wingtip 應用程式的任何其他部署所建立的資源。 建議您使用簡短的 [使用者] 名稱，例如您的姓名縮寫加上數字 (例如 *af1*)，然後將該名稱用於資源群組名稱 (例如 *wingtip-af1*)。 [使用者] 參數只能包含字母、數字和連字號 (無空格)。 第一個和最後一個字元必須是字母或數字 (建議全部都小寫)。


1. **部署應用程式**。

    * 按一下以同意條款和條件。
    * 按一下 [購買]。

1. 按一下 [通知] (搜尋方塊右邊的鈴鐺圖示) 來監視部署狀態。 部署 Wingtip Tickets SaaS 應用程式大約需要五分鐘。

   ![部署成功](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>下載並解除封鎖 Wingtip Tickets 管理指令碼

部署應用程式時，請下載原始程式碼和管理指令碼。

> [!IMPORTANT]
> 從外部來源下載 zip 檔案並進行解壓縮時，Windows 可能會封鎖可執行的內容 (指令碼、dll)。 從 zip 檔案解壓縮指令碼時，請遵循下列步驟先解除封鎖 .zip 檔案，再進行解壓縮。 這可確保允許執行指令碼。

1. 瀏覽至 [WingtipTicketsSaaS-DbPerTenant GitHub 存放庫](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)。
1. 按一下 [複製或下載]。
1. 按一下 [下載 ZIP]，並儲存檔案。
1. 以滑鼠右鍵按一下 **WingtipTicketsSaaS-DbPerTenant-master.zip** 檔案，然後選取 [屬性]。
1. 在 [一般] 索引標籤上，選取 [解除封鎖]，然後按一下 [套用]。
1. 按一下 [確定] 。
1. 將檔案解壓縮。

指令碼位於 *..\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules* 資料夾中。

## <a name="update-the-user-configuration-file-for-this-deployment"></a>更新此部署的使用者組態檔

執行任何指令碼之前，請在 **UserConfig.psm1** 中更新「資源群組」和「使用者」值。 將這些變數設定為您在部署期間使用的值。

1. 在 [PowerShell ISE] 中開啟 ...\\Learning Modules\\*UserConfig.psm1* 
1. 將 ResourceGroupName 和 Name 更新為您部署的特定值 (只在第 10 行和第 11 行)。
1. 儲存變更！

幾乎每一個指令碼中都會參考這些數值。

## <a name="run-the-application"></a>執行應用程式

應用程式以各種舉辦活動的場地 (例如，音樂廳、爵士俱樂部和運動俱樂部) 為目標。 成為 Wingtip Tickets (或租用戶) 的場地註冊者，輕鬆就能列出場地活動並售票。 每個場地都有客製化的網站，可以管理並列出活動及售票 (獨立且與其他租用戶隔離)。 實際上，每個租用戶都會有一個部署到 SQL 彈性集區的 SQL Database。

中央 [事件中樞] 頁面提供部署中租用戶的連結清單。

1. 在您的瀏覽器中開啟 [事件中樞]：http://events.wingtip-dpt.&lt;USER&gt;.trafficmanager.net (請以您部署的使用者值取代&lt;USER&gt;)：

    ![事件中樞](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. 按一下 [Events Hub (活動中心)] 中的 [Fabrikam Jazz Club (Fabrikam 爵士俱樂部)]。

   ![活動](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)


為了控制連入要求的發佈，應用程式會使用 [*Azure 流量管理員*](../traffic-manager/traffic-manager-overview.md)。 活動頁面 (租用戶專屬) 要求您必須在 URL中包含租用戶名稱。 所有租用戶 URL 都包含您的特定「使用者」值，且格式為 http://events.wingtipp-dpt.&lt;USER&gt;.trafficmanager.net/*fabrikamjazzclub*。 活動應用程式會剖析 URL 中的租用戶名稱，並以它來建立金鑰，以存取使用[*分區對應管理*](sql-database-elastic-scale-shard-map-management.md)實作的目錄。 目錄會將鍵值對應到租用戶的資料庫位置。 [事件中樞] 會使用目錄中的延伸中繼資料來擷取與每個資料庫相關聯的租用戶名稱，以提供 URL 清單。

在生產環境中，您通常會建立 CNAME DNS 記錄以[*將公司網際網路網域指向*](../traffic-manager/traffic-manager-point-internet-domain.md)流量管理員設定檔。

## <a name="start-generating-load-on-the-tenant-databases"></a>開始在租用戶資料庫上產生負載

應用程式已部署完成，請立即使用！ *Demo-LoadGenerator* PowerShell 指令碼會啟動對所有租用戶資料庫執行的工作負載。 許多 SaaS 應用程式的實際負載通常是偶發且無法預期的。 為了模擬這種類型的負載，產生器會產生分散到所有租用戶的負載，而且會依隨機間隔在每個租用戶上隨機暴增。 由於需要數分鐘的時間才會出現負載模式，因此，最好讓產生器先執行至少三或四分鐘，再監視負載。

1. 在 [PowerShell ISE] 中，開啟 ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* 指令碼。
1. 按 **F5** 來執行指令碼並啟動負載產生器 (目前先保留預設參數值)。

> [!IMPORTANT]
> 若要執行其他指令碼，請開啟新的 PowerShell ISE 視窗。 負載產生器正於您的本機 PowerShell 工作階段中以一系列作業的方式執行。 *Demo-LoadGenerator.ps1* 指令碼會啟動實際負載產生器指令碼，其以前景工作及一系列背景負載產生作業形式執行。 針對目錄中所註冊的每個資料庫，都會叫用負載產生器作業。 這些作業會在本機 PowerShell 工作階段中執行，因此關閉 PowerShell 工作階段會停止所有作業。 如果您暫停電腦，則負載產生會暫停並在您喚醒電腦時繼續。

負載產生器針對每個租用戶叫用負載產生作業之後，前景工作會保持作業叫用狀態，而它會針對後續佈建的任何新租用戶啟動額外的背景作業。 您可以使用 *Ctrl-C* 或按 [停止] 按鈕以停止前景工作，但現有背景作業仍會繼續在每個資料庫上產生負載。 如果您需要監視和控制背景作業，請使用 *Get-Job*、*Receive-Job* 和 *Stop-Job*。 正在執行前景工作時，您無法使用相同的 PowerShell 工作階段來執行其他指令碼。 若要執行其他指令碼，請開啟新的 PowerShell ISE 視窗。

如果您想要重新啟動負載產生器工作階段，例如使用不同的參數，則可以停止前景工作，然後重新執行 *Demo-LoadGenerator.ps1* 指令碼。 重新執行 *Demo-LoadGenerator.ps1* 會先停止任何目前正在執行的作業，然後重新啟動產生器，以使用目前參數來啟動一組新作業。

現在，讓負載產生器以工作叫用狀態執行。


## <a name="provision-a-new-tenant"></a>佈建新租用戶

初始部署會建立三個範例租用戶，但我們可以建立另一個租用戶來查看這對已部署的應用程式的影響。 [佈建和目錄教學課程](saas-dbpertenant-provision-and-catalog.md)會詳細說明 Wingtip Tickets SaaS 佈建租用戶工作流程。 在此步驟中，您會快速建立新的租用戶。

1. 在 [PowerShell ISE] 中，開啟 ...*Learning Modules\Provision and Catalog*\\Demo-ProvisionAndCatalog.ps1\\。
1. 按 **F5** 來執行指令碼 (目前先保持預設值)。

   > [!NOTE]
   > 許多 Wingtip SaaS 指令碼都會使用 *$PSScriptRoot* 來瀏覽資料夾，以在其他指令碼中呼叫函式。 按 **F5** 執行完整的指令碼時，才會評估這個變數。  醒目提示並執行選取項目 (**F8**) 可能會導致錯誤，因此請在執行指令碼時按 **F5**。

新的租用戶資料庫是在 SQL 彈性集區中建立、初始化並註冊在目錄中。 成功佈建之後，瀏覽器中就會顯示新租用戶的 Events 網站：

![新租用戶](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

重新整理「事件中樞」，而且新的租用戶現在會出現在清單中。


## <a name="explore-the-servers-pools-and-tenant-databases"></a>探索伺服器、集區和租用戶資料庫

既然您已經開始對租用戶集合執行負載，讓我們看看已部署的一些資源：

1. 在 [Azure 入口網站](http://portal.azure.com) 中，瀏覽至您的 SQL 伺服器清單，開啟 **catalog-dpt-&lt;USER&gt;** 伺服器。 目錄伺服器包含兩個資料庫：**tenantcatalog** 和 **basetenantdb** (複製來建立新租用戶的範本資料庫)。

   ![資料庫](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. 回到您的 SQL 伺服器清單，開啟包含租用戶資料庫的 **tenants1-dpt-&lt;USER&gt;** 伺服器。 每個租用戶資料庫都是 50 eDTU 標準集區中的「彈性標準」資料庫。 另請注意有 _Red Maple Racing_ 資料庫，這是您先前佈建的租用戶資料庫。

   ![伺服器](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>監視集區

如果負載產生器已經執行數分鐘，應該以有足夠的資料可供開始查看集區和資料庫的內建監視功能。

1. 瀏覽至伺服器 **tenants1-dpt-&lt;USER&gt;**，然後按一下 [Pool1] 檢視集區的資源使用率 (在下列圖表中，負載產生器已執行一個小時)：

   ![監視集區](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

上方圖表顯示集區 eDTU 使用量，下方圖表顯示集區中前 5 個資料庫的 eDTU 使用量。  這兩個圖表清楚說明：彈性集區和 SQL Database 非常適合 SaaS 應用程式工作負載。 50 eDTU 集區輕鬆就能支援四個暴增到最多 40 eDTU 的資料庫。 如果它們已佈建為獨立資料庫，則各需要為 S2 (50 DTU) 才能支援暴增。 4 個獨立 S2 資料庫的成本幾乎是集區價格的 3 倍，而且集區還有充足的空餘空間可建立更多資料庫。 在實際情況下，SQL Database 客戶目前在 200 eDTU 集區中執行多達 500 個資料庫。 如需詳細資訊，請參閱[效能監視教學課程](saas-dbpertenant-performance-monitoring.md)。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]

> * 如何部署 Wingtip Tickets SaaS 應用程式
> * 關於組成應用程式的伺服器、集區和資料庫
> * 租用戶會使用「類別目錄」來對應到其資料
> * 如何佈建新租用戶
> * 如何檢視集區使用率以監視租用戶活動
> * 如何刪除範例資源以停止相關計費

現在，請嘗試[佈建和類別目錄教學課程](saas-dbpertenant-provision-and-catalog.md)。



## <a name="additional-resources"></a>其他資源

* [在 Tickets SaaS Database Per Tenant 應用程式上建置的其他教學課程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* 若要了解彈性集區，請參閱[什麼是 Azure SQL 彈性集區](sql-database-elastic-pool.md)
* 若要了解彈性作業，請參閱[*管理相應放大的雲端資料庫*](sql-database-elastic-jobs-overview.md)
* 若要了解多租用戶 SaaS 應用程式，請參閱[*多租用戶 SaaS 應用程式的設計模式*](saas-tenancy-app-design-patterns.md)
