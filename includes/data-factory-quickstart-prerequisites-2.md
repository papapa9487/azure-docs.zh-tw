### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-powershell"></a>安裝 PowerShell
如果您的電腦上沒有最新的 PowerShell，請加以安裝。 

1. 在網頁瀏覽器中，瀏覽至 [Azure 下載](https://azure.microsoft.com/downloads/)頁面。 
2. 按一下 [命令列工具] -> [PowerShell] 區段中的 [Windows 安裝]。 
3. 若要安裝 PowerShell，請執行 **MSI** 檔案。 

如需詳細指示，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/install-azurerm-ps)。 

#### <a name="log-in-to-powershell"></a>登入 PowerShell

1. 在您的電腦上啟動 **PowerShell**。 讓 PowerShell 保持開啟，直到本快速入門結束為止。 如果您關閉並重新開啟，則需要再次執行這些命令。

    ![啟動 PowerShell](media/data-factory-quickstart-prerequisites-2/search-powershell.png)
1. 執行下列命令，並輸入您用來登入 Azure 入口網站的相同 Azure 使用者名稱和密碼：
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. 執行下列命令以檢視此帳戶的所有訂用帳戶：

    ```powershell
    Get-AzureRmSubscription
    ```
3. 如果您發現您的帳戶與多個訂用帳戶相關聯，請執行下列命令來選取您需要使用的訂用帳戶。 以您的 Azure 訂用帳戶識別碼取代 **SubscriptionId**：

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
