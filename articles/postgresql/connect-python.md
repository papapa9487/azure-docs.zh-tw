---
title: "從 Python 連線至 Azure Database for PostgreSQL | Microsoft Docs"
description: "本快速入門提供 Python 程式碼範例，您可用於從 Azure Database for PostgreSQL 連線及查詢資料。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: python
ms.topic: hero-article
ms.date: 08/10/2017
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 0d52a7728e2292946e9328065b973ca7ad37b4f5
ms.contentlocale: zh-tw
ms.lasthandoff: 08/10/2017

---
# <a name="azure-database-for-postgresql-use-python-to-connect-and-query-data"></a>Azure Database for PostgreSQL︰使用 Python 連線及查詢資料
此快速入門示範如何使用 [Python](https://python.org) 來連線至 Azure Database for PostgreSQL，然後從 Mac OS、Ubuntu Linux 和 Windows 平台使用 SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。 本文中的步驟假設您已熟悉使用 Python 進行開發，但不熟悉 Azure Database for PostgreSQL。

## <a name="prerequisites"></a>必要條件
本快速入門使用在以下任一指南中建立的資源作為起點︰
- [建立 DB - 入口網站](quickstart-create-server-database-portal.md)
- [建立 DB - CLI](quickstart-create-server-database-azure-cli.md)

您也會需要：
- 已安裝 [python](https://www.python.org/downloads/)
- 已安裝 [pip](https://pip.pypa.io/en/stable/installing/) 套件 (如果您使用從 [python.org](https://python.org) 下載的 Python 2 >=2.7.9 或 Python 3 >=3.4 二進位檔，便已安裝 pip，但您需要升級 pip)。

## <a name="install-the-python-connection-libraries-for-postgresql"></a>安裝適用於 PostgreSQL 的 Python 連線程式庫
安裝 [psycopg2](http://initd.org/psycopg/docs/install.html) 套件，這可讓您連線及查詢資料庫。 psycopg2 [可在 PyPI 上取得](https://pypi.python.org/pypi/psycopg2/)，其形式為適用於大多數常見平台 (Linux、OSX、Windows) 的 [wheel](http://pythonwheels.com/) 套件，以便使用 pip install 來取得模組的二進位版本 (包括所有相依性)：

```cmd
pip install psycopg2
```
務必使用 pip 的最新版本 (您可以使用諸如 `pip install -U pip` 將它升級)

## <a name="get-connection-information"></a>取得連線資訊
取得連線到 Azure Database for PostgreSQL 所需的連線資訊。 您需要完整的伺服器名稱和登入認證。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後搜尋您剛建立的伺服器 **mypgserver-20170401**。
3. 按一下伺服器名稱 [mypgserver-20170401]。
4. 選取伺服器的 [概觀] 頁面。 記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。
 ![Azure Database for PostgreSQL - 伺服器管理員登入](./media/connect-python/1-connection-string.png)
5. 如果您忘記伺服器登入資訊，請瀏覽至 [概觀] 頁面來檢視伺服器管理員登入名稱，並視需要重設密碼。

## <a name="how-to-run-python-code"></a>如何執行 Python 程式碼
- 使用慣用的文字編輯器，建立稱為 postgres.py 的新檔案，並將它儲存到專案資料夾。 將下面顯示的程式碼範例複製並貼入文字檔中。 以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 然後儲存檔案。 將檔案儲存在 Windows OS 中時，請一定要選取 UTF-8 編碼。 
- 若要執行程式碼，請啟動命令提示字元或 bash shell。 將目錄切換到專案資料夾，例如 `cd postgresql`。 然後，鍵入後接檔案名稱 (例如 `python postgres.py`) 的 python 命令。

> [!NOTE]
> 從 Python 第 3 版開始，您可能會在執行下面的程式碼區塊時看到錯誤 `SyntaxError: Missing parentheses in call to 'print'`。 如果發生這種情況，請將每個 `print "string"` 命令呼叫取代為使用括號的函式呼叫，例如 `print("string")`。

## <a name="connect-create-table-and-insert-data"></a>連線、建立資料表及插入資料
使用 [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) 函式搭配 **INSERT** SQL 陳述式，利用下列程式碼來連線和載入資料。 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 函式用來對 PostgreSQL 資料庫執行 SQL 查詢。 以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="read-data"></a>讀取資料
使用 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 函式搭配 **SELECT** SQL 陳述式，利用下列程式碼來讀取插入的資料。 此函式會接受查詢並傳回結果集，而您可以使用 [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) 反覆查詢結果集。 以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>更新資料
使用下列程式碼，藉由使用 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 函式搭配 **UPDATE** SQL 陳述式來更新您先前插入的清查資料列。 以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>刪除資料
使用下列程式碼，藉由使用 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 函式搭配 **DELETE** SQL 陳述式來刪除您先前插入的清查項目。 以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print "Deleted 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用匯出和匯入來移轉資料庫](./howto-migrate-using-export-and-import.md)

