可用性群組接聽程式是一個 IP 位址及網路名稱，可供 SQL Server 可用性群組接聽。 若要建立可用性群組接聽程式，請執行下列步驟：

1. <a name="getnet"></a>取得叢集網路資源名稱。

    a. 使用 RDP 連接到裝載主要複本的 Azure 虛擬機器。 

    b.這是另一個 C# 主控台應用程式。 開始容錯移轉叢集管理員。

    c. 選取 [網路]  節點，然後記下叢集網路名稱。 請在 PowerShell 指令碼的 `$ClusterNetworkName` 變數中使用這個名稱。 下圖中的叢集網路名稱為**叢集網路 1**：

   ![叢集網路名稱](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>新增用戶端存取點。  
    用戶端存取點是一個網路名稱，應用程式可用來連線到可用性群組中的資料庫。 在容錯移轉叢集管理員中建立用戶端存取點。

    a. 展開叢集名稱，然後按一下角色 。

    b.這是另一個 C# 主控台應用程式。 在 [角色] 窗格中，以滑鼠右鍵按一下可用性群組名稱，然後選取 [新增資源] > [用戶端存取點]。

   ![用戶端存取點](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. 在 [名稱] 方塊中，建立新接聽程式的名稱。 
   新接聽程式的名稱是應用程式將用來連線到 SQL Server 可用性群組中資料庫的網路名稱。
   
    d. 若要完成建立接聽程式，請按一下 [下一步] 兩次，再按一下 [結束]。 目前請勿讓接聽程式或資源上線工作。

3. <a name="congroup"></a>為可用性群組設定 IP 資源。

    a. 按一下 [資源] 索引標籤，然後展開您建立的用戶端存取點。  
    用戶端存取點離線。

   ![用戶端存取點](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b.這是另一個 C# 主控台應用程式。 以滑鼠右鍵按一下 IP 資源，然後按一下屬性。 記下 IP 位址的名稱，並將它使用於 PowerShell 指令碼的 `$IPResourceName` 變數中。

    c. 在 [IP 地址] 下，按一下 [靜態 IP 位址]。 將 [IP 位址] 設為與您在 Azure 入口網站中設定負載平衡器的相同地址。

   ![IP 資源](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>讓 SQL Server 可用性群組資源依存於用戶端存取點。

    a. 在 [容錯移轉叢集管理員] 中，按一下 [角色]，然後按一下您的 [可用性群組]。

    b.這是另一個 C# 主控台應用程式。 在 [資源] 索引標籤上，以滑鼠右鍵按一下 [其他資源] 下的可用性資源群組，並按一下 [屬性]。 

    c. 在 [相依性] 索引標籤上，新增用戶端存取點 (接聽程式) 資源的名稱。

   ![IP 資源](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. 按一下 [確定] 。

5. <a name="listname"></a>讓用戶端存取點資源依存於 IP 位址。

    a. 在 [容錯移轉叢集管理員] 中，按一下 [角色]，然後按一下您的 [可用性群組]。 

    b.這是另一個 C# 主控台應用程式。 在 資源 索引標籤中，以滑鼠右鍵按一下 伺服器名稱 下的用戶端存取點資源，然後按一下屬性。 

   ![IP 資源](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. 按一下 [相依性]  索引標籤。確認 IP 位址是相依性。 如果不是，請在 IP 位址上設定相依性。 如果列出多個資源，請確認 IP 位址具有 OR 相依性，而非 AND。 按一下 [確定] 。 

   ![IP 資源](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. 以滑鼠右鍵按一下接聽程式名稱，然後按一下上線。 

    >[!TIP]
    >您可以驗證相依性是否已正確設定。 在 容錯移轉叢集管理員 中，移至 角色，以滑鼠右鍵按一下可用性群組，按一下 其他動作，然後按一下顯示相依性報告。 正確設定相依性後，可用性群組會相依於網路名稱，而網路名稱則相依於 IP 位址。 


6. <a name="setparam"></a>在 PowerShell 中設定叢集參數。
    
    a. 將下列 PowerShell 指令碼複製到您的其中一個 SQL Server 執行個體。 請更新您環境的變數。     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b.這是另一個 C# 主控台應用程式。 在其中一個叢集節點中執行 PowerShell 指令碼，以設定叢集參數。  

    > [!NOTE]
    > 如果您的 SQL Server 執行個體位於不同的區域中，您需要執行 PowerShell 指令碼兩次。 若是第一次，請使用第一個區域的 `$ILBIP` 和 `$ProbePort`。 若是第二次，請使用第二個區域的 `$ILBIP` 和 `$ProbePort`。 叢集網路名稱和叢集 IP 資源名稱相同。 
