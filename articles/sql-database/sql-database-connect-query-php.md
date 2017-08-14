---
title: "使用 .PHP 查詢 Azure SQL Database | Microsoft Docs"
description: "本主題說明如何使用 PHP 來建立連線到 Azure SQL Database 的程式，並使用 Transact-SQL 陳述式查詢。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a 22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: hero-article
ms.date: 08/08/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3a43472ad2be4a0fd6f7126f72433acd8b5f25fd
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---
# <a name="use-php-to-query-an-azure-sql-database"></a>使用 PHP 查詢 Azure SQL 資料庫

此快速入門教學課程示範如何使用 [PHP](http://php.net/manual/en/intro-whatis.php) 來建立程式以連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門教學課程，請確定您具有下列項目︰

- Azure SQL Database。 本快速入門使用其中一個快速入門建立的資源︰ 

   - [建立 DB - 入口網站](sql-database-get-started-portal.md)
   - [建立 DB - CLI](sql-database-get-started-cli.md)
   - [建立 DB - PowerShell](sql-database-get-started-powershell.md)

- 在此快速入門教學課程中，您所使用電腦的公用 IP 位址[伺服器層級防火牆規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。

- 您已安裝適用於您作業系統的 PHP 和相關軟體。

    - **MacOS**：安裝 Homebrew 和 PHP，安裝 ODBC 驅動程式和 SQLCMD，然後再安裝 PHP Driver for SQL Server。 請參閱[步驟 1.2、1.3 和 2.1](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/mac/)。
    - **Ubuntu**：安裝 PHP 和其他必要的套件，然後安裝 PHP Driver for SQL Server。 請參閱[步驟 1.2 和 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)。
    - **Windows**：安裝 PHP for IIS Express 的最新版本、Microsoft Drivers for SQL Server in IIS Express 的最新版本、Chocolatey、ODBC 驅動程式，以及 SQLCMD。 請參閱[步驟 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/)。    

## <a name="sql-server-connection-information"></a>SQL Server 連線資訊

取得連線到 Azure SQL Database 所需的連線資訊。 您在下一個程序中需要完整的伺服器名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 
3. 在您資料庫的 [概觀] 頁面上，檢閱如下圖所示的完整伺服器名稱。 您可以將滑鼠移至伺服器名稱上，以帶出 [按一下以複製] 選項。  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果您忘記伺服器登入資訊，請瀏覽至 [SQL Database 伺服器] 頁面來檢視伺服器系統管理員名稱，並視需要重設密碼。     
    
## <a name="insert-code-to-query-sql-database"></a>插入程式碼以查詢 SQL 資料庫

1. 在您慣用的文字編輯器中，建立新的檔案 **sqltest.php**。  

2. 使用下列程式碼取代內容，並為您的伺服器、資料庫、使用者和密碼新增適當的值。

   ```PHP
   <?php
   $serverName = "your_server.database.windows.net";
   $connectionOptions = array(
       "Database" => "your_database",
       "Uid" => "your_username",
       "PWD" => "your_password"
   );
   //Establishes the connection
   $conn = sqlsrv_connect($serverName, $connectionOptions);
   $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid";
   $getResults= sqlsrv_query($conn, $tsql);
   echo ("Reading data from table" . PHP_EOL);
   if ($getResults == FALSE)
       echo (sqlsrv_errors());
   while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
   }
   sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行下列命令：

   ```php
   php sqltest.php
   ```

2. 請確認前 20 個資料列已傳回，然後關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟
- [設計您的第一個 Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft PHP Drivers for SQL Server](https://github.com/Microsoft/msphpsql/)
- [回報問題或發問](https://github.com/Microsoft/msphpsql/issues)

