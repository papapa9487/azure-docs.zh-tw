---
title: "從 MySQL Workbench 連線到 Azure Database for MySQL | Microsoft Docs"
description: "本快速入門提供的步驟，可以使用 MySQL Workbench 來連線及查詢 Azure Database for MySQL 的資料。"
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: seanli1988
ms.service: mysql-database
ms.custom: mvc
ms.topic: article
ms.date: 08/23/2017
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: ffeca6670521fe2b43c4ac47cac26fd2ad30f968
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---

# <a name="azure-database-for-mysql-use-mysql-workbench-to-connect-and-query-data"></a>Azure Database for MySQL︰使用 MySQL Workbench 來連線及查詢資料
本快速入門示範如何使用 MySQL Workbench 應用程式來連線到 Azure Database for MySQL。 

## <a name="prerequisites"></a>必要條件
本快速入門使用在以下任一指南中建立的資源作為起點︰
- [使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫伺服器](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [使用 Azure CLI 建立適用於 MySQL 的 Azure 資料庫伺服器](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>安裝 MySQL Workbench
從 [MySQL 網站](https://dev.mysql.com/downloads/workbench/)下載 MySQL Workbench，並安裝在您的電腦上。

## <a name="get-connection-information"></a>取得連線資訊
取得連線到 Azure Database for MySQL 所需的連線資訊。 您需要完整的伺服器名稱和登入認證。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後搜尋您所建立的伺服器，例如 **myserver4demo**。

3. 按一下伺服器名稱。

4. 選取伺服器的 [屬性] 頁面。 記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。

 ![Azure Database for MySQL 伺服器名稱](./media/connect-workbench/1-server-properties-name-login.png)
 
5. 如果您忘記伺服器登入資訊，請瀏覽至 [概觀] 頁面來檢視伺服器管理員登入名稱，並視需要重設密碼。

## <a name="connect-to-the-server-using-mysql-workbench"></a>使用 MySQL Workbench 來連線到伺服器 
若要使用 GUI 工具 MySQL Workbench 連線到 Azure MySQL 伺服器：

1.  在您的電腦上啟動 MySQL Workbench 應用程式。 

2.  在 [設定新連線] 對話方塊的 [參數] 索引標籤上輸入下列資訊︰

    ![設定新連線](./media/connect-workbench/2-setup-new-connection.png)

    | **設定** | **建議的值** | **欄位描述** |
    |---|---|---|
    |   連線名稱 | 示範連線 | 指定此連線的標籤。 |
    | 連線方式 | 標準 (TCP/IP) | 標準 (TCP/IP) 就足夠了。 |
    | 主機名稱 | 伺服器名稱 | 指定您稍早建立 Azure Database for MySQL 時所使用的伺服器名稱值。 顯示的範例伺服器是 myserver4demo.mysql.database.azure.com。使用如範例所示的完整網域名稱 (\*.mysql.database.azure.com)。 如果您不記得您的伺服器名稱，請依照上一節中的步驟執行，以取得連線資訊。  |
    | Port | 3306 | 連線至 Azure Database for MySQL 時一律使用連接埠 3306。 |
    | 使用者名稱 |  伺服器管理員登入名稱 | 輸入您稍早建立 Azure Database for MySQL 時所提供的伺服器管理員登入名稱。 我們的範例使用者名稱為 myadmin@myserver4demo。 如果您不記得使用者名稱，請依照上一節中的步驟執行，以取得連線資訊。 格式為 *username@servername*。
    | 密碼 | 您的密碼 | 按一下 [儲存在保存庫...] 按鈕以儲存密碼。 |

3.   按一下 [測試連線] 以測試所有參數是否都已設定正確。 

4.   按一下 [確定] 可儲存連線。 

5.   在 [MySQL 連線] 清單中，按一下對應至您伺服器的圖格，並等候建立連線。

6.   新的 SQL 索引標籤隨即開啟並出現空白的編輯器，可供您輸入查詢。

    > [!NOTE]
    > 預設會在您的伺服器上強制執行 SSL，該伺服器需要額外的設定才能連線成功。 請參閱[在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MySQL 的 Azure 資料庫](./howto-configure-ssl.md)。  如果您想要停用這個快速入門的 SSL，請造訪 Azure 入口網站，然後按一下 [連線安全性] 頁面，以停用強制 SSL 連線切換按鈕。

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>建立資料表、將資料插入、讀取資料、更新資料、刪除資料
1. 將範例 SQL 程式碼複製並貼到空白的 SQL 索引標籤，來說明某些範例資料。

    這段程式碼會建立名為 quickstartdb 的空白資料庫，然後會建立名為 inventory 的範例資料表。 它會插入一些資料列，然後讀取資料列。 它會使用 update 陳述式將資料進行變更，並再次讀取資料列。 最後會刪除資料列，然後再次讀取資料列。
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    執行完畢後，螢幕擷取畫面會在 SQL Workbench 中顯示 SQL 程式碼的範例和輸出。
    
    ![執行範例 SQL 程式碼的 MySQL Workbench SQL 索引標籤](media/connect-workbench/3-workbench-sql-tab.png)

2. 若要執行範例 SQL 程式碼，請在 [SQL 檔案] 索引標籤的工具列中按一下閃電圖示。
3. 請注意頁面中間的**結果集**區段中的三個索引標籤式結果。 
4. 請注意頁面底部的 [輸出] 清單。 隨即顯示每個命令的狀態。 

現在，您已使用 MySQL Workbench 連線到 Azure Database for MySQL，並使用 SQL 語言查詢資料。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用匯出和匯入來移轉資料庫](./concepts-migrate-import-export.md)

