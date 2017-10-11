---
title: "Azure SQL Database 常見問題集 | Microsoft Docs"
description: "客戶詢問有關雲端資料庫與 Azure SQL Database、Microsoft 的關聯式資料庫管理系統 (RDBMS) 以及資料庫作為雲端中服務的一般問題的解答。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 1da12abc-0646-43ba-b564-e3b049a6487f
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/07/2017
ms.author: sashan;carlrab
ms.openlocfilehash: 6ed02ead07c50b9a49e8868756b6f957d7b49b99
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="sql-database-faq"></a>SQL Database 常見問題集

## <a name="what-is-the-current-version-of-sql-database"></a>最新的 SQL Database 版本為何？
最新的 SQL Database 版本是 V12。 版本 V11 已被淘汰。

## <a name="what-is-the-sla-for-sql-database"></a>SQL Database 的 SLA 是什麼？
我們保證客戶在基本層、標準層或進階層中之單一或彈性 Microsoft Azure SQL Database 可與我們網際網路閘道正常連線的時間，至少須達 99.99%。 如需詳細資訊，請參閱 [SLA](http://azure.microsoft.com/support/legal/sla/)。

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>如何重設伺服器系統管理員的密碼？
在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Server]、從清單中選取伺服器，然後按一下 [重設密碼]。

## <a name="how-do-i-manage-databases-and-logins"></a>如何管理資料庫與登入？
請參閱[管理資料庫與登入](sql-database-manage-logins.md)。

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>如何確保只有授權的 IP 位址可以存取伺服器？
請參閱 [如何：在 SQL Database 上進行防火牆設定](sql-database-configure-firewall-settings.md)。

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>我的帳單上會用什麼方式顯示 SQL Database 的使用量？
SQL Database 是以可預測的每小時費率收費，同時根據服務層 + 單一資料庫的效能等級或每一彈性集區的 eDTU。 實際使用量會每小時依比例分配的方式計算，因此有可能會出現不滿一小時的帳單。 例如，如果資料庫在一個月內的存在時間是 12 個小時，則您的帳單會顯示 0.5 天的使用量。 此外，服務層 + 效能等級和每一集區 eDTU 會在帳單中劃分，以讓您方便看到在單一月份中每個所使用的資料庫天數。

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>如果單一資料庫作用中的時間少於一小時，或使用更高服務層的時間少於一小時，會發生什麼情況？
您需要支付使用最高服務層資料庫存在的時數 + 在該小時適用的效能等級，不論使用方式或資料庫是否在作用中少於一小時。 例如，假設您建立了單一資料庫並在五分鐘後刪除，您的帳單就會反映一個資料庫時數的費用。 

範例

* 如果您建立 Basic 資料庫，並立即將它升級到 Standard S1，則我們會以 Standard S1 的費率向您收取第一個小時的費用。
* 如果您在下午 10:00 將資料庫從「基本」升級為「進階」， 在隔天上午 1:35 完成升級， 則會從上午 1:00 開始「進階」費率計費。 
* 如果您在上午 11:00 將資料庫從「進階」降級為「基本」， 且該作業在下午 2:15 完成，則會針對資料庫以「進階」費率收費到下午 3:00，之後便以「基本」費率收費。

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>彈性集區使用量如何在我的帳單上顯示，以及變更每個集區的 eDTU 時會發生什麼？
在您的帳單上，彈性集區收費顯示為彈性 DTU (eDTU)，並在[價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)上以每一集區 eDTU 遞增顯示。 彈性集區沒有每一資料庫的費用。 對集區存在的每個小時，您均需要支付最高的 eDTU，不論使用方式或集區是否作用中少於一小時。 

範例

* 如果您在上午 11:18 以 200 個 eDTU 建立「標準」彈性集區，將五個資料庫加入至集區，則會向您收取該小時 200 個 eDTU 的費用，從上午 11:00 開始。 到當天剩餘的時間。
* 在第 2 天上午 5:05，資料庫 1 開始取用 50 eDTU 並穩定持有一天。 資料庫 2-5 會在 0 和 80 eDTU 之間波動。 在當天，您加入全天取用不同 eDTU 的其他五個資料庫。 第 2 天是全天，以 200 eDTU 計費。 
* 在第 3 天上午 5:00， 您加入另外 15 個資料庫。 資料庫使用量全天增加，到下午 8:05 您決定將集區的 eDTU 從 200 增加為 400。 以 200 個 eDTU 等級收費的有效期間是到下午 8:00，並針對其餘的四小時增加至 400 個 eDTU。 

## <a name="elastic-pool-billing-and-pricing-information"></a>彈性集區的計費和定價資訊
彈性集區會依據下列特性計費：

* 彈性集區在建立時就會開始計費，即使集區中沒有資料庫也一樣。
* 彈性集區會以每小時計費。 這和單一資料庫效能等級的計量頻率相同。
* 如果彈性集區調整為新的 eDTU 數目，則在調整作業完成之前，不會根據新的 eDTU 量來計算集區費用。 這會遵循與變更單一資料庫的效能等級相同的模式。
* 彈性集區的價格是以集區的 eDTU 數為計算基礎。 彈性集區的價格與其中之彈性資料庫的數目和使用量無關。
* 價格的計算方式為 (集區的 eDTU 數) x (每 eDTU 的單價)。

在同一個服務層中，彈性集區的 eDTU 單價大於單一資料庫的 DTU 單價。 如需詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 

若要了解 eDTU 及服務層，請參閱 [SQL Database 選項和效能](sql-database-service-tiers.md)。

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>在彈性集區中使用作用中異地複寫會在我的帳單上如何顯示？
與單一資料庫不同，搭配彈性資料庫使用[作用中異地複寫](sql-database-geo-replication-overview.md)對計費並沒有直接的影響。  您只需支付對每個集區佈建的 eDTU (主要集區和次要集區)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>使用稽核功能會如何影響帳單？
稽核內建在 SQL Database 服務供免費使用，且基本、標準、進階和進階 RS 資料庫皆可使用。 但是，為了儲存稽核記錄檔，稽核功能會使用 Azure 儲存體帳戶，而 Azure 儲存體的費率資料表和佇列 會根據稽核記錄檔的大小套用。

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>我如何找到單一資料庫和彈性集區的正確服務層和效能等級？
有幾個工具可供您使用。 

* 針對內部部署資料庫，請使用 [DTU 大小建議器](http://dtucalculator.azurewebsites.net/) 來建議需要的資料庫和 DTU，並針對彈性集區評估多個資料庫。
* 如果單一資料庫會因集區而受益，Azure 的智慧型引擎如果發現必要的歷程使用模式，就會建議彈性集區。 請參閱[使用 Azure 入口網站監視和管理彈性集區](sql-database-elastic-pool-manage-portal.md)。 如需有關如何自己進行數學計算的詳細資訊，請參閱[彈性集區的價格和效能考量](sql-database-elastic-pool.md)
* 若要查看您是否需要向上或向下調整單一資料庫，請參閱 [單一資料庫的效能指引](sql-database-performance-guidance.md)。

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>變更單一資料庫的服務層或效能等級可以多久進行一次？
您可以隨意且不限次數地，變更服務層 (在基本、標準、進階和進階 RS 之間) 或服務層內的效能等級 (例如 S1 到 S2)。 若為較早版本的資料庫，在 24 小時內，您總計只可以變更服務層或效能層級四次。

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>調整每一集區 eDTU 頻率為何？
視您所需，不限次數。

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>變更單一資料庫的服務層次或效能等級，或將資料庫移入和移出彈性集區需要多久的時間？
變更資料庫的服務層和移入和移出集區需要在平台上複製資料庫做為背景作業。 視資料庫的大小而定，變更服務層可能需要數分鐘到數小時的時間不等。 在這兩種情況下，在移動期間，資料庫將維持完全連線且可供使用的狀態。 如需變更單一資料庫的詳細資訊，請參閱 [變更資料庫的服務層](sql-database-service-tiers.md)。 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>何時應該使用單一資料庫與彈性資料庫？
一般而言，彈性集區是專為一般 [軟體即服務 (SaaS) 應用程式模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)而設計，其中每一客戶或租用戶會有一個資料庫。 購買個別資料庫並為了符合每個資料庫的變動尖峰需求而進行超規空間管理，通常不具成本效益。 利用集區，您可管理集區的集體效能，而資料庫會自動相應增加和相應減少。 Azure 的智慧型引擎如果發現必要的使用模式，就會對資料庫建議集區。 如需詳細資訊，請參閱[彈性集區指引](sql-database-elastic-pool.md)。

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>備份儲存體可高達 200% 的最大可佈建資料庫儲存體，這是什麼意思？
備份儲存體是與自動資料庫備份相關聯的儲存體，可用於[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)和[異地還原](sql-database-recovery-using-backups.md#geo-restore)。 Microsoft Azure SQL Database 提供的備份儲存體可高達 200% 的最大可佈建資料庫儲存體，且不須支付額外費用。 例如，如果您擁有可佈建 DB 大小為 250 GB 的標準 DB 執行個體，您就能免費獲得 500 GB 的備份儲存體。 若您的資料庫超過所提供的備份儲存體，您可以連絡 Azure 支援部門，選擇縮短保留期限，或是以標準讀取存取地理備援儲存體 (RA-GRS) 費率，另購額外的備份儲存體。 如需 RA-GRS 計費的詳細資訊，請參閱儲存體價格詳細資料。

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>我正從 Web/Business 移到新的服務層，我應該知道什麼？
Azure SQL Web 和 Business 資料庫現已淘汰。 基本、標準、進階、進階 RS 和彈性層取代淘汰 Web 和商務資料庫。 

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>在兩個 Azure 地理位置相同的區域之間，針對資料庫進行異地複寫時，預期的複寫延遲是多久？
我們目前支援五秒的 RPO，而且當地區次要資料庫裝載於 Azure 建議的配對區域中且位於同一服務層，複寫延遲便會小於五秒。

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>若地區次要資料庫建立於主要資料庫相同的區域中，預期的複寫延遲是多少？
根據實證資料，若使用 Azure 建議配對區域，區域內部和區域之間的複寫延遲不會有太大差異。 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>如果兩個區域之間網路故障，在已設定異地複寫的情況下，重試邏輯如何運作？
如果中斷連線，我們會每隔 10 秒重試，以便重新建立連線。

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>我該如何保證主要資料庫上的重大變更能夠確實複寫？
地區次要資料庫為非同步複本，我們不會嘗試將該資料庫與主要資料庫維持完整同步。 但是，我們會提供方法來強制進行同步處理，以確保重大變更 (例如，密碼更新) 的複寫。 強制進行同步處理會對效能產生影響，因為它會封鎖呼叫執行緒，直到所有認可的交易都完成複寫為止。 如需詳細資訊，請參閱 [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx)。 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>若要監視主要資料庫和地區資料庫之間的複寫延遲，有哪些工具可以使用？
我們會透過 DMV 將主要資料庫和地區次要資料庫之間的即時複寫延遲公開。 如需詳細資訊，請參閱 [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx)。

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>將資料庫移到相同訂用帳戶中的不同伺服器
* 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Database]、從清單中選取資料庫，然後按一下 [複製]。 如需詳細資訊，請參閱 [複製 Azure SQL Database](sql-database-copy.md) 。

## <a name="to-move-a-database-between-subscriptions"></a>在訂用帳戶之間移動資料庫
* 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Server]，然後從清單中選取裝載您資料庫的伺服器。 按一下 [移動] ，然後挑選要移動的資源以及要移入的訂用帳戶。
