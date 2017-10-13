---
title: "在適用於 PostgreSQL 的 Azure 資料庫中使用匯入和匯出移轉資料庫 | Microsoft Docs"
description: "描述如何將 PostgreSQL 資料庫擷取到指令碼檔案，並從該檔案將資料匯入目標資料庫。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/14/2017
ms.openlocfilehash: 5e306d516d04789e4526bfd09bf99139b83573ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>使用匯出和匯入來移轉 PostgreSQL 資料庫
您可以使用 [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) 將 PostgreSQL 資料庫擷取到指令碼檔案，並使用 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 從該檔案將資料匯入目標資料庫。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)，而且防火牆規則要允許存取其中的資料庫。
- 安裝 [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) 命令列公用程式
- 安裝 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 命令列公用程式

請遵循下列步驟來匯出和匯入 PostgreSQL 資料庫。

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>使用 pg_dump 建立指令碼檔案，其中包含要載入的資料
若要將內部部署或 VM 中的現有 PostgreSQL 資料庫匯出至 sql 指令碼檔，請在您現有的環境中執行下列命令︰
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
比方說，如果您有本機伺服器和名為 **testdb** 的資料庫
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postrgesql"></a>在目標「適用於 PostrgeSQL 的 Azure 資料庫」上匯入資料
您可以使用 psql 命令列和 -d, --dbname 參數，將資料匯入我們建立之適用於 PostrgeSQL 的 Azure 資料庫，並從 sql 檔案載入資料。
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
此範例使用 psql 公用程式和上一個步驟中名為 **testdb.sql** 的指令碼檔案，將資料匯入目標伺服器 **mypgserver-20170401.postgres.database.azure.com** 上的資料庫 **mypgsqldb**。
```bash
psql --file=testdb.sql --host=mypgserver-20170401.database.windows.net --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb
```

## <a name="next-steps"></a>後續步驟
- 若要使用傾印和還原移轉 PostgreSQL 資料庫，請參閱[使用傾印和還原來移轉 PostgreSQL 資料庫](howto-migrate-using-dump-and-restore.md)