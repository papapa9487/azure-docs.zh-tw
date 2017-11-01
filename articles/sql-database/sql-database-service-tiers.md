---
title: "Azure SQL Database 服務 | Microsoft Docs"
description: "深入了解單一和集區資料庫的服務層，以提供效能層級和儲存體大小。"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/20/2017
ms.author: carlrab
ms.openlocfilehash: 5d24ec2f0979283d3040a2f08fa8e681fab26fdd
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="what-are-azure-sql-database-service-tiers"></a>何謂 Azure SQL Database 服務層

[Azure SQL Database](sql-database-technical-overview.md) 為[單一資料庫](sql-database-single-database-resources.md)與[彈性集區](sql-database-elastic-pool.md)提供**基本**、**標準**、**進階**和**進階 RS** 服務層。 服務層主要是以一系列的效能層級和儲存體大小選項以及價格區分。  所有服務層皆提供變更效能層級和儲存體大小的彈性。  單一資料庫和彈性集區會根據服務層、效能層級和儲存體大小，以每小時的方式計費。   

## <a name="choosing-a-service-tier"></a>選擇服務層

服務層的選擇主要視業務持續性、儲存體和效能需求而定。
| | **基本** | **標準** |**高級** |**進階 RS** |
| :-- | --: |--:| --:| --:| 
|目標工作負載|開發與生產|開發與生產|開發與生產|工作負載可以容許由於服務失敗而造成的資料遺失最多 5 分鐘|
|執行時間 SLA|99.99%|99.99%|99.99%|預覽時，N/A|
|備份保留期|7 天|35 天|35 天|35 天|
|CPU|低|低、中、高|中、高|中型|
|IO 輸送量|低  | 中型 | 量級高於標準|與進階相同|
|IO 延遲|高於進階|高於進階|低於基本和標準|與進階相同|
|資料行存放區索引和記憶體內部 OLTP|N/A|N/A|支援|支援|
|||||

## <a name="performance-level-and-storage-size-limits"></a>效能層級和儲存體大小限制

單一資料庫的效能層級會以資料庫交易單位 (DTU) 表示，而彈性集區的效能層級則會以彈性資料庫交易單位 (eDTU) 表示。 如需 DTU 和 eDTU 的詳細資訊，請參閱[什麼是 DTU 和 eDTU？](sql-database-what-is-a-dtu.md)。

### <a name="single-databases"></a>單一資料庫

|  | **基本** | **標準** | **高級** | **進階 RS**|
| :-- | --: | --: | --: | --: |
| 儲存體大小上限* | 2 GB | 1 TB | 4 TB  | 1 TB  |
| DTU 上限 | 5 | 3000 | 4000 | 1000 |
||||||

### <a name="elastic-pools"></a>彈性集區

| | **基本** | **標準** | **高級** | **進階 RS**|
| :-- | --: | --: | --: | --: |
| 每個資料庫的儲存體大小上限*  | 2 GB | 1 TB | 1 TB | 1 TB |
| 每個集區的儲存體大小上限* | 156 GB | 4 TB | 4 TB | 1 TB |
| 每個資料庫的 eDTU 上限 | 5 | 3000 | 4000 | 1000 |
| 每個集區的 eDTU 上限 | 1600 | 3000 | 4000 | 1000 |
| 每個集區的資料庫數目上限 | 500  | 500 | 100 | 100 |
||||||

> [!IMPORTANT]
> \* 大於內含儲存體數量的儲存體大小尚在預覽中，而且會產生額外成本。 如需詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 
>
> \* 在進階層中，超過 1 TB 的儲存體目前在下列區域為可用狀態：美國東部 2、美國西部、美國維吉尼亞州政府、西歐、德國中部、東南亞、日本東部、澳大利亞東部、加拿大中部和加拿大東部。 請參閱 [P11-P15 目前限制](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
> 

如需特定的效能層級和可用儲存體大小選項的詳細資訊，請參閱 [SQL Database 資源限制](sql-database-resource-limits.md)。


## <a name="next-steps"></a>後續步驟

- 了解[單一資料庫資源](sql-database-single-database-resources.md)。
- 若要深入了解彈性集區，請參閱[彈性集區](sql-database-elastic-pool.md)。
- 了解 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)
* 了解 [DTU 與 eDTU](sql-database-what-is-a-dtu.md)。
* 若要深入了解監視 DTU 使用量，請參閱[監視和效能調整](sql-database-troubleshoot-performance.md)。

