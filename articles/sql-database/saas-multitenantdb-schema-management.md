---
title: "在多租用戶應用程式中管理 Azure SQL Database 結構描述 | Microsoft Docs"
description: "在使用 Azure SQL Database 的多租用戶應用程式中，管理多租用戶的結構描述"
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
ms.date: 11/14/2017
ms.author: billgib
ms.openlocfilehash: e4b8e38d20ec408869f2228597afdf2f9620515b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>在使用 Azure SQL Database 的多租用戶應用程式中，管理多租用戶的結構描述

[第一個 Wingtip Tickets SaaS 多租用戶教學課程](saas-multitenantdb-get-started-deploy.md)說明應用程式如何佈建共用的多租用戶資料庫並在目錄中註冊它。 就像任何應用程式一樣，Wingtip Tickets SaaS 應用程式會隨著時間演進，而且有時候會需要變更資料庫。 變更可能包含新增或變更的結構描述、新增或變更的參考資料，以及例行性資料庫維護工作以確保最佳的應用程式效能。 有了 SaaS 應用程式，這些變更需要以經過協調的方式部署到可能非常大規模的租用戶資料庫群。 變更也需要整合到佈建程序中，以供未來在租用戶資料庫中使用。

本教學課程會探討兩種案例：為所有租用戶部署參考資料更新，以及針對包含參考資料的資料表重新調整索引。 [彈性作業](sql-database-elastic-jobs-overview.md)功能是用來跨租用戶資料庫執行這些作業，而「範本」租用戶資料庫則是用來作為新資料庫的範本。

在本教學課程中，您了解如何：

> [!div class="checklist"]

> * 建立作業帳戶
> * 跨多個租用戶進行查詢
> * 更新所有租用戶資料庫中的資料
> * 針對所有租用戶資料庫中的資料表建立索引


若要完成本教學課程，請確定符合下列必要條件：

* 已部署 Wingtip Tickets SaaS 多租用戶資料庫應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip Tickets SaaS 多租用戶資料庫應用程式](saas-multitenantdb-get-started-deploy.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* 已安裝最新版的 SQL Server Management Studio (SSMS)。 [下載並安裝 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> 本教學課程使用的 SQL Database 服務功能處於有限預覽版狀態 (彈性資料庫工作)。 如果想要進行本教學課程，請將您的訂用帳戶識別碼提供給 SaaSFeedback@microsoft.com，並使用主旨 Elastic Jobs Preview (彈性作業預覽)。 在您收到訂用帳戶已啟用的確認之後，請[下載並安裝最新的發行前版本作業 Cmdlet (英文)](https://github.com/jaredmoo/azure-powershell/releases)。 這是有限預覽版，如果有相關問題或需要支援，請連絡 SaaSFeedback@microsoft.com。


## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS 結構描述管理模式的簡介

此範例中使用的共用多租用戶資料庫模型讓租用戶資料庫可包含數量不拘的租用戶。 此範例探索混合使用多租戶與單一租用戶資料庫的可能性，啟用「混合式」租用戶管理模型。 維護和管理這些資料庫很複雜。 [彈性作業](sql-database-elastic-jobs-overview.md)有助於 SQL 資料層的管理。 作業可讓您針對一組資料庫安全可靠地執行工作 (T-SQL 指令碼)，而不需要使用者互動或輸入。 這個方法可用來在應用程式中跨所有租用戶部署結構描述和一般參考資料變更。 彈性作業也可以用來維護建立新租用戶所使用的「範本」資料庫複本，確保它隨時具有最新的結構描述和參考資料。

![畫面](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>彈性作業有限預覽版

新版本的「彈性作業」現在是 Azure SQL Database 的整合功能 (不需要額外的服務或元件)。 這個新的「彈性作業」版本目前處於有限預覽版狀態。 這個有限預覽版目前支援使用 PowerShell 建立作業帳戶，以及使用 T-SQL 建立及管理作業。

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>取得 Wingtip Tickets SaaS 多租用戶資料庫應用程式原始碼和指令碼

可在 [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 存放庫中使用 Wingtip Tickets SaaS 多租用戶資料庫指令碼和應用程式來源程式碼。 關於下載和解除封鎖 Wingtip Tickets SaaS 指令碼的步驟，請參閱[一般指引](saas-tenancy-wingtip-app-guidance-tips.md)。 

## <a name="create-a-job-account-database-and-new-job-account"></a>建立作業帳戶資料庫和新的作業帳戶

本教學課程要求您必須使用 PowerShell 建立作業帳戶資料庫和作業帳戶。 如同 MSDB 和 SQL Agent，「彈性作業」會使用 Azure SQL 資料庫來儲存作業定義、作業狀態和歷程記錄。 一旦建立作業帳戶，您就可以立即建立及監視作業。

1. 在 [PowerShell ISE]\\* 中開啟 …**Learning Modules**Schema Management*\\Demo-SchemaManagement.ps1\\。
1. 按 **F5** 以執行指令碼。

*Demo-SchemaManagement.ps1* 指令碼會呼叫 *Deploy-SchemaManagement.ps1* 指令碼，以在類別目錄伺服器上建立名為 **jobaccount** 的 *S2* 資料庫。 然後，它會建立作業帳戶，將 jobaccount 資料庫當成參數傳遞至作業帳戶建立呼叫。

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>建立作業以將新的參考資料部署到所有租用戶

在定義要在場地舉辦之活動種類的 **VenueTypes** 資料表中，每個租用戶資料庫都包含一組場地類型。 在這個練習中，您要將更新部署到所有資料庫，以新增兩個額外的場地類型：*Motorcycle Racing* (機車賽) 和 *Swimming Club* (游泳俱樂部)。 這些場地類型會對應至您在租用戶活動應用程式中看到的背景影像。

按一下 [Venue Type (場地類型)] 下拉式功能表，然後確認只有 10 個場地類型選項可用，特別是清單中不包含 [Motorcycle Racing (機車賽)] 和 [Swimming Club (游泳俱樂部)]。

現在讓我們建立作業以更新所有租用戶資料庫中的 **VenueTypes** 資料表，並新增新的場地類型。

為了建立新的作業，我們會使用作業帳戶建立時，在 jobaccount 資料庫中建立的一組作業系統預存程序。

1. 在 SSMS 中，連線到租用戶伺服器：tenants1-mt-\<user\>.database.windows.net
2. 瀏覽至 *tenants1-mt-\<user\>.database.windows.net* 伺服器上的 *tenants1* 資料庫，並查詢 *VenueTypes* 資料表以確認 *Motorcycle Racing* (機車賽) 和 *Swimming Club* (游泳俱樂部) **不存在於**結果清單中。
3. 連線到目錄伺服器：catalog-mt-\<user\>.database.windows.net
4. 連線至目錄伺服器中的 jobaccount 資料庫。
5. 在 SSMS 中，開啟檔案 …\\Learning Modules\\Schema Management\\DeployReferenceData.sql
6. 修改陳述式：set @User = &lt;user&gt; ，並使用您在部署 Wingtip Tickets SaaS 多租用戶資料庫應用程式時使用的 User 值來取代。
7. 按 **F5** 以執行指令碼。

請注意下列 *DeployReferenceData.sql* 指令碼中的內容：
* **sp\_add\_target\_group** 會建立目標群組名稱 DemoServerGroup，現在請將目標成員新增至該群組。
* **sp\_add\_target\_group\_member** 會新增「伺服器」目標成員類型，這會將該伺服器 (請注意，這是包含租用戶資料庫的 tenants1-mt-&lt;user&gt; 伺服器) 中的所有資料庫視為作業執行時要包含在作業中的項目，為位於 catalog-mt-&lt;user&gt; 伺服器上的「範本」資料庫 (basetenantdb) 新增「資料庫」目標成員類型，最後是「資料庫」目標群組成員類型，以包括稍後教學課程要使用的 adhocreporting 資料庫。
* **sp\_add\_job** 會建立稱為「參考資料部署」的作業。
* **sp\_add\_jobstep** 會建立作業步驟，包含更新參考 VenueTypes 資料表的 T-SQL 命令文字。
* 指令碼中的其餘檢視會顯示物件是否存在，以及監視作業執行。 使用這些查詢來檢閱 **lifecycle** 資料行中的值，以判斷作業在租用戶資料庫上，以及包含參考資料表的兩個額外資料庫上成功完成的時間。

在 SSMS 中，瀏覽至 *tenants1-mt-&lt;user&gt;* 伺服器上的租用戶資料庫，並查詢 *VenueTypes* 資料表以確認 *Motorcycle Racing* (機車賽) 和 *Swimming Club* (游泳俱樂部) 現在*已新增*至資料表。


## <a name="create-a-job-to-manage-the-reference-table-index"></a>建立作業以管理參考資料表索引

類似於前一個練習，這個練習會建立作業以針對參考資料表的主索引鍵重建索引，這是將大量資料載入資料表後，系統管理員可能會執行的常見資料庫管理作業。


1. 在 SSMS 中，連線到 catalog-mt-&lt;User&gt;.database.windows.net 伺服器中的 jobaccount 資料庫。
2. 在 SSMS 中，開啟 …\\Learning Modules\\Schema Management\\OnlineReindex.sql。
3. 按 **F5** 以執行指令碼。

請注意下列 *OnlineReindex.sql* 指令碼中的內容：
* **sp\_add\_job** 會建立稱為「線上 PK 索引重建\_\_VenueTyp\_\_265E44FD7FD4C885」的新作業。
* **sp\_add\_jobstep** 會建立作業步驟，其中包含要更新索引的 T-SQL 命令文字。
* 指令碼中的其餘檢視會監視作業執行。 使用這些查詢來檢閱 **lifecycle** 資料行中的值，以判斷作業在所有目標群組成員上成功完成的時間。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]

> * 建立作業帳戶以跨多個租用戶查詢
> * 更新所有租用戶資料庫中的資料
> * 針對所有租用戶資料庫中的資料表建立索引

接下來請嘗試[臨機操作報告教學課程](saas-multitenantdb-adhoc-reporting.md)。


## <a name="additional-resources"></a>其他資源

<!--* Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [管理相應放大的雲端資料庫](sql-database-elastic-jobs-overview.md)
* [建立和管理相應放大的雲端資料庫](sql-database-elastic-jobs-create-and-manage.md)
