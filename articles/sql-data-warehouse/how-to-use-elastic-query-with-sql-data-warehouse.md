---
title: "Azure SQL 資料倉儲的彈性查詢概念 | Microsoft Docs"
description: "Azure SQL 資料倉儲的彈性查詢概念"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 09/18/2017
ms.author: elbutter
ms.openlocfilehash: 295cc59fdb23105534b4e7431902eaa720643330
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-elastic-query-with-sql-data-warehouse"></a>如何搭配使用彈性查詢與 SQL 資料倉儲



彈性查詢與 Azure SQL 資料倉儲的搭配，可讓您在透過使用外部資料表從遠端傳送到 Azure SQL 資料倉儲的 SQL Database 中撰寫 Transact-SQL。 使用這項功能可讓您節省成本，以及根據案例建置更高效能的架構。

這項功能實現兩個主要案例：

1. 網域隔離
2. 遠端查詢執行

### <a name="domain-isolation"></a>網域隔離

遠端隔離意指傳統的資料超市案例。 在某些案例中，我們可能會為了包括 (但不限於) 下文中的各種原因，將資料的邏輯網域提供給與其他資料倉儲隔離的下游使用者使用：

1. 資源隔離 - SQL Database 善於提供服務給大量的並行使用者，它提供的工作負載與資料倉儲保留的大量分析查詢稍有不同。 隔離能確保將正確的工具提供給正確的工作負載。
2. 安全性隔離 - 透過某些結構描述選擇性區分授權的資料子集。
3. 沙箱 - 提供一組範例資料來當做「遊樂場」，供使用者探索生產環境查詢等。

彈性查詢能讓您輕鬆地選取 SQL 資料倉儲資料的子集，再將它們移動到 SQL Database 執行個體。 再者，這項隔離功能未排除同時啟用遠端查詢執行的能力，讓您得以建置更有趣的「快取」案例。

### <a name="remote-query-execution"></a>遠端查詢執行

彈性查詢允許在 SQL 資料倉儲執行個體上進行遠端查詢執行。 使用者可以將熱資料和冷資料分別存放在 SQL Database 和 SQL 資料倉儲中，讓這兩種資料庫發揮最大功效。 使用者可以將近期內的資料保存在 SQL Database，以供製作報表和大量普通商務使用者取用。 然而，當需要更多資料或計算時，使用者可以將一部分查詢卸載到 SQL 資料倉儲執行個體，進行更快速、更有效率的大規模彙總處理。



## <a name="elastic-query-overview"></a>彈性查詢概觀

彈性查詢可用來將某個 SQL 資料倉儲中的資料提供給 SQL Database 執行個體使用。 彈性查詢允許 SQL Database 中的查詢參考遠端 SQL 資料倉儲執行個體中的資料表。 

第一個步驟是建立參考 SQL 資料倉儲執行個體的外部資料來源定義，其使用 SQL 資料倉儲中的現有使用者認證。 遠端 SQL 資料倉儲執行個體則不需要任何變更。 

> [!IMPORTANT] 
> 
> 您必須具備 ALTER ANY EXTERNAL DATA SOURCE 權限。 這個權限包含在 ALTER DATABASE 權限中。 需有 ALTER ANY EXTERNAL DATA SOURCE 權限，才能參考遠端資料來源。

接下來，我們在 SQL Database 執行個體中建立遠端外部資料表定義，指向 SQL 資料倉儲中的遠端資料表。 在運用使用外部資料表的查詢時，系統會將查詢中參考外部資料表的部分傳送到 SQL 資料倉儲執行個體進行處理。 當查詢完成時，系統會將結果集傳送回呼叫端的 SQL Database 執行個體。 如需在 SQL Database 和 SQL 資料倉儲之間設定彈性查詢的簡短教學課程，請參閱[設定 SQL 資料倉儲的彈性查詢][Configure Elastic Query with SQL Data Warehouse]。

如需 SQL Database 彈性查詢的詳細資訊，請參閱[Azure SQL Database 彈性查詢概觀][Azure SQL Database elastic query overview]。



## <a name="best-practices"></a>最佳作法

### <a name="general"></a>一般

- 使用遠端查詢執行時，請務必只選取必要的資料行及套用正確的篩選條件。 否則，不僅必要的計算負載會增加，連結果集的大小和需要在兩個執行個體之間移動的資料量也會增加。
- 利用叢集化資料行存放區保存 SQL 資料倉儲和 SQL Database 中的分析用資料，以提升分析效率。
- 確保來源資料表已針對查詢和資料移動等目的加以分割。
- 確保當做快取的 SQL Database 執行個體已經過分割，以便進行更精細的更新及簡化管理。 
- 最理想的情況是使用 PremiumRS 資料庫，因為 Premium 資料庫的折扣讓它們能著重於 IO 密集的工作負載，進而提供叢集資料行存放區索引的分析優勢。
- 載入後，使用載入或有效的日期識別資料行來 Upsert SQL Database 執行個體，維護快取和來源之間的完整。 
- 針對在外部資料來源中定義的 SQL Database 遠端登入認證，在 SQL 資料倉儲執行個體中建立個別的登入和使用者。 

### <a name="elastic-querying"></a>彈性查詢

- 外部資料表和內部快取資料表會以不同物件的形式存在 SQL Database 執行個體中。 請考慮建立能綜觀資料表快取部分和外部資料表的視野，取得這兩份資料表的聯集，以及在每份資料表的分界點套用篩選條件。

  想像我們要將最近一年來的資料保存在 SQL Database 執行個體中。 我們有兩份資料表：**ext.Orders** 參考資料倉儲訂單資料表，而 **dbo.Orders** 代表 SQL Database 執行個體中最近一年來的資料。 與其徵詢使用者要查詢哪一份資料表，不如在最近一年的分割點上建立綜觀這兩份資料表的視野。

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  以這種方式產生的視野，能讓查詢編譯器判斷是否需要使用資料倉儲執行個體來回答使用者的查詢。 

  提交、編譯、執行及移動每個彈性查詢的相關資料，會對資料倉儲執行個體造成負擔。 每個彈性查詢都會佔用並行位置和資源，因此請審慎進行。  


- 如果有人打算進一步鑽研來自資料倉儲執行個體的結果集，請考慮以 SQL Database 中的暫存資料表將其具體化，藉此提高效能及避免佔用不必要的資源。

### <a name="moving-data"></a>移動資料 

- 如果可以的話，請利用僅限附加的來源資料表保持資料管理的簡易性，讓資料倉儲和資料庫執行個體間的更新容易維護。
- 利用排清和填滿語意來移動分割區層級的資料，盡可能降低資料倉儲層級的查詢成本及減少移動的資料數量，讓資料庫執行個體保持最新狀態。 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>何時該選擇 Azure Analysis Services 和 SQL Database

#### <a name="azure-analysis-services"></a>Azure Analysis Services

- 您計劃使用快取來搭配提交大量小型查詢的 BI 工具
- 您需要少於一秒的查詢延遲
- 您是管理/開發 Analysis Services 模型的老手 

#### <a name="sql-database"></a>SQL Database

- 您想要利用 SQL 查詢快取資料
- 您需要從遠端執行某些查詢
- 您有較高的快取需求



## <a name="faq"></a>常見問題集

問：我可以使用彈性資料庫集區中的資料庫搭配彈性查詢嗎？

答： 會。 彈性集區中的 SQL Database 可以使用彈性查詢。 

問：用於彈性查詢的資料庫數目有上限嗎？

答：邏輯伺服器採用 DTU 限制來預防客戶意外過度使用。 如果您除了 SQL 資料倉儲之外另啟用好幾個資料庫來進行彈性查詢，可能會不經意地達到上限。 如果發生這種情況，請提交要求來提高邏輯伺服器的 DTU 限制。 您可以藉由[建立支援票證](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket)，並選取 [配額] 做為要求類型來增加配額

問：我可以使用資料列層級安全性/動態資料遮罩來搭配彈性查詢嗎？

答：想要使用較先進之安全功能來搭配 SQL Database 的客戶，可以先將資料移動及儲存在 SQL Database 中來實現。 您目前無法將資料列層級安全性或 DDM 套用在經由外部資料表查詢的資料上。 

問：我可以從 SQL Database 執行個體寫入資料倉儲執行個體嗎？

答：這項功能目前不受支援。 如果這是您希望未來能問世的功能，請造訪我們的[意見反應頁面][Feedback page]來提議/投票。 

問：我可以使用幾何/地理之類的空間類型嗎？

答：您可以使用 varbinary(max) 值的形式將空間類型儲存在 SQL 資料倉儲中。 當您使用彈性查詢查詢這些資料行時，可以在執行階段將它們轉換成適當類型。

![空間類型](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)





<!--Image references-->

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop/
[Configure Elastic Query with SQL Data Warehouse]: ./tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md

<!--MSDN references-->

<!--Other Web references-->