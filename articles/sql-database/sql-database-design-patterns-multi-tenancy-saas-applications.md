---
title: "多租用戶 SaaS 應用程式與 Azure SQL Database 的設計模式 | Microsoft Docs"
description: "本文討論雲端環境中執行的多租用戶資料庫應用程式需要考慮的需求和通用資料架構模式，以及這些模式相關的各種利弊取捨。 其中也說明 Azure SQL Database 搭配其彈性集區和彈性工具，如何在無需妥協的情況下滿足這些需求。"
keywords: 
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 1dd20c6b-ddbb-40ef-ad34-609d398d008a
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-design
ms.date: 02/01/2017
ms.author: srinia
ms.openlocfilehash: 0f6ba62a01f3211ccaae6b6c48f72e0de54aad78
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="design-patterns-for-multi-tenant-saas-applications-and-azure-sql-database"></a>多租用戶 SaaS 應用程式與 Azure SQL Database 的設計模式
在本文中，您可以了解雲端環境中執行的多租用戶軟體即服務 (SaaS) 資料庫應用程式的需求和通用資料架構模式。 其中也說明您需要考量的因素，以及不同設計模式的利弊取捨。 Azure SQL Database 中的彈性集區和彈性工具可協助您符合特定需求，而不會影響其他目標。

開發人員在設計多租用戶應用程式的資料層租用模型時，有時候會做出有損其長期最佳利益的選擇。 至少一開始，開發人員可能會察覺輕鬆開發和較低的雲端服務提供者成本，比應用程式的租用戶隔離或延展性更加重要。 此選項可能會導致客戶滿意度問題和稍後高成本的路線修正。

多租用戶應用程式是指雲端環境中裝載的應用程式，可將同一組服務提供給不共用或看不到彼此資料的數百或數千個租用戶。 例如，提供服務給雲端裝載環境中租用戶的 SaaS 應用程式。

## <a name="multi-tenant-applications"></a>多租用戶應用程式
在多租用戶應用程式中，可以輕鬆分割資料和工作負載。 您可以分割資料和工作負載 (例如，沿著租用戶界限)，因為大部分要求發生於租用戶的範圍內。 這是資料和工作負載與生俱來的屬性，可支援本文中討論的應用程式模式。

開發人員會將這類型的應用程式使用於整個雲端式應用程式的範疇內，包括：

* 轉換成雲端即 SaaS 應用程式的合作夥伴資料庫應用程式
* 從無到有針對雲端而建置的 SaaS 應用程式
* 直接面向客戶的應用程式
* 面向員工的企業應用程式

專為雲端設計的 SaaS 應用程式，以及以合作夥伴資料庫應用程式為根基的應用程式通常是多租用戶應用程式。 這些 SaaS 應用程式提供特殊的軟體應用程式即服務給其租用戶。 租用戶可以存取應用程式服務，而且對於隨應用程式一起儲存的相關資料具有完整的擁有權。 但若要利用 SaaS 的優點，租用戶必須放棄自己資料的一些控制權。 它們會信任 SaaS 服務提供者，以確保資料的安全並與其他租用戶的資料隔離。 這類多租用戶 SaaS 應用程式的範例包括 MYOB、SnelStart 和 Salesforce.com。每個應用程式可以依照租用戶界限分割，並支援我們在本文中討論的應用程式設計模式。

為取用者或組織內員工 (通常稱為使用者，而不是租用戶) 提供直接服務的應用程式，屬於多租用戶應用程式範疇內的另一種類別。 客戶會訂閱服務，並不擁有服務提供者所收集和儲存的資料。 除了政府強制規定的隱私權法規，服務提供者不會嚴格要求隔離客戶彼此的資料。 這種客戶對應的多租用戶應用程式範例包括 Netflix、Spotify 和 Xbox LIVE 等媒體內容提供者。 還有其他可輕鬆分割的應用程式範例，包括面向客戶的網際網路等級應用程式或物聯網 (IoT) 應用程式，其中每個客戶或裝置都可能成為分割區。 分割區界限可以劃分不同的使用者和裝置。

並非所有應用程式都可輕易地沿著單一屬性來分割，例如租用戶、客戶、使用者或裝置。 例如，複雜企業資源規劃 (ERP) 應用程式具有產品、訂單和客戶。 其通常使用具有數千個緊密相互連結資料表的複雜結構描述。

單一分割策略不可能適用於所有資料表和運用於應用程式的工作負載。 本文所著重的多租用戶應用程式，具有可輕鬆分割的資料和工作負載。

## <a name="multi-tenant-application-design-trade-offs"></a>多租用戶應用程式設計取捨
多租用戶應用程式開發人員選擇的設計模式通常是以下列因素考量作為基礎︰

* **隔離租用戶**。 開發人員必須確保租用戶不會越權存取其他租用戶的資料。 此隔離需求會延伸至其他屬性，例如避開吵雜的芳鄰、能夠還原租用戶的資料、以及實作租用戶特定的自訂。
* **雲端資源成本**。 SaaS 應用程式需要具有成本競爭力。 多租用戶應用程式開發人員可能會選擇最佳化，以降低雲端資源的使用成本，例如儲存體和計算成本。
* **簡化 DevOps**。 多租用戶應用程式開發人員需要納入隔離保護、維護及監視其應用程式和資料庫結構描述的健康情況，以及排解租用戶的疑難問題。 應用程式開發和操作的複雜性會直接導致成本增加以及租用戶滿意度面臨挑戰。
* **延展性**。 以累加方式加入更多租用戶的能力，而對於需要此能力的租用戶而言，這是 SaaS 作業成功不可或缺的能力。

上述每個因素都有其各自的取捨。 成本最低的雲端產品不見得提供最方便的開發經驗。 開發人員一定要在應用程式設計過程中，對這些選項及其取捨做出明智的選擇。

常見的開發模式是將多個租用戶裝入一個或幾個資料庫中。 此方法的優點是成本較低，因為您需支付少量的資料庫，而處理有限數目的資料庫相對簡單。 但經過一段時間，SaaS 多租用戶應用程式開發人員將會瞭解，這項選擇在租用戶隔離和延展性方面有些缺點。 如果租用戶隔離變得重要，則必須有額外的付出，才能使共用儲存體中的租用戶資料免於遭到未經授權的存取或避開吵雜的芳鄰。 這項額外的付出可能會大幅提升開發工作和隔離維護成本。 同樣地，如果需要加入租用戶，此設計模式通常需要憑藉專長，才能將租用戶資料重新分散於資料庫，以適當調整應用程式的資料層。  

在供應給其他企業和組織的 SaaS 多租用戶應用程式中，租用戶隔離通常是基本需求。 開發人員在簡單性和成本考量方面短視近利，而不重視隔離和延展性。 隨著服務不斷發展，以及租用戶隔離需求變得更加重要且需要在應用程式層次管理，這項取捨會使情況更趨於複雜且代價昂貴。 不過，在提供直接取用者對應的服務給使用者的多租用戶應用程式中，很可能為了讓雲端資源成本發揮最高效益，而較不重視租用戶隔離。

## <a name="multi-tenant-data-models"></a>多租用戶資料模型
放置租用戶資料的常見設計做法都遵循這三種不同的模型 (如圖 1 所示)。

![多租用戶應用程式資料模型](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)

圖 1︰多租用戶資料模型的常見設計做法

* **租用戶各有資料庫**。 每個租用戶都有自己的資料庫。 租用戶特定的所有資料都封閉在租用戶的資料庫內，與其他租用戶及其資料隔離。
* **共用資料庫 - 分區化**。 多個租用戶會共用多個資料庫的其中一個。 使用資料分割策略 (例如雜湊、範圍或清單分割)，可將一組不同的租用戶指派給每個資料庫。 此資料分散策略通常稱為分區化。
* **共用資料庫 - 單一**。 單一資料庫 (有時很大) 包含以租用戶識別碼資料行來區分的所有租用戶的資料。

> [!NOTE]
> 應用程式開發人員可選擇將不同的租用戶放在不同的資料庫結構描述中，然後使用結構描述名稱來區分不同的租用戶。 這種方法通常需要使用動態 SQL，也無法有效利用計畫快取，所以不建議採用。 在本文的其餘部分，我們著重於此類別的多租用戶應用程式共用資料表方法。
> 
> 

## <a name="popular-multi-tenant-data-models"></a>熱門的多租用戶資料模型
請務必依據我們已識別的應用程式設計取捨，評估不同類型的多租用戶資料模型。 這些因素有助於描繪上述三種最常見的多租用戶資料模型，以及其資料庫使用情況 (如圖 2 所示)。

* **隔離**。 以租用戶之間的隔離程度，衡量資料模型可達成的租用戶隔離效果。
* **雲端資源成本**。 在租用戶之間共用的資源規則，可以使雲端資源成本發揮最高效益。 資源可定義為計算和儲存體成本。
* **DevOps 成本**。 簡化應用程式開發、部署和管理性可降低整體的 SaaS 作業成本。  

在圖 2 中，Y 軸顯示租用戶隔離的等級。 X 軸顯示資源共用的層級。 中間的灰色對角線箭號表示 DevOps 成本的方向 (傾向於增加或減少)。

![熱門的多租用戶應用程式設計模式](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png)

圖 2︰熱門的多租用戶資料模型

圖 2 的右下方象限顯示使用可能很大、共用單一資料庫和共用資料表 (或另一個結構描述) 方法的應用程式模式。 因為所有租用戶都使用單一資料庫中相同的資料庫資源 (CPU、記憶體、輸入/輸出)，所以適合進行資源共用。 但租用戶隔離有限。 您可能需要採取額外步驟來保護應用程式層的各租用戶。 這些額外步驟可能會大幅增加開發和管理應用程式的 DevOps 成本。 延展性受限於用來裝載資料庫的硬體規模。

圖 2 左下方象限說明跨多個資料庫分區的多個租用戶 (通常為不同的硬體縮放單位)。 每個資料庫會裝載一部分的租用戶，以處理其他模式的延展性考量。 如果需要更多容量來因應更多租用戶，您可將租用戶輕鬆放在配置給新硬體縮放單位的新資料庫上。 不過，共用資源的數量會降低。 只有放在相同縮放單位上的租用戶會共用資源。 這種方法也無助於改善租用戶隔離，因為仍有許多租用戶共置，自然無法免於彼此動作的干擾。 應用程式複雜度仍然很高。

圖 2 左上方的象限是第三種方法。 它將每個租用戶的資料放在自己的資料庫中。 這種方法有良好的租用戶隔離特性，但是當每個資料庫提供自己專用的資源時，就會限制資源共用。 如果所有租用戶有可預測的工作負載，這會是個好方法。 如果租用戶工作負載變得更難預測，提供者就無法最佳化資源共用。 不可預測性是 SaaS 應用程式一般的特性。 提供者必須過度佈建，以滿足需求或減少資源。 任何一個動作都會導致成本提高或租用戶滿意度降低。 租用戶之間的資源共用程度越高，才能使解決方案越符合成本效益。 增加資料庫數目也會讓部署和維護應用程式的 DevOps 成本增加。 儘管有上述顧慮，此方法會提供最佳且最簡單的租用戶隔離。

這些因素也會影響客戶選擇的設計模式︰

* **租用戶資料的擁有權**。 租用戶保留自己資料的擁有權的應用程式，應該選擇每個租用戶單一資料庫的模式。
* **規模**。 以數十萬或數百萬個租用戶為目標的應用程式，應該選擇資料庫共用方法，例如分區化。 但仍可能要解決隔離需求。
* **價值和商務模型**。 如果應用程式的個別租用戶營收很少 (少於一美元)，隔離需求會變得較不重要，而共用資料庫會較有意義。 如果每個租用戶的營收只有數美元或更多金額，則租用戶各有資料庫模型比較可行。 它可能有助於降低開發成本。

根據圖 2 所示的設計取捨，理想的多租用戶模型必須兼顧良好的租用戶隔離特性，讓租用戶之間資源共用達到最佳化。 此模型符合圖 2 右上方象限中所描述類別的模型。

## <a name="multi-tenancy-support-in-azure-sql-database"></a>Azure SQL Database 中的多租用戶支援
Azure SQL Database 支援圖 2 所示的所有多租用戶應用程式模式。 搭配彈性集區，它也支援一種應用程式模式，其結合「租用戶各有資料庫」方法 (請參閱圖 3 的右上方象限) 的良好資源共用和隔離優點。 Azure SQL Database 中的彈性資料庫工具和功能，有助於降低開發和操作具有許多資料庫的應用程式的成本 (顯示於圖 3 的陰影區)。 這些工具有助於建置和管理採用任何多資料庫模式的應用程式。

![Azure SQL Database 中的模式](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png)

圖 3：Azure SQL Database 中的多租用戶應用程式模式

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>租用戶各有資料庫模型與彈性集區和工具
SQL Database 中的彈性集區會結合租用戶隔離與租用戶資料庫之間的資源共用，以加強支援「租用戶各有資料庫」方法。 SQL Database 是資料層方案，可供建置多租用戶應用程式的 SaaS 提供者使用。 在租用戶之間共用資源的負擔會從應用程式層轉移至資料庫服務層。 彈性作業、彈性查詢、彈性交易和彈性資料庫用戶端程式庫可簡化跨資料庫大規模管理和查詢的複雜性。

| 應用程式需求 | SQL Database 功能 |
| --- | --- |
| 租用戶隔離與資源共用 |[彈性集區](sql-database-elastic-pool.md)︰配置 SQL Database 資源的集區，讓數個資料庫共用這些資源。 此外，個別資料庫可以依需要從集區汲取資源，以因應租用戶工作負載變化而造成的容量需求遽增。 彈性集區本身也可以視需要而相應放大或縮小。 彈性集區也支援在集區層級上輕鬆地管理、監視和進行疑難排解。 |
| 跨資料庫簡化 DevOps |[彈性集區](sql-database-elastic-pool.md)︰如先前所述。 |
| | [彈性查詢](sql-database-elastic-query-horizontal-partitioning.md)︰跨資料庫查詢以進行報告或跨租用戶分析。 |
| | [彈性作業](sql-database-elastic-jobs-overview.md)︰封裝資料庫維護作業或資料庫結構描述變更，然後可靠地部署到多個資料庫。 |
| | [彈性交易](sql-database-elastic-transactions-overview.md)︰以不可部分完成和隔離的方式，處理多個資料庫的變更。 當應用程式在數個資料庫作業之間需要保證「全有或全無」時，就需要彈性交易。 |
| | [彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)︰管理資料分佈並將租用戶對應到資料庫。 |

## <a name="shared-models"></a>共用的模型
如先前所述，大多數 SaaS 提供者的共用模型方法可能會引起租用戶隔離問題，以及應用程式開發和維護的複雜性。 然而，對於直接提供服務給取用者的多租用戶應用程式，租用戶隔離需求的優先性可能不及降低成本。 他們能夠以高密度將租用戶封裝在一或多個資料庫中以降低成本。 使用單一資料庫或多個分區化資料庫的共用資料庫模型，可以提高資源共用和整體成本的效率。 Azure SQL Database 會提供一些功能，幫助取用者在資料層大規模建置隔離，以強化安全性和管理。

| 應用程式需求 | SQL Database 功能 |
| --- | --- |
| 安全性隔離功能 |[資料列層級安全性](https://msdn.microsoft.com/library/dn765131.aspx) |
| [資料庫結構描述](https://msdn.microsoft.com/library/dd207005.aspx) | |
| 跨資料庫簡化 DevOps |[彈性查詢](sql-database-elastic-query-horizontal-partitioning.md) |
| | [彈性工作](sql-database-elastic-jobs-overview.md) |
| | [彈性交易](sql-database-elastic-transactions-overview.md) |
| | [彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md) |
| | [彈性資料庫分割和合併](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>摘要
對於大多數 SaaS 多租用戶應用程式而言，租用戶隔離需求很重要。 提供隔離的最佳選項幾乎仰賴於「租用戶各有資料庫」方法。 其他兩種方法需要在複雜的應用程式層投入心力，並需要有熟練的開發人員來提供隔離，因而大幅增加成本與風險。 如果在服務開發初期不重視隔離需求，在前兩個模型中改造時會付出更大的代價。 「租用戶各有資料庫」模型的主要缺點在於，因為共用和維護不足，以及管理許多資料庫，導致雲端資源成本增加。 SaaS 應用程式開發人員經常掙扎於這些取捨之中。

雖然這些取捨可能是大部分雲端資料庫服務提供者的主要障礙，但 Azure SQL Database 服務能以其彈性集區和彈性資料庫功能，排除這些阻礙。 SaaS 開發人員可以結合「租用戶各有資料庫」模型的隔離特性，同時最佳化資源共用，並利用彈性集區和相關的工具，改善許多資料庫的管理效率。

沒有租用戶隔離需求，並能以高密度將租用戶封裝在資料庫中的多租用戶應用程式提供者，可能會發現共用資料模型可以提高資源共用的效率，並能降低整體成本。 Azure SQL Database 彈性資料庫工具、分區化程式庫和安全性功能，可協助 SaaS 提供者建置和管理多租用戶應用程式。

## <a name="next-steps"></a>後續步驟
[開始使用彈性資料庫工具](sql-database-elastic-scale-get-started.md) 。

透過使用彈性集區的範例應用程式，建立 [SaaS 的彈性集區自訂儀表板](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) ，以建置符合成本效益、可調整的資料庫解決方案。

使用 Azure SQL Database 工具來 [移轉要相應放大的現有資料庫](sql-database-elastic-convert-to-use-elastic-tools.md)。

若要使用 Azure 入口網站來建立彈性集區，請參閱[建立彈性集區](sql-database-elastic-pool-manage-portal.md)。  

了解如何 [監視和管理彈性集區](sql-database-elastic-pool-manage-portal.md)。

## <a name="additional-resources"></a>其他資源

* [部署及探索使用 Azure SQL Database 的多租用戶應用程式 - Wingtip SaaS](sql-database-saas-tutorial.md)
* [什麼是 Azure 彈性集區？](sql-database-elastic-pool.md)
* [使用 Azure SQL Database 相應放大](sql-database-elastic-scale-introduction.md)
* [使用彈性資料庫工具和資料列層級安全性的多租用戶應用程式](sql-database-elastic-tools-multi-tenant-row-level-security.md)
* [使用 Azure Active Directory 和 OpenID Connect 在多租用戶應用程式中進行驗證](../guidance/guidance-multitenant-identity-authenticate.md)
* [Tailspin Surveys 應用程式](../guidance/guidance-multitenant-identity-tailspin.md)


## <a name="questions-and-feature-requests"></a>問題和功能要求

如有問題，您可在 [SQL Database 論壇](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)中找到我們。 在 [SQL Database 意見反應論壇](https://feedback.azure.com/forums/217321-sql-database/)中提出功能要求。

