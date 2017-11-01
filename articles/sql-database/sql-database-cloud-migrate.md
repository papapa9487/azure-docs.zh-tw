---
title: "SQL Server 資料庫移轉至 Azure SQL Database | Microsoft Docs"
description: "了解將 SQL Server 資料庫移轉至雲端 Azure SQL Database 的相關做法。"
keywords: "database migration,sql server database migration,database migration tools,migrate database,migrate sql database,資料庫移轉,sql server 資料庫移轉,資料庫移轉工具,移轉資料庫,移轉 sql database"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: migrate
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/08/2017
ms.author: carlrab
ms.openlocfilehash: 6147c5d24214933566e0a909ac99c817350578c7
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>SQL Server 資料庫移轉至雲端 SQL Database
在本文中，您將了解兩種用來將 SQL Server 2005 或更新版本資料庫移轉到 Azure SQL Database 的主要方法。 第一種方法比較簡單，但在移轉期間需要一些可能較長期的停機時間。 第二種方法比較複雜，但可大幅免去移轉期間的停機時間。

不論是哪一種方法，您都需要使用 [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) 來確定來源資料庫與 Azure SQL Database 相容。 除了伺服器層級和跨資料庫作業的問題相關之外，SQL Database V12 的功能正逐漸與 SQL Server 的[功能相等](sql-database-features.md)。 依賴[部分支援或未支援功能](sql-database-transact-sql-information.md)的資料庫和應用程式需要一些[再造來修正這些不相容情況](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues)，然後才能移轉 SQL Server 資料。

> [!NOTE]
> 若要將非 SQL Server 資料庫 (包括 Microsoft Access、Sybase、MySQL Oracle 和 DB2) 移轉到 Azure SQL Database，請參閱 [SQL Server 移轉小幫手](https://blogs.msdn.microsoft.com/datamigration/2016/12/22/released-sql-server-migration-assistant-ssma-v7-2/)。
> 

## <a name="method-1-migration-with-downtime-during-the-migration"></a>方法 1︰在移轉期間會停機的移轉作業

 如果您可以經得起一些停機時間，或者您在執行生產資料庫的測試移轉，以便稍後進行移轉，請使用此方法。 如需教學課程，請參閱[移轉 SQL Server Database](sql-database-migrate-your-sql-server-database.md)。

下列清單包含使用此方法移轉 SQL Server 資料庫時的一般工作流程。

  ![VSSSDT 移轉圖表](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. 使用最新版 [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)，評估資料庫的相容性。
2. 準備 Transact-SQL 指令碼形式的任何必要修正。
3. 為所要移轉的來源資料庫製作具有交易一致性的複本，並確保未再對來源資料庫進行任何變更 (或者，您可以在移轉完成後手動套用任何這類變更)。 有許多方法可以停止資料庫，從停用用戶端連接性到建立 [資料庫快照集](https://msdn.microsoft.com/library/ms175876.aspx)。
4. 部署 Transact-SQL 指令碼，將修正套用至資料庫複本。
5. 將資料庫複本[匯出](sql-database-export.md)至本機磁碟機上的 BACPAC 檔案。
6. 使用任一 BACPAC 匯入工具將 BACPAC 檔案[匯入](sql-database-import.md)成為新的 Azure SQL Database，若要獲得最佳效能，建議使用 SQLPackage.exe 工具。

### <a name="optimizing-data-transfer-performance-during-migration"></a>將移轉期間的資料傳輸效能最佳化 

下列清單包含可在匯入程序期間獲得最佳效能的建議。

* 選擇預算許可的最高服務層級和效能層級，以獲得最大傳輸效能。 移轉完成後，您可以相應減少層級以節省成本。 
* 盡量縮短 BACPAC 檔案和目的地資料中心之間的距離。
* 在移轉期間停用自動統計資料
* 分割資料表和索引
* 捨棄索引檢視表，然後於移轉完成後重新建立
* 將鮮少查詢的歷程記錄資料移除到另一個資料庫，然後將此歷程記錄資料移轉至不同的 Azure SQL Database。 您接著可以使用[彈性查詢](sql-database-elastic-query-overview.md)查詢此歷程記錄資料。

### <a name="optimize-performance-after-the-migration-completes"></a>在移轉完成後將效能最佳化

在移轉完成後，執行完整掃描以[更新統計資料](https://msdn.microsoft.com/library/ms187348.aspx)。

## <a name="method-2-use-transactional-replication"></a>方法 2：使用異動複寫

當您在移轉發生時無法負擔從實際執行中移除 SQL Server 資料庫時，可以使用 SQL Server 異動複寫做為移轉解決方案。 若要使用此方法，來源資料庫必須符合[異動複寫需求](https://msdn.microsoft.com/library/mt589530.aspx)且與 Azure SQL Database 相容。 如需使用 AlwaysOn 的 SQL 複寫相關資訊，請參閱[設定 AlwaysOn 可用性群組 (SQL Server) 的複寫](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)。

若要使用此解決方案，您需將 Azure SQL Database 設定為您想要移轉之 SQL Server 執行個體的訂閱者。 異動複寫散發者會在新交易繼續進行的同時，從要被同步處理的資料庫 (發行者) 同步處理資料。 

使用異動複寫時，對您資料或結構描述所做的一切變更都會顯示在 Azure SQL Database 中。 同步處理完成且您已準備好進行移轉之後，請將您應用程式的連接字串變更成指向您的 Azure SQL Database。 當異動複寫清空留在來源資料庫上的所有變更，並且您的所有應用程式都指向 Azure DB 之後，您便可以將異動複寫解除安裝。 您的 Azure SQL Database 現在已是您的生產環境系統。

 ![SeedCloudTR 圖表](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> 您也可以使用異動複寫以移轉來源資料庫的子集。 您複寫至 Azure SQL Database 的發佈可以限制為複寫的資料庫中資料表的子集。 針對要複寫的每一個資料表，您可以將資料限制在資料列的子集和 (或) 資料行的子集。
>

### <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>使用異動複寫工作流程移轉到 SQL Database

> [!IMPORTANT]
> 請使用最新版的 SQL Server Management Studio 以便與 Microsoft Azure 及 SQL Database 更新保持同步。 舊版 SQL Server Management Studio 無法將 SQL Database 設定為訂閱者。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 

1. 設定散發套件
   -  [使用 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [使用 Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. 建立發佈
   -  [使用 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [使用 Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. 建立訂用帳戶
   -  [使用 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [使用 Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

### <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>一些秘訣和移轉至 SQL Database 的差異

1. 使用本機散發者 
   - 這會影響伺服器的效能。 
   - 如果無法接受效能影響，您可以使用另一部伺服器，但它會增加管理和系統管理的複雜度。
2. 當選取快照集資料夾時，請確定您選取的資料夾足以容納要複寫的每個資料表 BCP。 
3. 建立快照集會鎖定相關聯的資料表直到建立完成為止，因此請適當排程快照集。 
4. Azure SQL Database 僅支援推送訂用帳戶。 您只能從來源資料庫新增訂閱者。

## <a name="resolving-database-migration-compatibility-issues"></a>解決資料庫移轉相容性問題
您可能會發現各種不同的不相容問題，取決於來源資料庫中的 SQL Server 版本，以及您正在移轉的資料庫複雜度。 舊版 SQL Server 有更多的相容性問題。 除了使用您選擇之搜尋引擎的目標網際網路搜尋之外，請使用下列資源︰

* [Azure SQL Database 中不支援的 SQL Server 資料庫功能](sql-database-transact-sql-information.md)
* [SQL Server 2016 中已終止的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [SQL Server 2014 中已終止的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [SQL Server 2012 中已終止的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [SQL Server 2008 R2 中已終止的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [SQL Server 2005 中已終止的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

除了搜尋網際網路和使用這些資源，另請使用 [MSDN SQL Server 社群論壇](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver)或 [StackOverflow](http://stackoverflow.com/)。

## <a name="next-steps"></a>後續步驟
* 使用 Azure SQL EMEA 工程師部落格上的指令碼來[監視移轉期間的 tempdb 使用量](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/)。
* 使用 Azure SQL EMEA 工程師部落格上的指令碼來[監視移轉時資料庫的交易記錄檔空間](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0)。
* 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。
* 如需移轉後的 UTC 時間處理相關資訊，請參閱[修改當地時區的預設時區](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/)。
* 如需移轉後變更資料庫預設語言的相關資訊，請參閱[如何變更 Azure SQL Database 的預設語言](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/)。


