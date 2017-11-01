---
title: "Azure SQL 資料倉儲備份 - 快照集、異地備援 | Microsoft Docs"
description: "了解 SQL 資料倉儲內建資料庫備份，它可以讓您將 Azure SQL 資料倉儲還原到還原點或不同的地理區域。"
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: e76349ef7a2afa02d4f9e5295f299bb8084d1e08
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>SQL 資料倉儲中的備份與還原
本文說明 SQL 資料倉儲中備份的詳細資訊。 使用資料倉儲備份來將資料庫快照集還原到主要區域，或者將異地備份還原到您的地理配對區域。 

## <a name="what-is-a-data-warehouse-backup"></a>什麼是資料倉儲備份？
資料倉儲備份是您可以用來還原資料倉儲的資料庫複本。  由於 SQL 資料倉儲是一個分散式系統，所以一個資料倉儲備份會由位於 Azure 儲存體中的許多檔案組成。 資料倉儲備份包含本機資料庫快照集，以及與資料倉儲相關聯之所有資料庫和檔案的異地備份。 

## <a name="local-snapshot-backups"></a>本機快照集備份
SQL 資料倉儲會全天候建立您資料倉儲的快照集。 快照集的有效期是七天。 SQL 資料倉儲支援八小時的復原點目標 (RPO)。 您可以將主要區域中的資料倉儲還原到您在過去七天內所建立的任一個快照集。

若要查看上一個快照集的開始時間，請在您的線上 SQL 資料倉儲上執行此查詢。 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="geo-backups"></a>異地備份
SQL 資料倉儲每天都會對[配對的資料中心](../best-practices-availability-paired-regions.md)執行一次異地備份。 異地還原的 RPO 為 24 小時。 您可以將異地備份還原到地理配對區域中的伺服器。 萬一您無法存取主要地區中的快照集，異地備份可以確保您能夠還原資料倉儲。

預設會開啟異地備份。 如果您的資料倉儲已針對彈性最佳化，則可以視需要[選擇退出](https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicyredirectedfrom=msdn)。 您無法選擇退出具有針對計算最佳化之效能層級的異地備份。

## <a name="backup-costs"></a>備份成本
您將發現 Azure 帳單含有 Azure 進階儲存體的明細項目以及異地備援儲存體的明細項目。 進階儲存體的費用是在主要區域中儲存資料 (包含快照集) 的總成本。  異地備援的費用則涵蓋儲存異地備份的成本。  

您主要資料倉儲和 7 天 Azure Blob 快照集的總成本會四捨五入到最接近的 TB。 例如，如果您的資料倉儲為 1.5 TB，且快照集使用 100 GB，就會以 Azure 進階儲存體費率向您收取 2 TB 資料費用。 

> [!NOTE]
> 每個快照集一開始都是空白的，然後會隨著您變更主要資料倉儲而成長。 所有快照集的大小都會隨著資料倉儲變更而增加。 因此快照集的儲存體成本也會依據變更的速度而增加。
> 
> 

如果您正在使用異地備援儲存體，您會收到個別的儲存體收費項目。 異地備援儲存體是依據標準讀取權限異地備援儲存體 (RA-GRS) 費率收費。

如需 SQL 資料倉儲價格的相關詳細資訊，請參閱 [SQL 資料倉儲價格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>資料倉儲暫停時的快照集保留期
暫停資料倉儲時，SQL 資料倉儲不會建立快照集，快照集也不會過期。 暫停資料倉儲時，快照集存在時間不會改變。 快照集保留期是以資料倉儲上線的天數為依據，而不是以行事曆天數為依據。

例如，如果快照集是在 10 月 1 日下午 4 點開始，而資料倉儲是在 10 月 3 日下午 4 點暫停，則快照集最多是兩天前的。 當資料倉儲恢復上線時，快照集是兩天前的。 如果資料倉儲在 10 月 5 日下午 4 點恢復上線，快照集的存在時間為 2 天，並會繼續保留 5 天。

當資料倉儲恢復上線時，SQL 資料倉儲會繼續建立新的快照集，並在快照集包含超過 7 天的資料時讓快照集過期。

## <a name="can-i-restore-a-dropped-data-warehouse"></a>我可以還原卸除的資料倉儲嗎？
當您卸除資料倉儲時，SQL 資料倉儲會建立最後的快照集，並將它儲存七天。 您可以將資料倉儲還原到刪除期間建立的最後一個還原點。 

> [!IMPORTANT]
> 如果您刪除邏輯 SQL Server 執行個體，所有屬於該執行個體的資料庫也會一併刪除，且無法復原。 您無法還原已刪除的伺服器。
> 

## <a name="next-steps"></a>後續步驟
若要還原 SQL 資料倉儲，請參閱[還原 SQL 資料倉儲](sql-data-warehouse-restore-database-overview.md)。

如需商務持續性概觀，請參閱[商務持續性概觀](../sql-database/sql-database-business-continuity.md)
