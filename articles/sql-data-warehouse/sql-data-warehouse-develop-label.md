---
title: "在 SQL 資料倉儲中使用標籤來檢測查詢 | Microsoft Docs"
description: "在 Azure SQL 資料倉儲中使用標籤來檢測查詢以開發解決方案的秘訣。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 44988de8-04c1-4fed-92be-e1935661a4e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 9e75bbe528a427724a623305fbd45e2277e9d0af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>在 SQL 資料倉儲中使用標籤來檢測查詢
SQL 資料倉儲支援稱為查詢標籤的概念。 繼續進行之前，讓我們看看一個範例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最後一行將字串 'My Query Label' 標記為查詢。 這是特別有幫助的動作，因為標籤可透過 DMV 查詢。 這提供一種機制，可追蹤問題查詢，也可以協助透過 ETL 執行識別進度。

此時良好的命名慣例非常有幫助。 例如，類似 ' PROJECT : PROCEDURE : STATEMENT : COMMENT' 的項目有助於在原始檔控制中的幾乎所有程式碼中唯一識別查詢。

若要根據標籤搜尋，您可以使用下列使用動態管理檢視的查詢：

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 請務必在查詢時以方括弧或雙引號括住文字標籤。 標籤是一個保留的文字，而且如果未分隔，就會造成錯誤。
> 
> 

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀][development overview]。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
