
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-11-28 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started.md
articles/sql-database/sql-database-configure-firewall-settings
articles/sql-data-warehouse-get-started-provision.md

-->
### <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 入口網站中建立伺服器層級的防火牆規則

1. 在 SQL Server 刀鋒視窗的 [設定] 下，按一下 [防火牆] 來開啟 SQL Server 的防火牆刀鋒視窗。

    <!-- ![sql server firewall](../articles/sql-database/media/sql-database-get-started/sql-server-firewall.png) -->

2. 檢閱顯示的用戶端 IP 位址，並使用自行選擇的瀏覽器來驗證這是否為您在網際網路上的 IP 位址 (問「我的 IP 位址是什麼)。 偶爾會因為各種原因而不相符。

    <!-- ![your IP address](../articles/sql-database/media/sql-database-get-started/your-ip-address.png) -->

3. 假設的 IP 位址相符，按一下工具列上的 [新增用戶端 IP]。

    ![新增用戶端 IP](../articles/sql-data-warehouse/media/sql-data-warehouse-get-started-provision/add-client-ip.png)

    > [!NOTE]
    > 您可以在伺服器上的 SQL Database 防火牆，針對單一 IP 位址或整個位址區段作開放。 開放防火牆可讓 SQL 系統管理員和使用者登入伺服器上，任何他們具備有效認證的資料庫。
    >

4. 按一下工具列上的 儲存 來儲存此項伺服器層級防火牆規則，然後按一下確定。

    ![新增用戶端 IP](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png)

> [!Tip]
> 如需教學課程，請參閱 [SQL Database 教學課程︰建立伺服器、伺服器層級防火牆規則、範例資料庫、資料庫層級防火牆規則，以及與 SQL Server 連接](../articles/sql-database/sql-database-get-started.md)。    
>
