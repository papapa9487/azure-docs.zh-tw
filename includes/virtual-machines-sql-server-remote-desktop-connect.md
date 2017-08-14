使用下列步驟，透過遠端桌面連線到 SQL Server 虛擬機器︰

1. 建立並執行 Azure 虛擬機器後，請按一下 Azure 入口網站中的 [虛擬機器] 圖示，以檢視您的 VM。

1. 按一下新 VM 的省略符號 (**...**)。

1. 按一下 [ **連接**]。

   ![在入口網站中連線到 VM](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. 開啟您的瀏覽器為 VM 下載的 **RDP** 檔案。

1. [遠端桌面連線] 會通知您無法識別這個遠端連線的發行者。 按一下 [連接]  以繼續。

1. 在 [Windows 安全性] 對話方塊中，按一下 [使用不同帳戶]。 您可能必須按一下 [更多選擇] 才能看到它。 指定您建立 VM 時所設定的使用者名稱和密碼。 您必須在使用者名稱之前新增反斜線。

   ![遠端桌面驗證](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

連線到 SQL Server 虛擬機器之後，您可以啟動 SQL Server Management Studio，然後使用您的本機系統管理員認證透過「Windows 驗證」進行連線。 如果您已啟用 SQL Server 驗證，您也可以使用您在佈建期間所設定的 SQL 登入和密碼以 SQL 驗證連線。

存取電腦可讓您根據您的需求直接變更電腦和 SQL Server 設定。 例如，您可以設定防火牆設定或變更 SQL Server 組態設定。