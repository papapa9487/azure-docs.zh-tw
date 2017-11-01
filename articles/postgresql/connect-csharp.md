---
title: "從 C# 連線至 Azure Database for PostgreSQL | Microsoft Docs"
description: "本快速入門提供 C# (.NET) 程式碼範例，可讓您連線至 Azure Database for PostgreSQL 來查詢資料。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: csharp
ms.topic: quickstart
ms.date: 06/23/2017
ms.openlocfilehash: 1ad2e586960be1fcb302c5459975f5a11dd8068e
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2017
---
# <a name="azure-database-for-postgresql-use-net-c-to-connect-and-query-data"></a>Azure Database for PostgreSQL︰使用 .NET (C#) 連線及查詢資料
本快速入門示範如何使用 C# 應用程式來連線到 Azure Database for PostgreSQL。 它會顯示如何使用 SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。 本文中的步驟假設您已熟悉使用 C# 進行開發，但不熟悉 Azure Database for PostgreSQL。

## <a name="prerequisites"></a>必要條件
本快速入門使用在以下任一指南中建立的資源作為起點︰
- [建立 DB - 入口網站](quickstart-create-server-database-portal.md)
- [建立 DB - CLI](quickstart-create-server-database-azure-cli.md)

您也需要：
- 安裝 [.NET Framework](https://www.microsoft.com/net/download)。 請依照連結文章中的步驟，特別為您的平台 (Windows、Ubuntu Linux 或 macOS) 安裝 .NET。 
- 安裝 [Visual Studio](https://www.visualstudio.com/downloads/) 或 Visual Studio Code 以輸入及編輯程式碼。
- 安裝 [Npgsql](http://www.npgsql.org/doc/index.html) 程式庫，如下所述。

## <a name="install-npgsql-references-into-your-visual-studio-solution"></a>將 Npgsql 參考安裝在 Visual Studio 解決方案中
若要從 C# 應用程式連線到 PostgreSQL，請使用稱為 Npgsql 的開放原始碼 ADO.NET 程式庫。 NuGet 有助於輕鬆下載和管理參考。

1. 建立新的 C# 解決方案或開啟現有解決方案： 
   - 在 Visual Studio 中，按一下 [檔案] 功能表的 [新增] > [專案]，以建立解決方案。
   - 在 [新增專案] 對話方塊中，展開 [範本] > [Visual C#]。 
   - 選擇適當的範本，例如 [主控台應用程式 (.NET Core)]。

2. 使用 Nuget 套件管理員來安裝 Npgsql：
   - 按一下 [工具] 功能表 > [Nuget 套件管理員] > [套件管理員主控台]。
   - 在 [套件管理員主控台] 中，輸入 `Install-Package Npgsql`
   - 安裝命令會下載 Npgsql.dll 和相關組件，並將它們新增為解決方案中的相依性。

## <a name="get-connection-information"></a>取得連線資訊
取得連線到 Azure Database for PostgreSQL 所需的連線資訊。 您需要完整的伺服器名稱和登入認證。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後搜尋您所建立的伺服器，例如 **mypgserver-20170401**。
3. 按一下伺服器名稱 [mypgserver-20170401]。
4. 選取伺服器的 [概觀] 頁面。 記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。
 ![Azure Database for PostgreSQL - 伺服器管理員登入](./media/connect-csharp/1-connection-string.png)
5. 如果您忘記伺服器登入資訊，請瀏覽至 [概觀] 頁面來檢視**伺服器管理員登入名稱**，並視需要重設密碼。

## <a name="connect-create-table-and-insert-data"></a>連線、建立資料表及插入資料
使用下列程式碼搭配 **CREATE TABLE** 和 **INSERT INTO** SQL 陳述式來連線和載入資料。 此程式碼使用 NpgsqlCommand 類別搭配 [Open()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) 方法來對 PostgreSQL 資料庫建立連線。 然後，程式碼會使用 [CreateCommand()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) 方法，設定 CommandText 屬性，並呼叫 [ExecuteNonQuery()](http://www.npgsql.org/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) 方法來執行資料庫命令。 

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Npgsql;

namespace Driver
{
    public class AzurePostgresCreate
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mypgserver-20170401.postgres.database.azure.com";
        private static string User = "mylogin@mypgserver-20170401";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4}; SSL Mode=Prefer; Trust Server Certificate=true",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            var conn = new NpgsqlConnection(connString);

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "DROP TABLE IF EXISTS inventory;";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished dropping table (if existed)");

            command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished creating table");

            command.CommandText =
                String.Format(
                    @"
                                INSERT INTO inventory (name, quantity) VALUES ({0}, {1});
                                INSERT INTO inventory (name, quantity) VALUES ({2}, {3});
                                INSERT INTO inventory (name, quantity) VALUES ({4}, {5});
                            ",
                    "\'banana\'", 150,
                    "\'orange\'", 154,
                    "\'apple\'", 100
                    );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows inserted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="read-data"></a>讀取資料
使用下列程式碼搭配 **SELECT** SQL 陳述式來連線和讀取資料。 此程式碼使用 NpgsqlCommand 類別搭配 [Open()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) 方法來建立 PostgreSQL 連線。 然後，程式碼會使用 [CreateCommand()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) 和 [ExecuteReader()](http://www.npgsql.org/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteReader) 來執行資料庫命令。 接下來程式碼會使用 [Read()](http://www.npgsql.org/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_Read) 前往結果中的記錄。 最後，程式碼會使用 [GetInt32()](http://www.npgsql.org/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_GetInt32_System_Int32_) 和 [GetString()](http://www.npgsql.org/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_GetString_System_Int32_) 來剖析記錄中的值。

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Npgsql;

namespace Driver
{
    public class AzurePostgresRead
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mypgserver-20170401.postgres.database.azure.com";
        private static string User = "mylogin@mypgserver-20170401";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            var conn = new NpgsqlConnection(connString);

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "SELECT * FROM inventory;";

            var reader = command.ExecuteReader();
            while (reader.Read())
            {
                Console.WriteLine(
                    string.Format(
                        "Reading from table=({0}, {1}, {2})",
                        reader.GetInt32(0).ToString(),
                        reader.GetString(1),
                        reader.GetInt32(2).ToString()
                        )
                    );
            }

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```


## <a name="update-data"></a>更新資料
使用下列程式碼搭配 **UPDATE** SQL 陳述式來連線和更新資料。 此程式碼使用 NpgsqlCommand 類別搭配 [Open()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) 方法來建立 PostgreSQL 連線。 然後，程式碼會使用 [CreateCommand()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) 方法，設定 CommandText 屬性，並呼叫 [ExecuteNonQuery()](http://www.npgsql.org/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) 方法來執行資料庫命令。

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Npgsql;

namespace Driver
{
    public class AzurePostgresUpdate
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mypgserver-20170401.postgres.database.azure.com";
        private static string User = "mylogin@mypgserver-20170401";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            var conn = new NpgsqlConnection(connString);

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
            String.Format("UPDATE inventory SET quantity = {0} WHERE name = {1};",
                200,
                "\'banana\'"
                );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows updated={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```


## <a name="delete-data"></a>刪除資料
使用下列程式碼搭配 **DELETE** SQL 陳述式來連線和刪除資料。 

此程式碼使用 NpgsqlCommand 類別搭配 [Open()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) 方法來對 PostgreSQL 資料庫建立連線。 然後，程式碼會使用 [CreateCommand()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) 方法，設定 CommandText 屬性，並呼叫 [ExecuteNonQuery()](http://www.npgsql.org/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) 方法來執行資料庫命令。

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Npgsql;

namespace Driver
{
    public class AzurePostgresDelete
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mypgserver-20170401.postgres.database.azure.com";
        private static string User = "mylogin@mypgserver-20170401";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            var conn = new NpgsqlConnection(connString);

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
            String.Format("DELETE FROM inventory WHERE name = {0};",
                "\'orange\'");
            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows deleted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用匯出和匯入來移轉資料庫](./howto-migrate-using-export-and-import.md)
