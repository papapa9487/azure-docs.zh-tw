---
title: "如何在適用於 PostgreSQL 的 Azure 資料庫中傾印和還原 | Microsoft Docs"
description: "描述如何在適用於 PostgreSQL 的 Azure 資料庫中，將 PostgreSQL 資料庫擷取到傾印檔案，並從 pg_dump 所建立的封存檔案還原 PostgreSQL 資料庫。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 28727117dbd37f9c595b488639a632b4c7404496
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>使用傾印和還原來移轉 PostgreSQL 資料庫
您可以使用 [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) 將 PostgreSQL 資料庫擷取到傾印檔案，並使用 [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) 從 pg_dump 所建立的封存檔案還原 PostgreSQL 資料庫。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)，而且防火牆規則要允許存取其中的資料庫。
- 安裝 [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) 命令列公用程式

請遵循下列步驟來傾印和還原 PostgreSQL 資料庫：

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>使用 pg_dump 建立傾印檔案，其中包含要載入的資料
若要備份內部部署或 VM 中的現有 PostgreSQL 資料庫，請執行下列命令︰
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
比方說，如果您有本機伺服器和名為 **testdb** 的資料庫
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>使用 pg_restore 將資料還原至目標「適用於 PostrgeSQL 的 Azure 資料庫」
建立目標資料庫後，您可以使用 pg_restore 命令和 -d、--dbname 參數，從傾印檔案將資料還原至目標資料庫。
```bash
pg_restore -v –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
在此範例中，請從傾印檔案 **testdb.dump**，將資料還原至目標伺服器 **mypgserver-20170401.postgres.database.azure.com** 上的資料庫 **mypgsqldb**。
```bash
pg_restore -v --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>後續步驟
- 若要使用匯出和匯入移轉 PostgreSQL 資料庫，請參閱[使用匯出和匯入移轉 PostgreSQL 資料庫](howto-migrate-using-export-and-import.md)