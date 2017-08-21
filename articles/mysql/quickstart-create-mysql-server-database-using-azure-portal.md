---
title: "快速入門：建立 Azure Database for MySQL 伺服器 - Azure 入口網站 | Microsoft Docs"
description: "本文逐步引導您使用 Azure 入口網站在五分鐘內快速建立範例 Azure Database for MySQL 伺服器。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/15/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 829c7e73cbf22d866bbd6fd54edc7a954ad7174c
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>使用 Azure 入口網站建立 Azure Database for MySQL 伺服器
「適用於 MySQL 的 Azure 資料庫」是一個受管理的服務，可讓您在雲端執行、管理及調整高可用性 MySQL 資料庫。 本快速入門說明如何使用 Azure 入口網站，在五分鐘內建立 Azure Database for MySQL 伺服器。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-azure"></a>登入 Azure
開啟 Web 瀏覽器並瀏覽至 [Microsoft Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。

## <a name="create-azure-database-for-mysql-server"></a>建立 Azure Database for MySQL 伺服器
建立的「適用於 MySQL 的 Azure 資料庫」伺服器會有一組已定義的[計算和儲存體](./concepts-compute-unit-and-storage.md)資源。 伺服器會建立在 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)內。

請依照下列步驟來建立 Azure Database for MySQL 伺服器：

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕 (+)。

2. 從 [新增] 頁面中選取 [資料庫]，然後從 [資料庫] 頁面中選取 [適用於 MySQL 的 Azure 資料庫]。 您也可以在新頁面搜尋方塊中輸入 **MySQL** 以找到此服務。
![Azure 入口網站 - 新增 - 資料庫 - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. 在新伺服器詳細資料表單中填入下列資訊，如上圖所示︰

    **設定** | **建議的值** | **欄位描述** 
    ---|---|---
    伺服器名稱 | myserver4demo | 選擇可識別 Azure Database for MySQL 伺服器的唯一名稱。 網域名稱 mysql.database.azure.com 已附加至您為要連線之應用程式提供的伺服器名稱。 伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元，且必須包含 3 到 63 個字元。
    訂用帳戶 | 您的訂用帳戶 | 您要用於伺服器的 Azure 訂用帳戶。 如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。
    資源群組 | myresourcegroup | 您可以產生新的資源群組名稱，或使用您訂用帳戶中現有的資源群組名稱。
    伺服器管理員登入 | myadmin | 產生自己的登入帳戶，以便在連線至伺服器時使用。 系統管理員登入名稱不能是 'azure_superuser'、'admin'、'administrator'、'root'、'guest' 或 'public'。
    密碼 | 您的選擇 | 為伺服器管理帳戶建立新密碼。 此密碼必須包含 8 到 128 個字元。 您的密碼必須包含下列三個類別的字元 - 英文大寫字母、英文小寫字母、數字 (0-9) 和非英數字元 (!、$、#、% 等)。
    確認密碼 | 您的選擇| 確認管理帳戶密碼。
    位置 | 最接近使用者的區域| 選擇最靠近您的使用者或其他 Azure 應用程式的位置。
    版本 | 選擇最新版本| 除非您有特定需求，否則選擇最新版本。
    定價層 | [基本]、[50 個計算單位]、[50 GB] | 按一下 [定價層] 指定新資料庫的服務層和效能等級。 選擇頂端索引標籤中的 [基本] 層。 按一下 [計算單位] 滑桿左端，將值調整為本快速入門可用的最小金額。 按一下 [確定] 以儲存定價層選取項目。 請參閱下列螢幕擷取畫面。
    釘選到儀表板 | 勾選 | 選取 [釘選到儀表板] 選項，在 Azure 入口網站的前儀表板頁面上輕鬆追蹤伺服器。

    > [!IMPORTANT]
    > 需要伺服器系統管理員登入以及您在此處指定的密碼，稍後才能在本快速入門中登入伺服器及其資料庫。 請記住或記錄此資訊，以供稍後使用。
    > 

    ![Azure 入口網站 - 提供需要表單輸入以建立 MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  按一下 [建立] 以佈建伺服器。 佈建需要幾分鐘的時間 (最多 20 分鐘)。
   
5.  在工具列上，按一下 [通知] (鈴鐺圖示) 以監視部署程序。

## <a name="configure-a-server-level-firewall-rule"></a>設定伺服器層級防火牆規則

Azure Database for MySQL 服務會在伺服器層級建立防火牆。 此防火牆會防止外部應用程式和工具連線到伺服器及伺服器上的任何資料庫，除非已建立防火牆規則以針對特定的 IP 位址開啟防火牆。 

1.  在部署完成之後，找出您的伺服器。 如有需要，您可以搜尋它。 例如，從左側功能表中按一下 [所有資源]，然後輸入伺服器名稱 (例如 myserver4demo) 來搜尋新建立的伺服器。 按一下搜尋結果中列出的伺服器名稱。 伺服器的 [概觀] 頁面隨即開啟，並提供可進行進一步設定的選項。

2. 在伺服器頁面中，選取 [連線安全性]。

3.  在 [防火牆規則] 標題之下，按一下 [規則名稱] 欄中的空白文字方塊，開始建立防火牆規則。 

    在本快速入門中，我們可在每一欄的文字方塊中填入下列值，以允許所有 IP 位址連到伺服器：

    規則名稱 | 起始 IP | 結束 IP 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. 在 [連線安全性] 頁面的工具列上，按一下 [儲存]。 等候一段時間，在繼續執行之前，注意可顯示更新連線安全性已成功完成的通知。

    > [!NOTE]
    > Azure Database for MySQL 的連線透過連接埠 3306 進行通訊。 如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 3306 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 3306，否則您將無法連線到您的伺服器。
    > 

## <a name="get-the-connection-information"></a>取得連線資訊
若要連線到您的資料庫伺服器，您必須記住完整伺服器名稱和系統管理員登入認證。 您稍早可能已在快速入門文章中記下這些值。 若未這麼做，您可以從 Azure 入口網站的伺服器 [概觀] 或 [屬性] 頁面輕鬆尋找伺服器名稱和登入資訊。

1. 開啟伺服器的 [概觀] 頁面。 記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。 
    將您的游標停留在每個欄位上，複製圖示就會出現在文字右邊。 視需要按一下複製圖示來複製值。

    在此範例中，伺服器名稱為 myserver4demo.mysql.database.azure.com，而伺服器管理員登入為 myadmin@myserver4demo。

## <a name="connect-to-mysql-using-mysql-command-line-tool"></a>使用 mysql 命令列工具連線到 MySQL
您可以使用一些應用程式來連線到您的 Azure Database for MySQL 伺服器。 我們將使用 [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) 命令列工具來說明如何連線到該伺服器。  您可以如這裡所描述使用網頁瀏覽器和 Azure Cloud Shell，而不需要安裝任何其他軟體。 如果您自己的電腦本機上已安裝 mysql 公用程式，您也可以從該處進行連線。

1. 透過 Azure 入口網站網頁頂端的終端機圖示 ( >_ ) 啟動 Azure Cloud Shell。

2. Azure Cloud Shell 會在您的瀏覽器中開啟，讓您能夠輸入 bash shell 命令。

    ![命令提示字元 - mysql 命令列範例](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. 在 Cloud Shell 提示字元，於綠色提示字元處鍵入 mysql 命令來連線到 Azure Database for MySQL 伺服器。

    下列格式可用來透過 mysql 公用程式連線到 Azure Database for MySQL 伺服器：
    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    例如，下列命令會連線至我們的範例伺服器：
    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    mysql 參數 |建議的值|說明
    ---|---|---
    --host | 伺服器名稱 | 指定您稍早建立 Azure Database for MySQL 時所使用的伺服器名稱值。 顯示的範例伺服器是 myserver4demo.mysql.database.azure.com。 使用如範例所示的完整網域名稱 (\*.mysql.database.azure.com)。 如果您不記得您的伺服器名稱，請依照上一節中的步驟執行，以取得連線資訊。 
    --user | 伺服器管理員登入名稱 |輸入您稍早建立 Azure Database for MySQL 時所提供的伺服器管理員登入名稱。 如果您不記得使用者名稱，請依照上一節中的步驟執行，以取得連線資訊。  格式為 *username@servername*。
    --password | 等到出現提示為止 | 系統會提示您在輸入命令之後「輸入密碼」。 出現提示時，輸入您在建立伺服器時所提供的相同密碼。  請注意，輸入的密碼字元不會顯示在 bash 提示字元上。 在您輸入所有字元以進行驗證和連線之後，按 Enter 鍵。

   一旦連線，mysql 公用程式會顯示 `mysql>` 提示字元，供您鍵入命令。 

    mysql 輸出範例：
    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > 如果未將防火牆設定為允許 Azure Cloud Shell 的 IP 位址，就會發生下列錯誤：
    >
    > ERROR 2003 (28000)：不允許 IP 位址為 123.456.789.0 的用戶端存取伺服器。
    >
    > 若要解決此錯誤，請確定伺服器組態符合本文「設定伺服器層級防火牆規則」一節中的步驟。

4. 檢視伺服器狀態來確保連線可運作。 一旦它連線後，在 mysql> 提示輸入 `status`。
    ```sql
    status
    ```

   > [!TIP]
   > 如需其他命令，請參閱 [MySQL 5.7 參考手冊 - 第 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)。

5.  在 mysql> 提示字元輸入下列命令以建立空白的資料庫︰
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    此命令可能需要一些時間才能完成。 

    在適用於 MySQL Server 的 Azure 資料庫內，您可以建立一個或多個資料庫。 您可以選擇在每個伺服器建立單一資料庫以利用所有資源，或建立多個資料庫來共用資源。 可以建立的資料庫數目沒有限制，但多個資料庫會共用相同的伺服器資源。 

6. 在 mysql> 提示字元輸入下列命令以列出資料庫︰

    ```sql
    SHOW DATABASES;
    ```

7.  輸入 `\q`，然後按 ENTER 結束 mysql 工具。 您可以在完成後關閉 Azure Cloud Shell。

現在您已連線到 Azure Database for MySQL，並已建立空白的使用者資料庫。 繼續進行下一節，重複類似的練習，以使用其他常見工具 (MySQL Workbench) 連線到相同的伺服器。

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>使用 MySQL Workbench GUI 工具連線到伺服器
若要使用 GUI 工具 MySQL Workbench 連線到 Azure MySQL 伺服器：

1.  在您的用戶端電腦上啟動 MySQL Workbench 應用程式。 您可以從[這裡](https://dev.mysql.com/downloads/workbench/)下載並安裝 MySQL Workbench。

2.  在 [設定新連線] 對話方塊的 [參數] 索引標籤上輸入下列資訊︰

    ![設定新連線](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    | **設定** | **建議的值** | **欄位描述** |
    |---|---|---|
    |   連線名稱 | 示範連線 | 指定此連線的標籤。 |
    | 連線方式 | 標準 (TCP/IP) | 標準 (TCP/IP) 就足夠了。 |
    | 主機名稱 | 伺服器名稱 | 指定您稍早建立 Azure Database for MySQL 時所使用的伺服器名稱值。 顯示的範例伺服器是 myserver4demo.mysql.database.azure.com。 使用如範例所示的完整網域名稱 (\*.mysql.database.azure.com)。 如果您不記得您的伺服器名稱，請依照上一節中的步驟執行，以取得連線資訊。  |
    | Port | 3306 | 連線至 Azure Database for MySQL 時一律使用連接埠 3306。 |
    | 使用者名稱 |  伺服器管理員登入名稱 | 輸入您稍早建立 Azure Database for MySQL 時所提供的伺服器管理員登入名稱。 我們的範例使用者名稱為 myadmin@myserver4demo。 如果您不記得使用者名稱，請依照上一節中的步驟執行，以取得連線資訊。 格式為 *username@servername*。
    | 密碼 | 您的密碼 | 按一下 [儲存在保存庫...] 按鈕以儲存密碼。 |

    按一下 [測試連線] 以測試所有參數是否都已設定正確。 按一下 [確定] 可儲存連線。 

    > [!NOTE]
    > 預設會在您的伺服器上強制執行 SSL，該伺服器需要額外的設定才能連線成功。 請參閱[在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MySQL 的 Azure 資料庫](./howto-configure-ssl.md)。  如果您想要停用這個快速入門的 SSL，請造訪 Azure 入口網站，然後按一下 [連線安全性] 頁面，以停用強制 SSL 連線切換按鈕。

## <a name="clean-up-resources"></a>清除資源
藉由刪除 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md) (其包含資源群組中的所有資源)，或藉由刪除一個伺服器資源 (如果您希望其他資源維持不變)，清除您在快速入門中建立的資源。

> [!TIP]
> 此集合中的其他快速入門會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源。
>

若要刪除整個資源群組 (包括新建立的伺服器)：
1.  在 Azure 入口網站中找出您的資源群組。 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您的資源群組名稱 (例如 **myresourcegroup**)。
2.  在資源群組頁面上，按一下 [刪除]。 接著在文字方塊中輸入您的資源群組名稱 (例如 **myresourcegroup**) 以確認刪除，然後按一下 [刪除]。

或者要刪除新建立的伺服器：
1.  請在 Azure 入口網站中尋找您的伺服器 (如果您未將它開啟)。 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後搜尋您建立的伺服器。
2.  在 [概觀] 頁面上，按一下上方窗格的 [刪除] 按鈕。
![Azure Database for MySQL - 刪除伺服器](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  確認您要刪除的伺服器名稱，並且會顯示其下受影響的資料庫。 在文字方塊中輸入您的伺服器名稱 (例如 **myserver4demo**)，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設計您的第一個 Azure Database for MySQL 資料庫](./tutorial-design-database-using-portal.md)


