---
title: "監視 XTP 記憶體內儲存體 | Microsoft Docs"
description: "估計和監視 XTP 記憶體內儲存體使用量、容量；解決容量錯誤 41823"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jodebrui
ms.openlocfilehash: 613a9ced91d71cc9a65ea67e6ede1a78a03b4bd5
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="monitor-in-memory-oltp-storage"></a>監視記憶體內部 OLTP 儲存體
使用 [記憶體內部 OLTP](sql-database-in-memory.md)時，記憶體最佳化資料表中的資料和資料表變數位於記憶體內部 OLTP 儲存體中。 每個進階服務層都有最大的記憶體內部 OLTP 儲存體大小，如[單一資料庫資源限制](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels)與[彈性集區資源限制](sql-database-resource-limits.md#elastic-pool-change-storage-size)所說明。 一旦超過此限制，插入和更新作業可能會開始出錯 (錯誤碼 41823)。 屆時您將需要刪除資料以回收記憶體，或升級您的資料庫的效能層。

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>判斷資料是否在記憶體內部儲存容量上限內
判斷不同進階服務層的儲存體上限。 請參閱[單一資料庫資源限制](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels)和[彈性集區資源限制](sql-database-resource-limits.md#elastic-pool-change-storage-size)。

估計記憶體最佳化資料表的記憶體需求，其方式如同在 Azure SQL Database 中估計 SQL Server 的記憶體需求。 花幾分鐘的時間來檢閱 [MSDN](https://msdn.microsoft.com/library/dn282389.aspx)上的該主題。

請注意，資料表和資料表變數資料列以及索引都會計入最大的使用者資料大小。 此外，ALTER TABLE 需要足夠的空間來建立新版的完整資料表及其索引。

## <a name="monitoring-and-alerting"></a>監視和警示
您可以在 [Azure 入口網站](https://portal.azure.com/)中，透過效能層的儲存體上限百分比來監視記憶體內部儲存體使用情形： 

1. 在 [資料庫] 刀鋒視窗上，找出 [資源使用率方塊] 並按一下 [編輯]。
2. 選取計量 `In-Memory OLTP Storage percentage`。
3. 若要新增警示，請按一下 [資源使用率] 方塊以開啟 [計量] 刀鋒視窗，然後按一下 [新增警示]。

或使用下列查詢來顯示記憶體內部儲存體使用率：

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>更正記憶體不足情況 - 錯誤 41823
記憶體不足導致插入、更新和建立作業失敗，並出現錯誤訊息 41823。

錯誤訊息 41823 指出記憶體最佳化資料表和資料表變數已超過大小上限。

若要解決此錯誤，您可以：

* 從記憶體最佳化資料表中刪除資料，可能會將資料卸載至傳統、以磁碟為基礎的資料表；或者，
* 將服務層升級至具有足夠記憶體內部記憶體的服務層，以便儲存您需要保留在記憶體最佳化資料表中的資料。

## <a name="next-steps"></a>後續步驟
如需監視指引，請參閱[使用動態管理檢視監視 Azure SQL Database](sql-database-monitoring-with-dmvs.md)。
