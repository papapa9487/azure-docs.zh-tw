---
title: "Azure SQL Database 資源限制 | Microsoft Docs"
description: "此頁面描述一些 Azure SQL Database 常見的資源限制。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/25/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 66fa69b746ce2404e3083a9c3872300a9efee874
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="azure-sql-database-resource-limits"></a>Azure SQL Database 資源限制

## <a name="single-database-storage-sizes-and-performance-levels"></a>單一資料庫：儲存體大小與效能層級

對於單一資料庫，下表顯示了單一資料庫在每個服務層和效能層級的可用資源。 您可以使用 [Azure 入口網站](#manage-single-database-resources-using-the-azure-portal)、[TRANSACT-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql)、[PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell)、[Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) 或 [REST API](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api) 來為單一資料庫設定服務層、效能層級和儲存體數量。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="single-database-change-storage-size"></a>單一資料庫： 變更儲存體大小

- 單一資料庫的 DTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量和大小上限，請參閱[單一資料庫：儲存體大小與效能層級](#single-database-storage-sizes-and-performance-levels)。
- 可藉由使用 [Azure 入口網站](#manage-single-database-resources-using-the-azure-portal)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#update) 或 [REST API](/rest/api/sql/databases/update) 增加其大小上限，以佈建單一資料庫的額外儲存體。
- 單一資料庫之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="single-database-change-dtus"></a>單一資料庫：變更 DTU

一開始選取服務層、效能層級和儲存體數量之後，您可以根據實際經驗，使用 [Azure 入口網站](#manage-single-database-resources-using-the-azure-portal)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#update) 或 [REST API](/rest/api/sql/databases/update) 來動態地相應增加或相應減少單一資料庫。 

下列影片示範了如何動態變更效能層以增加單一資料庫的可用 DTU。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

變更資料庫的服務層和/或效能層級會在新的效能層級建立原始資料庫的複本，然後將連接切換到複本。 此程序期間不會遺失任何資料，但在我們切換到複本的短暫期間，資料庫的連接會停用，因此執行中的某些交易可能會回復。 切換的時間長度會有所不同，但通常是在 4 秒以下，而 99% 的時間會小於 30 秒。 如果在連線停用時正在執行大型交易，則切換的時間長度可能會更長。 

整個向上調整程序的期間取決於資料庫變更前後的大小和服務層。 例如，在標準服務層內進行變更的 250 GB 資料庫應在六小時內完成。 對於進階服務層內變更效能層級的相同大小資料庫，應該在三小時內完成相應增加。

> [!TIP]
> 若要檢查進行中的 SQL 資料庫調整作業的狀態，可以使用下列查詢：```select * from sys.dm_operation_status```。
>

* 如果您要升級到較高服務層或效能層級，除非您明確指定較大的大小 (大小上限)，否則資料庫大小上限不會增加。
* 若要將資料庫降級，資料庫已用的空間必須小於目標服務層和效能層級允許的大小上限。 
* 從**進階**或**進階 RS** 降級至**標準**層時，如果發生下列情況，會產生額外的儲存體成本：(1) 目標效能層級可支援資料庫的大小上限，而且 (2) 大小上限超過目標效能層級的內含儲存體數量。 例如，如果大小上限為 500 GB 的 P1 資料庫縮減為 S3，即會產生額外的儲存體成本，因為 S3 可支援 500 GB 的大小上限，而且其內含儲存體數量只有 250 GB。 因此，額外的儲存體數量為 500 GB – 250 GB = 250 GB。 如需額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。 
* 升級資料庫時，若已啟用[異地複寫](sql-database-geo-replication-portal.md)，您必須先將其次要資料庫升級為所需的效能層，然後再升級主要資料庫 (一般指引)。 升級至不同的版本時，必須先升級次要資料庫。
* 將資料庫降級時，若已啟用[異地複寫](sql-database-geo-replication-portal.md)，您必須先將其主要資料庫降級為所需的效能層，然後再降級次要資料庫 (一般指引)。 降級至不同的版本時，必須先降級主要資料庫。
* 還原服務會針對各種服務層提供不同的選項。 如果降級至**基本**層，會有較短的備份保留期 - 請參閱 [Azure SQL Database 備份](sql-database-automated-backups.md)。
* 完成變更之前，不會將新屬性套用至資料庫。

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>單一資料庫：當大小上限大於 1 TB 時，P11 和 P15 的限制

下列區域支援大小上限大於 1 TB 的 P11 和 P15 資料庫：美國東部 2、美國西部、美國維吉尼亞州政府、西歐、德國中部、東南亞、日本東部、澳大利亞東部、加拿大中部和加拿大東部。 下列考量與限制適用於大小上限大於 1 TB 的 P11 和 P15 資料庫：

- 如果您在建立資料庫時選擇的大小上限大於 1 TB (使用的值為 4 TB 或 4096 GB)，但資料庫佈建在不受支援的區域，create 命令會失敗並產生錯誤。
- 若現有的 P11 和 P15 資料庫位於其中一個受支援區域，則能以 256 GB 為單位，將儲存體上限提高到 1 TB 以上，最多可增加到 4 TB。 若要查看您所在的區域是否支援較大的大小，請使用 [DATABASEPROPERTYEX](/t-sql/functions/databasepropertyex-transact-sql) 函數或在 Azure 入口網站中檢查資料庫大小。 只有伺服器層級的主要登入或 dbmanager 資料庫角色的成員，才能執行現有的 P11 或 P15 資料庫升級。 
- 如果是在支援的區域中執行升級作業，則系統會立即更新組態。 在升級過程中，資料庫會保持離線狀態。 但是，在將實際的資料庫檔案升級至新的大小上限前，您無法完整地使用超過 1 TB 的儲存體。 所需的時間長短依進行升級的資料庫大小而定。 
- 建立或更新 P11 或 P15 資料庫時，您只能以 256 GB 為增加單位，在 1 TB 和 4 TB 的大小上限之間選擇。 在建立 P11/P15 時，系統會預先選取預設的 1 TB 儲存體選項。 若資料庫位於其中一個受支援區域，您可以將新的或現有單一資料庫的儲存體上限增加為 4 TB。 在其他所有區域中，無法將大小上限增加至 1 TB 以上。 當您選取 4 TB 的內含儲存體時，價格不會變更。
- 如果資料庫的大小上限設為大於 1 TB，即使使用的實際儲存體未達 1 TB，也無法變更為 1 TB。 因此，您無法將大小上限大於 1 TB 的 P11 或 P15 降級至 1 TB P11 或 1 TB P15，也無法降至較低的效能層，例如 P1-P6。 這項限制也適用於包括時間點、異地還原、長期備份保留期和資料庫備份等還原和複製案例。 一旦將資料庫的大小上限設定為 1 TB 以上，此資料庫的所有還原作業都必須以大於 1 TB 大小上限的 P11/P15 執行。
- 若是作用中異地複寫案例：
   - 設定異地複寫關聯性：如果 P11 或 P15 為主要資料庫，則次要資料庫也必須是 P11 或 P15；系統會拒絕使用較低的效能層級做為次要資料庫，因為這些資料庫無法支援 1 TB 以上的大小。
   - 在異地複寫關聯性中升級主要資料庫：將主要資料庫的大小上限變更為 1 TB 以上會在次要資料庫中觸發相同的變更。 這兩種升級方式都必須成功，在主要資料庫中的變更才會生效。 適用於大於 1-TB 選項的區域限制。 如果次要資料庫位於不支援大於 1 TB 的區域，就無法升級主要資料庫。
- 不支援使用匯入/匯出服務載入具有 1 TB 以上之 P11/P15 資料庫的功能。 使用 SqlPackage.exe 來[匯入](sql-database-import.md)和[匯出](sql-database-export.md)資料。

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>彈性集區：儲存體大小與效能層級

對於 SQL Database 彈性集區，下表顯示了每個服務層和效能層級的可用資源。 您可以使用 [Azure 入口網站](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell)、[Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) 或 [REST API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api)，設定服務層、效能層級和儲存體數量。

> [!NOTE]
> 根據 DTU 和服務層，彈性集區中個別資料庫的資源限制通常與集區外部之單一資料庫的資源限制相同。 例如，S2 資料庫的並行背景工作數上限是 120 個背景工作。 因此，如果集區中每個資料庫的最大 DTU 是 50 DTU (這相當於 S2)，標準集區中的資料庫最大並行背景工作數上限也會是 120 個背景工作。
>

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

如果彈性集區的所有 DTU 均已使用，則集區中的每個資料庫會收到等量的資源以處理查詢。 SQL Database 服務藉由確保運算時間的均等配量，提供資料庫之間的資源共用公平性。 彈性集區資源共用公平性不包括任何資源數量，否則當每個資料庫的最小 DTU 數設為非零的值時，便會對每個資料庫保證資源數量。

### <a name="database-properties-for-pooled-databases"></a>集區資料庫的資料庫屬性

下表描述集區資料表的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 每資料庫的 eDTU 上限 |集區中任何資料庫可以使用的 eDTU 數目上限，是否可用則是根據集區中其他資料庫的使用量而定。 每個資料庫的 eDTU 數目上限不等於資料庫的資源保證。 這個設定是全域設定，會套用至集區中的所有資料庫。 將每個資料庫的 eDTU 設定為最上限，以處理資料庫使用率的尖峰。 某種程度的過量使用是可預期的情況，因為集區通常會假設資料庫的熱門和冷門使用模式；在這些模式中，所有資料庫不會同時處於尖峰期。 例如，假設每個資料庫的尖峰使用量是 20 個 DTU，且集區中的 100 個資料庫只有 20% 會同時暴增到尖峰。 如果每一資料庫的 eDTU 上限設為 20 個 eDTU，則以 5 倍的量過量使用集區，並將每集區 eDTU 設為 400 個是合理的作法。 |
| 每資料庫的 eDTU 下限 |集區中單一資料庫能夠保證的最小 eDTU 數。 這個設定是全域設定，會套用至集區中的所有資料庫。 每個資料庫最小 eDTU 建議設定為 0，同時也是預設值。 此屬性會設為 0 到每一資料庫的 eDTU 使用量平均值之間的任意數。 集區中資料庫數目和每個資料庫 eDTU 數目下限的乘積不能超過每個集區的 eDTU。 例如，如果集區有 20 個資料庫，且每個資料庫的最小 eDTU 設定為 10 eDTU，則每個集區 eDTU 必須至少為 200 個 eDTU。 |
| 每個資料庫的儲存體上限 |集區中資料庫的儲存體上限。 集區資料庫共用集區儲存體，所以資料庫儲存體的大小會限制為較小的剩餘集區儲存體，以及每資料庫儲存體的上限。 每個資料庫的儲存體上限是指資料檔案的大小上限，並不包含記錄檔所使用的空間。 |
|||
 
## <a name="elastic-pool-change-storage-size"></a>彈性集區：變更儲存體大小

- 彈性集區的 eDTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量和大小上限，請參閱[彈性集區：儲存體大小與效能層級](#elastic-pool-storage-sizes-and-performance-levels)。
- 可藉由使用 [Azure 入口網站](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#update) 或 [REST API](/rest/api/sql/elasticpools#Update) 增加其大小上限，以佈建彈性集區的額外儲存體。
- 彈性集區之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="elastic-pool-change-edtus"></a>彈性集區：變更 eDTU

您可以使用 [Azure 入口網站](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#update) 或 [REST API](/rest/api/sql/elasticpools#Update)，根據資源需求來增加或減少彈性集區可用的資源。

- 重新調整集區 eDTU 時，會短暫中斷資料庫連線。 在為單一資料庫 (而非在集區中) 重新調整 DTU 時，也會發生相同的行為。 如需重新調整作業期間，資料庫中斷連線的持續時間和影響的詳細資訊，請參閱[重新調整單一資料庫的 DTU](#single-database-change-storage-size)。 
- 重新調整集區 eDTU 的持續時間可能取決於集區中所有資料庫使用的儲存空間總量。 一般情況下，重新調整延遲時間平均為每 100 GB 在 90 分鐘以內。 舉例來說，如果集區中所有資料庫使用的總空間為 200 GB，則重新調整集區的預期延遲時間為 3 小時內。 在標準層或基本層內的某些情況下，不論使用的空間量多寡，重新調整延遲時間可能會少於五分鐘。
- 一般而言，變更每個資料庫之 eDTU 下限或每個資料庫的 eDTU 上限的持續時間會在五分鐘內。
- 縮減集區 eDTU 的大小時，集區使用的空間必須小於目標服務層和集區 eDTU 允許的大小上限。
- 調整集區 eDTU 時，如果發生下列情況，會產生額外的儲存體成本：(1) 目標集區可支援集區的儲存體大小上限，而且 (2) 儲存體大小上限超過目標集區的內含儲存體數量。 例如，如果大小上限為 100 GB 的 100 eDTU 標準集區縮減為 50 eDTU 標準集區，則會產生額外的儲存體成本，因為目標集區可支援 100 GB 的大小上限，而且其內含儲存體數量只有 50 GB。 因此，額外的儲存體數量為 100 GB – 50 GB = 50 GB。 如需額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。 

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>達到資料庫和彈性集區資源限制時，會發生什麼事？

### <a name="compute-dtus-and-edtus"></a>計算 (DTU 和 eDTU)

當資料庫計算使用率 (根據 DTU 和 eDTU 測量) 變高時，查詢延遲會增加，甚至可能逾時。在下列情況下，查詢可能會透過服務排入佇列，並在資源變成可用時，提供資源以供執行。
遇到高計算使用率時，緩和選項包括：

- 提高資料庫或彈性集區的效能層級，提供更多 DTU 或 eDTU 給資料庫。 請參閱[單一資料庫：變更 DTU](#single-database-change-dtus) 與[彈性集區：變更 eDTU](#elastic-pool-change-edtus)。
- 將查詢最佳化，以降低每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>儲存體

當使用的資料庫空間達到大小上限，若資料庫的插入和更新作業會增加資料大小，動作即會失敗，且用戶端會收到[錯誤訊息](sql-database-develop-error-messages.md)。 資料庫 SELECTS 與 DELETES 會繼續執行下去。

遇到高空間使用率時，緩和選項包括：

- 增加資料庫或彈性集區的大小上限，或變更效能層級，以取得更多的內含儲存體。 請參閱 [SQL Database 資源限制](sql-database-resource-limits.md)。
- 如果資料庫在彈性集區，也可以將資料庫移出集區，如此便不會與其他資料庫共用儲存空間。

### <a name="sessions-and-workers-requests"></a>工作階段和背景工作角色 (要求) 

並行工作階段與背景工作角色的數量上限取決於服務層和效能層級 (DTU 與 eDTU)。  達到工作階段或背景工作角色的限制時，會拒絕新要求，而且用戶端會收到錯誤訊息。 雖然應用程式能控制可用的連線數目，但並行背景工作角色的數目通常難以預估及控制。 特別是在尖峰負載期間，資料庫資源達到上限，背景工作角色也因為長時間執行查詢而不斷累積。 

當工作階段或背景工作角色出現高使用率時，緩和選項包括：
- 提高資料庫或彈性集區的服務層或效能層級。 請參閱[單一資料庫：變更儲存體大小](#single-database-change-storage-size)、[單一資料庫：變更 DTU](#single-database-change-dtus)、[彈性集區：變更儲存體大小](#elastic-pool-change-storage-size)，以及[彈性集區：變更 eDTU](#elastic-pool-change-edtus)。
- 如果背景工作角色的使用率增加是爭用計算資源所造成，則可將查詢最佳化，以減少每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

## <a name="next-steps"></a>後續步驟

- 如需服務層的相關資訊，請參閱[服務層](sql-database-service-tiers.md)。
- 如需單一資料庫的相關資訊，請參閱[單一資料庫資源](sql-database-resource-limits.md)。
- 如需彈性集區的相關資訊，請參閱[彈性集區](sql-database-elastic-pool.md)。
- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
- 如需 DTU 與 eDTU 的相關資訊，請參閱 [DTU 與 eDTU](sql-database-what-is-a-dtu.md)。

