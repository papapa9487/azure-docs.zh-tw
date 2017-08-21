---
title: "Azure 入口網站：建立適用於 PostgreSQL 的 Azure 資料庫 | Microsoft Docs"
description: "本快速入門指南說明如何使用 Azure 入口網站使用者介面，以建立及管理適用於 PostgreSQL 的 Azure 資料庫伺服器。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/10/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 8bba5a97fdc4bbc15fe996ee68daf5b796d1bfac
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>在 Azure 入口網站中建立適用於 PostgreSQL 的 Azure 資料庫

「適用於 PostgreSQL 的 Azure 資料庫」是一種受管理的服務，可讓您在雲端執行、管理及調整高可用性的 PostgreSQL 資料庫。 本快速入門說明如何使用 Azure 入口網站，在五分鐘內建立 Azure Database for PostgreSQL 伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站
開啟 Web 瀏覽器並瀏覽至 [Microsoft Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。

## <a name="create-an-azure-database-for-postgresql"></a>建立適用於 PostgreSQL 的 Azure 資料庫

「適用於 PostgreSQL 的 Azure 資料庫」伺服器是以一組已定義的[計算和儲存體資源](./concepts-compute-unit-and-storage.md)所建立。 伺服器會建立在 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)內。

請依照下列步驟來建立「適用於 PostgreSQL 的 Azure 資料庫」伺服器：
1.  按一下 Azure 入口網站左上角的 [新增] 按鈕 (+)。
2.  從 [新增] 頁面中選取 [資料庫]，然後從 [資料庫] 頁面中選取 [適用於 PostgreSQL 的 Azure 資料庫]。
 ![適用於 PostgreSQL 的 Azure 資料庫 - 建立資料庫](./media/quickstart-create-database-portal/1-create-database.png)

3.  在新伺服器詳細資料表單中填入下列資訊，如上圖所示︰

    設定|建議的值|說明
    ---|---|---
    伺服器名稱 |*mypgserver-20170401*|選擇可識別 Azure Database for PostgreSQL 伺服器的唯一名稱。 網域名稱 postgres.database.azure.com 已附加至您為要連線之應用程式提供的伺服器名稱。 伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元，且必須包含 3 到 63 個字元。
    訂用帳戶|*您的訂用帳戶*|您要用於伺服器的 Azure 訂用帳戶。 如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。
    資源群組|*myresourcegroup*| 您可以產生新的資源群組名稱，或使用您訂用帳戶中現有的資源群組名稱。
    伺服器管理員登入 |*mylogin*| 產生自己的登入帳戶，以便在連線至伺服器時使用。 系統管理員登入名稱不能是 'azure_superuser'、'azure_pg_admin'、'admin'、'administrator'、'root'、'guest' 或 'public'，而且不能以 'pg_' 開頭。
    密碼 |您的選擇 | 為伺服器管理帳戶建立新密碼。 此密碼必須包含 8 到 128 個字元。 您的密碼必須包含下列三個類別的字元 - 英文大寫字母、英文小寫字母、數字 (0-9) 和非英數字元 (!、$、#、% 等)。
    位置|最接近使用者的區域| 選擇最靠近您的使用者的位置。
    PostgreSQL 版本|選擇最新版本| 除非您有特定需求，否則選擇最新版本。
    定價層 | [基本]、[50 個計算單位]、[50 GB] | 按一下 [定價層] 指定新資料庫的服務層和效能等級。 選擇頂端索引標籤中的 [基本] 層。 按一下 [計算單位] 滑桿左端，將值調整為本快速入門可用的最小金額。 按一下 [確定] 以儲存定價層選取項目。 請參閱下列螢幕擷取畫面。
    | 釘選到儀表板 | 勾選 | 選取 [釘選到儀表板] 選項，在 Azure 入口網站的前儀表板頁面上輕鬆追蹤伺服器。

  > [!IMPORTANT]
  > 需要伺服器系統管理員登入以及您在此處指定的密碼，稍後才能在本快速入門中登入伺服器及其資料庫。 請記住或記錄此資訊，以供稍後使用。

    ![Azure Database for PostgreSQL - 挑選定價層](./media/quickstart-create-database-portal/2-service-tier.png)

4.  按一下 [建立] 以佈建伺服器。 佈建需要幾分鐘的時間 (最多 20 分鐘)。

5.  在工具列上，按一下 [通知] 以監視部署程序。
 ![適用於 PostgreSQL 的 Azure 資料庫 - 查看通知](./media/quickstart-create-database-portal/3-notifications.png)
   
  根據預設，**postgres** 資料庫會建立在您的伺服器底下。 [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) 資料庫是要供使用者、公用程式及第三方應用程式使用的預設資料庫。 

## <a name="configure-a-server-level-firewall-rule"></a>設定伺服器層級防火牆規則

「適用於 PostgreSQL 的 Azure 資料庫」服務會在伺服器層級建立防火牆。 此防火牆會防止外部應用程式和工具連線到伺服器及伺服器上的任何資料庫，除非已建立防火牆規則以針對特定的 IP 位址開啟防火牆。 

1.  在部署完成之後，找出您的伺服器。 如有需要，您可以搜尋它。 例如，從左側功能表中按一下 [所有資源]，然後輸入伺服器名稱 (例如 mypgserver-20170401) 來搜尋新建立的伺服器。 按一下搜尋結果中列出的伺服器名稱。 伺服器的 [概觀] 頁面隨即開啟，並提供可進行進一步設定的選項。
 
    ![Azure Database for PostgreSQL - 搜尋伺服器名稱](./media/quickstart-create-database-portal/4-locate.png)

2.  在伺服器頁面上，選取 [連線安全性]。 
    ![Azure Database for PostgreSQL - 建立防火牆規則](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  在 [防火牆規則] 標題之下，按一下 [規則名稱] 欄中的空白文字方塊，開始建立防火牆規則。 

    在本快速入門中，我們可在每一欄的文字方塊中填入下列值，以允許所有 IP 位址連到伺服器：

    規則名稱 | 起始 IP | 結束 IP 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. 在 [連線安全性] 頁面的工具列上，按一下 [儲存]。 等候一段時間，在繼續執行之前，注意可顯示更新連線安全性已成功完成的通知。

    > [!NOTE]
    > 您的 Azure Database for PostgreSQL 伺服器連線會透過連接埠 5432 通訊。 如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 5432 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 5432，否則您將無法連線到您的伺服器。
    >

## <a name="get-the-connection-information"></a>取得連線資訊

當我們建立 Azure Database for PostgreSQL 伺服器時，系統會建立名為 **postgres** 的預設資料庫。 若要連線到您的資料庫伺服器，您必須記住完整伺服器名稱和系統管理員登入認證。 您稍早可能已在快速入門文章中記下這些值。 若未這麼做，您可以從 Azure 入口網站的伺服器 [概觀] 頁面輕鬆尋找伺服器名稱和登入資訊。

1. 開啟伺服器的 [概觀] 頁面。 記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。
    將您的游標停留在每個欄位上，複製圖示就會出現在文字右邊。 視需要按一下複製圖示來複製值。

 ![適用於 PostgreSQL 的 Azure 資料庫 - 伺服器管理員登入](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>在 Cloud Shell 中使用 psql 來連線到 PostgreSQL 資料庫

您可以使用一些應用程式來連線到您的 Azure Database for PostgreSQL 伺服器。 我們將使用 psql 命令列公用程式來說明如何連線到該伺服器。  您可以如這裡所描述使用網頁瀏覽器和 Azure Cloud Shell，而不需要安裝任何其他軟體。 如果您自己的電腦本機上已安裝 psql 公用程式，您也可以從該處進行連線。

1. 透過頂端瀏覽窗格上的終端機圖示啟動 Azure Cloud Shell。

   ![適用於 PostgreSQL 的 Azure 資料庫 - Azure Cloud Shell 終端機圖示](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Azure Cloud Shell 會在您的瀏覽器中開啟，讓您能夠輸入 bash shell 命令。

   ![適用於 PostgreSQL 的 Azure 資料庫 - Azure Shell Bash 提示字元](./media/quickstart-create-database-portal/8-bash.png)

3. 在 Cloud Shell 提示字元的綠色提示字元處，鍵入 psql 命令列，以連線到 Azure Database for PostgreSQL 伺服器。

    下列格式可用來透過 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 公用程式連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器：
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    例如，下列命令會連線至範例伺服器：

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    psql 參數 |建議的值|說明
    ---|---|---
    --host | 伺服器名稱 | 指定您稍早建立 Azure Database for PostgreSQL 時所使用的伺服器名稱值。 顯示的範例伺服器是 mypgserver-20170401.postgres.database.azure.com。 使用如範例所示的完整網域名稱 (\*.postgres.database.azure.com)。 如果您不記得您的伺服器名稱，請依照上一節中的步驟執行，以取得連線資訊。 
    --port | **5432** | 連線至 Azure Database for PostgreSQL 時一律使用連接埠 5432。 
    --username | 伺服器管理員登入名稱 |輸入您稍早建立 Azure Database for PostgreSQL 時所提供的伺服器管理員登入名稱。 如果您不記得使用者名稱，請依照上一節中的步驟執行，以取得連線資訊。  格式為 *username@servername*。
    --dbname | **postgres** | 使用預設系統產生的資料庫名稱 postgres 進行第一次連線。 您稍後會建立自己的資料庫。

    執行 psql 命令之後，使用您自己的參數值，系統會提示您輸入伺服器管理員密碼。 這是您在建立伺服器時所提供的相同密碼。 

    psql 參數 |建議的值|說明
    ---|---|---
    password | *您的系統管理員密碼* | 請注意，輸入的密碼字元不會顯示在 bash 提示字元上。 在您輸入所有字元以進行驗證和連線之後，按 Enter 鍵。

    一旦連線，psql 公用程式會顯示 postgres 提示字元，供您鍵入 sql 命令。 在初始連線輸出中，有可能因為 Azure Cloud Shell 中的 psql 版本與 Azure Database for PostgreSQL 伺服器版本不同，而顯示警告。 
    
    psql 輸出範例：
    ```bash
    psql (9.5.7, server 9.6.2)
    WARNING: psql major version 9.5, server major version 9.6.
        Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
    Type "help" for help.
   
    postgres=> 
    ```

    > [!TIP]
    > 如果未將防火牆設定為允許 Azure Cloud Shell 的 IP 位址，就會發生下列錯誤：
    > 
    > "psql: FATAL:  no pg_hba.conf entry for host "138.91.195.82", user "mylogin", database "postgres", SSL on FATAL：需要 SSL 連線。 請指定 SSL 選項，然後再試一次。
    > 
    > 若要解決此錯誤，請確定伺服器組態符合本文「設定伺服器層級防火牆規則」一節中的步驟。

4.  在提示字元輸入下列命令以建立空白的資料庫︰
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    此命令可能需要一些時間才能完成。 

5.  在提示字元，執行下列命令以將連線切換到新建立的資料庫 **mypgsqldb**。
    ```bash
    \c mypgsqldb
    ```

6.  輸入 \q，然後按 ENTER 結束 psql。 您可以在完成後關閉 Azure Cloud Shell。

現在您已連線到 Azure Database for PostgreSQL，並已建立空白的使用者資料庫。 繼續進行下一節，以使用另一個常見的工具 pgAdmin 連線。

## <a name="connect-to-postgresql-database-using-pgadmin"></a>使用 pgAdmin 連線到 PostgreSQL 資料庫

若要使用 GUI 工具_pgAdmin_ 連線到 Azure PostgreSQL 伺服器：
1.  在您的用戶端電腦上啟動 _pgAdmin_ 應用程式。 您可以從 http://www.pgadmin.org/ 安裝 _pgAdmin_。
2.  從 [儀表板] 頁面中央的 [快速連結] 區段，按一下 [新增伺服器] 圖示。
3.  在 [Create - Server (建立 - 伺服器)] 對話方塊的 [General (一般)] 索引標籤上，輸入伺服器的唯一易記名稱，例如 **Azure PostgreSQL Server 伺服器**。
![pgAdmin 工具 - 建立 - 伺服器](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  在 [Create - Server (建立 - 伺服器)] 對話方塊中的 [Connection (連線)] 索引標籤，使用如指定的設定，然後按一下 [Save (儲存)]。
   ![pgAdmin - 建立 - 伺服器](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    pgAdmin 參數 |建議的值|說明
    ---|---|---
    主機名稱/位址 | 伺服器名稱 | 指定您稍早建立 Azure Database for PostgreSQL 時所使用的伺服器名稱值。 顯示的範例伺服器是 mypgserver-20170401.postgres.database.azure.com。 使用如範例所示的完整網域名稱 (\*.postgres.database.azure.com)。 如果您不記得您的伺服器名稱，請依照上一節中的步驟執行，以取得連線資訊。 
    Port | **5432** | 連線至 Azure Database for PostgreSQL 時一律使用連接埠 5432。  
    維護資料庫 | **postgres** | 使用預設系統產生的資料庫名稱 postgres。
    使用者名稱 | 伺服器管理員登入名稱 | 輸入您稍早建立 Azure Database for PostgreSQL 時所提供的伺服器管理員登入名稱。 如果您不記得使用者名稱，請依照上一節中的步驟執行，以取得連線資訊。 格式為 *username@servername*。
    密碼 | *您的系統管理員密碼* |  您在本快速入門稍早建立伺服器時所選擇的密碼。
    角色 | 保留空白 | 此時不需要提供角色名稱。 將欄位保留空白。
    SSL 模式 | 必要 | 根據預設，所有的 Azure PostgreSQL 伺服器建立時都會開啟強制使用 SSL。 若要關閉強制使用 SSL，請參閱[強制使用 SSL](./concepts-ssl-connection-security.md) 中的詳細資訊。
    
5.  按一下 [儲存] 。
6.  在 [瀏覽器] 左窗格中，展開 [伺服器] 節點。 選擇您的伺服器，例如 [Azure PostgreSQL Server]，然後按一下來連線。
7. 展開伺服器節點，然後展開其下的 [資料庫]。 此清單應包含現有的 postgres 資料庫，以及我們在上一節中建立的任何新建使用者資料庫 (例如 mypgsqldb)。 請注意，您可以使用 Azure Database for PostgreSQL，為每一部伺服器建立多個資料庫。
8. 以滑鼠右鍵按一下 [資料庫]，選擇 [建立] 功能表，然後按一下 [資料庫]。
9.  在 [資料庫] 欄位中輸入您選擇的資料庫名稱，如範例中所示的 mypgsqldb。 
10. 從下拉式清單方塊中選取資料庫的 [擁有者]。 選擇您的伺服器系統管理員登入名稱，例如 mylogin。
10. 按一下 [儲存] 以建立新的空白資料庫。
11. 在 [瀏覽器] 窗格中，在您的伺服器名稱之下的 [資料庫] 清單中查看您所建立的資料庫。
 ![pgAdmin - 建立 - 資料庫](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>清除資源
藉由刪除 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md) (其包含資源群組中的所有資源)，或藉由刪除一個伺服器資源 (如果您希望其他資源維持不變)，清除您在快速入門中建立的資源。

> [!TIP]
> 此集合中的其他快速入門會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的資源。

若要刪除整個資源群組 (包括新建立的伺服器)：
1.  在 Azure 入口網站中找出您的資源群組。 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您的資源群組名稱 (例如 **myresourcegroup**)。
2.  在資源群組頁面上，按一下 [刪除]。 接著在文字方塊中輸入您的資源群組名稱 (例如 **myresourcegroup**) 以確認刪除，然後按一下 [刪除]。

或者要刪除新建立的伺服器：
1.  請在 Azure 入口網站中尋找您的伺服器 (如果您未將它開啟)。 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後搜尋您建立的伺服器。
2.  在 [概觀] 頁面上，按一下上方窗格的 [刪除] 按鈕。
![Azure Database for PostgreSQL - 刪除伺服器](./media/quickstart-create-database-portal/12-delete.png)
3.  確認您要刪除的伺服器名稱，並且會顯示其下受影響的資料庫。 在文字方塊中輸入您的伺服器名稱 (例如 **mypgserver-20170401**)，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用匯出和匯入來移轉資料庫](./howto-migrate-using-export-and-import.md)

