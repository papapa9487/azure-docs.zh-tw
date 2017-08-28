---
title: "Azure SQL Database 服務和效能層 | Microsoft Docs"
description: "比較單一資料庫的 SQL Database 服務層和效能等級，並且介紹 SQL 彈性集區"
keywords: "資料庫選項, 資料庫效能"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/30/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: b25ff5331f119efd44c61808f7d1d5decb226bd6
ms.contentlocale: zh-tw
ms.lasthandoff: 08/10/2017

---
# <a name="what-performance-options-are-available-for-an-azure-sql-database"></a>Azure SQL Database 可用的效能選項為何

[Azure SQL Database](sql-database-technical-overview.md) 提供針對單一和[集區](sql-database-elastic-pool.md)資料庫提供四個服務層。 這些服務層為：[基本]、[標準]、[進階] 和 [進階 RS]。 每個服務層內都有多個效能等級 ([DTU](sql-database-what-is-a-dtu.md)) 和儲存體選項可處理不同的工作負載和資料大小。 較高的效能等級會提供額外的計算和儲存體資源，旨在提供愈來愈高的輸送量和容量。 您可以動態變更服務層、效能等級和儲存體，而不需要停機。 
- [基本]、[標準] 和 [高階] 服務層都具備 99.99% 的執行時間 SLA、彈性的商務持續性選項、安全性功能，以及按小時計費。 
- **Premium RS**層提供相同的效能等級為 Premium 層，以降低的 SLA，因為它會比其他服務層中的資料庫副本的較低數字以執行。 因此，當服務失敗時，最慢可能會延遲 5 分鐘才能從備份中復原資料庫。

> [!IMPORTANT]
> Azure SQL 資料庫都保證會有一組資源，且您資料庫的預期效能特性不會受到 Azure 內的任何其他資料庫影響。 

## <a name="choosing-a-service-tier"></a>選擇服務層
下表提供最適用於不同應用程式工作負載的服務層範例。

| 服務層 | 目標工作負載 |
| :--- | --- |
| **基本** | 最適合於小型資料庫，通常會在指定的時間內支援單一的作用中作業。 範例包括用於開發或測試，或者不常使用之小規模應用程式的資料庫。 |
| **標準** |雲端應用程式具有低度到中等 IO 效能需求時的建議選項，支援多個並行查詢。 範例包括工作群組或 Web 應用程式。 |
| **高級** | 針對具有高 IO 效能需求的高交易量而設計，支援許多並行使用者。 範例包括支援任務關鍵性應用程式的資料庫。 |
| **進階 RS** | 專為需要大量 IO 但不需要高可用性保證的工作負載所設計。 範例包括測試高效能工作負載，或資料庫不是記錄系統的分析工作負載。 |
|||

您可以在具有特定[效能等級](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)的服務層內，建立具有專用資源的單一資料庫，也可以在 [SQL 彈性集區](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)內建立資料庫。 在 SQL 彈性集區中，單一邏輯伺服器內的多個資料庫會共用計算和儲存體資源。 

單一資料庫可用的資源會以資料庫交易單位 (DTU) 表示，而 SQL 彈性集區的資源則會以彈性資料庫交易單位 (eDTU) 表示。 如需 DTU 和 eDTU 的詳細資訊，請參閱[什麼是 DTU 和 eDTU？](sql-database-what-is-a-dtu.md)。

若要決定服務層，請先判斷您所需要的資料庫功能底限︰

| **服務層功能** | **基本** | **標準** | **高級** | **進階 RS**|
| :-- | --: | --: | --: | --: |
| 單一資料庫大小上限 | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| 彈性集區大小上限 | 156 GB | 2.9 TB | 4 TB* | 750 GB |
| 彈性集區中的資料庫大小上限 | 2 GB | 250 GB | 500 GB | 500 GB |
| 每個集區的資料庫數目上限 | 500  | 500 | 100 | 100 |
| 單一資料庫 DTU 上限 | 5 | 100 | 4000 | 1000 |
| 彈性集區中每個資料庫的 DTU 上限 | 5 | 3000 | 4000 | 1000 |
| 資料庫備份的保留期限 | 7 天 | 35 天 | 35 天 | 35 天 |
||||||

> [!IMPORTANT]
> 高達 4 TB 的儲存體目前已在下列區域提供：美國東部 2、美國西部、美國維吉尼亞州政府、西歐、德國中部、東南亞、日本東部、澳大利亞東部、加拿大中部和加拿大東部。 請參閱[目前的 4 TB 限制](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

一旦決定適當的服務層之後，接下來要決定資料庫的效能等級 (DTU 數目) 和儲存體數量。 

- [標準] 層中的 S2 和 S3 效能等級通常是不錯的起點。 
- 而對於具有高 CPU 或 IO 需求的資料庫，[進階] 層中的效能等級才是正確的起點。 
- 相較於 [標準] 層中的最高效能等級，[進階] 層可提供更多的 CPU 以及多了 10 倍的 IO。
- [PremiumRS] 層以較低的成本提供 [進階] 層的效能，但 SLA 會降低。

> [!IMPORTANT]
> 檢閱 [SQL 彈性集區](sql-database-elastic-pool.md)主題中有關將資料庫群組成 SQL 彈性集區以便共用計算和儲存體資源的詳細資訊。 本主題的其他部分著重於單一資料庫的服務層和效能等級。
>

## <a name="single-database-service-tiers-and-performance-levels"></a>單一資料庫服務層和效能等級
若為單一資料庫，每個服務層內會有多個效能等級和儲存體數量。 

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="scaling-up-or-scaling-down-a-single-database"></a>相應放大或相應縮小單一資料庫

一開始挑選服務層和效能層級後，您可以根據實際經驗，動態放大或縮小單一資料庫。  

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

變更資料庫的服務層和/或效能層級會在新的效能層級建立原始資料庫的複本，然後將連接切換到複本。 此程序期間不會遺失任何資料，但在我們切換到複本的短暫期間，資料庫的連接會停用，因此執行中的某些交易可能會回復。 切換的時間長度會有所不同，但通常是在 4 秒以下，而 99% 的時間會小於 30 秒。 如果在連線停用時正在執行大型交易，則切換的時間長度可能會更長。  

整個向上調整程序的期間取決於資料庫變更前後的大小和服務層。 例如，在標準服務層內進行變更的 250 GB 資料庫應在 6 小時內完成。 對於進階服務層內變更效能層級的相同大小資料庫，它應該在 3 小時內完成。

> [!TIP]
> 若要檢查進行中的 SQL 資料庫調整作業的狀態，可以使用下列查詢：```select * from sys.dm_operation_status```。
>

* 如果您要升級到較高服務層或效能等級，除非您明確指定較大的大小上限，否則資料庫大小上限不會增加。
* 若要將資料庫降級，資料庫必須小於目標服務層允許的大小上限。 
* 升級資料庫時，若已啟用[異地複寫](sql-database-geo-replication-portal.md)，您必須先將其次要資料庫升級為所需的效能層，然後再升級主要資料庫 (一般指引)。 升級至不同的版本時，必須先升級次要資料庫。 
* 將資料庫降級時，若已啟用[異地複寫](sql-database-geo-replication-portal.md)，您必須先將其主要資料庫降級為所需的效能層，然後再降級次要資料庫 (一般指引)。 降級至不同的版本時，必須先降級主要資料庫。 

* 還原服務會針對各種服務層提供不同的選項。 如果降級至 [基本] 層，您將會有較短的備份保留期 - 請參閱 [Azure SQL Database 備份](sql-database-automated-backups.md)。
* 完成變更之前，不會將新屬性套用至資料庫。


## <a name="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize"></a>P11 和 P15 資料庫目前的大小上限為 4 TB

某些區域中支援的 P11 與 P15 資料庫大小上限為 4 TB (如先前所討論)。 下列考量與限制適用於大小上限為 4 TB 的 P11 和 P15 資料庫：

- 如果您在建立資料庫時選擇 4 TB 的 maxsize 選項 (使用的值為 4 TB 或 4096 GB)，但資料庫佈建在不受支援的區域，create 命令將會失敗並產生錯誤。
- 若現有的 P11 和 P15 資料庫位於其中一個受支援區域，您可以將 maxsize 儲存體增加為 4 TB。 您可以使用 [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx)，或檢查 Azure 入口網站中的資料庫大小加以確認。 只有伺服器層級的主要登入或 dbmanager 資料庫角色的成員，才能執行現有的 P11 或 P15 資料庫升級。 
- 如果是在支援的區域中執行升級作業，則系統會立即更新組態。 在升級過程中，資料庫會保持離線狀態。 但是，在將實際的資料庫檔案升級至新的大小上限前，您無法完整地使用 4TB 儲存體。 所需的時間長短依進行升級的資料庫大小而定。  
- 建立或更新 P11 或 P15 資料庫時，您只能在 1TB 和 4TB 的大小上限之間選擇。 目前不支援中繼儲存體大小。 在建立 P11/P15 時，系統會預先選取預設的 1 TB 儲存體選項。 若資料庫位於其中一個受支援區域，您可以將新的或現有單一資料庫的儲存體上限增加為 4 TB。 在其他所有區域中，無法將大小上限增加至 1 TB 以上。 當您選取 4 TB 的內含儲存體時，價格不會變更。
- 即使實際使用的儲存體不足 1TB，您也無法將 4TB 的資料庫大小上限變更為 1TB。 因此，在我們為其他效能層級提供額外的儲存體選項前，您無法將 P11-4TB/P15-4TB 降級為 P11-1TB/P15-1TB 或更低的效能層級 (例如，降為 P1-P6)。 這項限制也適用於包括時間點、異地還原、長期備份保留期和資料庫備份等還原和複製案例。 一旦將資料庫設定為 4TB 的選項，此資料庫的所有還原作業都必須以 4TB 大小上限的 P11/P15 執行。
- 在不受支援的區域中建立或升級 P11/P15 資料庫時，該建立或升級作業會失敗，並且顯示下列錯誤訊息：**具備最多 4TB 儲存體的 P11 和 P15 資料庫僅供美國東部 2、美國西部、美國維吉尼亞州政府、西歐、德國中部、東南亞、日本東部、澳大利亞東部、加拿大中部和加拿大東部使用。**
- 若是作用中異地複寫案例：
   - 設定異地複寫關聯性：如果 P11 或 P15 為主要資料庫，則次要資料庫也必須是 P11 或 P15；系統會拒絕使用較低的效能層級作為次要資料庫，因為這些資料庫無法支援 4TB 的大小。
   - 在異地複寫關聯性中升級主要資料庫：將主要資料庫的大小上限變更為 4TB 會在次要資料庫中觸發相同的變更。 這兩種升級方式都必須成功，在主要資料庫中的變更才會生效。 適用於 4TB 選項的區域限制 (請參閱上述內容)。 如果次要資料庫位於不支援 4TB 大小的區域，就無法升級主要資料庫。
- 不支援使用匯入/匯出服務載入 P11-4TB/P15-4TB 資料庫的功能。 使用 SqlPackage.exe 來[匯入](sql-database-import.md)和[匯出](sql-database-export.md)資料。

## <a name="manage-single-database-service-tiers-and-performance-levels-using-the-azure-portal"></a>使用 Azure 入口網站管理單一資料庫服務層和效能等級

若要使用 Azure 入口網站設定或變更新的或現有 Azure SQL 資料庫的服務層、效能等級或儲存體數量，請按一下 [定價層 (調整 DTU)] 為您的資料庫開啟 [設定效能] 視窗 - 如下列螢幕擷取畫面所示。 

- 選取您工作負載的服務層，設定或變更服務層。 
- 使用 [DTU] 滑桿來設定或變更服務層內的效能等級 (**DTU**)。
- 使用 [儲存體] 滑桿來設定或變更效能等級的儲存體數量。 

  ![設定服務層和效能等級](./media/sql-database-service-tiers/service-tier-performance-level.png)

> [!IMPORTANT]
> 選取 P11 或 P15 服務層時，檢閱[P11 和 P15 資料庫目前的大小上限為 4 TB](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)。
>

## <a name="manage-single-database-service-tiers-and-performance-levels-using-powershell"></a>使用 PowerShell 管理單一資料庫服務層和效能等級

若要使用 PowerShell 來設定或變更 Azure SQL 資料庫服務層、效能等級和儲存體數量，請使用下列 PowerShell Cmdlet。 如果您需要安裝或升級 PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 

| Cmdlet | 說明 |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|建立資料庫 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|取得一或多個資料庫|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|設定資料庫的屬性，或將現有資料庫移到彈性集區中|


> [!TIP]
> 如需可監視資料庫的效能計量、將其調整為較高的效能等級，並建立其中一個效能計量之警示規則的 PowerShell 範例指令碼，請參閱[使用 PowerShell 來監視和調整單一 SQL 資料庫](scripts/sql-database-monitor-and-scale-database-powershell.md)。

## <a name="manage-single-database-service-tiers-and-performance-levels-using-the-azure-cli"></a>使用 Azure CLI 管理單一資料庫服務層和效能等級

若要使用 Azure CLI 來設定或變更 Azure SQL 資料庫服務層、效能等級和儲存體數量，請使用下列 [Azure CLI SQL Database](/cli/azure/sql/db) 命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在您的瀏覽器中執行 CLI，或在 macOS、Linux 或 Windows 中[安裝](/cli/azure/install-azure-cli)。 如需建立和管理 SQL 彈性集區，請參閱[彈性集區](sql-database-elastic-pool.md)。

| Cmdlet | 說明 |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#create) |建立資料庫|
|[az sql db list](/cli/azure/sql/db#list)|列出伺服器中的所有資料庫和資料倉儲，或彈性集區中的所有資料庫|
|[az sql db list-editions](/cli/azure/sql/db#list-editions)|列出可用的服務目標與儲存體限制|
|[az sql db list-usages](/cli/azure/sql/db#list-usages)|傳回資料庫使用方式|
|[az sql db show](/cli/azure/sql/db#show)|取得資料庫或資料倉儲|
|[az sql db update](/cli/azure/sql/db#update)|更新資料庫|

> [!TIP]
> 如需會在查詢資料庫的大小資訊後將單一 Azure SQL Database 調整為不同效能等級的 Azure CLI 範例指令碼，請參閱[使用 CLI 來監視和調整單一 SQL 資料庫](scripts/sql-database-monitor-and-scale-database-cli.md)。
>

## <a name="manage-single-database-service-tiers-and-performance-levels-using-transact-sql"></a>使用 Transact-SQL 管理單一資料庫服務層和效能等級

若要使用 Transact-SQL 來設定或變更 Azure SQL 資料庫服務層、效能等級和儲存體數量，請使用下列 Transact-SQL 命令。 您可以使用 Azure 入口網站、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs)，或任何可連線到 Azure SQL Database 伺服器並傳遞 TRANSACT-SQL 命令的其他程式來發出這些命令。 

| 命令 | 說明 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|建立新的資料庫。 您必須連線到 master 資料庫才能建立新的資料庫。|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |修改 Azure SQL 資料庫。 |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|傳回 Azure SQL 資料庫或 Azure SQL 資料倉儲的版本 (服務層)、服務目標 (定價層) 和彈性集區名稱 (如果有的話)。 若已登入 Azure SQL Database 伺服器中的 master 資料庫，則傳回所有資料庫的相關資訊。 對於 Azure SQL 資料倉儲，您必須連線到 master 資料庫。|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|列出 Azure SQL Database 伺服器上的資料庫數目、類型和持續時間。|

下列範例示範正在使用 ALTER DATABASE 命令變更 maxsize 的情形︰

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-databases-using-the-rest-api"></a>使用 REST API 管理單一資料庫

若要使用 REST API 來設定或變更 Azure SQL 資料庫服務層、效能等級和儲存體數量，請參閱 [Azure SQL Database REST API](/rest/api/sql/)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [DTU](sql-database-what-is-a-dtu.md)。
* 若要深入了解如何監視 DTU 使用量，請參閱[監視和效能調整](sql-database-troubleshoot-performance.md)。


