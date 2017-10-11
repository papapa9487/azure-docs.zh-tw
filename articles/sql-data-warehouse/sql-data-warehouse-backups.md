---
title: "Azure SQL 資料倉儲備份 - 快照集、異地備援 | Microsoft Docs"
description: "了解 SQL 資料倉儲內建資料庫備份，它可以讓您將 Azure SQL 資料倉儲還原到還原點或不同的地理區域。"
services: sql-data-warehouse
documentationcenter: 
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
ms.openlocfilehash: 54c0149a769e654139bbdf709802d49127f041ac
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="sql-data-warehouse-backups"></a>SQL 資料倉儲備份
SQL 資料倉儲備份提供本機和異地備份做為其資料倉儲備份功能的一部份。 這些包括 Azure 儲存體 Blob 快照集和異地備援儲存體。 使用資料倉儲備份來將您的資料倉儲還原至主要區域中的某一個還原點，或還原至不同的地理區域。 本文說明 SQL 資料倉儲中備份的詳細資訊。

## <a name="what-is-a-data-warehouse-backup"></a>什麼是資料倉儲備份？
資料倉儲備份是您可以用來將資料倉儲還原至某一特定時間點的資料。  由於 SQL 資料倉儲是一個分散式系統，所以一個資料倉儲備份是由儲存在 Azure Blob 中的許多檔案組成。 

資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 如需詳細資訊，請參閱[商務持續性概觀](../sql-database/sql-database-business-continuity.md)。

## <a name="data-redundancy"></a>資料備援
SQL 資料倉儲會透過將您的資料儲存在本地備援 (LRS) Azure 進階儲存體來保護您的資料。 這項 Azure 儲存體功能可將資料的多個同步複本儲存在當地的資料中心，以確保當地語系化失敗時能夠提供透明的資料保護。 資料備援可確保您的資料在發生基礎結構問題 (例如磁碟故障等) 時能夠存留。 資料備援可利用容錯和高可用性基礎結構來確保商務持續性。

若要深入了解：

* Azure 進階儲存體，請參閱 [Azure 進階儲存體簡介](../storage/common/storage-premium-storage.md)。
* 本地備援儲存體，請參閱 [Azure 儲存體複寫](../storage/common/storage-redundancy.md#locally-redundant-storage)。

## <a name="azure-storage-blob-snapshots"></a>Azure 儲存體 Blob 快照集
使用 Azure 進階儲存體的一項優點是，SQL 資料倉儲會使用 Azure 儲存體 Blob 快照集來將資料倉儲備份在本地位置。 您可以將資料倉儲還原至快照集還原點。 快照集至少每八個小時會啟動，並且可供使用七天。  

若要深入了解：

* Azure Blob 快照集，請參閱[建立 Blob 快照集](../storage/blobs/storage-blob-snapshots.md)。

## <a name="geo-redundant-backups"></a>異地備援備份
每 24 個小時，SQL 資料倉儲就會在標準儲存體中儲存完整資料倉儲。 完整資料倉儲的建立時間會與上一個快照集的時間相符。 標準儲存體屬於具備讀取權限的異地備援儲存體帳戶 (RA-GRS)。 Azure 儲存體 RA-GRS 功能會將備份檔案複寫到 [配對的資料中心](../best-practices-availability-paired-regions.md)。 萬一您無法存取主要地區中的快照集，此異地複寫可確保您能夠還原資料倉儲。 

這項功能為預設開啟。 如果您不想要使用異地備援備份，您可以 [退出] (https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicy?redirectedfrom=msdn)。 

> [!NOTE]
> 在 Azure 儲存體中，「複寫」一詞指的是將檔案從某個位置複製到另一個位置。 SQL 的「資料庫複寫」  指的是保持多個次要資料庫與主要資料庫同步。 
> 
> 

> [!NOTE]
> 您無法選擇退出 DWU 9000 和 DWU 18000 的異地備援備份。 
>
> 

若要深入了解：

* 異地備援儲存體，請參閱 [Azure 儲存體複寫](../storage/common/storage-redundancy.md)。
* RA-GRS 儲存體：請參閱 [讀取權限異地備援儲存體](../storage/common/storage-redundancy.md#read-access-geo-redundant-storage)。

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>資料倉儲備份排程與保留期限
SQL 資料倉儲每四到八小時就會在您的線上資料倉儲上建立快照集，並將每個快照集保留七天。 您可以將線上資料庫還原至過去七天內的其中一個還原點。 

若要查看上一個快照集的開始時間，請在您的線上 SQL 資料倉儲上執行此查詢。 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

如果您需要將快照集保留超過七天，您可以將還原點還原至新的資料倉儲。 完成還原之後，SQL 資料倉儲就會開始在新的資料倉儲上建立快照集。 如果您沒有變更新的資料倉儲，快照集就會保持空白，因此快照集成本會降到最低。 您也可以暫停資料庫來避免 SQL 資料倉儲建立快照集。

### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>當我的資料倉儲暫停時，我的備份保留期會發生什麼狀況？
暫停資料倉儲時，SQL 資料倉儲不會建立快照集，快照集也不會過期。 暫停資料倉儲時，快照集存在時間不會改變。 快照集保留期是以資料倉儲上線的天數為依據，而不是以行事曆天數為依據。

例如，如果快照集是在 10 月 1 日下午 4 點開始，資料倉儲是在 10 月 3 日下午 4 點暫停，快照的存在時間為 2 天。 無論資料倉儲何時恢復上線，快照集的存在時間都是 2 天。 如果資料倉儲在 10 月 5 日下午 4 點恢復上線，快照集的存在時間為 2 天，並會繼續保留 5 天。

當資料倉儲恢復上線時，SQL 資料倉儲會繼續建立新的快照集，並在快照集包含超過 7 天的資料時讓快照集過期。

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>已卸除資料倉儲的保留期限有多久？
當資料倉儲被卸除時，資料倉儲和快照集會儲存 7 天，然後就會被移除。 您可以將資料倉儲還原至任何一個已儲存的還原點。

> [!IMPORTANT]
> 如果您刪除邏輯 SQL Server 執行個體，所有屬於該執行個體的資料庫也會一併刪除，且無法復原。 您無法還原已刪除的伺服器。
> 
> 

## <a name="data-warehouse-backup-costs"></a>資料倉儲備份成本
您主要資料倉儲和 7 天 Azure Blob 快照集的總成本會四捨五入到最接近的 TB。 例如，如果您的資料倉儲為 1.5 TB，且快照集使用 100 GB，就會以 Azure 進階儲存體費率向您收取 2 TB 資料費用。 

> [!NOTE]
> 每個快照集一開始都是空白的，然後會隨著您變更主要資料倉儲而成長。 所有快照集的大小都會隨著資料倉儲變更而增加。 因此快照集的儲存體成本也會依據變更的速度而增加。
> 
> 

如果您正在使用異地備援儲存體，您會收到個別的儲存體收費項目。 異地備援儲存體是依據標準讀取權限異地備援儲存體 (RA-GRS) 費率收費。

如需 SQL 資料倉儲價格的相關詳細資訊，請參閱 [SQL 資料倉儲價格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

## <a name="using-database-backups"></a>使用資料庫備份
SQL 資料倉儲備份的主要用途，是用來將資料倉儲還原至保留期限內的其中一個還原點。  

* 若要還原 SQL 資料倉儲，請參閱[還原 SQL 資料倉儲](sql-data-warehouse-restore-database-overview.md)。

## <a name="related-topics"></a>相關主題
### <a name="scenarios"></a>案例
* 如需商務持續性概觀，請參閱[商務持續性概觀](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

* 若要還原資料倉儲，請參閱[還原 SQL 資料倉儲](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->

