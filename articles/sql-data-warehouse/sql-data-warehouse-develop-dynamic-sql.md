---
title: "SQL 資料倉儲中的動態 SQL | Microsoft Docs"
description: "使用 Azure SQL 資料倉儲中的動態 SQL 以開發解決方案的秘訣。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: a948c2c3-3cd1-4373-90a9-79e59414b778
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29228676373aee8dbc7b1b2a7d92ffc978333804
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL 資料倉儲中的動態 SQL
開發 SQL 資料倉儲的應用程式程式碼時，您可能須使用動態 SQL，以協助提供有彈性的泛型模組化解決方案。 不過，SQL 資料倉儲目前不支援 Blob 資料類型。 這可能會限制字串的大小，因為 blob 類型包括 varchar(max) 和 nvarchar(max) 類型。 如果您在建立超大型字串時，曾在應用程式的程式碼中使用這些類型，您必須將這些程式碼分成許多區塊，並以 EXEC 陳述式取代。

以下是簡單的範例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字串簡短，您可以像平常一樣使用 [sp_executesql][sp_executesql]。

> [!NOTE]
> 以動態 SQL 執行的陳述式仍會受限於所有的 TSQL 驗證規則。
> 
> 

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀][development overview]。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
