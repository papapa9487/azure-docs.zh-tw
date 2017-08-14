---
title: "從 C++ 連線到 Azure Database for MySQL | Microsoft Docs"
description: "本快速入門提供 C++ 程式碼範例，您可用於從 Azure Database for MySQL 連線及查詢資料。"
services: mysql
author: seanli1988
ms.author: seal
manager: janders
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.devlang: C++
ms.topic: hero-article
ms.date: 08/03/2017
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 63388b83b913d95136140fa4c56af0dbebbdad81
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---

# <a name="azure-database-for-mysql-use-connectorc-to-connect-and-query-data"></a>Azure Database for MySQL︰使用 Connector/C++ 來連線及查詢資料
本快速入門示範如何使用 C++ 應用程式來連線到 Azure Database for MySQL。 它會顯示如何使用 SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。 本文中的步驟假設您已熟悉使用 C++ 進行開發，但不熟悉 Azure Database for MySQL。

## <a name="prerequisites"></a>必要條件
本快速入門使用在以下任一指南中建立的資源作為起點︰
- [使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫伺服器](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [使用 Azure CLI 建立適用於 MySQL 的 Azure 資料庫伺服器](./quickstart-create-mysql-server-database-using-azure-cli.md)

您也需要：
- 安裝 [.NET Framework](https://www.microsoft.com/net/download)
- 安裝 [Visual Studio](https://www.visualstudio.com/downloads/)
- 安裝 [MySQL Connector/C++](https://dev.mysql.com/downloads/connector/cpp/) 
- 安裝 [Boost](http://www.boost.org/)

## <a name="install-visual-studio-and-net"></a>安裝 Visual Studio 和 .NET
本節中的步驟假設您已熟悉使用 .NET 進行開發。

### <a name="windows"></a>**Windows**
1. 安裝 Visual Studio 2017 Community，這是功能完整且可擴充的免費 IDE，用以建立適用於 Android、iOS、Windows 以及 Web 和資料庫應用程式和雲端服務的新式應用程式。 您可以安裝完整的 .NET Framework 或只安裝 .NET Core。 快速入門中的程式碼片段均可搭配使用。 如果您已在電腦上安裝 Visual Studio，請略過後續兩個步驟。
   - 下載 [Visual Studio 2017 安裝程式](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)。 
   - 執行安裝程式並依照安裝提示來完成安裝。

### <a name="configure-visual-studio"></a>**設定 Visual Studio**
1. 從 Visual Studio 的專案屬性 > 組態屬性 > C/C++ > 連結器 > 一般 > 其他 library 目錄，新增 c++ 連接器的 lib\opt 目錄 (也就是 C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\lib\opt)。
2. 從 Visual Studio 的專案屬性 > 組態屬性 > C/C++ > 一般 > 其他 include 目錄
   - 新增 c++ 連接器的 include/ 目錄 (也就是 C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\include\)
   - 新增 Boost 程式庫的根目錄 (也就是 C:\boost_1_64_0\)
3. 從 Visual Studio 的專案屬性 > 組態屬性 > C/C++ > 連結器 > 輸入 > 其他相依性，將 mysqlcppconn.lib 新增到文字欄位中
4. 從步驟 3 的 c++ 連接器程式庫資料夾將 mysqlcppconn.dll 複製到與應用程式可執行檔相同的目錄，或將它新增至環境變數，您的應用程式便可找到它。

## <a name="get-connection-information"></a>取得連線資訊
取得連線到 Azure Database for MySQL 所需的連線資訊。 您需要完整的伺服器名稱和登入認證。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後搜尋您所建立的伺服器，例如 **myserver4demo**。
3. 按一下伺服器名稱。
4. 選取伺服器的 [屬性] 頁面。 記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。
 ![Azure Database for MySQL 伺服器名稱](./media/connect-cpp/1_server-properties-name-login.png)
5. 如果您忘記伺服器登入資訊，請瀏覽至 [概觀] 頁面來檢視伺服器管理員登入名稱，並視需要重設密碼。

## <a name="connect-create-table-and-insert-data"></a>連線、建立資料表及插入資料
使用下列程式碼搭配 **CREATE TABLE** 和 **INSERT INTO** SQL 陳述式來連線和載入資料。 此程式碼使用 sql::Driver 類別搭配 connect() 方法來建立 MySQL 連線。 然後，程式碼會使用 createStatement() 和 execute() 方法來執行資料庫命令。 

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::Statement *stmt;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    stmt = con>createStatement();
    stmt>execute("DROP TABLE IF EXISTS inventory");
    cout << "Finished dropping table (if existed)" << endl;
    stmt>execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
    cout << "Finished creating table" << endl;
    delete stmt;

    pstmt = con>prepareStatement("INSERT INTO inventory(name, quantity) VALUES(?,?)");
    pstmt>setString(1, "banana");
    pstmt>setInt(2, 150);
    pstmt>execute();
    cout << "One row inserted." << endl;

    pstmt>setString(1, "orange");
    pstmt>setInt(2, 154);
    pstmt>execute();
    cout << "One row inserted." << endl;

    pstmt>setString(1, "apple");
    pstmt>setInt(2, 100);
    pstmt>execute();
    cout << "One row inserted." << endl;
    
    delete pstmt;   
    delete con;
    system("pause");
    return 0;

```

## <a name="read-data"></a>讀取資料

使用下列程式碼搭配 **SELECT** SQL 陳述式來連線和讀取資料。 此程式碼使用 sql::Driver 類別搭配 connect() 方法來建立 MySQL 連線。 然後，程式碼會使用 prepareStatement() 和 executeQuery() 方法來執行 select 命令。 程式碼最後會使用 next() 前往結果中的記錄。 接下來程式碼會使用 getInt() 和 getString() 來剖析記錄中的值。

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }   

//  select  
    pstmt = con>prepareStatement("SELECT * FROM inventory;");
    result = pstmt>executeQuery();  
    
    while (result>next())
        printf("Reading from table=(%d, %s, %d)\n", result>getInt(1), result>getString(2).c_str(), result>getInt(3));   
    
    delete result;
    delete pstmt;   
    delete con;
    system("pause");
    return 0;
}
```

## <a name="update-data"></a>更新資料
使用下列程式碼搭配 **UPDATE** SQL 陳述式來連線和讀取資料。 此程式碼使用 sql::Driver 類別搭配 connect() 方法來建立 MySQL 連線。 然後，程式碼會使用 prepareStatement() 和 executeQuery() 方法來執行 update 命令。 

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }   

    //update
    pstmt = con>prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?");
    pstmt>setInt(1, 200);
    pstmt>setString(2, "banana");
    pstmt>executeQuery();
    printf("Row updated\n");
    
    delete con;
    delete pstmt;
    system("pause");
    return 0;
}
```


## <a name="delete-data"></a>刪除資料
使用下列程式碼搭配 **DELETE** SQL 陳述式來連線和讀取資料。 此程式碼使用 sql::Driver 類別搭配 connect() 方法來建立 MySQL 連線。 然後，程式碼會使用 prepareStatement() 和 executeQuery() 方法來執行 update 命令。

以建立伺服器和資料庫時所指定的值，取代主機、資料庫名稱、使用者和密碼參數。 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
        
    //delete
    pstmt = con>prepareStatement("DELETE FROM inventory WHERE name = ?");
    pstmt>setString(1, "orange");
    result = pstmt>executeQuery();
    printf("Row deleted\n");    
    
    delete pstmt;
    delete con;
    delete result;
    system("pause");
    return 0;
}
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用傾印和還原來將 MySQL 資料庫移轉至適用於 MySQL 的 Azure 資料庫](concepts-migrate-dump-restore.md)

