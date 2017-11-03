---
title: "Azure SQL Database - 自動調整 | Microsoft Docs"
description: "Azure SQL Database 會分析 SQL 查詢，並自動針對使用者的工作負載調整。"
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 09/19/2017
ms.author: jovanpop
ms.openlocfilehash: 10f8cca8e519b28f0fe29605419b860f73e04a87
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database 中的自動調整

Azure SQL Database 是完全受管理的資料服務，可監視在資料庫上執行的查詢，並可自動提升資料庫工作負載效能。 Azure SQL Database 具備的內建[自動調整](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)智慧機制可自動調整，並可透過動態調整工作負載的資料庫來提升查詢效能。 您能在 Azure SQL Database 上啟用以最佳化查詢效能的最重要功能之一，可能是 Azure SQL Database 中的自動調整功能。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="automatic-tuning-options"></a>自動調整選項

Azure SQL Database 中的可用[自動調整](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)選項有：
 1. **CREATE INDEX**，能識別可能改善您的工作負載效能、建立索引，並確認它們改善查詢效能的索引。
 2. **DROP INDEX**，可識別備援和重複的索引，及很長一段時間未使用的索引。
 3. **PLAN REGRESSION CORRECTION**，可識別使用的執行計畫低於前一個良好計畫的 SQL 查詢，並使用最後一個已知的良好計畫而非迴歸計畫。

Azure SQL Database 可識別 **CREATE INDEX**、**DROP INDEX** 和 **PLAN REGRESSION CORRECTION** 建議，可以最佳化您的資料庫，並在 Azure 入口網站中顯示它們。 如需找出應變更的索引詳細資訊，可在 [Azure 入口網站中的尋找索引建議](sql-database-advisor-portal.md)中找到。 您可以使用入口網站手動套用建議，或者讓 Azure SQL Database 自動套用建議、監視變更後的工作負載，並確認建議能改善您的工作負載效能。

自動微調選項可以個別開啟或關閉每個資料庫，或可以在邏輯伺服器上設定，並在從伺服器繼承設定的每個資料庫上套用。 在伺服器上設定[自動微調](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)選項，並繼承伺服器的資料庫設定是設定自動調整的建議方法，因為它可簡化大量資料庫上的自動微調選項的管理。

如需使用 Azure 入口網站以[啟用自動調整](sql-database-automatic-tuning-enable.md)的步驟，請參閱本文章。

## <a name="next-steps"></a>後續步驟

- 若要在 Azure SQL Database 中啟用自動調整，並讓自動調整功能完全管理您的工作負載，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。
- 若要使用手動調整，您可以檢閱 [Azure 入口網站中的調整建議](sql-database-advisor-portal.md)並手動套用可改善查詢效能的建議。
- 深入了解用來微調 [Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/) 的內建智慧。
- 深入了解 Azure SQL Database 和 SQL Server 2017 中的[自動微調](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)。
