---
title: "什麼是 Azure SQL 資料倉儲？ | Microsoft Docs"
description: "企業級分散式資料庫，可處理資料量高達 PB 的關聯式與非關聯式資料。 它是業界首見能在幾秒內增加、縮減和暫停的雲端資料倉儲。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 2/28/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 575c49f83c8845edcea984459f3907490c62d269
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="what-is-azure-sql-data-warehouse"></a>什麼是 Azure SQL 資料倉儲？
Azure SQL 資料倉儲是一種巨量平行處理 (MPP) 以雲端為基礎、相應放大的關聯式資料庫，可處理大量的資料。 

SQL 資料倉儲：

* 結合 SQL Server 關聯式資料庫與 Azure 雲端相應放大功能。 
* 從計算減少儲存體。
* 啟用增加、減少、暫停或繼續計算。 
* 跨 Azure 平台間整合。
* 利用 SQL Server Transact-SQL (T-SQL) 和工具。
* 遵守各種法律和商務安全性需求，例如 SOC 和 ISO。

本文說明 SQL 資料倉儲的重要功能。

## <a name="massively-parallel-processing-architecture"></a>巨量平行處理架構
SQL 資料倉儲是大量平行處理 (MPP) 分散式資料庫系統。 在幕後，SQL 資料倉儲會將您的資料分散於多個不共用的儲存和處理單元。 資料會儲存在進階本地備援儲存體層級，在其頂端動態連結的計算節點會執行查詢。 SQL 資料倉儲可以採用「分治法」來執行載入和複雜的查詢。 要求是由控制節點接收、針對分佈最佳化，然後傳遞至計算節點來平行執行其工作。

使用減少的儲存體和計算，SQL 資料倉儲可以︰

* 擴大或縮小獨立計算的儲存體大小。
* 擴大或縮小計算能力，而不移動資料。
* 暫停計算容量，同時讓資料保持不變，只需要支付儲存體的費用。
* 在營運時間期間繼續計算容量。

下圖更詳細說明此架構。

![SQL 資料倉儲架構][1]

**控制節點︰** 控制節點會管理及最佳化查詢。 它是與所有應用程式與連接互動的前端。 在 SQL 資料倉儲中，控制節點採用 SQL Database 計算，而且連接它時的外觀及操作方式相同。 控制節點會暗中協調對分散式資料執行平行查詢所需的所有資料移動和計算。 當您將 TSQL 查詢提交到 SQL 資料倉儲時，控制節點會將其轉換為會在每個計算節點上平行執行的個別查詢。

**計算節點︰** 計算節點是 SQL 資料倉儲背後的動力。 它們是儲存資料和處理查詢的 SQL Database。 當您新增資料時，SQL 資料倉儲會將資料列分散到各個計算節點。 計算節點是會對您的資料執行平行查詢的背景工作角色。 經過處理後，它們會將結果傳回控制節點。 為了完成查詢，控制節點會彙總結果並傳回最終的結果。

**儲存體：** 您的資料會儲存在 Azure Blob 儲存體中。 當計算節點與您的資料互動時，它們會直接從 Blob 儲存體來回寫入和讀取。 由於 Azure 儲存體可大幅地明確擴充，SQL 資料倉儲同樣也可以。 計算和儲存體各自獨立，所以 SQL 資料倉儲可以自動調整儲存體，與計算分開調整，反之亦然。 Azure Blob 儲存體也是完全容錯，並可簡化備份和還原程序。

**資料移動服務︰** 資料移動服務 (DMS) 可在節點之間移動資料。 DMS 可讓計算節點存取聯結和彙總所需的資料。 DMS 不是 Azure 服務。 它是一項在所有節點上與 SQL Database 並排執行的 Windows 服務。 DMS 是背景處理序，無法直接互動。 不過，您可以查閱查詢計劃以查看 DMS 作業的發生時機，因為資料移動必須平行執行每項查詢。

## <a name="optimized-for-data-warehouse-workloads"></a>已針對資料倉儲工作負載最佳化
MPP 方法由許多資料倉儲特定效能最佳化輔助，包括：

* 一項分散式查詢最佳化工具和一組複雜的統計資料。 使用資料大小和散發的資訊，服務就能夠藉由評估特定分散式查詢作業的成本來最佳化查詢。
* 整合到資料移動程序中的進階演算法和技術，能夠視需要在計算資源之間有效率地移動資料以執行查詢。 這些資料移動作業均為內建，且資料移動服務的所有最佳化都會自動進行。
* 預設的叢集 **資料行存放** 區索引。 與傳統的資料列導向儲存體相比，使用資料行儲存體的 SQL 資料倉儲最多可讓壓縮平均提升 5 倍，查詢效能提升 10 倍以上。 需要掃描大量資料列的分析查詢更適合使用資料行存放區索引。


## <a name="predictable-and-scalable-performance-with-data-warehouse-units"></a>可預測且可調整的效能與資料倉儲單位
SQL 資料倉儲是使用與 SQL Database 類似的技術建置，表示使用者對於分析查詢可以預期一致且可預測的效能。 當使用者新增或減去計算節點時，他們應該會看到以線性方式調整的效能。 SQL 資料倉儲的資源配置是使用資料倉儲單位 (DWU) 測量。 DWU 是配置給 SQL 資料倉儲的基礎資源 (例如 CPU、記憶體、IOPS) 的量值。 增加 DWU 數目可增加資源和效能。 具體而言，DWU 有助於確保：

* 您可以調整您的資料倉儲，而不必擔心基礎硬體或軟體。
* 您可以在變更資料倉儲的計算之前，預測 DWU 層級的效能改進程度。
* 可以變更或移動您的執行個體的基礎硬體與軟體，而不會影響工作負載效能。
* Microsoft 可以改善服務的基礎架構，而不會影響工作負載的效能。
* Microsoft 可以快速提升 SQL 資料倉儲的效能，以可調整及平均影響系統的方式執行這項操作。

資料倉儲單位會提供包含三個度量的量值，而這些度量與資料倉儲工作負載效能高度相互關聯。 下列主要工作負載度量以線性方式調整 DWU。

**掃描/彙總**：標準資料倉儲查詢，該查詢會掃描大量資料列，然後執行複雜的彙總。 這是 I/O 和 CPU 密集作業。

**負載**將資料擷取至服務的能力。 使用來自 Azure 儲存體 Blob 或 Azure Data Lake 的 PolyBase 時的負載效能最佳。 這項度量是設計來對服務的網路和 CPU 層面給予壓力。

**Create Table As Select (CTAS)** ：CTAS 會測量複製資料表的能力。 這牽涉到從儲存體讀取資料、跨應用裝置的節點散發，以及重新寫入至儲存體。 這是 CPU、IO 和網路密集作業。

## <a name="built-on-sql-server"></a>建置在 SQL Server 上
SQL 資料倉儲以 SQL Server 關聯式資料庫引擎為基礎，且包含企業資料倉儲的許多功能。 如果您已經熟悉 T-SQL，則可輕鬆地將您的知識傳輸到 SQL 資料倉儲。 無論您是進階或新手使用者，文件範例都能夠協助您開始著手。 整體來說，您可以考慮我們建構 SQL 資料倉儲的語言元素的方式，如下所示：

* SQL 資料倉儲會將 T-SQL 語法用於許多作業。 並且支援一組廣泛的傳統 SQL 建構，例如預存程序、使用者定義函式、資料表資料分割、索引和定序。
* SQL 資料倉儲也包含各種較新的 SQL Server 功能，包括叢集**資料行存放區**索引、PolyBase 整合和資料稽核 (完整的威脅評估)。
* 較不常用於資料倉儲工作負載或是比 SQL Server 還新的特定 TSQL 語言元素目前可能無法使用。 如需詳細資訊，請參閱[移轉文件][Migration documentation]。

透過 SQL Server、SQL 資料倉儲、SQL Database 和「分析平台系統」之間的 Transact-SQL 與功能共通性，您能夠開發符合您資料需求的解決方案。 您可以根據效能、安全性和調整需求，決定儲存資料的位置，然後視需要在不同系統之間傳輸資料。

## <a name="data-protection"></a>資料保護
SQL 資料倉儲會將所有資料儲存在 Azure Premium 本地備援儲存體中。 並在當地資料中心維護多份資料的同步複本，確保當地語系化失敗時能夠提供透明的資料保護。 此外，SQL 資料倉儲會使用 Azure 儲存體快照集，定期自動備份作用中 (未暫停) 的資料庫。 若要深入了解備份和還原的運作方式，請參閱[備份與還原概觀][Backup and restore overview]。

## <a name="integrated-with-microsoft-tools"></a>與 Microsoft 工具整合
SQL 資料倉儲也整合了許多 SQL Server 使用者可能很熟悉的工具。 這些工具包括：

**傳統的 SQL Server 工具** ：SQL 資料倉儲會與 SQL Server Analysis Services、Integration Services 和 Reporting Services 完整整合。

**以雲端為基礎的工具**：SQL 資料倉儲可以與 Azure 中的各種服務整合，包括 Data Factory、串流分析、機器學習服務和 Power BI。 如需更完整的清單，請參閱[整合式工具概觀][Integrated tools overview]。

**協力廠商工具**：許多協力廠商工具提供者與 SQL 資料倉儲整合的工具都已經過認證。 如需完整清單，請參閱 [SQL 資料倉儲解決方案合作夥伴][SQL Data Warehouse solution partners]。

## <a name="hybrid-data-sources-scenarios"></a>混合式資料來源案例
Polybase 可讓您透過使用熟悉的 T-SQL 命令來運用不同來源的資料。 Polybase 讓查詢 Azure Blob 儲存體中的非關聯式資料就像查詢一般資料表般容易。 使用 Polybase 即可查詢非關聯式資料，或將非關聯式資料匯入 SQL 資料倉儲。

* PolyBase 使用外部資料表存取非關聯式資料。 資料表定義會儲存在 SQL 資料倉儲中，您可以像存取一般關聯式資料一樣，使用 SQL 和工具進行存取。
* 在其整合中無從得知 Polybase。 它會為其支援的所有來源提供相同的功能。 Polybase 可讀取各種格式的資料，包括分隔檔案或 ORC 檔案。
* PolyBase 可用來存取 Blob 儲存體，該儲存體也用來做為 HDInsight 叢集的儲存體。 這可讓您使用關聯式與非關聯式工具，以最新的方式存取相同的資料。

## <a name="sla"></a>SLA
SQL 資料倉儲提供產品層級的服務等級協定 (SLA) 做為 Microsoft Online Services SLA 的一部分。 如需詳細資訊，請參閱 [SQL 資料倉儲的 SLA][SLA for SQL Data Warehouse]。 如需關於所有其他產品的 SLA 資訊，您可以造訪[服務等級協定] Azure 頁面，或在[大量授權][Volume Licensing]頁面上下載。 

## <a name="next-steps"></a>後續步驟
現在您已稍微了解 SQL 資料倉儲，請了解如何快速[建立 SQL 資料倉儲][create a SQL Data Warehouse]和[載入範例資料][load sample data]。 如果您不熟悉 Azure，您可能會發現 [Azure 詞彙][Azure glossary]在您遇到新術語時很有幫助。 或者，也可以看一下其中一些其他 SQL 資料倉儲資源。  

* [客戶成功案例]
* [部落格]
* [功能要求]
* [影片]
* [客戶諮詢小組部落格]
* [建立支援票證]
* [MSDN 論壇]
* [Stack Overflow 論壇]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[建立支援票證]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[客戶成功案例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[部落格]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客戶諮詢小組部落格]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[功能要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 論壇]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow 論壇]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[影片]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[服務等級協定]: https://azure.microsoft.com/en-us/support/legal/sla/
