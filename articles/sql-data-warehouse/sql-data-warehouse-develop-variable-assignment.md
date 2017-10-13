---
title: "在 SQL 資料倉儲中指派變數 |Microsoft Docs"
description: "在 Azure SQL 資料倉儲中指派 TRANSACT-SQL 變數以便開發解決方案的秘訣。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 81ddc7cf-a6ba-4585-91a3-b6ea50f49227
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 045d5148cd3f12dac63c961ccf7c953d355ed725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="assign-variables-in-sql-data-warehouse"></a>在 SQL 資料倉儲中指派變數
SQL 資料倉儲中的變數是使用 `DECLARE` 陳述式或 `SET` 陳述式進行設定的。

下列各項是設定變數值的完全有效方式：

## <a name="setting-variables-with-declare"></a>使用 DECLARE 設定宣告
使用 DECLARE 初始化變數是在 SQL 資料倉儲中設定變數值的其中一種最具彈性的方式。

```sql
DECLARE @v  int = 0
;
```

您也可以使用 DECLARE，一次設定一個以上的變數。 您可以使用 `SELECT` 或 `UPDATE` 來執行此作業：

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

您無法在相同的 DECLARE 陳述式中初始化並使用變數。 為了說明這點，**不**允許以下範例，因為 @p1 已在相同的 DECLARE 陳述式中初始化和使用。 這會導致錯誤。

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>使用 SET 設定值
SET 是設定單一變數時很常見的方法。

下列所有範例都是使用 SET 設定變數的有效方式：

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

您一次只能使用 SET 設定一個變數。 不過，如上所示，可允許複合運算子。

## <a name="limitations"></a>限制
您無法使用 SELECT 或 UPDATE 進行變數指派。

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀][development overview]。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
