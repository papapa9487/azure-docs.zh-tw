---
title: "多租用戶 SaaS 教學課程 - Azure SQL Database | Microsoft Docs"
description: "部署及探索使用 Azure SQL Database 的獨立單一租用戶 SaaS 應用程式。"
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
ms.date: 11/30/2017
ms.author: genemi
ms.openlocfilehash: d38cd108821bce05824732bbdbdd322ae8563bde
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>部署及探索使用 Azure SQL Database 的獨立單一租用戶應用程式

在本教學課程中，您會部署及探索 Wingtip Tickets SaaS Standlone 應用程式。 應用程式是設計成用來展示簡化啟用 SaaS 案例的 Azure SQL Database 功能。

獨立應用程式模式會部署 Azure 資源群組，當中包含每個租用戶的單一租用戶應用程式和單一租用戶資料庫。  可以佈建應用程式的多個執行個體來提供多租用戶解決方案。

在本教學課程中，您可將數個租用戶的資源群組部署到您的 Azure 訂用帳戶。  此模式可讓資源群組部署到租用戶的 Azure 訂用帳戶。 Azure 的夥伴程式可讓服務提供者代表租用戶管理這些資源群組。 服務提供者是租用戶的訂用帳戶中的管理員。

在後面的部署區段中，有三個藍色的 [部署至 Azure] 按鈕。 每個按鈕可部署應用程式的不同執行個體。 每個執行個體會針對特定租用戶進行自訂。 按下每個按鈕時，對應的應用程式會在 5 分鐘內完整部署。  應用程式會部署在您的 Azure 訂用帳戶中。  您可以完整存取探索和使用個別應用程式元件。

在 [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub 存放庫可取得應用程式原始程式碼和管理指令碼。


您會在本教學課程中學到：

> [!div class="checklist"]
> * 如何部署 Wingtip Tickets SaaS Standalone 應用程式。
> * 在何處取得應用程式原始程式碼和管理指令碼。
> * 關於組成應用程式的伺服器和資料庫。

將會推出其他教學課程。 這些教學課程可讓您可以此應用程式模式作為基礎，探索各種管理情節。   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>部署 Wingtip Tickets SaaS Standalone 應用程式

部署三個所提供之租用戶的應用程式：

1. 按一下每個藍色的 [部署至 Azure] 按鈕可在 [Azure 入口網站](https://portal.azure.com)中開啟部署範本。 每個範本需要兩個參數值：新資源群組的名稱，以及區分此部署與應用程式中其他部署的使用者名稱。 下一個步驟將詳細說明如何設定這些值。<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso Concert Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. 輸入每個部署所需的參數值。

    > [!IMPORTANT]
    > 為了示範的目的，已刻意將某些驗證和伺服器防火牆設為不安全。 為每個應用程式部署**建立新的新資源群組**。  請勿使用現有的資源群組。 請不要將此應用程式或任何它所建立的資源用於生產環境。 當您不使用應用程式時，請刪除所有資源群組，以停止相關計費。

    資源名稱中最好只使用小寫字母、數字和連字號。
    * 針對**資源群組** - 選取 [新建] 並提供資源群組的小寫 [名稱]。
        * 建議您附加虛線，後接著您的首字母，接著是數字：例如，*wingtip-sa-af1*。
        * 從下拉式清單中選取 [位置]。

    * 針對**使用者** - 建議您選擇簡短的使用者 值，例如您的首字母加上數字：例如，af1。


3. **部署應用程式**。

    * 按一下以同意條款和條件。
    * 按一下 [購買]。

4. 按一下 [通知] (搜尋方塊右邊的鈴鐺圖示) 來監視所有三個部署的部署狀態。 部署應用程式需要五分鐘。


## <a name="run-the-application"></a>執行應用程式

應用程式會示範裝載事件的場地。 場地類型包括音樂廳、爵士俱樂部和運動俱樂部。 場地是 Wingtip Tickets 應用程式的客戶。 在 Wingtip Tickets 中，場地會註冊為*租用戶*。 成為租用戶讓場地能夠輕鬆列出活動，並對其客戶銷售票券。 每個場地都有客製化網站可列出其活動及售票。 每個租用戶都會與其他租用戶隔離，而且彼此獨立。 實際上，每個租用戶都會取得個別的應用程式執行個體，並擁有獨立的 SQL Database。

1. 在不同的瀏覽器索引標籤中開啟三個租用戶的 [事件] 頁面：

    - http://events.contosoconcerthall.&lt;USER&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;USER&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;USER&gt;.trafficmanager.net

    (在每個 URL 中，將 &lt;USER&gt; 取代為部署的使用者值)。

   ![事件](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

為了控制連入要求的發佈，應用程式會使用 [*Azure 流量管理員*](../traffic-manager/traffic-manager-overview.md)。 每個租用戶專屬的應用程式執行個體都包含租用戶名稱，以在 URL 中作為網域名稱的一部分。 所有租用戶 URL 都包含您的特定 [使用者] 值。 URL 的格式如下︰
- http://events.&lt;venuename&gt;.&lt;USER&gt;.trafficmanager.net

每個租用戶的資料庫 [位置] 都包含在對應已部署應用程式的應用程式設定中。

在生產環境中，您通常會建立 CNAME DNS 記錄以[將公司網際網路網域指向](../traffic-manager/traffic-manager-point-internet-domain.md)流量管理員設定檔的 URL。


## <a name="explore-the-servers-and-tenant-databases"></a>探索伺服器和租用戶資料庫

讓我們看看一些已部署的資源：

1. 在 [Azure 入口網站](http://portal.azure.com)中，瀏覽至資源群組的清單。
2. 選取 **wingtip-sa-catalog-&lt;USER&gt;** 資源群組。
    - 在此資源群組中，已部署 **catalog-sa-&lt;USER&gt;** 伺服器。 此伺服器包含 **tenantcatalog** 資料庫。
    - 您也應該會看到三個租用戶資源群組。
3. 開啟 **wingtip-sa-fabrikam-&lt;USER&gt;** 資源群組，其中包含 Fabrikam Jazz Club 部署的資源。  **fabrikamjazzclub-&lt;USER&gt;** 伺服器包含 **fabrikamjazzclub** 資料庫。

每個租用戶資料庫都是 50 DTU *獨立*資料庫。

## <a name="additional-resources"></a>其他資源

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- 若要了解多租用戶 SaaS 應用程式，請參閱[多租用戶 SaaS 應用程式的設計模式](saas-tenancy-app-design-patterns.md)。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]
> * 如何部署 Wingtip Tickets SaaS Standalone 應用程式。
> * 關於組成應用程式的伺服器和資料庫。
> * 如何刪除範例資源以停止相關計費。

