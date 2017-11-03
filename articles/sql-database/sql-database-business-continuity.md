---
title: "雲端商務持續性 - 資料庫復原 - SQL Database | Microsoft Docs"
description: "了解 Azure SQL Database 如何支援雲端商務持續性和資料庫復原，以及如何協助維持任務關鍵性雲端應用程式持續執行。"
keywords: "business continuity,cloud business continuity,database disaster recovery,database recovery,商務持續性,雲端商務持續性,資料庫災害復原,資料庫復原"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 18e5d3f1-bfe5-4089-b6fd-76988ab29822
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 08/25/2017
ms.author: sashan
ms.openlocfilehash: 936f95700cb57325a572e5509334398a724c4986
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>使用 Azure SQL Database 的商務持續性概觀

本概觀說明 Azure SQL Database 針對商務持續性和災害復原所提供的功能。 了解選項、建議和教學課程，以從可能導致資料遺失或造成資料庫和應用程式無法使用的干擾性事件中復原。 了解當使用者或應用程式錯誤影響資料完整性、Azure 區域中斷，或您的應用程式需要維護時該如何處理。

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>您可用來提供商務持續性的 SQL Database 功能

SQL Database 提供幾種商務持續性功能，包括自動備份和選用的資料庫複寫。 每個功能對於預估的復原時間 (ERT) 都有不同的特性，最近的交易都有可能遺失資料。 一旦您了解這些選項，就可以在其中選擇，而在大部分情況下，可以針對不同情況一起搭配使用。 當您開發商務持續性方案時，您必須了解應用程式在干擾性事件之後完全復原的最大可接受時間 - 這是您的復原時間目標 (RTO)。 您也必須了解在干擾性事件之後復原時，應用程式可忍受遺失的最近資料更新 (時間間隔) 最大數量，這就是您的復原點目標 (RPO)。

下表針對三種最常見的案例提供 ERT 與 RPO 的比較。

| 功能 | 基本層 | 標準層 | 高階層 |
| --- | --- | --- | --- |
| 從備份進行時間點還原 |7 天內的任何還原點 |35 天內的任何還原點 |35 天內的任何還原點 |
| 從異地複寫備份進行異地還原 |ERT < 12 小時，RPO < 1 小時 |ERT < 12 小時，RPO < 1 小時 |ERT < 12 小時，RPO < 1 小時 |
| 從 Azure 備份保存庫還原 |ERT < 12 小時，RPO < 1 週 |ERT < 12 小時，RPO < 1 週 |ERT < 12 小時，RPO < 1 週 |
| 主動式異地複寫 |ERT < 30 秒，RPO < 5 秒 |ERT < 30 秒，RPO < 5 秒 |ERT < 30 秒，RPO < 5 秒 |

### <a name="use-database-backups-to-recover-a-database"></a>使用資料庫備份來復原資料庫

SQL Database 會每週自動執行完整資料庫備份、每小時自動執行差異資料庫備份，以及每 5 到 10 分鐘自動執行交易記錄備份，透過這樣的備份組合來防止您的企業遺失資料。 針對「標準」和「進階」服務層中的資料庫，這些備份會在異地備援儲存體中儲存達 35 天，如果是「基本」服務層中的資料庫，則儲存天數為 7 天。 如需詳細資訊，請參閱[服務層](sql-database-service-tiers.md)。 如果服務層的保留期間不符合您的企業需求，您可以 [變更服務層](sql-database-service-tiers.md)來增長保留期間。 完整和差異資料庫備份也會複寫到[配對的資料中心](../best-practices-availability-paired-regions.md)，以防止發生資料中心中斷的情況。 如需詳細資訊，請參閱[自動資料庫備份](sql-database-automated-backups.md)。

如果應用程式內建的保留期間不夠用，您可以藉由為資料庫設定長期保留原則來延長此期間。 如需詳細資訊，請參閱[長期保留](sql-database-long-term-retention.md)。

您可以使用這些自動資料庫備份，將資料庫從各種干擾性事件復原，不論是在您的資料中心內復原，還是復原到另一個資料中心，都可以。 使用自動資料庫備份時，預估的復原時間取決於數個因素，包括在相同區域中同時進行復原的資料庫總數、資料庫大小、交易記錄大小，以及網路頻寬。 復原時間通常不到 12 小時。 復原到另一個資料區域時，每小時差異資料庫備份的異地備援儲存體就限制為可能遺失 1 小時的資料。

> [!IMPORTANT]
> 若要使用自動備份來進行復原，您必須是「SQL Server 參與者」角色的成員或訂用帳戶擁有者 - 請參閱 [RBAC︰內建角色](../active-directory/role-based-access-built-in-roles.md)。 您可以使用 Azure 入口網站、PowerShell 或 REST API 來進行復原。 您無法使用 Transact-SQL。
>

如果您的應用程式有下列狀況，可以使用自動備份做為您的商務持續性和復原機制︰

* 非關鍵性應用程式。
* 沒有繫結 SLA - 停機 24 小時或更長的時間不會衍生財務責任。
* 資料變更率低 (每小時的交易次數低)，並且最多可接受遺失一小時的資料變更。
* 成本有限。

如果您需要更快速的復原，請使用[主動式異地複寫](sql-database-geo-replication-overview.md) (會接著討論)。 如果您必須能夠復原 35 天之前的資料，請使用[長期備份保留](sql-database-long-term-retention.md)。 

### <a name="use-active-geo-replication-and-auto-failover-groups-in-preview-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>使用作用中異地複寫和自動容錯移轉群組 (預覽版)，以減少復原時間並限制與復原關聯的資料損失

除了在發生業務中斷時使用資料庫備份來進行資料庫復原之外，您還可以使用[主動式異地複寫](sql-database-geo-replication-overview.md)來設定資料庫，在您選擇的區域中最多可擁有 4 個可讀取的次要資料庫。 這些次要資料庫會使用非同步複寫機制與主要資料庫保持同步。 此功能可用來防範資料中心中斷或應用程式升級期間的業務中斷。 主動式異地複寫也可用來為地理位置分散的使用者，就唯讀查詢方面提供較佳的查詢效能。

若要啟用自動透明容錯移轉，您應使用 SQL Database [自動容錯移轉群組](sql-database-geo-replication-overview.md)功能 (預覽版)，將異地複寫資料庫分組。

如果主要資料庫意外離線，或者您需要離線進行維護活動，可以快速將次要升級成主要 (也稱為容錯移轉)，並且設定應用程式連線到已升級的主要資料庫。 若您的應用程式使用容錯移轉群組接聽程式連線至資料庫，則在完成容錯移轉後無須變更 SQL 連接字串組態。 使用計劃性容錯移轉時，不會有任何資料遺失。 使用非計劃性容錯移轉時，則由於非同步複寫的性質緣故，可能會有一些少量的資料遺失。 您可使用自動容錯移轉群組 (預覽版) 來自訂容錯移轉原則，以將潛在資料遺失風險降至最低。 容錯移轉之後，無論是根據計畫或當資料中心再次上線時，就可以容錯回復。 在所有情況下，使用者都會經歷短暫的停機時間，而需要重新連線。

> [!IMPORTANT]
> 若要使用作用中異地複寫和自動容錯移轉群組 (預覽版)，您必須是訂用帳戶擁有者，或是在 SQL Server 中擁有系統管理權限。 您可以使用 Azure 入口網站、PowerShell 或 REST API 並透過 Azure 訂用帳戶的權限來進行設定和容錯移轉，也可以使用 Transact-SQL 並透過 SQL Server 權限來進行。
> 

如果您的應用程式符合下列任何準則，請使用主動式異地複寫和自動容錯移轉群組 (預覽版)：

* 是關鍵性應用程式。
* 具有服務等級協定 (SLA)，不允許 24 小時以上的停機時間。
* 停機可能會衍生財務責任。
* 具有很高的資料變更率，而且不接受遺失一個小時的資料。
* 與潛在的財務責任和相關企業損失相較下，使用主動式異地複寫的額外成本較低。

>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>在使用者或應用程式錯誤之後復原資料庫
* 沒有人是完美的！ 使用者可能會不小心刪除某些資料、不小心卸除重要的資料表，或甚至是卸除整個資料庫。 或者，應用程式可能會因為應用程式缺陷，而意外以不正確的資料覆寫正確的資料。

在此案例中，以下是您的復原選項。

### <a name="perform-a-point-in-time-restore"></a>執行還原時間點
您可以使用自動備份，將資料庫的複本復原到已知是在資料庫保留期限內的時間點。 資料庫還原之後，您可以將原始資料庫取代為還原的資料庫，或從還原的資料將所需的資料複製到原始資料庫。 如果資料庫使用主動式異地複寫，我們建議從還原的複本將所需的資料複製到原始資料庫。 如果您將原始資料庫取代為還原的資料庫，則必須重新設定並重新同步處理主動式異地複寫 (大型資料庫可能要花費很久的時間)。 雖然這會將資料庫還原到最後一個可用的時間點，但目前不支援將地區次要資料庫還原至任何時間點。

如需詳細資訊以及使用 Azure 入口網站或 PowerShell 將資料庫還原至某個時間點的詳細步驟，請參閱 [還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)。 您無法使用 Transact-SQL 進行復原。

### <a name="restore-a-deleted-database"></a>還原已刪除的資料庫
如果資料庫已刪除，但邏輯伺服器尚未刪除，您可以將已刪除的資料庫還原到它被刪除時的時間點。 這會將資料庫備份還原到資料庫被刪除的相同邏輯 SQL 伺服器。 您可以使用原始名稱還原，或是為還原的資料庫提供新的名稱。

如需詳細資訊以及使用 Azure 入口網站或 PowerShell 來還原已刪除資料庫的詳細步驟，請參閱 [還原已刪除的資料庫](sql-database-recovery-using-backups.md#deleted-database-restore)。 您無法使用 Transact-SQL 進行還原。

> [!IMPORTANT]
> 如果邏輯伺服器已刪除，您就無法復原已刪除的資料庫。
>
>

### <a name="restore-from-azure-backup-vault"></a>從 Azure 備份保存庫還原
如果在自動備份的目前保留期間外發生資料遺失，且您的資料庫設定為長期保留，則您可以從 Azure 備份保存庫的每週備份還原至新的資料庫。 目前，您可以將原始資料庫取代為還原的資料庫，或從還原的資料庫將所需的資料複製到原始資料庫。 如果您需要在主要應用程式升級之前擷取舊版資料庫，請滿足稽核員或合法順序的要求，您可以使用 Azure 備份保存庫中儲存的完整備份來建立資料庫。  如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>將資料庫從 Azure 區域資料中心中斷復原到另一個區域
<!-- Explain this scenario -->

雖然很罕見，但 Azure 資料中心也可能會有中斷的時候。 發生中斷時，可能只會讓業務中斷幾分鐘，也可能會持續幾小時。

* 其中一個選項是在資料中心中斷結束時等待您的資料庫重新上線。 這適用於可以容忍資料庫離線的應用程式。 例如，您不需要不斷處理的開發專案或免費試用版。 當資料中心中斷時，您不會知道中斷會持續多久，因此這個選項僅適用於您可以一段時間暫時不需要資料庫。
* 另一個選項是容錯移轉到另一個資料區域 (如果您使用主動式異地複寫) 或使用異地備援資料庫備份來復原資料庫 (異地還原)。 容錯移轉只需要幾秒鐘的時間，而從備份復原資料庫則需要幾個小時。

當您採取行動時，復原所需的時間以及會遺失多少資料，取決於您如何決定在應用程式中使用這些商務持續性功能。 事實上，您可以根據應用程式需求，選擇使用資料庫備份和主動式異地複寫的組合。 若要探討使用這些商務持續性功能針對獨立資料庫和彈性集區進行應用程式設計時的考量，請參閱[設計雲端災害復原應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)和[彈性集區災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

下列各節概述使用資料庫備份或主動式異地複寫來進行復原的步驟。 如需包括規劃需求的詳細步驟、復原後步驟，以及有關如何模擬中斷以執行災害復原演練的資訊，請參閱[從中斷復原 SQL Database](sql-database-disaster-recovery.md)。

### <a name="prepare-for-an-outage"></a>準備中斷
無論您要使用何種商務持續性功能，您都必須︰

* 識別並準備目標伺服器，包括伺服器層級防火牆規則、登入和 master 資料庫層級權限。
* 決定如何將用戶端和用戶端應用程式重新導向到新的伺服器
* 記錄其他相依性，例如稽核設定和警示

如果您沒有適當地準備，在容錯移轉或資料庫復原後讓應用程式上線將會多花費時間，而且也可能需要在有壓力的情況下進行疑難排解 - 這是不良的情況組合。

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>容錯移轉至異地複寫的次要資料庫
若您使用作用中異地複寫和自動容錯移轉群組 (預覽版) 作為復原機制，則可設定自動容錯移轉原則或使用[手動容錯移轉](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-database)。 啟動容錯移轉後，次要資料庫就會成為新的主要資料庫，並準備好記錄新的交易以及回應查詢 - 只會遺失尚未複寫的資料。 如需關於設計容錯移轉程序的資訊，請參閱[設計雲端災害復原應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

> [!NOTE]
> 資料中心恢復連線時，舊的主要複本會自動重新連線至新的主要複本，且會成為次要資料庫。 若您需要將主要複本重新定位至原始區域，可手動啟動規劃的容錯移轉 (容錯回復)。 
> 

### <a name="perform-a-geo-restore"></a>執行異地還原
如果您使用自動備份搭配異地備援儲存體複寫作為復原機制，請[使用異地還原起始資料庫復原](sql-database-disaster-recovery.md#recover-using-geo-restore)。 復原通常會在 12 小時內進行 - 視最後一次每小時差異備份的執行和複寫時間而定，最多可能遺失 1 小時的資料。 在復原完成之前，資料庫無法記錄任何交易或回應任何查詢。 雖然這會將資料庫還原到最後一個可用的時間點，但目前不支援將地區次要資料庫還原至任何時間點。

> [!NOTE]
> 如果資料中心在您的應用程式切換到復原的資料庫之前就再次上線，您可以取消復原。  
>
>

### <a name="perform-post-failover--recovery-tasks"></a>執行容錯移轉後/復原後工作
從其中任何一種復原機制復原之後，您都必須執行下列額外的工作，您的使用者和應用程式才能回復正常執行狀態︰

* 重新導向用戶端與用戶端應用程式到新的伺服器與還原的資料庫
* 確定有適當的伺服器層級防火牆規則供使用者連線 (或使用 [資料庫層級防火牆](sql-database-firewall-configure.md#creating-and-managing-firewall-rules))
* 確定有適當的登入和 master 資料庫層級權限 (或使用 [自主的使用者](https://msdn.microsoft.com/library/ff929188.aspx))
* 依適當情況設定稽核
* 依適當情況設定警示

## <a name="upgrade-an-application-with-minimal-downtime"></a>在最少停機時間的情況下升級應用程式
有時，應用程式會因為計劃性維護 (例如應用程式升級) 而必須離線。 [管理應用程式升級](sql-database-manage-application-rolling-upgrade.md) 說明如何使用「主動式異地複寫」來輪流升級雲端應用程式，以將升級時的停機時間縮到最短，並提供發生錯誤時的復原路徑。 

## <a name="next-steps"></a>後續步驟
如需獨立資料庫和彈性集區的應用程式設計考量探討，請參閱[設計雲端災害復原應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)和[彈性集區災害復原策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
