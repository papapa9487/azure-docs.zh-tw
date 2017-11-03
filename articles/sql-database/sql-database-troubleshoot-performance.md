---
title: "監視和效能微調 - Azure SQL Database | Microsoft Docs"
description: "透過評估和改進來調整 Azure SQL Database 效能的秘訣。"
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "sql 效能調整，資料庫效能調整，sql 效能調整秘訣，sql 資料庫效能調整"
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: v-shysun
ms.openlocfilehash: 4eb344bf9e87ef1a8f469268211588e51696164e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="monitoring-and-performance-tuning"></a>監視和效能微調

Azure SQL Database 是自動管理和彈性的資料服務，您可以輕鬆監視使用狀況、新增或移除資源 (CPU、資源、IO)、尋找可改善資料庫效能的建議，或讓資料庫調整您的工作負載並自動最佳化效能。

本文提供 Azure SQL Database 中可用的監視和效能微調選項概觀。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>針對資料庫效能進行監視和疑難排解

Azure SQL Database 可讓您輕鬆地監視資料庫使用量並找出可能造成效能問題的查詢。 您可以使用 Azure 入口網站或系統檢視來監視資料庫效能。 您有下列選項可以用來針對資料庫效能進行監視和疑難排解：

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Database]、選取資料庫，然後使用監視圖表尋找接近其上限的資源。 預設會顯示 DTU 耗用量。 按一下 [編輯]  來變更所顯示的時間範圍和值。
2. 使用[查詢效能深入解析](sql-database-query-performance.md)來找出花費最多資源的查詢。
3. 您可以使用動態管理檢視 (DMV)、擴充的事件 (`XEvents`) 和 SSMS 中的查詢存放區即時取得效能參數。

如果您使用這些報告或檢視發現某些問題，請參閱[效能指引主題](sql-database-performance-guidance.md)來尋找可用來改善 Azure SQL Database 效能的技巧。

> [!IMPORTANT] 
> 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
>

## <a name="optimize-database-to-improve-performance"></a>最佳化要改進效能的資料庫

Azure SQL Database 可讓您檢閱[效能微調建議](sql-database-advisor.md)，找出不必變更資源卻能改善和最佳化查詢效能的機會。 遺漏索引與查詢最佳化不足是資料庫效能不佳的常見原因。 您可以套用這些調整建議來改善工作負載的效能。
您也可以套用所有發現的建議並驗證能否改善資料庫效能，讓 Azure SQL Database [自動最佳化查詢](sql-database-automatic-tuning.md)。 您可以使用下列選項來改善資料庫效能：

1. 使用 [SQL Database Advisor](sql-database-advisor-portal.md) 來檢視建立和卸除索引、參數化查詢，以及修正結構描述問題的建議。
2. [啟用自動調整](sql-database-automatic-tuning-enable.md)並讓 Azure SQL Database 自動修正發現的效能問題。

## <a name="improving-database-performance-with-more-resources"></a>使用更多資源提升資料庫效能

最後，如果沒有可改善資料庫效能的可操作項目，您可以變更 Azure SQL Database 中可用的資源數量。 您可以隨時變更獨立資料庫的[服務層](sql-database-service-tiers.md)或增加彈性集區的 eDTU。
1. 若是獨立資料庫，您可以視需要[變更服務層級](sql-database-service-tiers.md)以提升資料庫效能。
2. 若是多個資料庫，請考慮使用[彈性集區](sql-database-elastic-pool-guidance.md)來自動調整資源。

## <a name="tune-and-refactor-application-or-database-code"></a>調整和重構應用程式或資料庫程式碼

您可以變更應用程式程式碼，針對工作負載以最佳方式使用資料庫、變更索引、強制執行計劃，或使用提示來手動調整資料庫。 在[效能指引主題](sql-database-performance-guidance.md)文章中可找到手動調整或重寫程式碼的一些方針和秘訣。


## <a name="next-steps"></a>後續步驟

- 若要在 Azure SQL Database 中啟用自動調整，並讓自動調整功能完全管理您的工作負載，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。
- 若要使用手動調整，您可以檢閱 [Azure 入口網站中的調整建議](sql-database-advisor-portal.md)並手動套用可改善查詢效能的建議。
- 變更 [Azure SQL Database 服務層](sql-database-performance-guidance.md)來變更資料庫中可用的資源
