---
title: "Azure SQL Database - 自動調整 | Microsoft Docs"
description: "Azure SQL Database 會分析 SQL 查詢，並自動針對使用者的工作負載調整。"
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: drasumic
editor: danimir
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 11/08/2017
ms.author: jovanpop
ms.openlocfilehash: 34aa035368a0516f9fa1c71c1bda491daf5ab370
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database 中的自動調整

Azure SQL Database 自動調整能運用人工智慧進行持續的效能調整，來提供尖峰的效能與穩定的工作負載。

自動調整是完整受管理的服務，它能使用內建的智慧機制來持續監視在資料庫上執行的查詢，並且自動改善查詢的效能。 此功能是透過讓資料庫針對變動的工作負載進行動態調適，並套用調整建議來達成。 自動調整能透過人工智慧從 Azure 上的所有資料庫進行水平學習，並能動態地改善其調整動作。 Azure SQL Database 在開啟自動調整的情況下執行得愈久，自動調整的效果便愈好。

Azure SQL Database 自動調整是您可以啟用以提供穩定且執行良好之工作負載的其中一個最重要的功能。

## <a name="what-can-automatic-tuning-do-for-you"></a>自動調整可以為您做什麼？

- Azure SQL Database 的自動效能調整
- 自動驗證效能提升
- 自動復原與自我修正
- 調整歷程記錄
- 適用於手動部署的調整動作 T-SQL 指令碼
- 主動式工作負載效能監視
- 以數十萬個資料庫向外延展的功能
- 為 DevOps 資源及擁有權總成本帶來正面影響

## <a name="safe-reliable-and-proven"></a>安全、可靠且經過實證

套用到 Azure SQL Databases 的調整作業，對於您最密集之工作負載的效能是完全安全的。 系統已經精心設計成不會干擾使用者工作負載。 自動調整建議只會在低使用率的時段套用。 系統也可以暫時地停用自動調整作業，以保護工作負載效能。 在這種情況下，「被系統停用」的訊息會顯示在 Azure 入口網站中。 自動調整會將工作負載考慮為具有最高資源優先順序。

自動調整是成熟的機制，且已在數十萬個於 Azure 上執行的資料庫上達到完美。 已套用的自動調整作業都會進行自動驗證，以確保對工作負載效能有正面的改進。 系統會動態地偵測迴歸的效能建議，並迅速地做出還原。 透過調整歷程記錄，使用者可清楚查看對每個 Azure SQL Database 所做出的調整改進。 

![自動調整的運作方式為何](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database 自動調整與 SQL Server 自動調整引擎共用其核心邏輯。 如需內建智慧機制的其他技術資訊，請參閱 [SQL Server 自動調整](https://docs.microsoft.com/en-us/sql/relational-databases/automatic-tuning/automatic-tuning) \(英文\)。

## <a name="use-automatic-tuning"></a>使用自動調整

自動調整必須在您的訂用帳戶上手動啟用。 若要使用 Azure 入口網站來啟用自動調整，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。

自動調整可透過自動套用調整建議 (包括自動驗證效能提升) 來自主地運作。 

如需更充分的控制，可以關閉自動套用調整建議的功能，並透過 Azure 入口網站手動套用調整建議。 您也可以僅使用此解決方案來檢視自動調整建議，並透過偏好的指令碼和工具來手動套用那些調整建議。 

如需自動調整運作方式的概觀與典型的使用案例，請觀賞內嵌影片：


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>自動調整選項

Azure SQL Database 中可用的自動調整選項有：
 1. **CREATE INDEX**，能識別可能改善您的工作負載效能、建立索引，並確認它們改善查詢效能的索引。
 2. **DROP INDEX**，可識別備援和重複的索引，及很長一段時間未使用的索引。
 3. **FORCE LAST GOOD PLAN**，可識別正在使用較前一個良好計畫更為緩慢之執行計畫的 SQL 查詢，並使用最後一個已知的良好計畫來取代迴歸的計畫。

Azure SQL Database 可識別 **CREATE INDEX**、**DROP INDEX** 和 **FORCE LAST GOOD PLAN** 建議，可以最佳化您的資料庫，並在 Azure 入口網站中顯示它們。 如需找出應變更的索引詳細資訊，可在 [Azure 入口網站中的尋找索引建議](sql-database-advisor-portal.md)中找到。 您可以使用入口網站手動套用建議，或者讓 Azure SQL Database 自動套用建議、監視變更後的工作負載，並確認建議能改善您的工作負載效能。

自動微調選項可以個別開啟或關閉每個資料庫，或可以在邏輯伺服器上設定，並在從伺服器繼承設定的每個資料庫上套用。 在伺服器上設定自動調整選項，並在伺服器中繼承資料庫上的設定，是設定自動調整的建議方法，因為它可簡化大量資料庫上自動調整選項的管理。

## <a name="next-steps"></a>後續步驟

- 若要在 Azure SQL Database 中啟用自動調整以管理您的工作負載，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。
- 若要手動檢閱及套用自動調整建議，請參閱[尋找及套用效能建議](sql-database-advisor-portal.md)。
- 若要了解自動調整中使用的內建智慧機制，請參閱[透過人工智慧調整 Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/) \(英文\)。
- 若要了解自動調整在 Azure SQL Database 和 SQL Server 2017 中運作的方式，請參閱 [SQL Server 自動調整](https://docs.microsoft.com/en-us/sql/relational-databases/automatic-tuning/automatic-tuning) \(英文\)。
