---
title: "使用彈性集區管理多個 SQL Database - Azure | Microsoft Docs"
description: "管理及調整多個 SQL Database - 成百上千 - 使用彈性集區。 可視需要散發的資源只有一個價格。"
keywords: "多個資料庫, 資料庫資源, 資料庫效能"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: NA
ms.date: 08/25/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 2af5be1c2250c3ec363b97c21bc3fa05c8ace984
ms.contentlocale: zh-tw
ms.lasthandoff: 09/26/2017

---

# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>彈性集區可協助您管理及調整多個 Azure SQL Database

SQL Database 彈性集區是簡單、符合成本效益的解決方案，可用來管理及調整使用需求變化不定且無法預測的多個資料庫。 彈性集區中的資料庫位於單一 Azure SQL Database 伺服器上，並以固定價格共用固定數量的資源 ([彈性資料庫交易單位](sql-database-what-is-a-dtu.md) (eDTU))。 Azure SQL Database 中的彈性集區可讓 SaaS 開發人員將一組資料庫的價格效能最佳化在規定的預算內，同時為每個資料庫提供效能彈性。 

> [!NOTE]
> 彈性集區已在所有 Azure 區域中正式運作 (GA)，但印度西部除外，此區域目前提供預覽版。 我們將儘速在此區域提供彈性集區的 GA。
>

## <a name="what-are-sql-elastic-pools"></a>SQL 彈性集區是什麼？ 

SaaS 開發人員會在由多個資料庫組成的大規模資料層上建置應用程式。 常見的應用程式模式是為每個客戶佈建單一資料庫。 但是不同的客戶經常會有不同且無法預測的使用模式，而且很難預測每個個別資料庫使用者的資源需求。 您通常有兩個選項： 

- 根據尖峰使用量額外佈建資源並額外付款，或是
- 少量佈建來節省成本，但會降低尖峰期間的效能和客戶滿意度。 

彈性集區可藉由確保資料庫會在需要時取得它們所需的效能資源，來解決此問題。 它們會在可預測的預算內提供簡單的資源配置機制。 若要深入了解使用彈性集區的 SaaS 應用程式的設計模式，請參閱 [採用 Azure SQL Database 的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

彈性集區可讓開發人員為由多個資料庫共用的集區購買[彈性資料庫交易單位](sql-database-what-is-a-dtu.md) (eDTU)，以容納個別資料庫無法預期的使用量期間。 集區的 eDTU 需求取決於其資料庫的彙總使用量。 集區可用的 eDTU 數目是由開發人員預算控制。 開發人員只要將資料庫加入集區、設定資料庫的最少和最多 eDTU，然後根據其預算設定集區的 eDTU。 開發人員可以使用集區順暢地擴大其服務，以漸增的規模從精簡的新創公司到成熟的企業。

在集區內，會給予個別資料庫彈性以在設定的參數內自動調整。 負載量大時，資料庫可以取用更多的 eDTU 以滿足需求。 負載較輕的資料庫取用較少的 eDTU，而完全無負載的資料庫不會取用任何 eDTU。 針對整個集區佈建資源，而不是針對單一資料庫佈建資源，可簡化管理工作。 此外，您還可以有可預測的集區預算。 資料庫不必停機就可以在現有集區中新增額外的 eDTU，不過可能需要移動資料庫來為新的 eDTU 保留項目提供額外的計算資源。 同樣地，如果不再需要額外 eDTU，則隨時可以從現有集區中移除。 您也可以在集區加入或減少資料庫。 如果可以預測資料庫使用少量資源，則將它移出。

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>何時該考慮使用 SQL Database 彈性集區？

集區很適合具備特定使用模式的大量資料庫。 針對指定的資料庫，此模式的特徵是低平均使用量與相對不頻繁的使用量高峰。

您可以加入集區的資料庫愈多，可獲得的節約就愈高。 根據您的應用程式使用量模式，可能會發現與使用兩個 S3 資料庫一樣少的節約。 

下列各節會協助您了解如何評估您特定的資料庫集合是否可以因為位於集區而受益。 範例會使用標準集區，但是相同的原則也適用於基本和進階的集區。

### <a name="assessing-database-utilization-patterns"></a>評估資料庫使用量模式

下圖顯示資料庫的範例，該資料庫花費太多時間閒置，但也定期因活動達到尖峰。 這是適合集區的使用量模式：

   ![適合某個集區的單一資料庫](./media/sql-database-elastic-pool/one-database.png)

針對上述的五分鐘期間，DB1 尖峰最高達 90 個 DTU，但其整體平均使用量小於 5 個 DTU。 需要 S3 效能層級，才能在單一資料庫中執行此工作負載，但這會在活動較少的期間保留大多數的資源未使用。

集區可讓這些未使用的 DTU 跨多個資料庫共用，並因此減少需要的 DTU 和整體成本。

以上一個範例為建置基礎，假設有其他資料庫具有與 DB1 類似的使用量模式。 在接下來的兩個圖形中，4 個資料庫和 20 個資料庫的使用量分層放在相同的圖形，來說明經過一段時間其使用量非重疊的本質：

   ![使用量模式適合某個集區的 4 個資料庫](./media/sql-database-elastic-pool/four-databases.png)

   ![使用量模式適合某個集區的 20 個資料庫](./media/sql-database-elastic-pool/twenty-databases.png)

在上圖中，黑色線條說明跨所有 20 個資料庫的彙總 DTU 使用量。 這顯示彙總的 DTU 使用量永遠不會超過 100 個 DTU，並指出 20 個資料庫可以在這段期間共用 100 個 eDTU。 相較於將每個資料庫放在單一資料庫的 S3 效能層級，這會導致 DTU 減少 20 倍且價格降低 13 倍。

由於以下原因，此範例很理想：

* 每一資料庫之間的尖峰使用量和平均使用量有相當大的差異。 
* 每個資料庫的尖峰使用量會在不同時間點發生。
* eDTU 會在許多資料庫之間共用。

集區的價格是集區 eDTU 的函式。 雖然集區的 eDTU 單價較單一資料庫的 DTU 單價高 1.5 倍，但是**集區 eDTU 可由多個資料庫共用，而需要的 eDTU 總數會比較少**。 價格方面和 eDTU 共用的這些差異是集區可以提供價格節約潛力的基礎。 

下列資料庫計數和資料庫使用量相關規則的經驗法則，可協助確保集區可提供相較於使用單一資料庫的效能層級降低的成本。

### <a name="minimum-number-of-databases"></a>資料庫的最小數目

如果單一資料庫之效能層級的 DTU 總和大於集區所需 eDTU 的 1.5 倍，則彈性集區會更符合成本效益。 如需可用的大小，請參閱[彈性集區和彈性資料庫的 eDTU 和儲存體限制](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)。

***範例***<br>
100 個 eDTU 集區需要至少 2 個 S3 資料庫或至少 15 個 S0 資料庫，才能較使用單一資料庫的效能層級更具成本效益。

### <a name="maximum-number-of-concurrently-peaking-databases"></a>並行尖峰資料庫的最大數目

藉由共用 eDTU，並非集區中的所有資料庫都能同時使用 eDTU 達到使用單一資料庫的效能層級時的最大限制。 同時尖峰的資料庫愈少，可以設定的集區 eDTU 愈低，集區就能更符合成本效益。 一般而言，集區中應該不能有超過 2/3 (或 67%) 的資料庫同時達到其 eDTU 限制的尖峰。

***範例***<br>
為了降低 200 個 eDTU 集區中 3 個 S3 資料庫的成本，最多可以有 2 個資料庫同時到達其使用量尖峰。 否則，如果 4 個 S3 資料庫中超過 2 個同時尖峰，則必須將集區調整為超過 200 個 eDTU。 如果將集區調整大小為超過 200 個 eDTU，則需要加入更多的 S3 資料庫至集區，以使成本保持低於單一資料庫的效能層級。

請注意，此範例不考慮集區中其他資料庫的使用量。 如果所有資料庫在任何指定的時間點都有一些使用量，則可同時到達尖峰的資料庫會少於 2/3 (或 67%)。

### <a name="dtu-utilization-per-database"></a>每個資料庫的 DTU 使用量
資料庫的尖峰和平均使用量之間的差異為，長時間的低使用量和短時間的高使用量。 這個使用量模式非常適合在資料庫之間共用資源。 若資料庫的尖峰使用量為平均使用量的 1.5 倍大，就應該將該資料庫視為集區。

***範例***<br>
尖峰為 100 個 DTU 且平均使用 67 個 DTU 或更少的 S3 資料庫是在集區中共用 eDTU 的良好候選項目。 或者，尖峰為 20 個 DTU 且平均使用 13 個 DTU 或更少的 S1 資料庫是集區的良好候選項目。

## <a name="how-do-i-choose-the-correct-pool-size"></a>如何選擇正確的集區大小？

集區的最佳大小取決於彙總的 eDTU 和集區中所有資料庫所需的儲存體資源。 這牽涉到決定下列各項的較大值：

* 集區中所有資料庫使用的最大 DTU。
* 集區中所有資料庫使用的最大儲存體位元組。

如需可用的大小，請參閱[彈性集區和彈性資料庫的 eDTU 和儲存體限制](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)。

SQL Database 會自動評估現有 SQL Database 伺服器中資料庫過去的資源使用量，並在 Azure 入口網站中建議適當的集區組態。 除了這些建議之外，內建體驗也會預估伺服器上一組自訂資料庫的 eDTU 使用量。 這可讓您以互動方式將資料庫新增至集區並加以移除，藉此進行「假設」分析，以在認可變更前取得資源使用量分析和大小建議。 如需相關作法，請參閱 [監視、管理和估算彈性集區大小](sql-database-elastic-pool-manage-portal.md)。

在無法使用工具的情況下，下列步驟可協助您預估集區是否比單一資料庫更符合成本效益：

1. 估計集區所需的 eDTU，如下所示：

   最大值(<DB 總數 X 每個 DB 的平均 DTU 使用量>，<br>
   <並行尖峰 DB 的數目** X 每個 DB 的尖峰 DTU 使用量**)
2. 加總集區中所有資料庫所需的位元組數目，以估計集區所需的儲存空間。 然後判斷可提供此儲存體數量的 eDTU 集區大小。 如需以 eDTU 集區大小為基礎的集區儲存體限制，請參閱 [彈性集區和彈性資料庫的 eDTU 和儲存體限制](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)。
3. 採用步驟 1 和步驟 2 中較大的 eDTU 估計值。
4. 請參閱 [SQL Database 價格頁面](https://azure.microsoft.com/pricing/details/sql-database/) 並尋找大於步驟 3 估計值的最小 eDTU 集區大小。
5. 將步驟 5 的集區價格與單一資料庫適當效能層級的價格相比較。

## <a name="using-other-sql-database-features-with-elastic-pools"></a>搭配彈性集區使用其他的 SQL Database 功能

### <a name="elastic-jobs-and-elastic-pools"></a>彈性作業和彈性集區

使用集區，只要在**[彈性作業](sql-database-elastic-jobs-overview.md)**中執行指令碼，就能簡化管理工作。 彈性作業會消除與大量資料庫相關聯的冗長工作。 若要開始，請參閱[開始使用彈性工作](sql-database-elastic-jobs-getting-started.md)。

如需可供使用多個資料庫之其他資料庫工具的詳細資訊，請參閱[使用Azure SQL Database 向上調整](sql-database-elastic-scale-introduction.md)。

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>彈性集區之中的資料庫所適用的業務持續性選項
集區資料庫通常會支援單一資料庫可用的相同[商務持續性功能](sql-database-business-continuity.md)。

- **還原時間點**：還原時間點會使用自動資料庫備份，將集區中的資料庫復原到特定的時間點。 請參閱 [還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)

- **異地還原**：異地還原會在資料庫因裝載區域中的事件而無法使用時，提供預設復原選項。 請參閱 [還原 Azure SQL Database 或容錯移轉到次要資料庫](sql-database-disaster-recovery.md)

- **作用中異地複寫**：針對較異地還原需要更主動復原的應用程式，設定[作用中異地複寫](sql-database-geo-replication-overview.md)。

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>使用 Azure 入口網站管理彈性集區和資料庫

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>使用 Azure 入口網站建立新的 SQL Database 彈性集區

在 Azure 入口網站中建立彈性集區的方式有兩種。 如果您知道您要的集區設定則可從頭開始進行，或從服務的建議著手。 SQL Database 擁有的內建智慧功能會根據您資料庫過去的使用狀況遙測，為您建議更符合成本效益的彈性集區設定。 

要將現有資料庫移到彈性集區中最簡單的方式，是從入口網站中的現有伺服器頁面建立彈性集區。 您也可以在 [Marketplace] 中搜尋 **SQL 彈性集區**，或按一下 [SQL 彈性集區] 頁面上的 [+新增]，以建立彈性集區。 您可以透過此集區佈建工作流程來指定新的或現有的伺服器。

> [!NOTE]
> 您可以在伺服器上建立多個集區，但無法將來自不同伺服器的資料庫新增到相同的集區。
> 

集區的定價層決定了集區中彈性資料庫可用的功能，還有每個資料庫可用的 eDTU 數目上限 (eDTU MAX) 與儲存體 (GB)。 如需詳細資訊，請參閱[彈性集區的資源限制](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)。

若要變更集區的定價層，請依序按一下 [定價層]、您想要的定價層及 [選取]。

> [!IMPORTANT]
> 在最後一個步驟中選擇好定價層並按一下 [確定] 認可變更後，您就無法再變更集區的定價層。 若要變更現有彈性集區的定價層，在所需的定價層中建立一個彈性集區，並將資料庫移轉至這個新的集區。
>

如果您要使用的資料庫有足夠的歷史使用量遙測資料，[預估的 eDTU 和 GB 使用量] 圖形和 [實際的 eDTU 使用量] 長條圖便會更新以幫助您決定要使用的組態。 此外，該服務可能會提供您建議訊息，協助您決定集區的適當大小。

SQL Database 服務會評估使用量的歷史資料，並為您推薦一或多個比使用單一資料庫更符合成本效益的集區。 每個推薦集區都是以最適合該集區的伺服器資料庫唯一子集進行設定。

![建議的集區](./media/sql-database-elastic-pool-create-portal/recommended-pool.png) 

集區建議包含下列內容︰

- 集區的定價層 (基本、標準、進階或進階 RS)
- 適當的 [集區 eDTU]  \(也稱為每一集區的最大 eDTU)
- 每一資料庫的 [eDTU 上限] 和 [eDTU 下限]
- 集區的建議資料庫清單

> [!IMPORTANT]
> 服務在建議集區時，會計算過去 30 天的遙測。 為了讓資料庫被視為彈性集區的候選項目，它必須存在至少 7 天。 已在彈性集區中的資料庫不會被視為彈性集區建議候選項目。
>

服務會評估將每個服務層中的單一資料庫移至同一層集區的資源需求和成本效益。 例如，會評估伺服器上的所有 Standard 資料庫是否適合 Standard 彈性集區。 這表示服務不會進行跨層建議，例如將 Standard 資料庫移到 Premium 集區。

將資料庫新增至集區之後，會根據您所選資料庫的過去使用情況來動態產生建議。 這些建議會顯示在 eDTU 和 GB 使用情況圖表，以及 [設定集區] 頁面頂端的建議橫幅中。 這些建議旨在協助您為特定的資料庫建立最佳化彈性集區。

![動態建議](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

### <a name="manage-and-monitor-an-elastic-pool"></a>管理及監視彈性集區

在 Azure 入口網站中，您可以監視彈性集區與集區內資料庫的使用率。 也可以對彈性集區進行一些變更，並且一次提交所有的變更。 這些變更包括新增或移除資料庫、變更您的彈性集區設定，或變更您的資料庫設定。

下圖顯示彈性集區範例。 此檢視包括︰

* 圖表，可供監視彈性集區和集區中內含資料庫的資源使用量。
* [設定]  集區按鈕，以對彈性集區進行變更。
* [建立資料庫] 按鈕，以建立資料庫並將它加入至目前的彈性集區。
* 彈性工作，可藉由對清單中的所有資料庫執行 Transact SQL 指令碼，協助您管理大量資料庫。

![集區檢視](./media/sql-database-elastic-pool-manage-portal/basic.png)

您可以移至特定集區，以查看其資源使用率。 根據預設，集區已設定為顯示過去 1 小時內的儲存體和 eDTU 使用量。 此圖表可以設定為顯示各種時間範圍的不同度量。 按一下 [彈性集區監視] 下的 [資源使用率] 圖表，以顯示指定時間範圍內的指定計量的詳細畫面。

![彈性集區監視](./media/sql-database-elastic-pool-manage-portal/basic-2.png)

![計量頁面](./media/sql-database-elastic-pool-manage-portal/metric.png)

### <a name="to-customize-the-chart-display"></a>自訂圖表顯示

您可以編輯此圖表和 [計量] 頁面，以顯示其他計量，例如所用的 CPU 百分比、資料 IO 百分比和記錄 IO 百分比。

![按一下 [編輯]。](./media/sql-database-elastic-pool-manage-portal/edit-metric.png)

在 [編輯圖表] 表單上，您可以選取一個時間範圍 (過去 1 小時、今天或上一週)，或按一下 [自訂] 以選取過去兩週內的任何日期範圍。 您可以選擇長條圖或折線圖，然後選取要監視的資源。

> [!Note]
> 圖表中只能同時顯示具有相同測量單位的度量。 例如，如果您選取 [eDTU 百分比]，則只能選取以百分比做為測量單位的其他度量。
>

![按一下 [編輯]。](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

### <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>管理及監視彈性集區中的資料庫

您也可以監視個別資料庫的潛在問題。 在 [彈性資料庫監視] 之下，有一個圖表可顯示五個資料庫的度量。 根據預設，此圖表會依過去一小時內的平均 eDTU 使用量顯示集區中的前 5 個資料庫。 

![彈性集區監視](./media/sql-database-elastic-pool-manage-portal/basic-3.png)

按一下 [彈性資料庫監視] 下的 [過去一小時的資料庫 eDTU 使用量]。 這會開啟 [資料庫資源使用量]，並對於集區中的資料庫使用量提供詳細檢視。 使用頁面下半部中的方格，可以選取集區中的任何資料庫，以在圖表中顯示其使用情況 (最多 5 個資料庫)。 也可以按一下 [編輯圖表] ，自訂圖表中顯示的度量和時間範圍。

![資料庫資源使用率頁面](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-view"></a>自訂檢視

您可以編輯圖表，選取一個時間範圍 (過去 1 小時或過去 24 小時)，或按一下 [自訂]以選取要顯示過去 2 週內的不同一天。

![按一下編輯圖表](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

![按一下自訂](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

您也可以按一下 [比較資料庫依據] 下拉式清單，選取比較資料庫時所要使用的不同計量。

![編輯圖表](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>選取要監視的資料庫

在 [資料庫資源使用率] 頁面的資料庫清單中，瀏覽清單中的頁面或輸入資料庫的名稱，即可找到特定的資料庫。 使用此核取方塊來選取資料庫。

![搜尋要監視的資料庫](./media/sql-database-elastic-pool-manage-portal/select-dbs.png)


### <a name="add-an-alert-to-an-elastic-pool-resource"></a>將警示新增到彈性集區資源

您可以將規則新增到彈性集區，以在當彈性集區達到您設定的使用率閾值時，傳送電子郵件給人員或傳送警示字串到 URL 端點。

**將警示加入任何資源：**

1. 按一下 [資源使用率] 圖表以開啟 [計量] 頁面，按一下 [新增警示]，然後在 [新增警示規則] 頁面中填寫資訊 ([資源] 會自動設定為使用中的集區)。
2. 輸入可供您和收件者辨別警示的 [名稱] 和 [描述]。
3. 從清單中選擇要警示的 [度量]  。

   圖表會以動態方式顯示該度量的資源使用量，協助您選擇閾值。

4. 選擇 [條件] \(大於、小於等等) 和 [臨界值]。
5. 選擇警示觸發程序之前，計量規則必須滿足的 [期間]。
6. 按一下 [確定] 。

如需詳細資訊，請參閱[在 Azure 入口網站中建立 SQL Database 警示](sql-database-insights-alerts-portal.md)。

### <a name="move-a-database-into-an-elastic-pool"></a>將資料庫移入彈性集區

您可以從現有的集區中新增或移除資料庫。 資料庫可以位於其他集區。 不過，您只可以新增位於相同邏輯伺服器上的資料庫。

 ![按一下 [設定集區]](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

![按一下 [新增到集區]](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

![選取要新增的資料庫](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

![擱置中的新增集區](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

![按一下 [儲存]。](./media/sql-database-elastic-pool-manage-portal/click-save.png)

### <a name="move-a-database-out-of-an-elastic-pool"></a>將資料庫移出彈性集區

![資料庫清單](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

### <a name="change-performance-settings-of-an-elastic-pool"></a>變更彈性集區的效能設定

當您監視彈性集區的資源使用率時，可能會發現需要一些調整。 也許集區的效能或儲存體限制需要變更。 您可能想要變更集區中的資料庫設定。 您可以隨時變更集區設定，以在效能和成本之間取得最佳平衡。 如需詳細資訊，請參閱[何時應該使用彈性集區？](sql-database-elastic-pool.md)

若要變更每個集區的 eDTU 和儲存體限制，以及每個資料庫的 eDTU：

![彈性集區資源使用量](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>使用 PowerShell 來管理彈性集區和資料庫

若要使用 Azure PowerShell 建立和管理 SQL Database 彈性集區，請使用下列 PowerShell 指令程式。 如果您需要安裝或升級 PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 若要建立和管理資料庫、伺服器和防火牆規則，請參閱[使用 PowerShell 建立和管理 Azure SQL Database 伺服器和資料庫](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell)。 

> [!TIP]
> 如需 PowerShell 範例指令碼，請參閱[使用 PowerShell 建立彈性集區並在集區之間移動資料庫以及將其移出集區](scripts/sql-database-move-database-between-pools-powershell.md)和[使用 PowerShell 在 Azure SQL Database 中監視和調整 SQL 彈性集區](scripts/sql-database-monitor-and-scale-pool-powershell.md)。
>

| Cmdlet | 說明 |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|在邏輯 SQL Server 建立彈性資料庫集區。|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|取得邏輯 SQL Server 上的彈性集區及其屬性值。|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|修改邏輯 SQL Server 上的彈性資料庫集區屬性。 例如，使用 **StorageMB** 屬性可修改彈性集區的最大儲存體。|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|刪除邏輯 SQL Server 上的彈性資料庫集區。|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|取得邏輯 SQL server 上的彈性集區作業狀態。|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|在現有的集區建立新的資料庫，或建立新的資料庫做為單一資料庫。 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|取得一或多個資料庫。|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|設定資料庫的屬性，或將現有資料庫移入彈性集區、移出彈性集區，或在彈性集區之間移動。|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|移除資料庫。|


> [!TIP]
> 使用入口網站或一次只建立單一資料庫的 PowerShell Cmdlet 在彈性集區中建立許多資料庫可能需要花費一些時間。 若要自動建立成彈性集區，請參閱 [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)。
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>使用 Azure CLI 來管理彈性集區和資料庫

若要使用 [Azure CLI](/cli/azure/overview) 建立和管理 SQL Database 彈性集區，請使用下列 [Azure CLI SQL Database](/cli/azure/sql/db) 命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在您的瀏覽器中執行 CLI，或在 macOS、Linux 或 Windows 中[安裝](/cli/azure/install-azure-cli)。 

> [!TIP]
> 關於 Azure CLI 範例指令碼，請參閱[使用 CLI 移動 SQL 彈性集區中的 Azure SQL Database](scripts/sql-database-move-database-between-pools-cli.md)和[使用 Azure CLI 在 Azure SQL Database 中調整 SQL 彈性集區](scripts/sql-database-scale-pool-cli.md)。
>

| Cmdlet | 說明 |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#create)|建立彈性集區。|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#list)|傳回將伺服器中的彈性集區列出的清單。|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#list-dbs)|傳回將彈性集區中的資料庫列出的清單。|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#list-editions)|也包含可用的集區 DTU 設定、儲存體限制，以及個別資料庫設定。 為了減少繁複度，額外的儲存空間限制和個別資料庫設定預設為隱藏。|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#update)|更新彈性集區。|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#delete)|刪除彈性集區。|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>使用 Transact-SQL 來管理彈性集區內的資料庫

若要在現有彈性集區中建立並移動資料庫，或傳回 SQL Database 彈性集區與 Transact-SQL 的資訊，請使用下列 T-SQL 命令。 您可以使用 Azure 入口網站、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs)，或任何可連線到 Azure SQL Database 伺服器並傳遞 Transact-SQL 命令的其他程式來發出這些命令。 若要建立和管理資料庫、伺服器和防火牆規則，請參閱[使用 Transact-SQL 建立和管理 Azure SQL Database 伺服器和資料庫](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql)。

> [!IMPORTANT]
> 您無法使用 Transact-SQL 建立、更新或刪除 Azure SQL Database 彈性集區。 您可以新增或移除彈性集區中的資料庫，也可以使用 DMV 傳回現有彈性集區的資訊。
>

| 命令 | 說明 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|在現有的集區建立新的資料庫，或建立新的資料庫做為單一資料庫。 您必須連線到 master 資料庫才能建立新的資料庫。|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |將資料庫移入彈性集區、將資料庫移出彈性集區，或在彈性集區之間移動資料庫。|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|刪除資料庫。|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|傳回邏輯伺服器中的所有彈性資料庫集區的資源使用量統計資料。 每個彈性資料庫集區，每 15 秒報告時間範圍會傳回一列 (每分鐘四列)。 包括集區中所有資料庫的 CPU、IO、記錄、儲存體使用情況和並行的要求/工作階段使用量。|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|傳回 Azure SQL 資料庫或 Azure SQL 資料倉儲的版本 (服務層)、服務目標 (定價層) 和彈性集區名稱 (如果有的話)。 若已登入 Azure SQL Database 伺服器中的 master 資料庫，則傳回所有資料庫的相關資訊。 對於 Azure SQL 資料倉儲，您必須連線到 master 資料庫。|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>使用 REST API 來管理彈性集區和資料庫

若要建立及管理 SQL Database 彈性集區，請使用這些 REST API 的要求。

| 命令 | 說明 |
| --- | --- |
|[彈性集區 - 建立或更新](/rest/api/sql/elasticpools/createorupdate)|建立新的彈性集區或更新現有的彈性集區。|
|[彈性集區 - 刪除](/rest/api/sql/elasticpools/delete)|刪除彈性集區。|
|[彈性集區 - 取得](/rest/api/sql/elasticpools/get)|取得彈性集區。|
|[彈性集區 - 依伺服器列出](/rest/api/sql/elasticpools/listbyserver)|傳回將伺服器中的彈性集區列出的清單。|
|[彈性集區 - 更新](/rest/api/sql/elasticpools/update)|更新現有的彈性集區。|
|[建議的彈性集區 - 取得](/rest/api/sql/recommendedelasticpools/get)|取得建議的彈性集區。|
|[建議的彈性集區 - 依伺服器列出](/rest/api/sql/recommendedelasticpools/listbyserver)|傳回建議的彈性集區。|
|[建議的彈性集區 - 列出計量](/rest/api/sql/recommendedelasticpools/listmetrics)|傳回建議的彈性集區計量。|
|[彈性集區活動](/rest/api/sql/elasticpoolactivities)|傳回彈性集區活動。|
|[彈性集區資料庫活動](/rest/api/sql/elasticpooldatabaseactivities)|傳回資料庫內彈性集區上的活動。|
|[資料庫 - 建立或更新](/rest/api/sql/databases/createorupdate)|建立新的資料庫或更新現有資料庫。|
|[資料庫 - 取得](/rest/api/sql/databases/get)|取得資料庫。|
|[資料庫 - 依彈性集區取得](/rest/api/sql/databases/getbyelasticpool)|取得彈性集區內的資料庫。|
|[資料庫 - 依建議的彈性集區取得](/rest/api/sql/databases/getbyrecommendedelasticpool)|取得建議之彈性集區內的資料庫。|
|[資料庫 - 依彈性集區列出](/rest/api/sql/databases/listbyelasticpool)|傳回將彈性集區中的資料庫列出的清單。|
|[資料庫 - 依建議的彈性集區列出](/rest/api/sql/databases/listbyrecommendedelasticpool)|傳回建議彈性集區內的資料庫清單。|
|[資料庫 - 依伺服器列出](/rest/api/sql/databases/listbyserver)|傳回伺服器中的資料庫清單。|
|[資料庫 - 更新](/rest/api/sql/databases/update)|更新現有的資料庫。|

## <a name="next-steps"></a>後續步驟

* 若要觀賞影片，請參閱[有關 Azure SQL Database 彈性功能的 Microsoft Virtual Academy 視訊課程](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* 若要深入了解使用彈性集區的 SaaS 應用程式的設計模式，請參閱 [採用 Azure SQL Database 的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。
* 如需使用彈性集區的 SaaS 教學課程，請參閱 [Wingtip SaaS 應用程式簡介](sql-database-wtp-overview.md)。

