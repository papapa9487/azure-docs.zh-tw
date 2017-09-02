---
title: "針對效能問題進行疑難排解並將資料庫最佳化 | Microsoft Docs"
description: "對 SQL Database 套用效能建議以及了解如何獲得對資料庫執行之查詢效能的深入解析"
metakeywords: azure sql database performance monitoring recommendation
services: sql-database
documentationcenter: 
manager: jhubbard
author: jan-eng
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,monitor & tune
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: f9ae96cdc80c347593f229cb2fce3f2d4d8e7caf
ms.contentlocale: zh-tw
ms.lasthandoff: 06/15/2017

---
# <a name="troubleshoot-performance-issues-and-optimize-your-database"></a>針對效能問題進行疑難排解並將資料庫最佳化

遺漏索引與查詢最佳化不足是資料庫效能不佳的常見原因。 您會在本教學課程中學到：
> [!div class="checklist"]
> * 檢閱、套用和還原效能改進建議
> * 尋找具有高資源使用率的查詢
> * 尋找長時間執行的查詢

> 您必須對發生效能問題 (例如，遺漏索引) 的資料庫進行連續的工作負載，才能獲得建議。
>

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="review-and-apply-a-recommendation"></a>檢閱並套用建議

遵循下列步驟來套用系統對資料庫所提出的建議︰

1. 按一下資料庫刀鋒視窗中的 [效能建議] 功能表。

    ![效能建議](./media/sql-database-performance-tutorial/perf_recommendations.png)

2. 從建議清單中，選取作用中的建議。 在此範例中是「建立索引」。

    ![選取建議](./media/sql-database-performance-tutorial/create_index.png)

3. 按一下 [套用] 按鈕來套用建議。 (選擇性) 檢閱建議的詳細資料，然後按一下 [檢視指令碼] 按鈕來查看要執行的 T-SQL 指令碼。

    ![套用建議](./media/sql-database-performance-tutorial/apply.png)

4. [選擇性] 啟用自動調整以讓系統自動套用建議。

    ![自動調整](./media/sql-database-performance-tutorial/auto_tuning.png)

## <a name="revert-a-recommendation"></a>還原建議

Database Advisor 會監視系統所實作的每項建議。 如果某項建議無法改善工作負載，系統就會自動將該項建議還原。 您可以手動還原建議，但大部分情況下並不需要這麼做。 若要還原建議：

1. 移至效能建議功能表，然後選取其中一項已套用的建議。

    ![選取建議](./media/sql-database-performance-tutorial/select.png)

2. 在詳細資料檢視中，按一下 [還原]。

    ![還原建議](./media/sql-database-performance-tutorial/revert.png)

## <a name="find-the-query-that-consumes-the-most-resources"></a>尋找耗用最多資源的查詢

請遵循下列步驟來尋找耗用最多資源的查詢︰

1. 按一下資料庫刀鋒視窗中的 [查詢效能深入解析] 功能表。

    ![查詢深入解析](./media/sql-database-performance-tutorial/query_perf_insights.png)

2. 選取資源類型。

    ![查詢深入解析](./media/sql-database-performance-tutorial/select_resource_type.png)

3. 選取資料表中的第一個查詢。

    ![查詢深入解析](./media/sql-database-performance-tutorial/select_query.png)

4. 檢閱查詢詳細資料。

    ![查詢深入解析](./media/sql-database-performance-tutorial/query_details.png)

## <a name="find-the-longest-running-query"></a>尋找執行最久的查詢

1. 移至 [查詢效能深入解析]，然後選取 [長時間執行的查詢] 索引標籤。

    ![查詢深入解析](./media/sql-database-performance-tutorial/long_running.png)

3. 選取資料表中的第一個查詢。

    ![查詢深入解析](./media/sql-database-performance-tutorial/select_first_query.png)

4. 檢閱查詢詳細資料。

    ![查詢深入解析](./media/sql-database-performance-tutorial/review_query_details.png)



## <a name="next-steps"></a>後續步驟 
遺漏索引與查詢最佳化不足是資料庫效能不佳的常見原因。 在本教學課程中，您已了解：
> [!div class="checklist"]
> * 檢閱、套用和還原效能改進建議
> * 尋找具有高資源使用率的查詢
> * 尋找長時間執行的查詢

[SQL Database 效能調整秘訣](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-performance)

