---
title: "開始使用彈性資料庫工具 | Microsoft Docs"
description: "Azure SQL Database 彈性資料庫工具功能的基本解說，包括易於執行的範例應用程式。"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
ms.openlocfilehash: 600334c58ce62a1e53e8a57dd1566bd211249164
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="get-started-with-elastic-database-tools"></a>開始使用彈性資料庫工具
本文件將藉由協助您執行範例應用程式來介紹開發人員體驗。 範例會建立簡易的分區化應用程式，並探討彈性資料庫工具的主要功能。 此範例會示範[彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)。

若要安裝程式庫，請移至 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。 程式庫是使用範例應用程式所安裝，如下一節所述。

## <a name="prerequisites"></a>必要條件
* 含 C# 的 Visual Studio 2012 或更新版本。 請在 [Visual Studio 下載](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)上下載免費版本。
* NuGet 2.7 或更新版本。 若要取得最新版本，請參閱[安裝 NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)。

## <a name="download-and-run-the-sample-app"></a>下載及執行範例應用程式
**適用於 Azure SQL 的彈性 DB 工具 - 開始使用**範例應用程式會解說使用彈性資料庫工具開發分區化應用程式時最重要的開發體驗層面。 此範例應用程式著重在[分區對應管理](sql-database-elastic-scale-shard-map-management.md)、[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)和[多分區查詢](sql-database-elastic-scale-multishard-querying.md)的主要使用案例。 若要下載及執行範例，請遵循下列步驟： 

1. 從 MSDN 下載 [Azure SQL 的彈性 DB 工具：開始使用範例](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6)。 將範例解壓縮至您選擇的位置。

2. 若要建立專案，從 **C#** 目錄開啟 **ElasticScaleStarterKit.sln** 方案。

3. 在範例專案的方案中，開啟 **app.config** 檔案。 然後依照檔案中的指示，來新增您的 Azure SQL Database 伺服器名稱和您的登入資訊 (使用者名稱和密碼)。

4. 建置並執行應用程式。 出現提示時，允許 Visual Studio 還原解決方案的 NuGet 套件。 這會從 NuGet 下載最新版的彈性資料庫用戶端程式庫。

5. 利用不同的選項進行實驗，以深入了解用戶端程式庫功能。 請記下應用程式在主控台輸出中採用的步驟，並盡情探索其後的程式碼。
   
    ![Progress][4]

恭喜 - 您已使用彈性資料庫工具，在 SQL Database 上成功建置並執行您的第一個分區化應用程式。 請使用 Visual Studio 或 SQL Server Management Studio 連接到您的 SQL 資料庫，以快速瀏覽範例所建立的分區。 您會看見範例所建立的新範例分區資料庫和分區對應管理員資料庫。

> [!IMPORTANT]
> 建議您一律使用最新版的 Management Studio，如此就能與 Azure 及 SQL Database 更新保持同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

### <a name="key-pieces-of-the-code-sample"></a>程式碼範例的主要部分
* **管理分區和分區對應**：此程式碼會解說如何使用 **ShardManagementUtils.cs** 檔案中的分區、範圍和對應。 如需詳細資訊，請參閱[使用分區對應管理員相應放大資料庫](http://go.microsoft.com/?linkid=9862595)。  

* **資料相依路由**：**DataDependentRoutingSample.cs** 中示範如何將交易路由傳送至正確的分區。 如需詳細資訊，請參閱[資料相依路由](http://go.microsoft.com/?linkid=9862596)。 

* **查詢多個分區**：**MultiShardQuerySample.cs** 檔案中說明如何在各個分區間進行查詢。 如需詳細資訊，請參閱[多分區查詢](http://go.microsoft.com/?linkid=9862597)。

* **新增空的分區**：反覆新增空分區的作業，是由 **CreateShardSample.cs** 檔案中的程式碼所執行。 如需詳細資訊，請參閱[使用分區對應管理員相應放大資料庫](http://go.microsoft.com/?linkid=9862595)。

### <a name="other-elastic-scale-operations"></a>其他 Elastic Scale 作業
* **分割現有的分區**：分割分區的功能是透過**分割合併工具**來提供。 如需詳細資訊，請參閱[在向外延展的雲端資料庫之間移動資料](sql-database-elastic-scale-overview-split-and-merge.md)。

* **合併現有的分區**：分區合併也可使用**分割合併工具**來執行。 如需詳細資訊，請參閱[在向外延展的雲端資料庫之間移動資料](sql-database-elastic-scale-overview-split-and-merge.md)。   

## <a name="cost"></a>成本
彈性資料庫工具是免費的。 當您使用彈性資料庫工具時，不會收到您的 Azure 使用成本以外的任何額外費用。 

例如，範例應用程式會建立新資料庫。 此費用取決於您選擇的 SQL Database 版本，以及您應用程式的 Azure 使用量。

如需價格資訊，請參閱 [SQL Database 定價詳細資料](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="next-steps"></a>後續步驟
如需有關彈性資料庫工具的詳細資訊，請參閱下列頁面：

* 程式碼範例： 
  * [適用於 Azure SQL 的彈性 DB 工具 - 開始使用 (英文)](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
  * [適用於 Azure SQL 的彈性 DB 工具 - Entity Framework 整合 (英文)](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [指令碼中心的分區彈性](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* 部落格：[Elastic Scale 公告 (英文)](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy：[使用分區化與彈性資料庫用戶端程式庫視訊實作相應放大](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* 第 9 頻道：[Elastic Scale 概觀影片 (英文)](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* 討論論壇：[Azure SQL Database 論壇](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* 若要測量效能︰ [分區對應管理員的效能計數器](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

