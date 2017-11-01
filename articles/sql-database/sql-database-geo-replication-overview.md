---
title: "容錯移轉群組和主動式異地複寫 - Azure SQL Database | Microsoft Docs"
description: "使用具有作用中異地複寫功能的自動容錯移轉群組，並在發生中斷時啟用自動容錯移轉。"
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 2a29f657-82fb-4283-9a83-e14a144bfd93
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 10/11/2017
ms.author: sashan
ms.openlocfilehash: 0b424e2b260ec527f33cdbfe49d1d981b14edfda
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="overview-failover-groups-and-active-geo-replication"></a>概觀︰容錯移轉群組和主動式異地複寫
主動式異地複寫可讓您在相同或不同資料中心位置 (區域) 中設定最多 4 個可讀取的次要資料庫。 在資料中心中斷或在無法連線至主要資料庫的情況下，便可使用次要資料庫進行查詢和容錯移轉。 容錯移轉必須由使用者的應用程式手動起始。 容錯移轉之後，新的主要資料庫會有不同的連接端點。 

> [!NOTE]
> 主動式異地複寫可供所有區域之所有服務層中的所有資料庫使用。
>  

Azure SQL Database 的自動容錯移轉群組 (預覽版) 是一項 SQL Database 功能，其設計目的是要自動管理異地複寫關聯性、連線能力和大規模容錯移轉。 客戶若使用此功能，就能夠在導致主要區域中完全或部分喪失 SQL Database 服務可用性的嚴重區域失敗或其他非計劃事件之後，自動復原次要地區中的多個相關資料庫。 此外，他們可以使用可讀取次要資料庫來卸載唯讀工作負載。  因為自動容錯移轉群組牽涉到多個資料庫，所以必須在主要伺服器上進行設定。 主要和次要伺服器都必須位於相同的訂用帳戶。 自動容錯移轉群組支援將群組中的所有資料庫都只複寫到不同區域的一部次要伺服器。 主動式異地複寫在未搭配自動容錯移轉群組時，於任何區域中最多可以有四個次要資料庫。

如果您使用主動式異地複寫，而且主要資料庫因為任何原因而失敗，或者只是需要離線，您可以起始容錯移轉至任何次要資料庫。 容錯移轉至其中一個次要資料庫啟動時，所有其他次要複本會自動連結至新的主要複本。 如果您使用自動容錯移轉群組 (預覽版) 來管理資料庫復原，則影響群組中一或多個資料庫的任何中斷都會造自動容錯移轉。 您可以設定最符合您應用程式需求的自動容錯移轉原則，或者您可以選擇不設定而使用手動啟動。 此外，自動容錯移轉群組 (預覽版) 還提供在容錯移轉期間仍保持不變的讀寫和唯讀接聽程式端點。 無論您使用手動或自動啟動容錯移轉，容錯移轉都會將群組中所有次要資料庫切換到主要資料庫。 資料庫容錯移轉完成後，DNS 記錄會自動更新以將端點重新導向至新的區域。

您可以使用主動式異地複寫管理伺服器上或彈性集區中個別資料庫或一組資料庫的複寫和容錯移轉。 您可以使用 

- [Azure 入口網站](sql-database-geo-replication-portal.md)
- [PowerShell：單一資料庫](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell：彈性集區](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [PowerShell：容錯移轉群組](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Transact-SQL：單一資料庫或彈性集區](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API：單一資料庫](/rest/api/sql/replicationlinks/failover)
- [REST API：容錯移轉群組](/rest/api/sql/failovergroups/failover) 
 
容錯移轉之後，請確認已在新的主要資料庫上設定伺服器和資料庫的驗證需求。 如需詳細資訊，請參閱 [災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。 這適用於主動式異地複寫和自動容錯移轉群組 (預覽版) 兩者。

主動式異地複寫會利用 SQL Server 的 [Always On (英文)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) 技術，使用讀取認可快照隔離 (RCSI) 以非同步方式將主要資料庫上認可的交易複寫到次要資料庫。 自動容錯移轉群組在主動式異地複寫之上提供群組語意，但使用相同的非同步複寫機制。 雖然次要資料可能會在任何指定時間點稍微落後主要資料庫，但是次要資料保證絕對不會含有部分交易。 跨區域備援可讓應用程式從天然災害、災難性人為錯誤或惡意行為所造成的全部或部分資料中心永久遺失快速復原。 在 [商務持續性概觀](sql-database-business-continuity.md)中可以找到特定的 RPO 資料。

下圖顯示以美國中北部區域的主要資料庫和美國中南部區域的次要資料庫所設定的作用中異地複寫範例。

![異地複寫關聯性](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

因為次要資料庫可讀取，所以可用來卸載唯讀工作負載，例如報告作業。 如果您使用主動式異地複寫，可以在具有主要資料庫的相同區域中建立次要資料庫，但這樣不會增加應用程式發生嚴重失敗時的恢復能力。 如果您使用自動容錯移轉群組 (預覽版)，則一律要在不同區域中建立次要資料庫。

除了災害復原之外，主動式異地複寫還可用於下列案例︰

* **資料庫移轉**：您可以使用主動式異地複寫，以最少的停機時間將資料庫從一部伺服器移轉到另一個線上伺服器。
* **應用程式升級**：您可以在應用程式升級期間建立額外的次要資料庫做為容錯回復複本。

若要達到真正的業務持續性，新增資料中心之間的資料庫備援只是解決方案的一部分。 在災難性失敗後要端對端復原應用程式 (服務) 需要復原構成服務的所有元件和任何相依的服務。 這些元件的範例包括用戶端軟體 (例如自訂 JavaScript 的瀏覽器)、web 前端、儲存體和 DNS。 所有元件都必須對相同的失敗具有恢復功能，並且在應用程式的復原時間目標 (RTO) 內可供使用。 因此，您需要識別所有相依服務並了解其提供的保證與功能。 然後，您必須採取適當步驟以確保服務功能在它所依賴的服務容錯移轉期間都正常。 如需有關設計災害復原解決方案的詳細資訊，請參閱[使用主動式異地複寫設計災害復原的雲端解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="active-geo-replication-capabilities"></a>主動式異地複寫功能
主動式異地複寫功能提供下列基本功能：
* **自動非同步複寫**︰您只能藉由加入至現有資料庫來建立次要資料庫。 您可以在任何 Azure SQL Database 伺服器建立次要資料庫。 一旦建立之後，次要資料庫就要填入從主要資料庫複製的資料。 這個程序稱為植入。 建立並植入次要資料庫之後，主要資料庫的更新會以非同步方式自動複製到次要資料庫。 非同步複寫表示交易會先在主要資料庫上受到認可，才會複製到次要資料庫。 
* **可讀取的次要資料庫**：應用程式可以存取次要資料庫，使用用於存取主要資料庫的相同安全性主體或不同安全性主體進行唯讀作業。 在快照集隔離模式中執行次要資料庫，以確保主要資料庫更新的複寫 (記錄重播) 不會被次要資料庫上執行的查詢延遲。

   > [!NOTE]
   > 如果主要資料庫上有結構描述更新，次要資料庫上的記錄重播會延遲， 因為後者需要次要資料庫上的結構描述鎖定。 
   > 

* **多個可讀取次要資料庫**：兩個或以上的次要資料庫可增加主要資料庫和應用程式的備援和保護層級。 如果有多個次要資料庫存在，即使其中一個次要資料庫失敗，應用程式仍會受到保護。 如果只有一個次要資料庫卻失敗了，應用程式會暴露在更高的風險中，直到建立新的次要資料庫。

   > [!NOTE]
   > 如果您使用主動式異地複寫建置分散在世界各地的應用程式，而且必須在四個以上的區域中提供唯讀存取資料，您可以建立次要資料庫的次要資料庫 (稱為鏈結的程序)。 如此一來您就可以達到幾乎無限擴充的資料庫複寫。 此外，鏈結可減少來自主要資料庫的複寫額外負荷。 缺點是會增加分葉最尾端之次要資料庫上的複寫延遲。 
   >

* **支援彈性集區資料庫**：您可以為任何彈性集區中的任何資料庫設定主動式異地複寫。 次要資料庫可以在其他彈性集區中。 對於一般資料庫，只要服務層相同，次要資料庫可以是彈性集區，反之亦然。 
* **次要資料庫的可設定效能層級**：主要和次要資料庫皆必須有相同的服務層 (基本、標準、進階)。 可以使用比主要資料庫低的效能層級 (DTU) 建立次要資料庫。 對於具有高資料庫寫入活動的應用程式不建議使用這個選項，因為增加的複寫延遲會提高容錯移轉後遺失重要資料的風險。 此外，在容錯移轉之後應用程式的效能會受到影響，直到新的主要資料庫升級至較高的效能層級。 在 Azure 入口網站上的記錄 IO 百分比圖表，提供預估次要資料庫承受複寫負載所需的最少效能層級的好方法。 例如，如果您的主要資料庫是 P6 (1000 DTU) 和其記錄 IO 百分比為 50%，則次要資料庫必須至少是 P4 (500 DTU)。 您也可以使用 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 或 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 資料庫檢視來擷取記錄 IO 資料。  如需 SQL Database 效能層級的詳細資訊，請參閱 [SQL Database 選項和效能](sql-database-service-tiers.md)。 
* **使用者控制的容錯移轉和容錯回復**：應用程式或使用者可以隨時明確也將次要資料庫切換到主要角色。 在實際的中斷期間，應該使用「非計劃性」選項，這樣會立即將次要升級為主要。 當失敗的主要資料庫復原，並且可再次使用時，系統會自動將復原的主要資料庫標示為次要資料庫，並讓它與新的主要資料庫保持更新。 由於複寫的非同步本質，如果主要資料庫在將最新的變更複寫至次要資料庫之前失敗，則非計劃的容錯移轉期間會有少量的資料遺失。 當具有多個次要資料庫的主要資料庫容錯移轉時，系統會自動重新設定複寫關聯性，並且將剩餘的次要資料庫連結至新升級的主要資料庫，而不需要任何使用者介入。 解決造成容錯移轉的中斷之後，可能想要讓應用程式返回主要區域。 若要這麼做，應該使用「計劃」選項叫用容錯移轉命令。 
* **保持認證和防火牆規則同步**︰我們建議針對異地複寫資料庫使用 [資料庫防火牆規則](sql-database-firewall-configure.md) ，這樣一來，這些規則可以使用資料庫複寫，以確保所有次要資料庫具有與主要資料庫相同的防火牆規則。 此方法不需要客戶手動設定及維護同時裝載主要和次要資料庫之伺服器上的防火牆規則。 同樣地，針對資料存取使用 [自主資料庫使用者](sql-database-manage-logins.md) ，確保主要與次要資料庫永遠具有相同的使用者認證，在容錯移轉時不會因為登入和密碼不相符而有任何中斷。 使用額外的 [Azure Active Directory](../active-directory/active-directory-whatis.md)，客戶可以管理主要和次要資料庫的使用者存取，並且排除在資料庫中管理認證的需求。

## <a name="auto-failover-group-capabilities"></a>自動容錯移轉群組功能

自動容錯移轉群組功能支援群組層級的複寫和自動容錯移轉，提供強大的主動式異地複寫抽象概念。 此外，它提供額外的接聽程式端點，不需要在容錯移轉之後變更 SQL 連接字串。 

* **容錯移轉群組**︰可以在不同區域 (主要和次要伺服器) 的兩部伺服器之間建立一或多個容錯移轉群組。 每個群組可包含一或多個作為復原單位的資料庫，以防所有或部分的主要資料庫因為主要區域服務中斷而變成無法使用。  
* **主要伺服器**︰容錯移轉群組中裝載主要資料庫的伺服器。
* **次要伺服器**︰容錯移轉群組中裝載次要資料庫的伺服器。 次要伺服器和主要伺服器不能位於相同區域。
* **在容錯移轉群組中新增資料庫**︰您可以將伺服器或彈性集區內的多個資料庫放在同一個容錯移轉群組中。 如果您在群組中新增獨立的資料庫，它會使用相同的版本和效能層級自動建立次要資料庫。 如果主要資料庫在彈性集區中，則會使用相同名稱在該彈性集區中建立次要資料庫。 如果您新增在次要伺服器中已經有次要資料庫的資料庫，群組就會繼承該異地複寫。

   > [!NOTE]
   > 新增在伺服器中已經有次要資料庫但不屬於容錯移轉群組一部分的資料庫時，會在次要伺服器中建立新的次要資料庫。 
   >

* **容錯移轉群組讀寫接聽程式**︰指向目前主要伺服器 URL 且格式為 **&lt;容錯移轉群組名稱&gt;.database.windows.net** 的 DNS CNAME 記錄。 它可讓讀寫 SQL 應用程式在容錯移轉之後，當主要資料庫變更時毫無障礙地重新連線至主要資料庫。 
* **容錯移轉群組唯讀接聽程式**︰指向目前次要伺服器 URL 且格式為 **&lt;容錯移轉群組名稱&gt;.secondary.database.windows.net** 的 DNS CNAME 記錄。 它可讓唯讀 SQL 應用程式使用指定的負載平衡規則毫無障礙地連線到次要資料庫。 您可以選擇性指定當次要伺服器無法使用時，是否要將唯讀流量自動重新導向到主要伺服器。
* **自動容錯移轉原則**︰容錯移轉群組預設是利用自動容錯移轉原則設定。 一旦偵測到失敗，系統就會觸發容錯移轉。 如果您想要控制應用程式的容錯移轉工作流程，可以關閉自動容錯移轉。 
* **手動容錯移轉**︰無論自動容錯移轉設定為何，您都可以在任何時候手動起始容錯移轉。 如果未設定自動容錯移轉原則，就需要手動容錯移轉才能復原容錯移轉群組中的資料庫。 您可以起始強制或易用容錯移轉 (具有完整的資料同步處理)。 後者可用來將作用中伺服器重新放置到主要區域。 容錯移轉完成時，DNS 記錄會自動更新以確保能連線到正確的伺服器。
* **資料遺失的寬限期**︰因為主要和次要資料庫是使用非同步複寫進行同步處理，容錯移轉可能會導致資料遺失。 您可以自訂自動容錯移轉原則，以反映應用程式對資料遺失的容錯程度。 設定 **GracePeriodWithDataLossHours**，可以控制系統在起始可能造成資料遺失的容錯移轉之前等候的時間長度。 

   > [!NOTE]
   > 當系統偵測到群組中的資料庫仍保持線上狀態 (例如，中斷只影響了服務控制平面) 時，它會立即啟動具完整資料同步處理的容錯移轉 (易用容錯移轉)，無論 **GracePeriodWithDataLossHours** 所設定的值為何。 此行為可確保復原期間不會遺失任何資料。 寬限期只有在無法使用易用容錯移轉時才有效。 如果中斷情況在寬限期到期之前就趨緩，則不會啟動容錯移轉。
   >

* **多個容錯移轉群組**︰您可以為相同的兩部伺服器設定多個容錯移轉群組來控制容錯移轉的規模。 每個群組分別進行容錯移轉。 如果您的多租用戶應用程式使用彈性集區，可以使用這項功能來混合每個集區中的主要和次要資料庫。 如此一來，可以讓中斷只影響一半的租用戶。

## <a name="best-practices-of-building-highly-available-service"></a>建置高可用性服務的最佳作法

若要建置使用 Azure SQL Database 的高可用性服務，您應遵循下列指導方針：

- **使用容錯移轉群組**︰可以在位於不同區域 (主要和次要伺服器) 的兩部伺服器之間建立一或多個容錯移轉群組。 每個群組可包含一或多個作為復原單位的資料庫，以防所有或部分的主要資料庫因為主要區域服務中斷而變成無法使用。 容錯移轉群組會使用和主要資料庫相同的服務目標，來建立異地次要資料庫。 如果您在容錯移轉群組中新增現有的異地複寫關聯性，請確定地區次要資料庫所設定的服務等級目標和主要資料庫相同。
- **針對 OLTP 工作負載使用讀取寫入接聽程式**：在執行 OLTP 作業時使用 **&lt;容錯移轉群組名稱&gt;.database.windows.net** 做為伺服器 URL，連線會自動導向至主要伺服器。 在容錯移轉之後，不會變更此 URL。  
請注意，容錯移轉牽涉到更新 DNS 記錄，因此只有在重新整理用戶端 DNS 快取之後，才會將用戶端連線重新導向至新的主要伺服器。
- **針對唯讀工作負載使用唯讀接聽程式**：如果您有容忍某些過時資料的邏輯隔離唯讀工作負載，則可以使用應用程式中的次要資料庫。 針對唯讀工作階段，請使用 **&lt;容錯移轉群組名稱&gt;.secondary.database.windows.net** 做為伺服器 URL，連線會自動導向至次要伺服器。 也建議您使用 **ApplicationIntent=ReadOnly**，在連接字串中表示讀取意圖。 
- **對效能降低做好心理準備**：應用程式的其餘部分或所使用的其他服務並不會影響 SQL 的容錯移轉決策。 應用程式可能會「混用」某個區域的某些元件和另一個區域的某些元件。 若要避免效能降低，請務必要在 DR 區域中部署備援應用程式，並遵循本文中的指導方針。  
請注意，DR 區域中的應用程式不一定非得使用不同的連接字串。  
- **對資料遺失做好心理準備**：如果系統偵測到服務中斷，SQL 會在就我們所知並無資料遺失時，自動觸發讀寫容錯移轉。 否則，它會等候您透過 **GracePeriodWithDataLossHours** 所指定的這段時間。 如果您指定了 **GracePeriodWithDataLossHours**，請做好可能會遺失資料的心理準備。 服務中斷期間，Azure 一般會傾向維持可用性。 如果您無法承擔資料遺失情況，請務必將 **GracePeriodWithDataLossHours** 設定為足夠大的數字，例如 24 小時。 


## <a name="upgrading-or-downgrading-a-primary-database"></a>升級或降級主要資料庫
您可以將主要資料庫升級或降級至不同的效能層級 (在相同的服務層內)，而不會中斷連接任何次要資料庫。 升級時，我們建議您先升級次要資料庫，然後再升級主要資料庫。 降級時，順序相反︰先降級主要資料庫，然後再降級次要資料庫。 當您將資料庫升級或降級到不同的服務層時，會強制執行這項建議。 

> [!NOTE]
> 如果您已在容錯移轉群組設定中建立次要資料庫，則不建議降級次要資料庫。 這是為了確保您的資料層在容錯移轉啟動之後有足夠的容量來處理一般工作負載。 
>

## <a name="preventing-the-loss-of-critical-data"></a>防止重要資料遺失
由於廣域網路的高度延遲，連續複製採用非同步複寫機制。 如果發生失敗，非同步複寫導致部分資料遺失是無法避免的。 不過，有些應用程式可能會要求資料不能遺失。 若要保護這些重大更新，應用程式開發人員可以在認可交易後立即呼叫 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 系統程序。 呼叫 **sp_wait_for_database_copy_sync** 會封鎖呼叫執行緒，直到最後認可的交易傳輸到次要資料庫。 不過，它不會等候在次要資料庫上重新執行和認可傳輸的交易。 **sp_wait_for_database_copy_sync** 以特定的連續複製連結為範圍。 任何具備主要資料庫連接權限的使用者都可以呼叫此程序。

> [!NOTE]
> **sp_wait_for_database_copy_sync** 可避免在容錯移轉之後資料遺失，但是不保證讀取權限會完整同步。 **sp_wait_for_database_copy_sync** 程序呼叫所造成的延遲可能會相當明顯，且取決於呼叫時的交易記錄大小。 
> 

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>以程式設計方式管理容錯移轉群組和主動式異地複寫
如前所述，自動容錯移轉群組 (預覽版) 和主動式異地複寫也可以使用 Azure PowerShell 和 REST API，以程式設計的方式管理。 下表描述可用的命令集。

**Azure Resource Manager API 和以角色為基礎的安全性**︰主動式異地複寫包含一組可管理的 Azure Resource Manager API，包括 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 和 [Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/azure/overview)。 這些 API 需要使用資源群組，並支援以角色為基礎的安全性 (RBAC)。 如需如何實作存取角色的詳細資訊，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-what-is.md)。

## <a name="manage-sql-database-failover-using-transact-sql"></a>使用 Transact-SQL 管理 SQL 資料庫容錯移轉

| 命令 | 說明 |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |使用 ADD SECONDARY ON SERVER 引數，針對現有資料庫建立次要資料庫並開始資料複寫 |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |使用 FAILOVER 或 FORCE_FAILOVER_ALLOW_DATA_LOSS，將次要資料庫切換為主要資料庫以便開始容錯移轉 |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |使用 REMOVE SECONDARY ON SERVER，來終止 SQL Database 和指定次要資料庫間的資料複寫。 |
| [sys.geo_replication_links (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |針對 Azure SQL Database 邏輯伺服器上的每個資料庫，傳回所有結束複寫連結的相關資訊。 |
| [sys.dm_geo_replication_link_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |針對指定的 SQL Database，取得上次複寫時間、上次複寫延遲，以及複寫連結的其他相關資訊。 |
| [sys.dm_operation_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |顯示所有資料庫作業的狀態，包括複寫連結的狀態。 |
| [sp_wait_for_database_copy_sync (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |導致應用程式等候，直到作用中次要資料庫複寫並認可所有認可的交易為止。 |
|  | |

## <a name="manage-sql-database-failover-using-powershell"></a>使用 PowerShell 管理 SQL 資料庫容錯移轉

| Cmdlet | 說明 |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |取得一或多個資料庫。 |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |針對現有資料庫建立次要資料庫並開始資料複寫。 |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |將次要資料庫切換為主要資料庫以開始容錯移轉。 |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |終止 SQL Database 和指定次要資料庫間的資料複寫。 |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |取得 Azure SQL Database 和資源群組或 SQL Server 之間的異地複寫連結。 |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| [Remove-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | 從伺服器移除容錯移轉群組，並刪除包含群組的所有次要資料庫 |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | 擷取容錯移轉群組設定 |
| [Set-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   修改容錯移轉群組的設定 |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | 觸發容錯移轉群組的容錯移轉到次要伺服器 |
|  | |

> [!IMPORTANT]
> 如需範例指令碼，請參閱[使用作用中異地複寫設定單一資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)、[使用作用中異地複寫設定集區資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)、[設定單一資料庫的容錯移轉群組並進行容錯移轉 \(預覽\)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)。
>

## <a name="manage-sql-database-failover-using-the-rest-api"></a>使用 REST API 管理 SQL 資料庫容錯移轉
| API | 說明 |
| --- | --- |
| [Create or Update Database (createMode=Restore)](/rest/api/sql/Databases/CreateOrUpdate) |建立、更新或還原主要或次要資料庫。 |
| [取得建立或更新資料庫狀態](/rest/api/sql/Databases/CreateOrUpdate) |在建立作業期間傳回狀態。 |
| [將次要資料庫設定為主要資料庫 (計劃性容錯移轉)](/rest/api/sql/replicationlinks/failover) |從目前主要複本資料庫進行容錯移轉，以設定主要的複本資料庫。 |
| [將次要資料庫設定為主要資料庫 (非計劃的容錯移轉)](/rest/api/sql/replicationlinks/failoverallowdataloss) |從目前主要複本資料庫進行容錯移轉，以設定主要的複本資料庫。 這項作業可能會導致資料遺失。 |
| [取得複寫連結](/rest/api/sql/replicationlinks/get) |取得異地複寫關聯性中指定 SQL Database 的特定複寫連結。 它會擷取 sys.geo_replication_links 目錄檢視中顯示的資訊。 |
| [複寫連結 - 依資料庫列示](/rest/api/sql/replicationlinks/listbydatabase) | 取得異地複寫關聯性中指定 SQL Database 的所有複寫連結。 它會擷取 sys.geo_replication_links 目錄檢視中顯示的資訊。 |
| [刪除複寫連結](/rest/api/sql/databases/delete) | 刪除資料庫複寫連結。 無法在容錯移轉期間進行。 |
| [建立或更新容錯移轉群組](/rest/api/sql/failovergroups/createorupdate) | 建立或更新容錯移轉群組 |
| [刪除容錯移轉群組](/rest/api/sql/failovergroups/delete) | 從伺服器中移除容錯移轉群組 |
| [容錯移轉 (計劃性)](/rest/api/sql/failovergroups/failover) | 從目前主要伺服器容錯移轉到此伺服器。 |
| [強制容錯移轉允許資料遺失](/rest/api/sql/failovergroups/forcefailoverallowdataloss) |從目前主要伺服器容錯移轉到此伺服器。 這項作業可能會導致資料遺失。 |
| [取得容錯移轉群組](/rest/api/sql/failovergroups/get) | 取得容錯移轉群組。 |
| [依伺服器列出容錯移轉群組](/rest/api/sql/failovergroups/listbyserver) | 列出伺服器中的容錯移轉群組。 |
| [更新容錯移轉群組](/rest/api/sql/failovergroups/update) | 更新容錯移轉群組。 |
|  | |

## <a name="next-steps"></a>後續步驟
* 如需範例指令碼，請參閱：
   - [使用作用中異地複寫設定單一資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
   - [使用作用中異地複寫設定集區資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
   - [設定單一資料庫的容錯移轉群組並進行容錯移轉 (預覽)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。
* 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)。
* 若要深入了解新的主要伺服器和資料庫的驗證需求，請參閱 [災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。

