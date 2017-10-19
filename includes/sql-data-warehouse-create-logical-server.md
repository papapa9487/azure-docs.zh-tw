### <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>在 Azure 入口網站中建立新的邏輯 SQL Server

1. 按一下 [新增]，搜尋**邏輯伺服器**，然後按 **ENTER**。

    ![搜尋邏輯伺服器](./media/sql-data-warehouse-create-logical-server/search-logical-server.png)
2. 選取 [SQL Server (邏輯伺服器)] 

    ![選取邏輯伺服器](./media/sql-data-warehouse-create-logical-server/select-logical-server.png)
  
3. 按一下 [建立] 以開啟新的 SQL Server (邏輯伺服器) 刀鋒視窗。

   <kbd> ![開啟邏輯伺服器刀鋒視窗](./media/sql-data-warehouse-create-logical-server/open-logical-server-blade.png) </kbd> <kbd>![邏輯伺服器刀鋒視窗](./media/sql-data-warehouse-create-logical-server/logical-server-blade.png) </kbd>
  
3. 在 [SQL Server (邏輯伺服器)] 刀鋒視窗的伺服器名稱文字方塊中，為新的邏輯伺服器提供有效名稱。 綠色核取記號指示您提供的名稱有效。
    
    ![新的伺服器名稱](./media/sql-data-warehouse-create-logical-server/new-name-logical-server.png)

    > [!IMPORTANT]
    > 您的新伺服器完整名稱將會是 <your_server_name>.database.windows.net。
    >
    
4. 在 [伺服器管理登入] 文字方塊中，為此伺服器提供用於 SQL 驗證登入的使用者名稱。 此登入就是所謂的伺服器主體登入。 綠色核取記號指示您提供的名稱有效。
    
    ![SQL 管理員登入](./media/sql-data-warehouse-create-logical-server/sql-admin-login.png)
5. 在 [密碼] 和 [確認密碼] 文字方塊中，提供伺服器主體登入帳戶的密碼。 綠色核取記號指示您提供的密碼有效。
    
    ![SQL 管理員密碼](./media/sql-data-warehouse-create-logical-server/sql-admin-password.png)
6. 選取您有權限建立物件的訂用帳戶。

    ![訂用帳戶](./media/sql-data-warehouse-create-logical-server/subscription.png)
7. 在 [資源群組] 文字方塊中，選取 [建立新的]，接著在 [資源群組] 文字方塊中，為新的資源群組提供有效名稱 (如果您已為自己建立了一個資源群組，也可以使用現有的資源群組)。 綠色核取記號指示您提供的名稱有效。

    ![新的資源群組](./media/sql-data-warehouse-create-logical-server/new-resource-group.png)

8. 在 [位置] 文字方塊中，選取您的位置適用的資料中心 - 例如「澳大利亞東部」。
    
    ![伺服器位置](./media/sql-data-warehouse-create-logical-server/server-location.png)
    
    > [!TIP]
    > [允許 Azure 服務存取伺服器] 核取方塊無法在此刀鋒視窗中變更。 您可以在伺服器防火牆刀鋒視窗上變更此設定。 如需詳細資訊，請參閱[安全性入門](../articles/sql-database/sql-database-manage-servers-portal.md)。
    >
    
9. 按一下 [建立] 。

    ![建立按鈕](./media/sql-data-warehouse-create-logical-server/create.png)

