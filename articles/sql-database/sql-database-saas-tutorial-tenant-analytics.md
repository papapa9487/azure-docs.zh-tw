---
title: "針對多個 Azure SQL Database 執行分析查詢 | Microsoft Docs"
description: "從租用戶資料庫將資料擷取至分析資料庫，以便進行離線分析"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: billgib; sstein
ms.openlocfilehash: 4a96efb15268c56e3625832b0b4d6dd8f6a78614
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="extract-data-from-tenant-databases-into-an-analytics-database-for-offline-analysis"></a>從租用戶資料庫將資料擷取至分析資料庫，以便進行離線分析

在本教學課程中，您可以使用彈性作業來對每個租用戶資料庫執行查詢。 該作業會擷取票券銷售資料，並將其載入至分析資料庫 (或資料倉儲) 中進行分析。 查詢此分析資料庫，即可以從所有租用戶日常作業資料中擷取深入解析。


在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立租用戶分析資料庫
> * 建立排定作業以擷取資料並填入分析資料庫

若要完成本教學課程，請確定符合下列必要條件：

* 已部署 Wingtip SaaS 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip SaaS 應用程式](sql-database-saas-tutorial.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* 已安裝最新版的 SQL Server Management Studio (SSMS)。 [下載並安裝 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="tenant-operational-analytics-pattern"></a>租用戶操作分析模式

SaaS 應用程式的其中一個絕佳機會是使用儲存在雲端的豐富租用戶資料。 使用此資料來深入了解應用程式和租用戶的作業與使用方式。 此資料可以引導應用程式和平台中的功能開發、使用性改進及其他投資。 在單一多租用戶資料庫中存取此資料很容易，但資料大規模分散於可能數千個資料庫時則不太容易存取。 存取此資料的其中一種方法是使用彈性作業，其可讓您在輸出資料庫和資料表中擷取作業執行的結果傳回查詢結果。

## <a name="get-the-wingtip-application-scripts"></a>取得 Wingtip 應用程式指令碼

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得 Wingtip Tickets 指令碼和應用程式原始程式碼。 [用於下載 Wingtip SaaS 指令碼的步驟](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。

## <a name="deploy-a-database-for-tenant-analytics-results"></a>部署租用戶分析結果的資料庫

在本教學課程中，您必須部署一個資料庫才能擷取指令碼作業執行的結果，其中包含結果傳回的查詢。 讓我們針對此目的，建立一個稱為 tenantanalytics 的資料庫。

1. 在 PowerShell ISE 中開啟 …\\Learning Modules\\Operational Analytics\\Tenant Analytics\\Demo-TenantAnalyticsDB.ps1 並設定下列值：
   * **$DemoScenario** = **2** *部署操作分析資料庫*
1. 按 **F5** 以執行可建立租用戶分析資料庫的示範指令碼 (它會呼叫 *Deploy-TenantAnalyticsDB.ps1* 指令碼)。

## <a name="create-some-data-for-the-demo"></a>建立一些資料以供示範

1. 在 PowerShell ISE 中開啟 …\\Learning Modules\\Operational Analytics\\Tenant Analytics\\Demo-TenantAnalyticsDB.ps1 並設定下列值：
   * **$DemoScenario** = **1** *購買各地事件的票證*
1. 按 **F5** 以執行指令碼並建立票證購買歷程記錄。


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>建立排定作業以擷取有關票證購買的租用戶分析

此指令碼會建立一個作業，以擷取所有租用戶的票證購買資訊。 一旦彙總成單一資料表，您就可以取得有關各租用戶票證購買模式的豐富詳細計量。

1. 開啟 SSMS 並連線到 catalog-&lt;user&gt;.database.windows.net 伺服器
1. 開啟 ...\\Learning Modules\\Operational Analytics\\Tenant Analytics\\TicketPurchasesfromAllTenants.sql
1. 修改 &lt;User&gt;，使用您在 **sp\_add\_target\_group\_member** 和 **sp\_add\_jobstep** 指令碼上方部署 Wingtip SaaS 應用程式時使用的使用者名稱
1. 按一下滑鼠右鍵並選取 [連線]，然後連線到 catalog-&lt;User&gt;.database.windows.net 伺服器 (如果您尚未連線)
1. 確定您已連線到 **jobaccount** 資料庫，然後按 **F5** 以執行指令碼

* **sp\_add\_target\_group** 會建立名為 TenantGroup 的目標群組，我們現在需要新增目標成員。
* **sp\_add\_target\_group\_member** 會新增「伺服器」 目標成員類型，其認為在作業執行時該伺服器內的所有資料庫 (請注意，這是在作業執行時含有租用戶資料庫的 customer1-&lt;User&gt; 伺服器) 都應該包含在作業中。
* **sp\_add\_job** 會建立新的每週排定作業，其名稱為「所有租用戶的票證購買」
* **sp\_add\_jobstep** 會建立含有 T-SQL 命令文字的作業步驟，以擷取所有租用戶的所有票證購買資訊，並將傳回的結果集複製到名為 AllTicketsPurchasesfromAllTenants 的資料表中
* 指令碼中的其餘檢視會顯示物件是否存在，以及監視作業執行。 檢閱 [生命週期] 資料行中的狀態值以監視狀態。 一旦成功，便已所有租用戶資料庫和含有參考資料表的其他兩個資料庫上順利完成此作業。

成功執行指令碼應會導致類似的結果︰

![results](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>建立一個作業以擷取所有租用戶的票證購買摘要計數

此指令碼會建立一個作業，以擷取所有租用戶的票證購買總和。

1. 開啟 SSMS 並連線到 catalog-&lt;User&gt;.database.windows.net 伺服器
1. 開啟 …\\Learning Modules\\Provision and Catalog\\Operational Analytics\\Tenant Analytics\\Results-TicketPurchasesfromAllTenants.sql 檔案
1. 修改 &lt;User&gt;，使用您在指令碼和 **sp\_add\_jobstep** 預存程序中部署 Wingtip SaaS 應用程式時使用的使用者名稱
1. 按一下滑鼠右鍵並選取 [連線]，然後連線到 catalog-&lt;User&gt;.database.windows.net 伺服器 (如果您尚未連線)
1. 確定您已連線到 **tenantanalytics** 資料庫，然後按 **F5** 以執行指令碼

成功執行指令碼應會導致類似的結果︰

![results](media/sql-database-saas-tutorial-tenant-analytics/total-sales.png)



* **sp\_add\_job** 會建立新的每週排定作業，其名稱為 “ResultsTicketsOrders”

* **sp\_add\_jobstep** 會建立含有 T-SQL 命令文字的作業步驟，以擷取所有租用戶的所有票證購買資訊，並將傳回的結果集複製到名為 CountofTicketOrders 的資料表中

* 指令碼中的其餘檢視會顯示物件是否存在，以及監視作業執行。 檢閱 [生命週期] 資料行中的狀態值以監視狀態。 一旦成功，便已所有租用戶資料庫和含有參考資料表的其他兩個資料庫上順利完成此作業。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 部署租用戶分析資料庫
> * 建立排定作業以擷取各租用戶的分析資料

恭喜！

## <a name="additional-resources"></a>其他資源

* 其他[以 Wingtip SaaS 應用程式為基礎的教學課程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [彈性作業](sql-database-elastic-jobs-overview.md)
